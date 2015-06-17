# When to use an application integration technology that is not RESTful

# Problem

You want to know whether it is a good idea to use an application integration technology that is not RESTful (e.g. Apache Thrift).

# Solution

## In General, use REST

In general, follow the guidance found in the cookbook titled [Approach to Integration](approach-to-integration.md):

> Rackspace has adopted a RESTful Service Oriented Architecture (SOA). All integration API functionality should be provided by services that follow the RESTful best practices described by entries in the Rackspace Service Oriented Architecture Cookbooks, which instruct developers on how to create services that follow the Rackspace SOA Guidelines.
>
> ...
>
> The default architectural style for Rackspace services is HTTP based Representational State Transfer (REST) consistent with Roy Fielding's dissertation and various good descriptions of it on the web and in books such as the RESTful Web Services Cookbook by Subbu Allamaraju.

## Exceptions to the RESTful Rule

There possible exceptions to the general rule that APIs should be RESTful.  If your situation seems to be described by one of the below exceptions, it may be reasonable to use a non-RESTful application integration technology.

### Integrating application components within a service boundary

Recall the following definitions found in the cookbook titled [Approach to Integration](approach-to-integration.md):

> A *service* is an independent unit of software that substantially adheres to the eight service-oriented design principles and provides service capabilities (see below) that are applicable to a well-defined, distinct functional context.
>
> A *service capability* is a function exposed through a service contract.

One of the core tenets of SOA is that the services have a clearly defined boundary. Services explicitly choose to expose certain (business) functions outside the boundary.  In the Rackspace SOA, these functions are exposed as service capabilities in a RESTful service contract.

Another tenet of SOA is that a service has design-time autonomy regarding the implementation details of what happens *within* its boundary.  An implementation of a service capability exposed in a RESTful service contract may happen to involve an application integration that is abstracted away by the service contract.  That is, the service is a "black box" to clients, which are unaware that the implementation of the black box happens to involve an application integration under the covers.  Application integrations that are within a service boundary may use a non-RESTful application integration technology.

### High performance requirements that preclude the use of RESTful APIs

If your service must provide high-performance application integration options, and if you have exhausted RESTful integration following best-practices described in the Rackspace Service-Oriented Architecture Cookbooks such as [caching](using-caching-in-a-restful-service-implementation.md), then you may have a good reason to expose a non-RESTful interface to your service that allows you to meet your performance requirements.

### Stream-oriented application integration

Since the creation of the Rackspace SOA Guidelines, Rackspace architects have found a recurring need to support a new class of integration, which we will refer to here as "streaming" services.  This class of service is defined by the following characteristics:

1.  High throughput data and/or low data latency requirements
2.  Service consumers are generally internal, or at least limited to restricted to groups that "buy in" to the alternative service construction
3.  Willingness to accept tool and/or language constraints for processing horsepower (examples: "use Kafka", "use Storm", "use Hadoop")
4.  Willingness to accept more centralized metadata governance in exchange for processing horsepower

If your situation seems to be described by these characteristics, then you may have a good reason to expose a non-RESTful interface for your service that is instead stream-oriented.

## Discussion

### Why REST should be the Default Choice for Application Integration

We'll begin the discussion by again quoting from the cookbook titled [Approach to Integration](approach-to-integration.md):

> A collaboration of architecture teams collectively representing all of Rackspace have agreed to use RESTful service oriented architectures for integration. This applies to all APIs provided by Rackspace systems where consumers are internal or external. The foundational document for the Rackspace approach is the Rackspace SOA Guidelines.

The problem statement found in the Rackspace SOA Guidelines states, in part:

> In the absence of an overall technical architecture, Rackspace products and services have been built as "islands"—monolithic, tightly coupled systems that are not reusable. As a result, we’ve experienced rising technical debt, increasing work required to simply integrate new systems into the Rackspace product portfolio, and unacceptable compromises between organizational agility and system stability.

Service orientation is identified in that document as an architectural approach that would help to remedy these and other issues.  Of course, service orientation can be achieved using a variety of protocols or styles, and REST is just one possible style. The critical question that this cookbook aims to answer is whether it is ever okay to use an application integration style / technology that is not RESTful.

To understand the reasoning that led to the solution stated above in this cookbook, it is helpful to examine the eight basic principles of SOA described in the Rackspace SOA Guidelines.

1.  *Standardized Service Contract*: The Standardized Service Contract design principle simply states that all service contracts within a given service inventory should follow the same set of design standards and guidelines.
2.  *Loose Coupling:* Coupling should occur only as consumer-to-contract and contract-to-logic. Other forms of coupling should be avoided.
3.  *Service Abstraction:* Contracts should expose the minimum information necessary and should hide implementation details behind the contract.
4.  *Service Reuse-ability:* Service capabilities should be reused. There should be a single global authoritative capability for any functionality and any service capability that provides such functionality should be traceable to the authority.
5.  *Service Autonomy:* Services should have a high degree of design time control over their runtime environment, to isolate their capabilities from shared infrastructure components.
6.  *Service Statelessness:* Services should minimize the need to manage in-memory state.
7.  *Service Discoverability:* Services should be described with metadata that allows information about their properties to be discovered.
8.  *Service Composability:* Services capabilities should be reusable as compositions within the service layer.

Of these principles, standardized service contracts is the most fundamental. This principle maximizes intrinsic interoperability, reduces the need for data transformations, and makes it easier for consumers to comprehend services across the inventory at design time.  At Rackspace, we've chosen to adhere to this principle by representing our services contracts using the HTTP standard's uniform interface and various artifacts such as XML Schema, WADL, and standardized link relations.  In the RESTful service model, which will remain the norm, we offer general purpose "application" integration and want to impose as few couplings as possible to users, so that they can discover and use our solutions with a wide a set of tools (of their choice) as possible.

The other principles are enabled by following the RESTful architectural style found in Roy Fielding's dissertation is defined using a set of constraints (these constraints are listed in the cookbook titled [Approach to Integration](approach-to-integration.md)).  These constraints align to the SOA principles we seek to follow, while also adding desirable properties (e.g. cache-ability).

Any deviation from the standard use of REST at Rackspace risks sending us back into the problem statement quoted above, that "Rackspace products and services have been built as "islands"—monolithic, tightly coupled systems that are not reusable."  While it is possible to avoid this situation with thoughtful use of a non-RESTful application integration technology, we feel that there is enough value in standardizing on REST that exceptions should be rare and well justified.

### Thoughtful Exceptions to the RESTful Rule

Of course alternatives to REST have various attractive properties such as improved performance, improved ease of integration, etc.  Some specific technologies may even offer benefits such as auto-generated service documentation, etc.  However, we must weigh these benefits against the cost of not aligning toward our SOA goal of increased intrinsic interoperability.

Intrinsic interoperability is not affected by the use of a non-RESTful integration only if that integration occurs within a service boundary.  Therefore, service implementors are free to use non-RESTful integrations within a service boundary if they so choose.

Of course it won't matter if a service meets the goal of intrinsic interoperability when doing so comes at the cost of not meeting demanding performance objectives.  There may be cases where a service exposed RESTfully simply cannot meet performance objectives.  The RESTful architectural style makes this case rare, thanks to the *statelessness constraint* that enables horizontal scalability, the *cache-ability constraint* that enables offloading of read-heavy capabilities, and the *layered system constraint* that permits intermediary servers to improve system scalability by enabling load balancing and by providing shared caches.

The layered system constraint also offers another important benefit: the ability to reuse intermediaries to support various cross-cutting concerns.  The Rackspace SOA makes extensive use of Repose as an HTTP intermediary that handles various concerns related to security, rate-limiting, etc.  Keep in mind that if you opt to use a non-RESTful integration style, you may lose the ability to re-use some or all of these cross-cutting features in your own service.

That said, if you find that you simply cannot meet your performance objectives via a RESTful integration, then offering a non-RESTful service contract is acceptable.  We suggest that you consider offering a [concurrent contract](http://soapatterns.org/design_patterns/concurrent_contracts) in this case, where consumers that demand top performance are able to use your non-RESTful technology, while less demanding consumers can take advantage of the intrinsic interoperability of a RESTful contract that exposes similar service capabilities.

The streaming services model is not trying to appeal to the general API user. It's not really targeting "application" integration, so much as analytics and stream processing. Because stream processing is best supported by libraries that handle concerns such as exactly-once read semantics, concerns about technology coupling become moot with stream-oriented integrations (i.e. you'll probably plan on coupling to technology / protocol anyway, by virtue of needing streaming).  Therefore, stream-oriented integrations need not be RESTful.

# Good and Bad Practices

## Good Practices

1.  Default to using RESTful services
2.  Hide non-RESTful application integrations behind a RESTful service contract that abstracts away this implementation detail from clients
3.  Before adopting a non-RESTful application integration style / technology because of performance reasons, first examine options for improving performance RESTfully such as caching, leveraging service intermediaries, etc.
4.  When non-RESTful service must be offered to meet performance objectives, offer a concurrent contract that is RESTful for less demanding consumers so that they can benefit from intrinsic interoperability

## Bad Practices

1.  Don't use non-RESTful application integration styles / technologies without getting governance approval (which will be heavily scrutinized).

# Unresolved Issues

None.

# Frequently Asked Questions

The following are frequently asked questions whose answers are worth collecting in one place.

*Who should I talk to after reading this cookbook if I still think that I want to use a non-RESTful application integration style or technology?*

If your team is part of the Global Enterprise Technologies (GET) department at Rackspace, please seek input from the Enterprise Architecture Team.

# References

1.  [Approach to Integration](approach-to-integration.md)
2.  [Rackspace Service-Oriented Architecture Cookbooks](cookbooks.md)
3.  [Using Caching in a RESTful Service Implementation](using-caching-in-a-restful-service-implementation.md)
4.  Rackspace SOA Guidelines
5.  [Concurrent Contract SOA Design Pattern](http://soapatterns.org/design_patterns/concurrent_contracts)

# Contributors

Ben Truitt
