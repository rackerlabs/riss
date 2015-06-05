# Using HTTP Headers for Expiration Caching

# Problem

You want to know how to use expiration-based caching in your RESTful service.

# Solution

## Origin Servers

Determine a time period during which caches can serve a representation, called the *freshness lifetime*. After this time, caches will consider cached representation stale.

When serving a representation, include a *`Cache-Control`* header with a `max-age `value (in seconds) equal to the freshness lifetime.

If you determine that caches must not serve cached copies, add a *`Cache-Control`* header with the value `no-cache`.

See the following list for Cache-Control directives and their applicability for HTTP responses sent from origin servers (for a full list, refer to [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html#sec13)):

*Cache-Control directives*

Directive
Applicability
public
This is the default. You can also use this directive when the request is authenticated but you still want to allow shared caches to serve cached responses.
private
Use this directive when the response is private to the client or the user. When this directive is present, any client-side cache can cache the representation, but shared caches such as those at the server or along the network must not cache it. Add this directive when serving representations based on client or user authentication.
no-cache
Add this directive to prevent any cache from serving this representation in response to a subsequent request.
no-store
Add this directive to prevent any cache from storing this representation. Per Rackspace security guidelines, CONFIDENTIAL or RESTRICTED data should be marked with this directive.
max-age
Use the freshness lifetime in seconds as the value of this directive.
s-maxage
For responses to authenticated requests that may be cached by a shared cache and returned by that cache to other clients, use the freshness lifetime in seconds as the value of this directive.

## Origin Servers - Example Java Code

*Origin Servers: Java Example*

```
// Here is an example of setting the max-age cache control directive.
...
final CacheControl cacheControl = new CacheControl(); cacheControl.setMaxAge(freshnessLifetimeSeconds);
response = Response.ok().entity(entity).cacheControl(cacheControl).build();
...
// Here is an example of marking a response as private ...
// set private for authenticated content that is private to a client
cacheControl.setPrivate(true);
...
// Here is an example of marking a response as no-cache for content
// that should not be cached
...
cacheControl.setNoCache(true);
...
// Here is an example of marking a response as no-store for content
// that should not even be stored
...
cacheControl.setNoStore(true);
...
// Here is an example of setting the s-maxage directive to set the max
// age for the content in a shared cache
...
cacheControl.setSMaxAge(SOME_CONSTANT_VALUE);
response = Response.ok().entity(customer).cacheControl(cacheControl).build();
...
```

## Origin Servers - Example (J)Ruby on Rails Code

Here is an example of setting the `max-age` cache control directive.

*Ruby Example: max-age Cache Control*

```
class ProductsController < ApplicationController
  def show
    expires_in freshness_lifetime.seconds, :public => true
    @product = Product.find(params[:id])
    render :action => 'show'
  end
end
```

Here is an example of marking a response as private for authenticated content that is private to a user / client

*Ruby Example: marking a response as private*

```
expires_in freshness_lifetime.seconds, :public => false
```

Here is an example of marking a response as no-cache

*Ruby Example: response no-cache*

```
response.headers["Cache-Control"] = "no-cache"
```

Here is an example of marking a response as no-store for content that should not even be stored

*Ruby Example: no-store*

```
response.headers["Cache-Control"] = "no-store"
```

Here is an example of setting the s-maxage directive to set the max age for the content in a shared cache

*Ruby Example: s-maxage*

```
response.headers['Cache-Control'] = 'public, s-maxage=300'
```

## Origin Servers - Example Python (Pylons) Code

Here is an example of setting the `max-age` cache control directive.

*Python Example: max-age*

```
class SampleController(BaseController):
  def index(self):
    # To ensure pages aren’t accidentally cached in dynamic web
    # applications, Pylons default behavior sets the Pragma and
    # Cache-Control headers to no-cache. Before setting cache
    # headers, these default values should be cleared.
    del response.headers['Cache-Control']
    del response.headers['Pragma']
    response.cache_control = {'max-age': 30, 'public': True}
    return render('/index.html')
```

Here is an example of marking a response as private for authenticated content that is private to a user / client

*Python Example: Private Response*

```
response.cache_control = {'max-age': 30, 'public': False}
```

Here is an example of marking a response as no-cache

*Python Example: no-cache*

```
response.cache_control = 'no-cache'
```

Here is an example of marking a response as no-store for content that should not even be stored

*Python Example: no-store*

```
response.cache_control = 'no-store'
```

Here is an example of setting the s-maxage directive to set the max age for the content in a shared cache

*Python Example: s-maxage*

```
response.cache_control = {'s-maxage': 30, 'public': True}
```

Clients

If client side caching is used, use only an approved client-side cache library in order to minimize the risk of implementation errors that could expose your client application to bugs or security vulnerabilities.

See the following list for Cache-Control directives and their applicability for HTTP requests sent from clients (for a full list, refer to [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html#sec13)):

*Cache-Control directives*

Directive
Applicability
no-cache
The *no-cache* directive notifies a cache that it cannot return a cached copy. Even if a fresh copy of the response—with a specific expiration time—is in the cache, the client's request must be forwarded to the origin server. RFC 2616 calls such a request an "end-to-end validation" (Section 14.9.4). The no-cache directive is sent when you click on the Reload button on your browser.
max-age
The *max-age* directive specifies in seconds the maximum age of a cached response that the client is willing to accept. *max-age* has lower precedence than an origin server expiration time. *max-age* can cause validation to occur but can never prevent it from occurring. RFC 2616 calls a \`max-age=0\` request an end-to-end revalidation, because it forces the cache to revalidate its stored response. The client might still get that stored response, unlike with an end-to-end reload (*no-cache*), where a cache is not allowed to return a cached response.

## Clients - Example Java Code

*Java Example: no-cache*

```
// Here is an example of marking a request as no-cache 
final Client client = createClient(); 
final WebResource webResource = client.resource(url); 
return webResource.header(HttpHeaders.CACHE_CONTROL, "no-cache").header("Pragma", "no-cache").get();
// Here is an example of setting the max-age cache control 
// directive on a request 
final Client client = createClient(); 
final WebResource webResource = client.resource(url); 
return webResource.header(HttpHeaders.CACHE_CONTROL, "max-age=" + maxAgeSeconds).get();
```

## Clients - Example (J)Ruby on Rails Code

At the time of this writing, ActiveResource does not support HTTP caching.

## Clients - Example Python Code

Here is an example of marking a request as no-cache

*Python Example: no-cache*

```
import httplib2 
h = httplib2.Http(".cache") 
resp, content = h.request(url, headers={'cache-control':'no-cache'})
```

Here is an example of setting the max-age cache control directive on a request

*Python Example: no-cache*

```
import httplib2 
h = httplib2.Http(".cache") 
resp, content = h.request(url, headers={'cache-control':'max-age=30'})
```

# Discussion

Caching is one of the most useful features built on top of HTTP’s uniform interface. You can take advantage of caching to reduce end user perceived latency, to increase reliability, to reduce bandwidth usage and cost, and to reduce server load. Caches operate efficiently when they can serve as many responses as possible without contacting the origin server.

There are two major aspects to caching in HTTP/1.1: reducing the need to send requests, and reducing the need to send full responses. This document only talks about the first of these aspects: reducing the need to send requests. This obviously reduces the number of network round-trips required for many operations; we use an "expiration" caching mechanism for this purpose.

Before continuing, you may want to check out [Things Caches Do](http://tomayko.com/writings/things-caches-do) for a clear description of HTTP caching.

When performance issues arise, HTTP caching is commonly seen as a potential solution. However, don't implement HTTP caching to overcome a conflict between the SLA demanded of your application and the SLA offered by a service that your application depends upon. Instead, work with your users and the owners of the upstream service to reconcile differences between SLA requirements. Caching is not a panacea for performance issues and has the potential to complicate a solution. Therefore, generally speaking, caching should be used to further improve performance, but not to meet basic performance requirements. Having said that, in some cases third-party services cannot be modified to meet your SLA requirements. Please refer to the Question and Answer section below for a discussion of this topic.

## Origin Servers - Freshness

A cached representation is considered "fresh" when its expiration time has not been reached. Fresh cached representations can be sent to the client without first checking with the origin server, thus reducing latency because the request does not need to be passed to the origin server, and the origin server does not need to undertake work to generate a new response.

It is your responsibility to determine an acceptable freshness lifetime for each response that will be cached. This will vary by resource, and will typically be determined by analyzing the duration between updates for a resource, or by seeking guidance from business representatives about how acceptable staleness is for a given resource.

## Origin Servers - HTTP/1.1 Support

Caching in HTTP/1.1 is defined by the [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html#sec13), which serves as the HTTP/1.1 specification.

The `Cache-Control` general-header field is used to specify directives that must be obeyed by all caching mechanisms along the request/response chain. The `Cache-Control` header is defined in [RFC 2616 section 14.9](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9). See the above table for a list of the most common directives that may be used by an origin server to control cache behavior.

When the `max-age cache-control`directive is present in a cached response, the response is stale if its current age is greater than the age value given (in seconds) at the time of a new request for that resource. The `max-age` directive on a response implies that the response is cacheable (i.e., "public") unless some other, more restrictive cache directive is also present. A stale response may not be returned to the client unless it is first validated with the origin server. Validation is the topic of [Supporting HTTP Cache Validation](supporting-http-cache-validation.md).

In addition to setting the max-age directive, the `Cache-Control` header is used to restrict what is cacheable, and in what ways. Specifically, the `public`, `private` and `no-cache` directives allow an origin server to override the default cache-ability of a response. To fully understand the distinctions between these directives, it is important to know the difference between a shared cache and a private cache.

A shared cache is shared by more than one client/user. A proxy cache is typically a shared cache. A private cache is private to a single HTTP client. A web browser's cache is a good example of a private cache. Note that the term private does not mean that the data are encrypted.

When a shared cache receives a request containing an \*Authorization\* header, it must not return the corresponding response as a reply to any other request, unless one of the following specific exceptions holds:

1.  If the response includes the `s-maxage Cache-Control` directive, the cache may use that response in replying to a subsequent request. But if the specified maximum age has passed, the cache must first revalidate it with the origin server, using the request-headers from the new request to allow the origin server to authenticate the new request.
2.  If the response includes the `must-revalidate cache-control` directive, the cache may use that response in replying to a subsequent request. But if the response is stale, the cache must first revalidate it with the origin server, using the request-headers from the new request to allow the origin server to authenticate the new request.
3.  If the response includes the `public cache-control` directive, it may be returned in reply to any subsequent request.

Similarly, when a response includes the `private cache-control` directive, the response may not be cached.

## Clients

Clients may cache representations for subsequent use, but please be aware of the following considerations before choosing to use a client-side cache in your application:

Using a client-side cache exposes your application to the risk that it might use data it believes to be fresh, but that is no longer consistent with the state of the data in the origin server. Unlike advanced reverse proxy caches, client side caches do not typically support out-of-band invalidation messages when the data is modified. This means that the client may believe a cache entry is fresh because its `max-age` has not been reached when in fact the entry is no longer valid because it has been updated since the cache entry was originally stored.

Before implementing a client-side cache, start by introducing a proxy cache between your clients and servers. This leaves room for expansion since the proxy can be shared by all clients.

Architecting your caching solution in a manner that maximizes sharing among clients tends to be a more optimal approach to caching as opposed to asking each client application to repeat their own client cache implementation.

Be aware that placing a cache within the same runtime can introduce memory and CPU contention between the client application code and the caching code. This can make tuning the client more difficult.

# Good and Bad Practices

## Good Practices

1.  Specify a freshness lifetime for each response you serve using appropriate HTTP headers in order to maximize the opportunity to improve performance from an end-user perspective by introducing HTTP caches.
2.  For responses that may not be safely cached, specify a `no-cache Cache-Control` directive.
3.  For responses that are private to the client or the user (i.e. based on client / user authentication), specify a `private Cache-Control` directive.
4.  Clients that choose to use client-side caching should use a proven client-side cache library. Although it is possible to build client applications that support HTTP’s caching protocol without using a library for this purpose, in practice, building and maintaining a cache within a client application is a nontrivial task. It involves correctly implementing expiration directives such as`no-store`, `no-cache`, and `must-revalidate`, and honoring the `Vary` header. Moreover, any errors in implementation could lead to security vulnerabilities in the client. Using a proven library helps to mitigate these risks.
5.  Teams responsible for origin servers should ideally provide a reverse-proxy cache that can be shared by all clients (please refer to the [Glossary of caching terminology in Confluence](https://confluence.core.rackspace.com/display/EArch/Glossary+of+caching+and+data+grid+terminology) for a definition of reverse proxy). Proxy cache architecture is the topic of [Using HTTP Proxy Caching](using-http-proxy-caching.md).
6.  For origin servers on a separate network from the client, consider using a forward proxy cache in the client network between the clients and the remote origin server. Clients that share the forward proxy cache may benefit from reduced latency for certain requests (please refer to the [Glossary of caching terminology in Confluence](https://confluence.core.rackspace.com/display/EArch/Glossary+of+caching+and+data+grid+terminology) for a definition of forward proxy).

## Bad Practices

1.  Do permit caching of a response in a shared cache when the response is private to a client/user. Instead, use the `private Cache-Control` directive.
2.  Don't implement HTTP caching to overcome a conflict between the SLA demanded of your application and the SLA offered by a service that your application depends upon. Instead, work with your users and the owners of the upstream service to reconcile differences between SLA requirements. Use an HTTP cache where necessary to help improve responsiveness and resilience against downtime of services you depend upon.

# Frequently Asked Questions

The following are frequently asked questions whose answers are worth collecting in one place.

*Where can I find a clear overview of HTTP caching?*

Check out [Things Caches Do](http://tomayko.com/writings/things-caches-do) for a clear description of HTTP caching.

*Should I add support for expiration-based caching from the start, or can I add it to my implementation later? How do I know when to add it?*

You can add support for this type of caching at any time.

You may find that it is preferable to support expiration-based caching from the beginning of your implementation. Benefits to implementing HTTP headers for expiration-based caching from the beginning include:

1.  You can determine the be freshness lifetime (if any) for your resources as part of your requirements gathering effort.
2.  End-users can benefit sooner from the improved performance that comes from this type of caching strategy.

On the other hand, unless you intend to use a proxy cache or your clients intend to implement client-side caching, it may be that there is no performance benefit to this type of caching strategy.

*How do I support caching for composite resources?*

Use the shortest freshness lifetime of the composition members to determine the freshness lifetime of the composite resource (e.g. if a composite is made up of one resource with a one hour freshness lifetime and a second resource with a freshness lifetime of one minute, the freshness lifetime of the composite resource should be less than or equal to one minute).

Similarly, determine privacy requirements of the composite resource by restricting privacy to the most restrictive privacy directive of the composition members (e.g. if a composite is made up of one resource with simply the default '*public*' directive and a second resource with a '*private*'`Cache-Control` directive, the privacy of the composite resource should be limited to '*private*', or may even be marked as *no-cache* or *no-store* which are even more restrictive).

# References

1.  [RESTful Web Services Cookbook](http://search.safaribooksonline.com/9780596809140), Subbu Allamaraju. via Safari, especially Chapter 9, recipes 9.1 through 9.4.
2.  [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616.html) Hypertext Transfer Protocol -- HTTP/1.1
3.  [RFC 2068](http://www.ietf.org/rfc/rfc2068.txt) Hypertext Transfer Protocol -- HTTP/1.0
4.  [Wikipedia on Hypertext Transfer Protocol](http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol).
5.  [Caching Tutorial](http://www.mnot.net/cache_docs/).
6.  [Glossary of caching terminology in Confluence](https://confluence.core.rackspace.com/display/EArch/Glossary+of+caching+and+data+grid+terminology)
7.  [Things Caches Do](http://tomayko.com/writings/things-caches-do), Ryan Tomayko.

# Contributors

Benjamin Truitt
Mark Morga
Dave Slotnick
Yogeshwar Srikrishnan

Mark Morga
