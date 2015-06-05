# Supporting HTTP Cache Validation

# Problem

You want to know how to support conditional GET requests for cache validation of representations served by your service.

# Solution

Use a *strong validator* whose value is changed each time the underlying data is changed as an *ETag*. The details of the solution you adopt will depend upon whether or not you have control over the data store used for storing resources.

If you have control on the data store used for storing resources, modify the storage schema for each resource to include a timestamp for the modified date-time and a sequence number to keep track of a version.

In the case of relational databases, use database triggers to automatically update these fields whenever the data is modified.

If you cannot modify the storage schema or if your data store does not permit maintaining timestamps or sequence numbers, use some function of the resource data to generate a value for the *ETag* header. Store this value and a timestamp in a separate table or data store such that the server does not need to reload the entire representation to compute this.

If the representation size is not large, use the representation body to generate an MD5 hash value for the *ETag*. Alternatively, use some field of the data that changes every time the resource is updated.

Strive to use a different *ETag* value for each version of the resource. Different resources may share the same *ETag* value without implying equality.

See the following list for HTTP headers related to conditional GETs and their applicability:

*HTTP headers for conditional GETs*

HTTP Header
Request or Response
Applicability
Last-Modified
Response
The Last-Modified response header indicates the date and time at which the origin server believes the resource was last modified.
ETag
Response
An entity tag, or ETag is a value used by the origin server to uniquely identify a version of a resource representation. It is normally a strong validator since its value can be changed every time the server modifies the representation.
If-Modified-Since
Request
The If-Modified-Since request header is used with a method to make it conditional: if the requested resource
has not been modified since the time specified in this field, an entity will not be returned from the server;
instead, a 304 (not modified) response will be returned without any message-body. The purpose of this feature is to
allow efficient updates of cached information with a minimum amount of transaction overhead.
If-None-Match
Request
The If-None-Match request header is used with a method to make it conditional: if the ETag that would have been returned in an unconditional GET request matches the ETag that is specified in this request header, an entity will not be returned from the server; instead, a 304 (not modified) response will be returned without any message-body. The purpose of this feature is to allow efficient updates of cached information with a minimum amount of transaction overhead.

## Origin Servers - Example Java Code

*Java Example: ETag*

```
@GET
@Path("{id}")
public Response conditionalGetExample(@PathParam("id") final String id, final Request request) {
    final ExampleEntity entity = getExampleEntity(id);
    if (entity == null) {
        return Response.status(Status.NOT_FOUND).build();
    }
    final EntityTag eTag = new EntityTag(entity.getRevisionNumber());
    // From the javadoc, this call will return null if the
    // preconditions are met (i.e. the timestamp or eTag are out of
    // date) or a ResponseBuilder set with the appropriate status if
    // the preconditions are not met.  A returned ResponseBuilder
    // will include an ETag header set with the value of eTag.
    ResponseBuilder responseBuilder =
    request.evaluatePreconditions(entity.getLastModified(), eTag);
    if (responseBuilder == null) {
        responseBuilder = Response.ok(entity);
    }
    return responseBuilder.lastModified(entity.getLastModified())
            .build();
}
```

## Origin Servers - Example (J)Ruby on Rails Code

If you’re using respond\_to or calling render yourself, use code like:

*Ruby Example: ETag*

```
class ProductsController < ApplicationController
  def show @product = Product.find(params[:id])
    # If the request is stale according to the given timestamp and etag
    # value (i.e. it needs to be processed again) then execute this
    # block
    if stale?(:last_modified => @product.updated_at.utc,
              :etag => @product)
      respond_to do |wants|
        # ... normal response processing
      end
    end
    # If the request is fresh (i.e. it's not modified) then you don't
    # need to do anything. The default render checks for this using
    # the parameters used in the previous call to stale? and will
    # automatically send a :not_modified.  So that's it, you're done.
end
```

If you’re not using `respond_to` or calling `render` yourself, use code like this:

*Ruby Example: ETag*

```
class ProductsController < ApplicationController
  # This will automatically send back a :not_modified if the request is 
  # fresh, and will render the default template (product.*) if it's 
  # stale.
  def show 
    @product = Product.find(params[:id]) 
    fresh_when :last_modified => @product.published_at.utc, :etag => @product 
  end
end
```

## Origin Servers - Example Python (Pylons) Code

Here is an example of setting the Last-Modified header

*Ruby Example: ETag*

```
class SampleController(BaseController):
  def index(self):
    ...
    response.last_modified = entity.last_modified()
    ...

# Note, Pylons does not appear to offer out-of-the-box support
# for the If-Modified-Since request header. Adding support for
# this header is left as an exercise for the reader.
```

Here is an example of setting the ETag header and supporting the \`If-None-Match\` header

*Ruby Example: If-None-Match*

```
from pylons.controllers.util import etag_cache

def my_action(self):
  ...
  # If a “If-None-Match” header is found, and equivalent to key,
  # then a 304 HTTP message will be returned with the ETag to tell
  # the browser that it should use its current cache of the page.
  # Otherwise, the ETag header will be added to the response headers.
  # This works because etag_cache will raise an HTTPNotModified
  # exception if the ETag received matches the key provided. 
  etag_cache(entity.revision_number())
  # serve response here 
  ...
```

## Clients - Example Java Code

*Java Example: ETag*

```
// Here is an example of using the org.apache.httpcomponents
// CachingHttpClient
public static void main(final String[] args) throws IOException {
    final CacheConfig cacheConfig = new CacheConfig();
    cacheConfig.setMaxCacheEntries(MAX_CACHE_ENTRIES);
    cacheConfig.setMaxObjectSizeBytes(MAX_OBJECT_SIZE_BYTES);
    final CachingHttpClient client = new CachingHttpClient(
        new DefaultHttpClient(), cacheConfig);
    final HttpGet get = new HttpGet(URL);
        HttpResponse response;
        for (int i = 0; i &lt; REQUEST_COUNT; i++) {
            response = client.execute(get);
            consumeResponse(response);
        }
    System.out.println("Cache Hits: " + client.getCacheHits() +
            "; Cache Misses: " + client.getCacheMisses() +
            "; Cache Updates: " + client.getCacheUpdates());
}
```

## Clients - Example (J)Ruby on Rails Code

At the time of this writing, ActiveResource does not support HTTP caching.

## Clients - Example Python Code

Here is an example of making a conditional request using the *If-None-Match* header and *If-Modified-Since* header

*Python Example: If-None-Match, If-Modified-Since*

```
import httplib2

h = httplib2.Http(".cache")
resp, content = h.request(url)
# That's it! httplib2 does it for you transparently. pretty cool.
```

# Discussion

Caching is one of the most useful features built on top of HTTP’s uniform interface. You can take advantage of caching to reduce end user perceived latency, to increase reliability, to reduce bandwidth usage and cost, and to reduce server load. Caches operate efficiently when they can serve as many responses as possible without contacting the origin server.

There are two major aspects to caching in HTTP/1.1: reducing the need to send requests, and reducing the need to send full responses. This document only talks about the second of these aspects: reducing the need to send full responses.

Before continuing, you may want to check out [Things Caches Do](http://tomayko.com/writings/things-caches-do) for a clear description of HTTP caching.

Conditional GET requests help clients and caches validate that a cached representation can still be considered fresh.

Servers use *Last-Modified* and *ETag* response headers to enable clients to perform conditional requests. Clients use *If-Modified-Since* and *If-None-Match* headers for validating cached representations.

Services in the Rackspace Foundation RESTful SOA should support cache validation with conditional GETs for representations of resources that they serve.

Caching in HTTP/1.1 is defined by the [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html#sec13), which serves as the HTTP/1.1 specification.

RFC 2616 distinguishes between weak and strong validators. An entity tag is considered a strong validator when any change to the entity results in a change to the entity tag. An entity tag is considered a weak validator when the server prefers to change it only on semantically significant changes. An example of a strong validator is an integer that is incremented in stable storage every time an entity is changed. An example of a weak validator is an entity's modification time represented with one-second granularity, since it is possible for the resource to be modified more than once in a given second. You should always seek to use a strong validator for the entity tag in services you create.

# Good and Bad Practices

## Good Practices

1.  Origin servers should send a strong *ETag* header unless it is not feasible to generate one.
2.  Origin servers that use a strong *ETag* must change the entity tag whenever the associated entity value changes in any way. They should avoid reusing a specific entity tag value for two different representations of the same resource.
3.  Origin servers that use version numbers for generating *ETag* values should ensure that this value is representation specific. For instance, if two representations of a resource vary by the media type, use the media type value along with the version identifier to make the *ETag* values representation specific.
4.  Origin servers should send a *Last-Modified* header unless it is not feasible to generate one.
5.  Origin servers should look for and respond appropriately to an *If-Modified-Since* header or an *If-None-Match* header. Refer to sections 14.25 and 14.26 of [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html#sec14) for details.
6.  Clients that choose to perform a conditional GET request should favor the use of *ETag*-based validation (using the *If-None-Match* header) over the use of *Last-Modified*-based validation (using the *If-Modified-Since* header). This mitigates the risk of an edge-case in which more than one update uses the same timestamp. In this edge-case, it is possible for a client to retrieve a resource in the middle of the sequence, but within the same timestamp. If so, the *ETag* would show the client that the representation has changed, but a *Last-Modified* timestamp might not.
7.  Clients that choose to perform a conditional GET request based on the *Last-Modified* timestamp should set the value of the *If-Modified-Since* header to the exact date string received in a previous *Last-Modified* header. This will mitigate the risks that a) the server's clock is not in sync with that of the client, and b) the server uses equality for comparison rather than the less than operator.

## Bad Practices

1.  Origin servers should not reload the entire representation to compute the *ETag* and *Last-Modified* values. Instead, these values should be fetched from, e.g., a database table to minimize response times for conditional GET requests.
2.  Clients should not perform conditional GET requests unless they have a cached copy of the resource available locally.

# Frequently Asked Questions

The following are frequently asked questions whose answers are worth collecting in one place.

*Where can I find a clear overview of HTTP caching?*

Check out [Things Caches Do](http://tomayko.com/writings/things-caches-do) for a clear description of HTTP caching.

*How do I set the Last-Modified header for composite resources?*

Use the most recent modification date from the composition members in the composite resource (e.g. if a composite is composed of a resource that was modified on 'Sun, 09 Aug 2009 00:40:14 GMT', and second resource that was modified on 'Sat, 08 Aug 2009 00:20:11 GMT', the more recent value of 'Sun, 09 Aug 2009 00:40:14 GMT' should be used).

*How do I compute an ETag value for a composite resource?*

It is not possible for the composition controller to store a sequence number because the composite members may process mutating requests independently of the composition controller.

In general, it is also not possible to rely on a field in the composite resource for uniqueness since the composition members may change their representations independently.

We also cannot count on using *ETags* provided by composition members, since they may not support the recommendations set out in this cookbook entry and may not provide an *ETag*.

Therefore, use the representation body to generate an MD5 hash value for the *ETag*.

*Why is MD5 the recommended hash algorithm for generating ETags based on content? Is it okay to use a different hash algorithm?*

MD5 is the recommended hashing algorithm because it is widely supported across languages and it is relatively fast compared with other available hashing algorithms, as this [hash algorithm performance comparison](http://www.cryptopp.com/benchmarks.html) shows.

Having said that, you are free to use any hash algorithm you choose, provided the algorithm is highly unlikely to generate the same value for different content.

*How does the Last-Modified response header relate to expiration-based caching?*

This header is unrelated to expiration-based HTTP caching. With that form of caching, the service specifies a *freshness lifetime* that specifies the amount of staleness that should be tolerated for the given resource.

Whether the freshness lifetime is 0 seconds or 1 year, the *Last-Modified* header simply provides information about the last time the resource was updated. This information is independent of its expiration time.

For more details on expiration-based HTTP caching, please refer to this separate cookbook entry [Using HTTP Headers for Expiration Caching](using-http-headers-for-expiration-caching.md).

*Rather than storing the ETag and Last-Modified timestamps in an underlying data store, can I just store these in memory?*

Yes, you can, with the following caveats:

1.  You must use a clustered cache that is replicated or distributed synchronously so that all cluster members use the same value.
2.  It must not be possible for another process to modify the underlying data. For example, if an ETL process periodically updates your datastore, you'll instead need to use database triggers or base the *ETag* on the data itself as described elsewhere in this document.
3.  In the event that the data is not available from cache (e.g. because of a cluster-wide restart), you should treat a conditional GET the same way you would treat a request with an invalid *ETag* / timestamp. That is, you should return the full representation.

Provided the above issues are not a concern for your implementation, caching these values may result in improved performance.

*What other uses exist for ETag and Last-Modified headers?*

The *ETag* and *Last-Modified* can also be used to support optimistic concurrency control for write operations, using *If-Match* and *If-Unmodified-Since* HTTP headers in the request.

Supporting this functionality requires explicit support in both the origin server and in the client implementations. This topic is outside the scope of this cookbook entry. Interested readers are encouraged to refer to [RESTful Web Services Cookbook](http://search.safaribooksonline.com/9780596809140) by Subbu Allamaraju.

# References

1.  [RESTful Web Services Cookbook](http://search.safaribooksonline.com/9780596809140), Subbu Allamaraju. especially Chapter 10, recipes 10.1 through 10.3.
2.  [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616.html) Hypertext Transfer Protocol -- HTTP/1.1
3.  [RFC 2068](http://www.ietf.org/rfc/rfc2068.txt) Hypertext Transfer Protocol -- HTTP/1.0
4.  [Wikipedia article on Hypertext Transfer Protocol](http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol).
5.  [Glossary of caching terminology in Confluence](https://confluence.core.rackspace.com/display/EArch/Glossary+of+caching+and+data+grid+terminology).
6.  [Things Caches Do](http://tomayko.com/writings/things-caches-do), Ryan Tomayko.
7.  [Using HTTP Headers for Expiration Caching](using-http-headers-for-expiration-caching.md)

# Contributors

* Benjamin Truitt
* Mark Morga
* Dave Slotnick
