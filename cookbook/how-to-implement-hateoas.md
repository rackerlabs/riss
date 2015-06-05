# Hypermedia as the Engine of Application State (HATEOAS)

# Problem

How should your RESTful service manage the application state of a client.

# Solution

The client's application state should be defined entirely by representations transferred to it by the server. Clients should change application state only by interacting with resources identified in those representation using the uniform interface. That is, state transitions occur when clients issue an HTTP request to one of the hyperlinks withthin the represenation.

# Discussion

The REST architectural style is very particular about how state is managed. There are two distinct kinds of state that are allowed in RESTful solutions: resource state and application state. Conversational or client session state is not allowed. Every interaction between a client and server uses a representation to transfer state. When the client receives a representation, it's application state changes. When it writes one back (or deletes one) the server resource state changes.

*REST APIs must be hypertext-driven*

A REST API should be entered with no prior knowledge beyond the initial URI (bookmark) and set of standardized media types that are appropriate for the intended audience (i.e., expected to be understood by any client that might use the API). From that point on, all application state transitions must be driven by client selection of server-provided choices that are present in the received representations or implied by the user’s manipulation of those representations. The transitions may be determined (or limited by) the client’s knowledge of media types and resource communication mechanisms, both of which may be improved on-the-fly (e.g., code-on-demand).

-- Roy Fielding, [REST APIs must be hypertext-driven](http://roy.gbiv.com/untangled/2008/rest-apis-must-be-hypertext-driven)

The benefits of HATEOAS are many, and align well with the goals of SOA:

1.  Abstraction of State Transition Logic - clients do not need to know the business logic rules that allow a state transition to be valid. They are simply told which transitions can be invoked in the state they are in. By eliminating out-of-band shared knowledge about valid system state transitions, we remove hidden logic coupling that creates brittleness that impedes maintainability on longevity of the solution.
2.  Decoupling of Clients from URI Structure Contracts - Clients do not couple to the details of URI construction and may treat URIs (correctly) as being opaque. This eliminates a whole class of bugs when clients get this logic wrong or fail to track changes. Clients should never have to construct resource URIs.
3.  Increased extensibility via Backwards Compatible API Changes - Leveraging the extensibility of XML, you can add application state transitions (provided you support the old ones) that a client can optionally ignore if they do not understand them. If you use semantic linking, you can restructure the client's state machine without needing to change the service contract.
4.  Standardized Traversal Patterns - Symantic linking can be used to distill out navigation idioms for common data structures so that they can be reused. For example, we can define an idiom for paging throuh a large data (forward scolling linked list) set using a "next" link relation, or through a tree using the "up" relation. There is an [IANA link relation registry](http://www.iana.org/assignments/link-relations/link-relations.xhtml), for example.
5.  Support for Unanticipated Use Cases - By laying out every possible application state transition explicitly, clients can see the full set of what is possible and identify solutions to use cases that the server was not intended to support. For example, spiders should be able to crawl the resource web, indexing it for various purposes, and automated testing tools can seek to automate certain tests because they can fully enumerate application state transitions.

# Good and Bad Practices

## Good Practices

1.  Explicitly provide hyperlinks for every application state transition relevant to the client.
2.  Use opaque URIs and provide them explicitly to the client.
3.  Use semantic linking (see Semantic Linking [forthcoming]) with standard link relations.
4.  Leverage standard media types and link relations, prefering reuse over proliferation.

## Bad Practices

1.  Assume that clients will build URIs from id's and entity primary keys.
2.  Don't rely on out of band information to inform clients which operations make sense on which URIs.
3.  Don't proliferate new media types when existing ones will work fine.

# Unresolved Issues

None.

# Frequently Asked Questions

The following are frequently asked questions whose answers are worth collecting in one place.

*Can an API be RESTful if it isn't using HATEOAS?*

REST is an architectural style, so this there's no specification to answer such questions definitively, but Roy Fielding has made clear that HATEOAS is a hallmark of RESTful ness. Leonard Richardson devised a REST maturity model that defines three tiers of RESTfulness. The third tier is HATEOAS.

# References

1.  [REST APIs must be hypertext-driven](http://roy.gbiv.com/untangled/2008/rest-apis-must-be-hypertext-driven), Roy Fielding.
2.  [IANA link relation registry](http://www.iana.org/assignments/link-relations/link-relations.xhtml)
3.  [RESTful Web Services Cookbook](http://search.safaribooksonline.com/9780596809140), Subbu Allamaraju.
4.  [Architectural Styles and the Design of Network-based Software Architectures](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm), Roy Fielding. (Fielding's dissertation defines REST as an architectural style.)

# Contributors

* Bryan Taylor
* Mark Morga

## Comments:

1. Mark Morga and Bryan Taylor - I suggest adding a good practice that we only return hyperlinks for state transitions that the current user is permitted to perform.

    For example, if Alice is not permitted to delete a resource, but Bob is permitted to do so, then only Bob should see a hyperlink for deleting the resource.

    This would depend on including semantics about the hyperlink (i.e. differentiating a delete relationship from an update relationship for the same resource).

    Thoughts?

    Ben Truitt
