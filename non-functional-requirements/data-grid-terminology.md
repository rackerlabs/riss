# Data Grid terminology

## Data Grid

A Data Grid is a system composed of multiple servers that work together to manage information and related operations - such as computations - in a distributed environment. See: [Defining a Data Grid](http://coherence.oracle.com/display/COH33UG/Defining+a+Data+Grid)

## In-Memory Data Grid

An *In-Memory Data Grid* is a Data Grid that stores the information in memory in order to achieve very high performance, and uses redundancy - by keeping copies of that information synchronized across multiple servers - in order to ensure the resiliency of the system and the availability of the data in the event of server failure.

An In-Memory Data Grid achieves low response times for data access by keeping the information in-memory and in the application object form, and by sharing that information across multiple servers. In other words, applications may be able to access the information that they require without any network communication and without any data transformation step such as ORM. In cases where network communication is required, a data grid avoids introducing a Single Point of Bottleneck (SPOB) by partitioning - spreading out - information across the grid, with each server being responsible for managing its own fair share of the total set of information. See: [Defining a Data Grid](http://coherence.oracle.com/display/COH33UG/Defining+a+Data+Grid)

## Application Objects

The *application objects* are the actual components of the application that contain information that is shared across multiple servers and that must survive server failure in order for the application to be continuously available. These objects are typically built in an object-oriented language such as Java or Ruby. Unlike a relational schema, application objects are often hierarchical in nature, and may contain information that is pulled from any database. See: [Defining a Data Grid](http://coherence.oracle.com/display/COH33UG/Defining+a+Data+Grid)

## Shared Resource

An example of a *shared resource* is a database. A data grid is interposed between application servers that share a common shared resource in order to reduce contention resulting from concurrent access to that shared resource. Because a data grid may contain multiple copies of the same application objects, no single resource (i.e. the database) becomes a bottleneck.

## ORM

Because an application object is not relational, in order to retrieve it from a relational database the information must be mapped from a relational query into the object; this is known as Object/Relational Mapping (*ORM*). Examples include Java's JPA or Rails' ActiveRecord. The same technology allows that object to be stored in a relational database by deconstructing the object (or changes to the object) into a series of SQL inserts, updates and deletes. Since a single object may be composed of information from many tables, the cost of accessing objects from a database using Object/Relational Mapping can be significant, both in terms of the load on the database and the latency of the data access. See: [Defining a Data Grid](http://coherence.oracle.com/display/COH33UG/Defining+a+Data+Grid)

## Failover

*[Failover](http://en.wikipedia.org/wiki/Failover)* is the process of switching over automatically to a redundant or standby computer server, system, or network upon the failure or abnormal termination of the previously active server, system, or network. Failover happens without human intervention and generally without warning.

# HTTP Cache terminology

## HTTP Cache

*HTTP cache*s such as [Squid](http://www.squid-cache.org/) or [Apache Traffic Server](https://trafficserver.apache.org/) do not require clients and servers to call any special programming API to manage data in the cache. This is not the case with object caches. For instance, in order to use memcached, you must use memcached's programming API to store, retrieve, and delete objects. HTTP caches are based on the same uniform interface that clients and servers use. Therefore, as long as you are using HTTP as defined, you should be able to add a caching layer without making code changes. See also: [Web Caching](http://search.safaribooksonline.com/book/web-development/web-services/9780596809140/web-caching/chapter-caching#X2ludGVybmFsX0ZsYXNoUmVhZGVyP3htbGlkPTk3ODA1OTY4MDkxNDAvY2hhcHRlci1jYWNoaW5n)

## Origin Server

The *Origin Server* is the HTTP server that originates an HTTP response that may subsequently be cached by other servers and/or clients. This term is commonly used to differentiate the origin server from any particular server (such as a cache server) in the request-response chain.

## Expiration caching

*Expiration caching* is based on `Cache-Control` and `Expires` headers. These headers instruct clients and caches to keep a copy of the representation returned by the server for a specific length of time. Caches can fulfill any subsequent requests within or even beyond that time window by serving a cached copy of the representation without contacting the origin server.

## Stale response

A *stale response* is an HTTP response that is beyond its cache expiration, and is therefore stale.

## Revalidation & Conditional `GET` and `HEAD` requests

When a client stores representations locally, it can use conditional GET or HEAD requests to find whether the locally stored representation is still fresh.

Specifically, the client can include the following headers to make these requests "conditional":

* `If-Modified-Since` header with a value of the stored `Last-Modified` header
* `If-None-Match` header with a value of the stored `ETag` header

## Proxy Cache

A *proxy cache* is a proxy server that is interposed between a client and an origin server. This server intercepts requests from the client, and serves cached responses when they are available. When no cache response is available for a given request, the proxy forwards the request to the origin server, and saves a copy of the response when applicable before passing the resulting response to the client.

A special form of proxy cache, called a *transparent proxy cache* acts in a manner that is transparent to both the client and server (i.e. they do not require additional configuration or knowledge to benefit from the participation of the proxy cache in the request-response chain).

Proxy caches are further classified as either a *forward proxy* or *[reverse proxy](http://en.wikipedia.org/wiki/Reverse_proxy)*. While a forward proxy is usually situated between the client application and the server(s) hosting the desired resources, a reverse proxy is usually situated closer to the server(s). Reverse proxies may server other purposes as well, such as acting as an SSL termination point, performing content based routing, etc.

If both clients and servers are within the same network, a forward proxy may be unnecessary. The server can deploy a cache that can be shared by all clients. But if your clients are interacting with third-party web services in other networks, using a forward proxy can help reduce round-trip times between clients and servers. See also: [Web Caching](http://search.safaribooksonline.com/book/web-development/web-services/9780596809140/web-caching/chapter-caching#X2ludGVybmFsX0ZsYXNoUmVhZGVyP3htbGlkPTk3ODA1OTY4MDkxNDAvcmVjaXBlLWhvdy10by11c2UtZXhwaXJhdGlvbi1oZWFkZXJzLWluLWNsaWVudHM=)

## Shared Cache

A shared cache is shared by more than one client. Proxy caches are typically shared caches. Shared caches can be beneficial when more than one client is likely to request the same resource, and the resource permits caching in a shared cache.

## Private Cache

A private cache is private to a single client. A web browser's cache is an example of a private cache. Items in a private cache will not be shared with other clients. HTTP responses may include a `Cache-Control` header with a 'private' directive that instructs shared caches to not cache the response, but that indicates private caches may cache the response.

## `ETag`

An entity tag, or ETag is a value used by the origin server to uniquely identify a version of a resource representation. It is a strong validator since its value can be changed every time the server modifies the representation. An entity tag is like an object's hash code. You can use entity tags to compare representations of a resource.

## Fresh cache

A fresh cache is a cache with information that is up to date.

## Warm cache

A warm cache is a cache that is relatively full with data.

## HTTP cache channel

An HTTP cache channel is an extension to the standard `Cache-Control` HTTP header. This extension is currently supported by Squid. It allows an origin server to specify a URL where it will publish events related to potentially cached data so that caches of that data can be modified appropriately. See [here](http://ietfreport.isoc.org/idref/draft-nottingham-http-cache-channels) for further detail.

## SSL Offloading

[SSL offloading](http://www.f5.com/glossary/ssl-offloading.html) relieves a Web server of the processing burden of encrypting and/or decrypting traffic sent via SSL.

## SSL Termination

[SSL termination](http://www.f5.com/glossary/ssl-termination.html) refers to the process that occurs at the server end of an SSL connection, where the traffic transitions between encrypted and unencrypted forms. A device that handles both SSL offloading and SSL termination can be advantageous in some situations.
