# REST Service Scoring Guide

The purpose of this page is to provide an *objective* way to evaluate a service's adherence to RESTful and Rackspace standards.

As this guide is developed it should be annotated with links that explain the why's and how's of each scoring item.

The intent of this guide is not to assign a Pass or Fail grade to your service but instead provide a means by which you can make pragmatic decisions on RESTful features that will make your service safer, faster, and easier to use.

## Subjective Guidelines

This guide is meant to include only objective criteria. There are several aspects of RESTful development that are difficult or impossible to express in a truly objective way. Examples include service route, resource, and representation design. Despite being more subjective in their interpretation of what makes good design, these are important to do well and will be discussed in other parts of this Wiki.

Without further ado, here are the guidelines.

# Documentation

1.  Includes a Rackspace Service Profile
2.  Includes a valid annotated WADL (or doctools generated documentation)
3.  Includes human readable documentation

# Security

1.  Validates Authentication with Identity
2.  Uses appropriate Authorization (via LDAP or Identity)

# Resource Design

1.  `Last-Modified` and `ETag` headers are included to aid in Caching and conditional `GET`s.
2.  Collection resources are named with a plural name. For example: `/users/bob`rather than `/user/bob`. This allows distinguishing between singular and collection resources.

## Collection Resources

Single Resources

## Routes

1.  Keep the base API url short, for example http://api.example.com.
2.  Keep urls lowercase, avoid camelCase (even for slugs!) and use hyphens in your slugs rather than underscores. Treating `/users/bob` and `/users/Bob` as two different instances is confusing (and most likely incorrect as well).
3.  Use nouns in urls, not verbs.
4.  Pagination: `/users?limit=20&offset=60` for simple pagination. Consider [Atom](http://tools.ietf.org/html/rfc4287)/[AtomPub](http://tools.ietf.org/html/rfc5023) for more extensive lists and pagination.
5.  Search:  `/search?q=my+search`
6.  Ordering:  /posts?sortByAsc=date` and `/posts?sortByDesc=date`
7.  Search within a specific collection: `/users/search?q=bob`.
8.  Versioning: use `/v1/users` (an alternative is to change the media type but I advise against this; it is too obscure, you would be able to use the same url for two or more different representations)
9.  Request partial result: `/users/bob?fields=firstName,lastName`
10. Avoid communicating minor version numbers (like 1.1), minor versions must be backwards compatible and can be published without worrying about consumer compatibility. Only backwards incompatibile changes warrent releasing a new version in the url of your service (also, limiting the number of supported versions reduces the maintenance burden).
11. Don't expose surrogate keys like generated sequence numbers or UUIDs in the interface, use a slug or some other generated string.

# Representations & Schemas Design

## Media Types

1.  Representations are encoded in MIME type representations
2.  JSON based representations are available by default
3.  XML based representations are available if needed
4.  Representations are based on simple MIME types such as: `application/xml`, `application/json`
5.  Representations based on versioned application specific MIME types such as: `application/vnd.rackspace.productcatalog.item`

## HATEOAS

1.  Representations include *self* links.
2.  Representations include links to available operations.
3.  Representations include links to related resource.

## JSON

1.  Write JSON in camelCase, never snake_case. JSON is most likely consumed by front-end JavaScript and JSON originated from JavaScript; stick to the JavaScript convention.
2.  Send as much data as possible about a resource instance, even if the consumer is unlikely to consume it all. Only hide data properties that are security sensitive or used internally only (for example, a surrogate key).
3.  Avoid unnecessary data envelopes. The client knows he is requisting a user when the url is `/users/bob`, wrapping it like `{ "user": { "firstName": "Bob" } }` is meaningless.
4.  Forget about UNIX timestamps, use [ISO8601](http://www.iso.org/iso/iso8601) timestamps without exception in your service protocol. UNIX timestamps do not support timezones nor milliseconds.
5.  Every resource instance in a service response must contain a `href` attribute containing its location. The developer consuming your service should get accustomed to these `href` attributes as much as possible so he feels comfortable using them to link to other resources.
6.  Prefer linking resources to embedding resources unless the linked resource is almost always required while fetching the main resource. This is context-dependent. For example, a `GET /users/bob` might return the following:

    ```
    {
        "firstName": "Bob",
        "lastName": "Marley",
        "address": {
            "href": "http://example.com/users/bob/address"
        }
    }
    ```

    When accessing the address is fairly uncommon this response works well for service consumers; the client has sufficient details to retrieve the address if it needs to. However, if in say 80% of requests the consumer also requests the user's address you can lower the number of network requests by embedding the resource within the `GET /users/bob` response body.

7.  When a resource is available under under two different urls (a scenario common when  embedding resources) consider using partial result queries instead (e.g. `GET /users/bob?fields=firstName,lastLame.` Having two different urls for the same resource may improperly invalidate a HTTP cache causing two different representations of the same resource to exist at the same time.
8.  Be descriptive in error messages and include a code that can be interpreted by a machine. This is useful for machine interpretation, and internationalization in particular.
```json
{
    "status": 401,
    "message": "Insufficient priviliges",
    "code": 1205,
    "more_info": "http://www.someresult.com/docs/errors/1205"
}
```

# Versioning Features

# HTTP Features

1.  The server MUST treat [If-Match](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.24) and [If-None-Match](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.26) headers provided by clients as defined in [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

2.  `PUT` or `DELETE` API requests SHOULD provide an [If-Match](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.24) header, and SHOULD react meaningfully to 412 (Precondition Failed) responses.

## Headers

## Methods

1.  Methods are used correctly for safety and idempotency.

Method | Safe | Idempotent
-------|------|------------
GET    | Yes  | Yes
HEAD   | Yes  | Yes
OPTIONS | Yes | Yes
PUT    | No   | Yes
DELETE | No   | Yes
POST   | No   | No

Note that:

- Safe methods (i.e. `GET` operations) are not expected to cause side effects (read-only operations)
- Idempotent methods always returns same results, equivalent to setters in programming languages (i.e. to implement a `DELETE` as idempotent, always return a `200` (OK), even if the resource would otherwise cause a `404` when not found).

When HTTP methods are implemented for safety and idempotency, clients can limit the responses to handle to:

-   2xx: OK
-   4xx: client errors
-   5xx: server errors
-   otherwise: retry or abort

A corollary to this is that `GET` is always safe and idempotent: do not abuse it (i.e. `GET /item/1/delete` should not be available).

1.  HTTP Methods are used correctly:
    1.  `GET` – Retrieve a resource
    2.  `POST` – Create a resource (\*), non-idempotent or unsafe operations
        1.  `POST` should be used for
            -   annotation on resources
            -   posting messages
            -   providing form data
            -   extending databases

        2.  Use `POST` when creating a new resource and the server determines the resource identifier.
        3.  Use `POST` if you run queries with very large inputs (only as a last resort because of limited URI length).
        4.  `POST` methods are not resubmitted automatically, but always requires user to accept that. Sometimes `POST` must be preferred to `GET`, because URIs are used as `Referer` header for linked resources (and they contain query string parameters), and to avoid queries with too many parameters.
        5.  Resources can be created with `POST` as “Factory Method Pattern”. A common practice is to use a collection resource as a factory.
        6.  A Slug header may suggest the server a name to use when creating the resource.
        7.  A successful `POST` to create a resource will return a `201` (Created) and a `Location` header with the URI of the new resource. A `Content-Location` header with the URI of the resource should be added if the response body contains a full description of that resource.
        8.  `Last-Modified` and `ETag` headers will help in defining cached results.
        9.  `POST` can be used for asynchronous tasks, such as calculating a value that requires some time. In such situations, a `202` (Accepted) is returned when the task is started. The response should contain the current status, and related information (i.e., a time estimate).

            When the client asks for that resource (with a GET), it receives:

            -   a `200` (OK), with a representation of the resource and its status
            -   a `303` (See Other) on completion and a Location header with the URI of the outcome
            -   a `200` (OK) with an error in the body, describing the failure of the task.

    3.  `PUT` – Update or create a resource
        1.  Create resources with `PUT` when clients can decide URIs of resources (i.e., decide where to put a new file in a folder). Otherwise, use `POST`.
        2.  To let clients assign URIs, the server needs to explain to clients how they are organized and their validation rules. Filtering and security rules may be added to restrict naming.
        3.  Asynchronous updates can be achieved similar to `POST`.
        4.  Use `PUT` if you want to overwrite an existing resource (e.g. update all properties of `/users/bob`).

    4.  `DELETE` – Delete a resource

        1.  `DELETE` can be used for asynchronous deletion. A `202` (Accepted) is returned, and successive `200` (OK) will track the result of the operation. (See `POST`)

    5.  `PATCH`

        1.  Use `PATCH` if you want to partially update an existing resource (e.g. only update the first and last name of a user entity). While `PATCH` is supported fairly well and is defined in [RFC 5789](http://tools.ietf.org/html/rfc5789), it should be avoided if service consumers are not able to make `PATCH` requests.

    6.  `HEAD`

    7.  `OPTIONS`

    8.  `TRACE`

2.  Service implements no custom HTTP methods (verbs). `POST` should be used instead.

## Requests

1.  Requests are stateless

## Responses

1.  HTTP response codes are used correctly.
    1.  *`200` OK*: when a request was processed successfully, for example when a partial update succeeded.
    2.  *`201` CREATED*: when creating a new resource instance using `POST` succeeded. Send the new resource representation in the body.
    3.  *`202` ACCEPTED*: when a `PUT`, `POST` or `DELETE` initiated an asynchronous request and it was accepted. The response body should contain the current status of the asynchronous job and provide a link to track the progress of the request. Make a clear distinction between the asynchronous job and the resource that is being created as a result of that job. The job itself was accepted and should return a `200` OK result with status of the asynchronous job (for example, running, deleted or failed). If the job was successfull and produced a new resource return `303` SEE OTHER with a Location header containing the new resource url.
    4.  *`204` NO CONTENT*:  when the request was processed successfully but no response body is available.
    5.  *`400` BAD REQUEST*: when a syntactical error occurred, for example when a validation constraint failed.
    6.  *`401` UNAUTHORIZED*: when user is not authorized and authenticated. When user is authenticated but just not authorized issue a 403 FORBIDDEN.
    7.  *`403` FORBIDDEN*: when the user is authenticated but has insufficient priviliges to access the resource.
    8.  *`404` NOT FOUND*: when a resource was not found.
    9.  *`405` NOT ALLOWED*: when a HTTP method is not allowed, for example when a `GET` request is received but only `POST` is returned. The standard says an `Allow`-header MUST be present when issuing a `405`, e.g.: `Allow: GET, HEAD, PUT`.
    10. *`406` NOT ACCEPTABLE*: when the client sends a list of `Accept-Encoding` values and none of the encodings are available for the requested resource return `406`.
    11. *`409` CONFLICT*: when a resource already exists (i.e. the business key exists). The response body should describe how to resolve the conflict.
    12. *`410` GONE*: when the resource no longer exists. Use `404 NOT FOUND` when you don't keep track of removed resources.
    13. *`412` PRECONDITION FAILED*: when a `PUT` requests contains `If-Unmodified-Since` or `If-Match` HTTP headers do not match the `ETag` values on the server. These tags are used to implement concurrency control for `PUT` requests.
    14. *`413` REQUEST ENTITY TOO LARGE*: when the body of a `POST` or `PUT` request is too large.
    15. *`415` UNSUPPORTED MEDIA TYPE*: when a client sent a message body in a format the service does not understand.
    16. *`500` INTERNAL SERVER ERROR*: when an exception occurred on the server. This is a good "catch-all" or "fallback" error code when an uncaught exception occurred.
    17. *`503` SERVICE UNAVAILABLE*: when the server cannot fulfill a request for some time. Add a `Retry-After` HTTP header if possible.

# Performance Features

1.  Service supports cache

# Scalability Features

# Monitoring Features

# Logging Features

# References

- http://di-side.com/di-side/services/web-solutions/rest-guidelines/
- http://www.nielskrijger.com/2013/08/rest-and-json-api-guidelines-and-best.html
- https://developer.atlassian.com/display/DOCS/Atlassian+REST+API+Design+Guidelines+version+1
