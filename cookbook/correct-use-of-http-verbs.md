# Correct Use of HTTP Verbs

# Problem

You want to use HTTP's uniform interface of request method verbs correctly. When do you use `GET`, `POST`, `PUT`, `DELETE`, `OPTIONS`, and `HEAD`?

# Solution

Use `GET` for safe retrieval that does not change resource state. Use `POST` to submit a message data block to a data handling process. Use `PUT` to replace the resource at the request URI with the submitted message data block. Use `PATCH` to update just part of a resource. Use `DELETE` to tell the server to remove the resource at the given URI. Use `OPTIONS` to determine which other HTTP verbs are available on a given resource. Use `HEAD` to check for validity, accessibility, and recent modification.

# Discussion

The relationship between CRUD operations (create, retrieve, update, delete) and the HTTP request methods (`GET`, `POST`, `PUT`, `DELETE`) is not a simple correspondence, though this incorrect assertion is often made. The following table captures these nuances better:

*CRUD to HTTP Verb Conditional Mapping*

CRUD Operation | HTTP Request Method | Condition
---------------|-----------|-----------
Create         | `PUT`     | if and only if a representation of the complete resource state is transferred and the resource at the request URI comes into existence with this state
Create         | `POST`    | if the message block is a representation that results in a subordinate resource coming to exist with matching state at a new URI, typically at a new URI created by the server
Retrieve       | `GET`     | if the retrieval is safe
Update         | `PUT`     | if and only if a representation of a complete resource state is transferred and the preexisting resource at the request URI changes state to match
Update         | `PATCH`   | if the transferred representation causes the resources at the request URI to partially change state
Delete         | `DELETE`  | if the resource at the reqeust URI will no longer exist

`GET` generally retrieves a resource representation. `GET` is defined in [RFC 7231 section 4.3.1](http://tools.ietf.org/html/rfc7231#section-4.3.1) as a method for information retrieval. `GET`'s use should be both idempotent and safe. The terms "safe" and "idempotent" are discussed in in [RFC 7231 section 4.2](http://tools.ietf.org/html/rfc7231#section-4.2). `GET` is designed so that all information necessary for the interaction is part of the URI, thus promoting URI addressability. In fact, any retrieval which is safe from side-effects should use `GET` and only `GET`. See this [URIs - Axioms of Web Architecture](http://www.w3.org/DesignIssues/Axioms.html#`GET`2) from Tim Berners-Lee's.

`POST` is defined by [RFC 7231 section 4.3.3](http://tools.ietf.org/html/rfc7231#section-4.3.3) `POST` need not be safe or idempotent. The URI in a `POST` request identifies the resource that will handle the enclosed entity. The actual function performed by the `POST` method is determined by the server and is usually dependent on the Request-URI. The posted entity is subordinate to that URI in the same way that a file is subordinate to a directory containing it, a news article is subordinate to a newsgroup to which it is posted, or a record is subordinate to a database. With `POST`, some information intended to affect change to the resource state may be part of the protocol headers, not in the URI. With this approach, the resulting URI for identifying the resource may be shorter, but the advantages of URI addressability are lost.

`PUT` is defined by [RFC 7231 section 4.3.4](http://tools.ietf.org/html/rfc7231#section-4.3.4) `PUT` requests that the included request body should be stored at the requested URL. If an existing resource already exists at the requested URL, `PUT` will overwrite it with the message body. `PUT` is idempotent. Note that the client request supplies a suggested URI where the new resource might be located.

`PATCH` is defined by [RFC 5789](http://tools.ietf.org/html/rfc5789). Clients should avoid collisions from multiple PATCH requests by using a conditional request such that the request will fail if the resource has been updated since the client last accessed the resource.

`DELETE` is defined by [RFC 7231 section 4.3.5](http://tools.ietf.org/html/rfc7231#section-4.3.5) `DELETE` should be idempotent. The `DELETE` method requests that the origin server delete the resource identified by the Request-URI.

`OPTIONS` is defined by [RFC 7231 section 4.3.7](http://tools.ietf.org/html/rfc7231#section-4.3.7) `OPTIONS` allows the client to determine the options and/or requirements associated with a resource, or the capabilities of a server, without implying a resource action or initiating a resource retrieval.

`HEAD` is defined by [RFC 7231 section 4.3.2](http://tools.ietf.org/html/rfc7231#section-4.3.2) The `HEAD` method is identical to `GET` except that the server MUST NOT return a message-body in the response. This method is often used for testing hypertext links for validity, accessibility, and recent modification.

# Good and Bad Practices

## Good Practices

1.  Use `GET` to retrieve representations in a safe and idempotent way.
2.  `POST` a representation to a URI to create it under a new child URI
3.  Use `PUT` or `DELETE` and not `POST` if the operation will be idempotent.
4.  Prefer the use of `PATCH` over `PUT` for partial updates to a resource.
5.  Use `PATCH` with a conditional request to make the operation idempontent.

## Bad Practices

1.  With the exception of `PATCH`, do not use HTTP methods that aren't part of the HTTP Specification ([RFC 7231 section 4](http://tools.ietf.org/html/rfc7231#section-4)).
2.  Do not alter resource state with `GET`
3.  Do not use another method besides `GET` to do a safe retrieval. EG: don't `POST` a "getXYZ" message.

# Frequently Asked Questions

The following are frequently asked questions whose answers are worth collecting in one place.

1. *Do HTML forms support all the HTTP verbs?*

    Answer: HTML v4 supports form objects with the methods `GET` and `POST` only. However HTML v5 will support all four verbs equally.

2. *Can I use other HTTP verbs, such as those of WebDAV, or exotic or custom ones?*

    Answer: Not in a way compliant with the Rackspace SOA approach to RESTful Web Services. RESTful architectures must adopt a single uniform interface. RESTful Web Services as Rackspace defines it adopt the one defined by the W3C's HTTP specification  (RFC 7231). While technologies like WebDAV (RFC 2518) define additional HTTP verbs, and [several other request methods are listed by IANA](http://www.iana.org/assignments/http-methods/http-methods.xhtml), using these is not generally accepted as an approach to RESTful Web Services. The "Web" connotates HTTP with the verb set from section 4 of RFC 2731, and we've extended this to include `PATCH` as a specific [work-around condoned by none other than Roy Fielding](http://roy.gbiv.com/untangled/2008/rest-apis-must-be-hypertext-driven). While using a different but static verb set in an architecture might sometimes fit a general REST architectural style, it cannot be called Web Services.

    Adding exotic or custom HTTP verbs not only conflicts with the notion of RESTful Web Services, but worse, it creates coupling to a mutated network protocol. Loose coupling counsels us to rely on things that don't change so that we can create robustness against things that do, and clients written against mutant forms of HTTP inherently couple to such changes in a non transportable way that ruins the value of HTTP as a ubiquitous and simple protocol.

3. *What do you support queries with large inputs that would overflow length limits on the URI if it was done with `GET`.*

    Answer: In this situation, you have no choice but to use `POST` even though this cuts against the semantics of HTTP's uniform interface and makes answers non-cacheable. This is discussed in Recipe 8.3 of the RESTful Web Services Cookbook, Subbu Allamaraju.

4. *How can we support clients that do not have support for `PUT` and `DELETE`.*

    Answer: There are two common approaches:

    1.  Use `POST`, but include an `X-HTTP-Method-Override` header, popularized by the Google's GData API. If supplied, the server should act as if the HTTP operation given in the header's value had been called instead of `POST`.
    2.  Use `POST`, but include a hidden field on a form called `_method`, which if supplied, acts as-if that method instead of `POST` had been called.

    Either approach is fine. The latter is a little easier to support browsers, while the former keeps the override HTTP operation visible to intermediaries.

5. *What exactly does "idempotent" mean, and why is idempotency important.*

    Answer: An operation is idempotent if the effect on the server of one or multiple calls in succession is the same. Idempotency is important because it allows operations to be made reliable by safely repeating them until they report success. This works because an earlier operation may have succeeded, but the communication of that success failed. If the operation is idempotent, it is not a problem to repeat it even though it already succeeded. Idempotency is discussed more in the [HTTP Semantics and Content RFC 7231](http://tools.ietf.org/html/rfc7231, section4).

# References

1.  [RESTful Web Services Cookbook](http://search.safaribooksonline.com/9780596809140), Subbu Allamaraju. via Safari, especially Chapter 1, recipes 1.4 through 1.11.
2.  [RFC 7231](http://tools.ietf.org/html/rfc7231) Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content
3.  [Wikipedia on Hypertext Transfer Protocol](http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol).
4.  W3C document on [the Architecture of the WWW](http://www.w3.org/TR/webarch/)
5.  Tim Berners-Lee's [URIs - Axioms of Web Architecture](http://www.w3.org/DesignIssues/Axioms.html).
6.  [RESTful Web Services Cookbook, Recipe 8.3](http://search.safaribooksonline.com/9780596809140/recipe-how-to-support-queries-with-large-inputs), Subbu Allamaraju. on How to Support Query Requests with Large Inputs.
7.  [REST APIs must be hypertext-driven](http://roy.gbiv.com/untangled/2008/rest-apis-must-be-hypertext-driven)

# Contributors

* Bryan Taylor
* Mark Morga
* Ben Truitt
