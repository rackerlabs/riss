---
layout: decision
title: "Selecting an Event Channel for Event-Driven Integration"
tags: event-driven-architecture
authors:
    - name: Ben Truitt
    - title: Principal Architect
---

# Selecting an Event Channel for Event-Driven Integration

Early draft - not official

This is a first draft, and has not been reviewed or accepted by the Enterprise Architecture team. It is not yet official or recommended.

## Decision point

There are many technologies that can realize the event channel abstraction in your application integration architecture. Examples include JMS topics, atom feeds, AWS Kinesis streams, etc.

Choosing the right technology for the conduit through which event notifications travel from event producers to event consumers can have a significant impact on the non-functional requirements you must solve for, such as scalability, monitor-ability, reusability, etc.

## Decision Context

For additional context, please refer to the Rackspace [Approach to Event-Driven Architecture](../cookbook/approach-to-event-driven-architecture.md).

Contextually, the selected integration technology should ideally conform to the [Rackspace SOA Guidelines](https://one.rackspace.com/download/attachments/49480223/RackspaceSOAGuidelines_v2.0_DRAFT1.pdf?version=1&modificationDate=1390945322000&api=v2), *Consumer-to-Contract*, and should comport with our [Approach to (service-oriented) Integration](approach-to-integration.md).

Additionally, relevant legal, compliance and policy constraints apply to the decision, such as data classification and data storage policies.

While not a constraint, an important consideration to consider is that many application to application integrations have already been implemented using the Rackspace Cloud Feeds technology.

## Evaluation Criteria

Evaluation criteria will vary between different business problems / architectural contexts. Here is a list of evaluation criteria that were considered for this decision point:

* Shall enable buffering of messages so that spikes in message volume do not overwhelm consumers.
* Shall guarantee that all messages produced by a particular producer from a single thread of execution are always delivered to subscribed consumers in a first-in-first-out (FIFO) order by all consumers.
* Shall have no more than 10 seconds of latency from the time a message is produced to the time it is made available to all subscribing consumers.
* Shall be available 99.999% of the time for all times other than scheduled maintenance windows that are communicated ahead of time.
* Shall scale to support at least 10 producers creating messages at the same time on a particular message channel (a/k/a feed, stream, topic, etc.)
* Shall scale to support at least 20 consumers consuming messages at the same time from a particular message channel (a/k/a feed, stream, topic, etc.)
* Shall be able to accept from a particular producer more than 100,000 messages within a 5 minute period on a particular message channel (a/k/a feed, stream, topic, etc.)
* Shall be able to accept from a set of producers more than 50,000 messages within a 5 minute period on a particular message channel (a/k/a feed, stream, topic, etc.)
* Shall be able to deliver to all subscribers for a given feed / topic / stream more than 50,000 messages within a 5 minute period on a particular message channel (a/k/a feed, stream, topic, etc.)
* Shall be able to accept messages of up to 20KB is size.
* Shall shall not require downtime longer then 5 minutes for software upgrades, or regular maintenance.
* Shall provide documentation such as administrative guides, user guides, etc.
* Shall provide a REST API for producers
* Shall provide a Public REST API for producers
* Shall provide a REST API for consumers
* Shall have a operational support team 24x7x365 for troubleshooting
* Shall provide a metrics dashboard to be used by operational support to view throughput, errors, latency
* Shall make messages available to consumers for at least 24 hours from the time a message is produced.
* Shall be resilient to component failure. The failure of on component (a/k/a node / broker / worker / etc) in the system must not reduce availability or lose data.
* Shall support encryption of data in transit
* Shall support encryption of data at rest
* Must be cost-competitive for the volume of data expected to be used at Rackspace for the foreseeable future, and for the number of individual message channels we expect to scale to.
* Should expose a RESTful interface
* Should support the [Atom Publishing Protocol](https://tools.ietf.org/html/rfc5023)
* Should accept event notifications represented using the [Atom Syndication Format](https://tools.ietf.org/html/rfc4287)
* Should offer the ability for clients to publish or consume events in either XML or JSON, and adapt from one format to another transparently
* Should optionally validate event notifications against a per-channel schema to ensure that producers only produce event notifications that conform to the published representation contract

## Alternatives

The following technologies were considered as part of objectively evaluating this decision point:

* Rackspace [Cloud Feeds](https://support.rackspace.com/how-to/cloud-feeds-overview/)
* AWS Kinesis
* AWS SQS
* Apache Kafka
* Oracle Fusion Middleware
* Apache ActiveMQ
* Azure Event Hub
* Flume
* RabbitMQ
* ZeroMQ
* OpenMP
* HornetQ
* Kestrel
* MongoDB

## Future Developments

We are not aware of any future products that aren't currently available.

## Decision Tools

In general, we prefer the use of [Cloud Feeds](https://support.rackspace.com/how-to/cloud-feeds-overview/) as an event channel.

### When not to use Cloud Feeds

1. When your integration requires faster delivery of events than is possible with Cloud Feeds.
   - At the time of this writing, the current implementation of Cloud Feeds includes a 10 second delay between event publication and the event becoming available for consumption due to an implementation detail.
   - HTTP, AtomPUB and Atom Syndication are less efficient for messaging than higher performance messaging technologies such as Apache Kafka, JMS, etc.
2. When your event producer does not have an internal IP address.
  - At the time of this writing the public interface Cloud Feeds is read-only. It does not offer a capability for event producers to publish event notifications from outside of our internal network.
3. When the payload size of your event notification exceeds the maximum size supported by Cloud Feeds.
4. When the volume of event notifications exceeds the maximum volume supported by Cloud Feeds.

### Other considerations

1. Cloud Feeds does not currently offer a capability for self-service feed creation. Currently creating a new feed requires developer / operational effort, which may extend your timeline for including a new feed in your application design.

TODO: add decision-making aids to assist the reader in choosing a recommended solution. For example, a flow chart / decision tree.

## Decision Justification

Reasons we prefer Cloud Feeds for realizing an event channel in application-to-application event-driven integration include:

1. Offers a RESTful interface, which conforms to our [Approach to (service-oriented) Integration](approach-to-integration.md).
2. Implements the [Atom Publishing Protocol](https://tools.ietf.org/html/rfc5023), a simple, widely used HTTP-based protocol for publishing web resources.
3. Represents event notifications using the [Atom Syndication Format](https://tools.ietf.org/html/rfc4287), a well-defined, widely used, extensible format for specifying the details about what is being published.
   - Many software libraries are already available for parsing atom entries (e.g. Apache Abdera).
4. Offers the ability for clients to publish or consume events in either XML or JSON, and adapts from one format to another transparently.
5. Optionally validates event notifications against a per-channel schema to ensure that producers only produce event notifications that conform to the published representation contract.
6. Widely used at Rackspace already
   - Once a team has created code to produce to or consume from a feed, it becomes very easy to repeat this on another project.
   - Code reuse is easy for producer and consumer software libraries in various languages
7. Customers may consume events from Cloud Feeds using an existing publicly available API.
8. By standardizing on this event channel, it is easier for the data warehouse to have only one place to gather event notifications from for reporting purposes. As described above, a single ETL job could pull in event notifications that span multiple event channels that are all realized by Cloud Feeds.
9. Along similar lines, business activity monitoring (BAM) and business event management (BEM) solutions can leverage the centrally available event data to offer business level metrics and monitoring.
10. Cloud Feeds is already well integrated with Rackspace security solutions like the Identity Service, Repose-based Role-Based-Access-Control (RBAC), etc.

## Areas of Uncertainty, Disagreement, Tradeoff and/or Compromise

TODO

# Frequently Asked Questions

The following are frequently asked questions whose answers are worth collecting in one place.

*How can I parallelize consumption of events across multiple consumer nodes when using Cloud Feeds?*

TODO

*How should consumers keep track of where they are in the feed?*

TODO

*How can I know whether a message is consumed from a feed by a particular consumer?*

TODO

**

# References

1. [Approach to Event-Driven Architecture](../cookbook/approach-to-event-driven-architecture.md).
2. [Approach to (service-oriented) Integration](approach-to-integration.md)
3. [Rackspace SOA Guidelines](https://one.rackspace.com/download/attachments/49480223/RackspaceSOAGuidelines_v2.0_DRAFT1.pdf?version=1&modificationDate=1390945322000&api=v2)

# Contributors

Ben Truitt
