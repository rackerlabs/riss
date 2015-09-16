# RESTful URI Design

## Problem

You want to know how to choose reasonable URLs for the resources in your RESTful API.

## Guidelines

1.  Prefer short URLs (using short hostnames and short path components). This makes them easy to write down or spell or remember.
2.  URL paths should be hierarchical, and should permit consumers to "hack" the path "up the tree." The user should be able to remove the leaf path and get a meaningful parent resource.
3.  URLs should be meaningful. They should name the resource using the [ubiquitous language](http://martinfowler.com/bliki/UbiquitousLanguage.html) of the domain that the service is part of.
4.  The naming scheme you use for URLs in your api should be consistent across resources. If you use extensions, do not use .html in one location and .htm in another. Consistent patterns make URLs more predictable.
5.  URLs should be readable. That is, they should not use mysterious abbreviations, etc.
6.  Strive to limit your resource hierarchy to no more than a depth of 3 resources. You shouldn’t need to go deeper than resource/identifier/resource.
    ```
    /resource/identifier/resource
    ```
7.  Avoid URI aliases and do NOT associate different URIs to identify the same resource. If this cannot be avoided, one resource should be considered canonical, and requests to any alias should redirect to the canonical resource.
8.  Treat query parameters as optional with sensible defaults. If you think a query parameter is required, that may indicate that you need to separate out a different resource.
9.  Expose fine grained "refinements" of a resource at child path elements to allow partial updates or edits. See examples below.
10.  Do NOT use domain nouns from another service in the canonical path. A service must be free to control its own namespace, so coupling to another service's domain model for canonical naming of resources is bad. See examples below.
11.  If you decide to use URI versioning then put the version number at the base of your URL (see Versioning APIs for discussion on if this should be done).
    * As described in the Versioning cookbook recipe,
        * The root level of the path component will be a version identifier when using versioned URIs
            ```
            http://example.com/v1/path/to/resource
            ```
        * The root level of the path component will be the top level domain entity when using permalinks
            ```
            http://example.com/permalink/to/resource
            ```

## Standards

1.  Resources must be identified using nouns, not verbs.
2.  Use plural nouns only for consistency (no singular nouns).
    ```
    /conversations
    ```
3.  Use matrix parameters to enable filtering collections of resources.  Matrix parameters can be included in the middle of a path hierarchy, but query paramters can apply only to the leaf node of the path hierarchy.
    ```
    /conversations;author=ben;topic=design
    ```
4.  URLs should be all lower case.
5.  Use hyphens rather than spaces or underlines.
    ```
    /configuration-boms
    ```
6.  URL vs. header: The [HTTP standard on resources](https://tools.ietf.org/html/rfc7231#section-2) points out that a design goal of HTTP is to separate the resource identification (URI) from the request semantics (method and a few headers).
    * If it changes 'what' you are identifying, put it in the URL
    * If it doesn’t 'what' you are identifying, like OAuth info, put it in the header.
7.  Use the HTTPS protocol (rather than HTTP).

## Recipes

1.  Identify the nouns in the business domain by analyzing the requirements. Determine which are in the functional scope of the service. Place these in a hierarchy based on the parent-child relationships and map the hierarchy to a path. The general rule is that domain names for entities or anything else owned by the service should be path elements, while data elements owned by external services should be query parameters.
2.  Inheritance hierarchies of domain entities should generally be mapped to a single entity name of the lowest type that all concrete classes inherit from for the purposes of naming URIs. Queries, however, may substitute the specific subtype noun for the general one to express that we are searching only for resources of the restricted type (or it's children).

    Get all animals (which will include, among others, the ducks):
    ```
    http://example.com/animals
    ```
    Get just ducks:
    ```
    http://example.com/ducks
    ```
3.  Actions or events that happen to a resource can be treated as subordinate domain nouns, using the plural noun form of the action verb or the event name.
    ```
    http://example.com/requests/{id}/cancelations
    ```
Submitting an HTTP POST request to this resource is the way that clients will invoke that action.
4.  Refinements to a resource, which are useful for things like partial updates, can be given domain names and placed under the parent resource in the path.  
```
http://example.com/people/{id}/email
```
Submitting a PUT request to this resource updates just the email domain entity, and not the other fields of the person entity.
5.  There are times when location independent URIs will be appropriate, or that we wish to refer to "real world object" as opposed to document representations. In these cases, we use a URN, either from the urn: scheme, or from the http: schema (see FAQ question \#1 for more info).

## Examples

These examples are shown using versioned URIs.  Again, please refer to the versioning document for discussion about whether this is what you'd like to do for your API.

1.  Showing a collection of all orders also uses the plural domain name `orders`, with various search parameters. `POST`ing to the base plural noun creates a new order (and returns its canonical URI with the singular form and its Id in the path.

    `https://order.api.rackspace.com/v1.0/orders`

    Note that we don't put customer in the path because the order service doesn't own this entity:

    `https://order.api.rackspace.com/v1.0/orders?customerId={customerId}`

    `https://order.api.rackspace.com/v1.0/orders?placementDate={date}`

2.  Suppose that orders have several lifecycle states. At some point after being drafted, an order is placed, fulfilled, or cancelled. Information about these order actions would be located by putting the action name in plural noun form.

    `https://order.api.rackspace.com/v1.0/orders/{orderId}/placements`

    `https://order.api.rackspace.com/v1.0/orders/{orderId}/fulfillments`

    `https://order.api.rackspace.com/v1.0/orders/{orderId}/cancellations`

    When these actions are idempotent (repeated calls do not change state), so these actions can be requested by use PUT of the appropriate representation to these URIs. When they are not idempotent, the are created by posting to the plural form. For example, to track approvals order, we could `POST` to:

    `https://order.api.rackspace.com/v1.0/orders/{orderId}/approvals`

    and then we see information about individual approvals by doing a `GET` against:

    `https://order.api.rackspace.com/v1.0/orders/{orderId}/approvals/1`

    It is often useful to use an aggregate called something like `actions` to find all actions:

    `https://order.api.rackspace.com/v1.0/orders/{orderId}/actions`

    We could `POST` to this, letting the representation declare what type of action is meant.

    You can also get a list of actions across individual orders by leaving the `{orderId}` parameter off:

    `https://order.api.rackspace.com/v1.0/orders/actions/placements`

    `https://order.api.rackspace.com/v1.0/orders/actions`

    These can be searched by adding query parameters:

    `https://order.api.rackspace.com/v1.0/orders/actions/placements?since={sinceTimestamp}`

4.  When naming resources in an inheritance hierarchy, the canonical path uses the base entity. For example, if order can be subclassed into a subscription order, consulting order, and license order, and we represent these differently in the domain model (and in the representation of the resource), then we would still name all of the entities using "order":

    `https://order.api.rackspace.com/v1.0/orders/1` is a subscription

    `https://order.api.rackspace.com/v1.0/orders/2` is consulting

    `https://order.api.rackspace.com/v1.0/orders/3` is a license

    However, we might search for all license orders for a customer by replacing orders with licenses to denote the restricted search scope:

    `https://order.api.rackspace.com/v1.0/licenses?customerId={customerId}`

    Or we could just use a matrix parameter to accomplish a filter

    `https://order.api.rackspace.com/v1.0/orders;type=license?customerId={customerId}`

5.  Sometimes we may want to expose a resource that allows for editing of part of a parent resource. For example, the order resource may contain billing information as part of it. We might wish to update just the billing information, which we can do by exposing this a sub-resource:

    `https://order.api.rackspace.com/v1.0/orders/{orderId}/billing-information`

The URI we use should be canonical in some sense. In particular, we should not identify the same resource systematically by multiple URIs in the owning services namespace. If this happens, we say that one URI is an alias for another: it ALWAYS means the same thing. If we have two URIs for the same thing, one of them should redirect to the other. Violating the no-aliases rule will break caching because intermediaries cannot tell that the representations obtained are the same and might provide stale data for one and not the other when changes occur.

It can be easy to break the "no aliases" rule when inheritance hierarchies are involved if we are not careful. We should NOT refer to the license order from example 4 above by these equivalent URIs:

`https://order.api.rackspace.com/v1.0/orders/3`

`https://order.api.rackspace.com/v1.0/licenses/3` Violates the "no aliases" rule, duplicating `order/3`.

It is not a violation of the aliasing rule for two resources to temporarily have similar enough state so that the produce the same representation for a while. For example:

`http://hostname/blogs/current` and

`http://hostname/blogs/2010/8/6/2`

might happen to produce the same representation until the former changes when a new blog entry is added. This is not an alias. A resource is a time varying function into a multiset of representations, so temporary matching of representations does not imply that for all time the resources are the same. An alias exists when all representations at all times must be the same. For example, if a resources

`http://hostname/blogs/6-Aug-2010/2` and

`http://hostname/blogs/2010/8/6/2`

exists, then this IS an alias because we've simply represented the date in two different ways.

Because a resource is a time varying multiset of representations, if we form a derived "variant" resource, by changing the set of allowed media types (typically to a single one) then we are strictly not violating the no aliases rule. For example if

`http://hostname/blogs/current` has and both html and pdf representations, it's variants,

`http://hostname/blogs/current.html` and

`http://hostname/blogs/current.pdf`

are not exactly the same resource and do NOT violate the no-aliasing rule. Said differently, two resources that handle content negotiation differently are distinct resources even if this is the only difference between them.

## Frequently Asked Questions

The following are frequently asked questions whose answers are worth collecting in one place.

1.  What is the difference between a URI, URL, and URN?

This is defined by [RFC 3986](http://www.ietf.org/rfc/rfc3986.txt) (RFC 2396 is often cited but is obsoleted by 3986) and explained well by the [wikipedia URI](http://en.wikipedia.org/wiki/Uniform_Resource_Identifier) article. A URI is either a URL or a URN. A URL provides a means of locating the resource by describing its primary access mechanism (e.g., its network "location"). A URN names a resource, meaning they provide persistent, location-independent, resource identifiers.

A URN is often expressed within the "urn:" schema see RFC 2141, but RFC 3986 says The term "Uniform Resource Name" (URN) has been used historically to refer to both URIs under the "urn" scheme [RFC2141], which are required to remain globally unique and persistent even when the resource ceases to exist or becomes unavailable, and to any other URI with the properties of a name.

The [W3C document Cool URIs for the Semantic Web](http://www.w3.org/TR/cooluris/) discusses two common ways of making http: scheme URI's for "real world objects", the hash ("\#") fragment method and the status code "303 See Other". The latter method was accepted and [announced](http://lists.w3.org/Archives/Public/www-tag/2005Jun/0039.html) after long debate by the W3C Technical Architecture Group (TAG). The possible ambiguity of http: URIs have been discussed by Tim Berners Lee in [What do HTTP URIs Identify?](http://www.w3.org/DesignIssues/HTTP-URI.html).

2.  Should machines infer meaning from path hiearchy?

Looking at the guideline above that says 'URL paths should be hierarchical, and should permit consumers to "hack" the path "up the tree." The user should be able to remove the leaf path and get a meaningful parent resource.', please interpret this as a convenience for humans to discovery API capabilities more easily.  Machines on the other hand should not have logic for path traversal. Prefer HATEOAS links for this purpose (see representation design chapter).

## References

1.  RESTful Web Services Cookbook, Subbu Allamaraju. Available [here](http://search.safaribooksonline.com/9780596809140) via Safari, especially Chapter 2. Identifying Resources and Chapter 4. Designing URIs.
2.  Architectural Styles and the Design of Network-based Software Architectures, Roy Fielding. Fielding's [dissertation](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm) defines REST as an architectural style.
3.  [RFC 3986](http://www.ietf.org/rfc/rfc3986.txt) Uniform Resource Identifier (URI): Generic Syntax
4.  Tim Berners Lee in [What do HTTP URIs Identify?](http://www.w3.org/DesignIssues/HTTP-URI.html)
5.  The [wikipedia URI](http://en.wikipedia.org/wiki/Uniform_Resource_Identifier) article
6.  [RESTful URI Design](http://blog.2partsmagic.com/restful-uri-design/)
7.  [Whitehouse API Standards](https://github.com/WhiteHouse/api-standards)
