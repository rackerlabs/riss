# Approach to Integration

# Problem

You are developing a software solution that needs to integrate with other Rackspace systems. What is the basic approach to integration adopted by Rackspace?

# Solution

Rackspace has adopted a RESTful Service Oriented Architecture [(SOA)](#references). All integration API functionality should be provided by services that follow the RESTful best practices. In addition to synchronous API calls, Rackspace also uses event driven architecture for service integration.

# Discussion

A collaboration of architecture teams collectively representing all of Rackspace have agreed to use RESTful service oriented architectures for integration. This applies to all APIs provided by Rackspace systems where consumers are internal or external. The foundational document for the Rackspace approach is the Rackspace SOA Guidelines.

The Rackspace SOA Guidelines adopt the approach to SOA popularized in the books of Thomas Erl. These books reflect the industry consensus on what service orientation means. Both the Rackspace SOA Guidelines, and references that follow the approach of Erl adopt certain basic terminology regarding service orientation, which we now provide.

A *service* is an independent unit of software that substantially adheres to the eight service-oriented design principles and provides service capabilities (see below) that are applicable to a well-defined, distinct functional context.

A *service capability* is a function exposed through a service contract.

A *service inventory* is a collection of services that are independently governed and standardized and which collectively implement a distinct, independent service oriented architecture. Rackspace has chosen to define a single, unified enterprise inventory that includes all the services produced throughout the company. See the Rackspace entry on using the service inventory.

The Rackspace SOA Guidelines elaborate on eight basic principles of SOA:

1.  *Standardized Service Contract*: Our RESTful services express their contracts using the HTTP standard's uniform interface and various artifacts such as XML Schema, WADL, and standardized link relations.
2.  *Loose Coupling:* Coupling should occur only as consumer-to-contract and contract-to-logic. Other forms of coupling should be avoided.
3.  *Service Abstraction:* Contracts should expose the minimum information necessary and should hide implementation details behind the contract.
4.  *Service Reuse-ability:* Service capabilities should be reused. There should be a single global authoritative capability for any functionality and any service capability that provides such functionality should be traceable to the authority.
5.  *Service Autonomy:* Services should have a high degree of design time control over their runtime environment, to isolate their capabilities from shared infrastructure components.
6.  *Service Statelessness:* Services should minimize the need to manage in-memory state.
7.  *Service Discoverability:* Services should be described with metadata that allows information about their properties to be discovered.
8.  *Service Composability:* Services capabilities should be reusable as compositions within the service layer.

These principles are elaborated in much greater detail

The default architectural style for Rackspace services is HTTP based Representational State Transfer (REST) consistent with [Roy Fielding's dissertation](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm) and various good descriptions of it on the web and in books such as the RESTful Web Services Cookbook by Subbu Allamaraju. RESTful HTTP based architectures have certain fundamental concepts:

1.  *Resources*: RESTful HTTP is concerned with accessing resources on the network. A resource can be essentially any coherent and meaningful concept that may be addressed and has an identity. Usually the resources relevant to IT systems are information resource (a document or system behavior defined by software), but sometimes a resource can correspond to a real world or abstract entity.
2.  *Uniform Resource Identifiers*: Resources are identified with strings called URIs defined by [RFC 3986](http://www.apps.ietf.org/rfc/rfc3986.html) and clarified by [RFC 3305](http://www.apps.ietf.org/rfc/rfc3305.html). These strings should obey certain properties: they should be immutable, should not be aliased by the same owner, and should be opaque.
3.  *Representations*: A representation of a resource is the expression in a transferable document, that is, a sequence of bytes plus metadata defined by the media type, of the current or intended state of a resource. When a resource is accessed by HTTP, the resource itself is not transferred across the network, but rather a representation of it is. A representation manifests some view of the state of the resource, at a particular point in time. A resource may have multiple representations, and the set of representations may change over time. When multiple representations are available, the term variant is used (by [RFC 2703](http://www.apps.ietf.org/rfc/rfc2703.html)) to refer to one of the several possible representations.
4.  *Uniform Interface*: RESTful architectures require all interactions with resources to use a fixed set of operations that are generic rather than resource specific. HTTP defines a uniform set of operations through it's standardized set of "verbs": GET, POST, PUT, DELETE, HEAD, OPTIONS, and TRACE. Applying one of these operations to a resource will have the same semantics as the same method applied to any resource.
5.  *Client-Server Constraint*: All interactions in RESTful systems obey the client-server constraint. A server component, offering a set of services, listens for requests upon those services. A client component, desiring that a service be performed, sends a request to the server via a connector. The server either rejects or performs the request and sends a response back to the client.
6.  *Manipulation of Resources through Representations*: Clients perform actions on a server resource by using a retrieved representation to capture the current or intended state of that resource and transferring such representation between components.
7.  *Hypermedia as the Engine of Application State* ([HATEOAS](how-to-implement-hateoas.md)): A REST API should be entered with no prior knowledge beyond the initial URI (bookmark) and set of standardized media types that are appropriate for the intended audience (i.e., expected to be understood by any client that might use the API). From that point on, all application state transitions must be driven by client selection of server-provided choices that are present in the received representations or implied by the user’s manipulation of those representations.
8.  *Statelessness Constraint*: Each request from client to server must contain all of the information necessary to understand the request, and cannot take advantage of any stored context on the server. Session state is therefore kept entirely on the client. Servers track resource state only.
9.  *Cache-ability Constraint*: The data within a response to a request is implicitly or explicitly labeled as cacheable or non-cacheable. If a response is cacheable, then a client cache is given the right to reuse that response data for later, equivalent requests.
10. *Layered System Constraint*: A client cannot ordinarily tell whether it is connected directly to the end server, or to an intermediary along the way. Intermediary servers may improve system scalability by enabling load balancing and by providing shared caches. They may also enforce security policies.
11. *Self Descriptive Messages*: REST enables intermediate processing within a layered system by constraining messages to be self-descriptive: interaction is stateless between requests, standard methods and media types are used to indicate semantics and exchange information, and responses explicitly indicate cache-ability.
12. *Code on Demand*: REST allows client functionality to be extended by downloading and executing code as a resource. For example, browser clients often run javascript supplied by servers.

##Event Driven Architecture
Here are the benefits we have seen with Event Driven Architecture:
1. Pattern promotes loose coupling.
2. Enables more resilient design, application dependencies are minimized.
3. Event emitters dont need to know about their consumers.
4. Set of consumers can change over time without modifying event emitter.
5. Broadcast a single event to multiple consumers.
6. Enables [Event Sourcing Pattern](http://martinfowler.com/eaaDev/EventSourcing.html)

At Rackspace we use [Cloud Feeds](https://www.rackspace.com/knowledge_center/article/cloud-feeds-overview) as our realization of event driven architecture. We choose to use Cloud Feeds because it offers a RESTful API, which confers the advantages described above.

# Good and Bad Practices

## Good Practices

1.  A service must exchange JSON that complies with a published schema.
2.  A service may provide alternate representations (such as XML or SVG) of a resource. The data models of such representations should be consistent with the JSON Schema.
3.  Define the integration entry points for your service using RAML.
4.  Service compositions should be expressed as another service capability. Use a separate service if necessary when the composition scope isn't a good fit for an existing service. If reuse is likely and it does fit, use an existing service.
5.  Use the uniform interface defined by HTTP correctly. See the cookbook entry specifically on this topic.

## Bad Practices

1.  Don't alias resources by giving them multiple URIs. Eliminate the redundancy or use redirects instead.
2.  Don't expect clients to parse or construct URIs. URIs should be opaque. Instead, use WADL to define URI entry points, and use HATEOAS to give them the URIs they need after that.
3.  Don't use SOAP without getting governance approval (which will be heavily scrutinized).
4.  Do not use direct database access as an integration strategy when information crosses service boundaries.

# Unresolved Issues

None.

# Frequently Asked Questions

The following are frequently asked questions whose answers are worth collecting in one place.

## How do we approach event based integration?

Use ATOM for basic needs. Sometimes use cases will require advanced event based capabilities and then alternatives may be used, if justified. These might include messaging brokers such as JMS or AMQP or HTTP based techniques such as PuSH (pubsubhubbub).

## How do I integrate with vendor tools, especially when they don't support REST?

Whether they support REST or not, it is usually a good idea to encapsulate access to vendor tools within a service. The standardization on REST applies when clients integrate to services that they are not part of. Internally to a service, solutions will deal with integrations in a variety of less constrained ways. What is important is that integration to vendor products (or legacy internal systems) by other services, UIs, and external clients use vendor agnostic interactions, to avoid coupling.

## How do I do distributed transactions between services?

You don't. ACID transactions require conversational state that is simply not possible using REST (and arguably is suspect given SOA's statelessness requirement as well). Instead, push transactions inside of one service, or refactor the solution so that it doesn't need distributed transactions. This can be done by using compensating operations generally, or sometimes using reliable messaging (which can be achieved using idempotent delivery). Often the correct RESTful solution will create resources using the "provisional-final" pattern and manage them as application state. EG: create a quote resource that becomes an order resource.

# <a name="references"></a> References

1.  [Rackspace Service Oriented Architecture Cookbooks](cookbooks.md)
2.  [Rackspace SOA Guidelines](https://one.rackspace.com/download/attachments/49480223/RackspaceSOAGuidelines_v2.0_DRAFT1.pdf?version=1&modificationDate=1390945322000&api=v2)
3.  [SOA: Principles of Service Design, Thomas Erl](http://search.safaribooksonline.com/9780132344821).
4.  [SOA Design Patterns, Thomas Erl](http://my.safaribooksonline.com/9780136135166).
5.  [RESTful Web Services Cookbook, Subbu Allamaraju](http://search.safaribooksonline.com/9780596809140).
6.  [Architectural Styles and the Design of Network-based Software Architectures](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm), Roy Fielding. Fielding's dissertation defines REST as an architectural style.
7.  [Hypermedia as the Engine of Application State (HATEOAS)](how-to-implement-hateoas.md).
8.  [Correct Use of HTTP Verbs](correct-use-of-http-verbs.md).

# Contributors

* Bryan Taylor
* Mark Morga
* Ben Truitt
