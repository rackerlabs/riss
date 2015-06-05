# Identifying Resources with URIs

# Problem

How should you give the resources of a service a URI to identify them?

# Solution

Identify the nouns in the business domain by analyzing the requirements. Determine which are in the functional scope of the service. Place these in a hierarchy based on the parent-child relationships and map the hierarchy to a path. The general rule is that domain names for entities or anything else owned by the service should be path elements, while data elements owned by external services should be query parameters.

Generally use https: scheme URLs to locate resource representations. The root level of the path part of the URI will be a version identifier when using versioned URIs, but will be the top level domain entity when using permalinks, as described in the Versioning cookbook recipe.

Top level domain entities will be at a path that gives the plural domain noun, then the entity identifier. Child domain entities in the hierarchy extend from their parent, also with a singlular noun and identifier.

Collection returning queries are placed at the same base path as the domain noun of what is being searched for. Perform simple queries using query parameters when the searched by noun is not a child of the parent. Searches will generally return a representation that is a collection even if there is only one hit.

Compositions and aggregate views should be given a descriptive noun for a name and otherwise treated like a canonical domain entity with plural form. Composition paths can be extended with entity names for the service (or with actions as below).

Inheritance hierarchies of domain entities should generally be mapped to a single entity name of the lowest type that all concrete classes inherit from for the purposes of naming URIs. Queries, however, may substitute the specific subtype noun for the general one to express that we are searching only for resources of the restricted type (or it's children).

Actions or events that happen to a resource can be treated as subordinate domain nouns, using the gerund form of the action verb or the event name, but should be placed under a meaningful path identifier such as "actions" or "events" or something similar. The resource representation that will be returned expresses state data about the action, so that POST or PUT operates as a request. Note that it's quite common for actions and events to form an inheritance hierarchy, and the rules above apply.

Refinements to a resource, which are useful for things like partial updates, can be given domain names and placed under the parent resource in the path.

# Discussion

The solution is best illustrated by examples.

## Examples

These examples are shown using versioned URIs.

1.  An order service could expose an order by id under the canonical path using the plural "orders" and an id:

`https://order.api.rackspace.com/v1.0/orders/{orderId}`

1.  When the domain entities of the service have a natural structure, we reflect this using the path. For example, the order service may own orders and order items.

`https://order.api.rackspace.com/v1.0/orders/{orderId}/orderItems/{orderItemId}`

`https://order.api.rackspace.com/v1.0/orders/{orderid}/orderItems`

`https://order.api.rackspace.com/v1.0/orders/{orderid}/orderItems?sku={skuId}`

3. Showing a collection of all orders also uses the plural domain name "orders", with various search parameters. POSTing to the base plural noun creates a new order (and returns its canonical URI with the singular form and its Id in the path.

`https://order.api.rackspace.com/v1.0/orders`

Note that we don't put customer in the path because the order service doesn't own this entity:

`https://order.api.rackspace.com/v1.0/orders?customerId={customerId}`

`https://order.api.rackspace.com/v1.0/orders?placementDate={date}`

1.  A composition and an aggregate view should be given a name and used in the base path. Suppose we created an "orderHistory" aggregate that shows a view of orders that can be searched by customer:

`https://order.api.rackspace.com/v1.0/orderHistories?customerId={customerId}`

Here orderHistories is again plural. If, on the other hand, we wanted a collection of the aggregate or composition meeting a criteria, we would use the plural:

`https://order.api.rackspace.com/v1.0/orderHistories?customerHighProfileFlag=true`

1.  Suppose that orders have several lifecycle states. At some point after being drafted, an order is placed, fulfilled, or cancelled. Information about these order actions would be located by putting the action name in plural noun form under the resource path with "/actions" to return details if the actions state is active, or 404 NOT FOUND otherwise.

`https://order.api.rackspace.com/v1.0/orders/{orderId}/actions/placements`

`https://order.api.rackspace.com/v1.0/orders/{orderId}/actions/fulfillments`

`https://order.api.rackspace.com/v1.0/orders/{orderId}/actions/cancellations`

When these actions are idempotent (an order cannot be placed twice in a row), so these actions can be requested by use PUT of the appropriate representation to these URIs. When they are not idempotent, the are created by posting to the plural form. For example, to track approvals order, we could POST to:

`https://order.api.rackspace.com/v1.0/orders/{orderId}/approvals`

and then we see information about individual approvals by doing a GET against:

`https://order.api.rackspace.com/v1.0/orders/{orderId}/approval/1`

It is often useful to use an aggregate called something like "actions" to find all actions:

`https://order.api.rackspace.com/v1.0/orders/{orderId}/actions`

We could POST to this, letting the representation declare what type of action is meant.

You can also get a list of actions across individual orders by leaving the `{orderId}` parameter off:

`https://order.api.rackspace.com/v1.0/orders/actions/placements`

`https://order.api.rackspace.com/v1.0/orders/actions`

These can be searched by adding query parameters:

`https://order.api.rackspace.com/v1.0/orders/actions/placements?since={sinceTimestamp}`

1.  Inheritance hierarchies don't affect the canonical URI much as the canonical path uses the base entity. If order can be subclassed into a subscription order, consulting order, and license order, and we represent these differently in the domain model (and in XML), then we would still name all of the entities using "order":

`https://order.api.rackspace.com/v1.0/orders/1 is a subscription`

`https://order.api.rackspace.com/v1.0/orders/2 is consulting`

`https://order.api.rackspace.com/v1.0/orders/3 is a license`

However, we might search for all license orders for a customer by replacing orders with licenses to denote the restricted search scope:

`https://order.api.rackspace.com/v1.0/licenses?customerId={customerId}`

1.  Sometimes we may want to expose a resource that allows for editing of part of a parent resource. For example, the order resource may contain billing information as part of it. We might wish to update just the billing information, which we can do by exposing this a sub-resource:

`https://order.api.rackspace.com/v1.0/orders/{orderId}/billing-information`

The URI we use should be canonical in some sense. In particular, we should not identify the same resource systematically by multiple URIs in the owning services namespace. If this happens, we say that one URI is an alias for another: it ALWAYS means the same thing. If we have two URIs for the same thing, one of them should redirect to the other. Violating the no-aliases rule will break caching because intermediaries cannot tell that the representations obtained are the same and might provide stale data for one and not the other when changes occur.

It can be easy to break the "no aliases" rule when inheritance hierarchies are involved if we are not careful. We should NOT refer to the license order from example 6 above by these equivalent URIs:

`https://order.api.rackspace.com/v1.0/orders/3`

`https://order.api.rackspace.com/v1.0/licenses/3` Violates the "no aliases" rule, duplicating order/3.

It is not a violation of the aliasing rule for two resources to temporarily have similar enough state so that the produce the same representation for a while. For example:

`http://hostname/blogs/current` and

`http://hostname/blogs/2010/8/6/2`

might happen to produce the same representation until the former changes when a new blog entry is added. This is not an alias. A resource is a time varying function into a multiset of representations, so temporary matching of representations does not imply that for all time the resources are the same. An alias exists when all representations at all times must be the same. For example, if a resources

`http://hostname/blogs/6-Aug-2010/2` and

`http://hostname/blogs/2010/8/6/2`

exists, then this IS an alias because we've simply represented the search date in two different ways.

Becaues a resource is a time varying multiset of representations, if we form a derived "variant" resource, by changing the set of allowed media types (typically to a single one) then we are strictly no violating the no aliases rule. For example if

`http://hostname/blogs/current` has and both html and pdf representations, it's variants,

`http://hostname/blogs/current.html` and

`http://hostname/blogs/current.pdf`

are not exactly the same resource and do NOT violate the no-aliasing rule. Said differently, two resources that handle content negotiation differently are distinct resources even if this is the only difference between them.

There are times when location independent URIs will be appropriate, or that we wish to refer to "real world object" as opposed to document representations. In these case, we use a URN, either from the urn: scheme, or from the http: schema ([see FAQ question \#1 for more info](http://one.rackspace.com#url-urn-uri)).

# Good and Bad Practices

## Good Practices

1.  Provide URIs as identifiers for all resources of interest.
2.  Assign distinct canonical URIs to distinct resources. Exceptions for variant representations are OK.
3.  Reuse URI schemes (rather than create a new one) when it provides the desired properties of identifiers and their relation to resources. More specifically, we commonly use use `http:` , `https:` , or `urn:` to identify resources.
4.  Use urn: scheme URNs to identify a resource by name when dereferencing a resource representation is not an issue or a good namespace identifier (NID) exists.
5.  Use http: schema URNs to identify a resource by name using either technique described by W3C document [Cool URIs for the Semantic Web](http://www.w3.org/TR/cooluris/) (hash or 303 redirect) when it's desirable to dereference something related.
6.  Use plural nouns owned by the service in the path.
7.  Treat query parameters as optional with sensible defaults.
8.  POST to a collection URI to create a subordinate resource. The response should include a location header with the canonical path URI to the resource. It should also generally return the resource representation too, which should have self links.
9.  Put action resources as path extensions for the parent resource using the gerund for of the verb. The representation should provide state metadata about the action's processing lifecycle.
10. Expose fine grained "refinements" of a resource at child path elements to allow partial updates or edits.
11. Create separate variant URIs to refer to resources represented in a single specific media types. This does not violate the "no aliases" rule because resources that handle content negotiation differently are different.

## Bad Practices

1.  Agents making use of URIs SHOULD NOT attempt to infer properties of the referenced resource. URIs should be opaque to clients. Use URI templates, only when directed to by the server, such as via the WADL.
2.  Avoid URI aliases and do NOT associate different URIs to inherently identify the same resource.
3.  Do NOT use domain nouns from another service in the canonical path. A service must be free to control its own namespace, so coupling to another services domain model for canonical naming of resources is bad. Search based on these, because versioned representations will contain these names.
4.  Do NOT confuse people by using plural forms when you return a single element. Plural nouns imply collections. Collections in XML will have a parent container element.
5.  Do NOT leave out "self" references. For entities, use the canonical path. For collections use an appropriate query parameter set.
6.  The canonical URI path for a resource should not include subtypes from an inheritance hierarchy.
7.  Do not use serve inconsistent media types from the URI for a variant. EG: `http://hostname/blogs/current.pdf` should only return a PDF, regardless of accepts: headers used.

# Unresolved Issues

None.

# Frequently Asked Questions

The following are frequently asked questions whose answers are worth collecting in one place.

*What is the difference between a URI, URL, and URN?*

This is defined by [RFC 3986](http://www.ietf.org/rfc/rfc3986.txt) (RFC 2396 is often cited but is obsoleted by 3986) and explained well by the [Wikipedia URI](http://en.wikipedia.org/wiki/Uniform_Resource_Identifier) article. A URI is either a URL or a URN. A URL provides a means of locating the resource by describing its primary access mechanism (e.g., its network "location"). A URN names a resource, meaning they provide persistent, location-independent, resource identifiers.

A URN is often expressed within the "`urn:`" schema see RFC 2141, but RFC 3986 says:

> The term "Uniform Resource Name" (URN) has been used historically to refer to both URIs under the "urn" scheme [RFC2141], which are required to remain globally unique and persistent even when the resource ceases to exist or becomes unavailable, and to any other URI with the properties of a name.

The W3C document [Cool URIs for the Semantic Web](http://www.w3.org/TR/cooluris/) discusses two common ways of making http: scheme URI's for "real world objects", the hash ("\#") fragment method and the status code "303 See Other". The latter method was accepted and [announced](http://lists.w3.org/Archives/Public/www-tag/2005Jun/0039.html) after long debate by the W3C Technical Architecture Group (TAG). The possible ambiguity of http: URIs have been discussed by Tim Berners Lee in [What do HTTP URIs Identify?](http://www.w3.org/DesignIssues/HTTP-URI.html).

# References

1.  [RESTful Web Services Cookbook](http://search.safaribooksonline.com/9780596809140), Subbu Allamaraju. Especially Chapter 2. Identifying Resources and Chapter 4. Designing URIs.
2.  [Architectural Styles and the Design of Network-based Software Architectures](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm), Roy Fielding. Fielding's dissertation defines REST as an architectural style.
3.  [RFC 3986](http://www.ietf.org/rfc/rfc3986.txt): Uniform Resource Identifier (URI): Generic Syntax
4.  [W3C document Cool URIs for the Semantic Web](http://www.w3.org/TR/cooluris/)
5.  Tim Berners Lee in [What do HTTP URIs Identify?](http://www.w3.org/DesignIssues/HTTP-URI.html)
6.  The [Wikipedia URI](http://en.wikipedia.org/wiki/Uniform_Resource_Identifier) article

# Contributors

* Bryan Taylor
* Mark Morga
