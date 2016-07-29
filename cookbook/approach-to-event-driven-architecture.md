---
layout: recipe
title: "Approach to Event-Driven Architecture"
tags: event-driven-architecture
authors:
    - name: Ben Truitt
    - title: Principal Architect
---

# Approach to Event-Driven Architecture

Early draft - not official

This is a first draft, and has not been reviewed or accepted by the Enterprise Architecture team. It is not yet official or recommended.

# Problem

You want to use event-driven architecture to produce event notifications in response to state change so that event consumers can react to events in a loosely coupled manner. You want to know how to deal with considerations like selecting the right message channel.

# Solution

Publish events from your application to an event channel so that consumers can integrate with your application in a loosely coupled manner to react to events that indicate state change for the data elements that your application is authoritative for.

In general, use the [Atom Publishing Protocol](https://tools.ietf.org/html/rfc5023) to publish events represented using the [Atom Syndication Format](https://tools.ietf.org/html/rfc4287) to an atom feed. Consume the atom entries from the atom feed.

In rare cases, it is appropriate to use a different message channel technology.

## Discussion

## Event-Driven Architecture
Event-Driven Architecture is an architectural pattern that is based on publishing event notifications in response to a change in state (i.e. an event), and reacting to those events in a loosely coupled manner.

### Fundamentals of event-driven architecture

#### Terminology
An event-driven architecture includes:

1. **Events** - A significant change in state. Events do not travel, they just happen. Note that 'event notifications' are often referred to simply as events as a form of shorthand, which can lead to confusion.
2. **Event notifications** - A message that is sent (typically asyncronously) to registered event consumers over an event channel to inform them of the event that happened.
3. **Event producers** - Publish event messages to event channels when the state of a resource changes. An event producer does not know about event consumers (i.e. whether they exist, how many there are, how events are or are not used by event consumers).
4. **Event consumers** - React to events. Event consumers do not know about one another.
5. **Event channels** - Conduits through which event notifications travel from event producers to event consumers. Examples include JMS topics, atom feeds, AWS Kinesis streams, etc.

#### Principles
Event-driven architecture is fundamentally different than request-driven service oriented architecture. Here are some of the fundamental aspects of this architectural style:

- Asynchronous “Push”-Based Messaging Pattern
Instead of the traditional “pull”-based synchronous request/response model
   - event-driven architecture implements the [Publish-Subscribe Channel](http://www.enterpriseintegrationpatterns.com/patterns/messaging/PublishSubscribeChannel.html) messaging pattern to “push” notifications out to interested listeners, in a “fire-and-forget” (does not block and wait for a synchronous response), uni-directional, asynchronous interaction.
- Autonomous Messages
   - Each message contains just enough information to describe a change in state such that event consumers can take appropriate action.
   - Each event notification stands on its own and does not require additional context such as in-memory session state of the event consumers.
- Higher decoupling of distributed systems      
   - Reduced contract coupling (see below for more discussion)
   - Event producers do not need to know about event consumers (see below for more discussion)             
   - Functional processes in the sender system, where the business event took place, do not depend on the availability and completion of remote processes in downstream distributed systems.
   - Unlike in a request-driven architecture, event producers do not depend on the availability and completion of remote functions in event consumer systems in order to successfully complete their own processing.
   - In synchronous request-driven architectures, connected systems are often required to meet the service-level requirements (i.e., availability and throughput) and scalability / elasticity of the system that has the highest transaction volume. But in asynchronous event-driven architectures, the SLA concerns of one system do not dictate the concerns of dependent systems, providing these teams with more design-time autonomy over things like infrastructure design.
   - This also means that changes to each connected system can be deployed more independently (and thus more frequently) with minimal impact to other systems, due to the overall reduced dependencies.

#### Benefits
Benefits of Event-Driven Architecture include:

1. Improves flexibility and reliability
   - Enables more resilient design, since event notifications are asyncronous, meaning that a failure in an event consumer does not affect the event producer's availability.
   - Event emitters don't need to know about their consumers so application dependencies are minimized. Changes to an event consumer do not affect an event producer.
   - Set of consumers can change over time without modifying event emitter. New consumers can be added, and existing consumers removed without affecting the event producer.
2. Allows an event producer to broadcast a single event to multiple event consumers.
3. Enables effective data integration
   - Event-driven architecture is inherently based on data about updates to business state. No central data coordination is required - interested consumers can simply subscribe to data feeds to obtain data updates they care about.
   - Ingestion of data into a data warehouse is simpler (see below for discussion).
4. Reduced information latency
   - Event notifications are delivered in near real-time.
   - Operational systems can have the most accurate, current view of the state of business.
5. Improved scalability
   - Asynchronous systems tend to be more scalable than synchronous systems.
6. Improved business agility
   - Allows different functional domains to interact while preserving a high degrees of local autonomy.
   - Business concepts and activities are modeled in simpler terms with responsibilities appropriately delegated to corresponding owners.
   - Reduced dependencies allow technical and functional changes to be planned and released more independently and frequently, achieving a higher level of IT agility and, hence, business agility.

### Event Processing Styles

An event-driven architecture includes at least one of these styles, or may include any combination of these styles:

1. *Simple event processing*: Concerns events that are directly related to specific, measurable changes of state. In simple event processing, a notable event happens which initiates downstream action(s). An example of a simple event is a new account being created.
2. *Event stream processing*: In event stream processing, notable event notifications are mixed in with ordinary event notifications in the event channel. Event consumers filter the event stream to identify and react to the notable events. An example of event stream processing is a temperature sensor in a data center hall. Most of the readings will be ordinary (i.e. within threshold), but some events will be notable (i.e. because they exceed threshold boundaries). This style allows the threshold business logic to live in the consumer rather than the producer.
3. *Complex event processing*: Allows a pattern of events to indicate that something has happened. The events (notable or ordinary) may cross event types and occur over a long period of time. The event correlation may be causal, temporal, or spatial. An example of complex event processing is identifying a security breach from a stream of events that, taken on their own may seem innocuous, but when considered as a whole, indicate a system compromise. This style allows complicated business logic to be implemented in a source that considers many inputs without needing to embed any business logic or dependencies within those individual input systems.

### Event Taxonomy
Event-driven architecture benefits from a clearly defined and standardized event taxonomy so that the events are meaningful and can be easily understood.

### Event notification contracts
As discussed above, event-driven architecture limits coupling between event producers and event consumers. This is desirable because it improves design-time autonomy for the owners of these systems, as well as run-time autonomy for the operators of the systems.

Other than the details of connecting to the event channel for sending and getting event notifications, this asynchronous message-based interaction model does not need to be as concerned with some aspects of request-response models such as input parameters, service interface definitions such as RAML, and fine-grained security.

One area in which coupling cannot be avoided is the representation of the event details in the event notification. We'll refer to this as the event notification contract.  

As explained in the [Rackspace SOA Guidelines](https://one.rackspace.com/download/attachments/49480223/RackspaceSOAGuidelines_v2.0_DRAFT1.pdf?version=1&modificationDate=1390945322000&api=v2), *Consumer-to-Contract* coupling is a desirable form of coupling that occurs when consumers use services through published, standardized contracts.

It is important that event producers publish a contract for the representation of event notifications that will appear on the event channel. This may be an XML schema or JSON schema, for example.

### Pipes and filters

Event-driven architecture permits event consumers to also be event producers (typically on a different event channel), which means that it is possible to chain together event processors as described by the [pipes and filters enterprise integration pattern](http://www.enterpriseintegrationpatterns.com/patterns/messaging/PipesAndFilters.html).

This may be useful for architects who wish to decompose a task that performs complex processing into a series of discrete elements that can be reused. This pattern can improve performance, scalability, and reusability by allowing task elements that perform the processing to be deployed and scaled independently.

### Event sourcing
Event-driven architecture enables the [Event Sourcing](http://martinfowler.com/eaaDev/EventSourcing.html) pattern. Event Sourcing ensures that all changes to application state are stored as a sequence of events. Not only can we query these events, we can also use the event log to reconstruct past states or to rebuild state to recover from a fault in the primary data store.

Event sourcing can also be combined with the [Command and Query Responsibility Segregation (CQRS) Pattern](http://martinfowler.com/bliki/CQRS.html) to address non-functional requirements like performance, scalability, security, and flexibility.

Event sourcing is a large topic, and we won't cover it in detail in this entry. The interested reader is referred to [Martin Fowler's Blog Post on the Event Sourcing Pattern](http://martinfowler.com/eaaDev/EventSourcing.html), and to the book [Implementing Domain Driven Design](https://www.amazon.com/Implementing-Domain-Driven-Design-Vaughn-Vernon/dp/0321834577) by Vaughn Vernon.

### Ingesting events into the data lake

TODO: take some of this material from Natasha:

    For the purpose of this discussion I would separate Data Lake component from Data Warehouse. Data Lake ingests data without any transformation. As such, when sourcing from a message bus that contains data and metadata the ingestion process can be fully automated. The automation handles:
    1.      1. Creation of new objects in data lake:
    a.      Ingestion process reads message bus metadata and recognize that there is a new object on the message bus
    b.      Ingestion process creates location in Data Lake where the new object will be loaded
    c.      Ingestion process reads metadata of minimally structured message and discovers message structure
    d.      Indigestion process creates new object metadata information in Data Lake
    e.      Ingestion process start loading data
    2.      2. Change in message structure
    a.      Ingestion process reads metadata for every message consumed and compares it with the Data Lake metadata
    b.      Ingestion process finds new field in message metadata and modifies Data Lake accordingly
    c.      Ingestion process finds missing field in the message and structure the record accordingly

    The Data Lake ingestion process automation will enable us to bring data into analytical side of the house quickly and reliably and with no human intervention. New objects and attributes will be available in the Data Lake as soon as they appear on the message bus. This will enable analysts to consume new objects and attributes immediately after we roll out new applications or upgrades.

     Data Warehouse ETL processes starts from Data Lake – that is when we talk specifics. When new objects appear in the Data Lake and none asks for them on Data Warehouse side no ETL process needs to change. Otherwise, there is a development effort where we bring new entities and attributes into Data Warehouse – the place where we maintain structures and data that answer known questions.

    Minimally structured message would be optimal feed for Data Lake automated ingestion, therefore the process should consume from the interface rather than data store.

    When we talk about Data Lake ingestion automation there are some features that message bus should possess:
    1.      1.Message bus metadata should be exposed;
    2.      2. Message bus should support plug-ins to implement custom features if necessary;
    3.      3.Message bus should start reliably serving data from existing applications (over 80) as soon as possible, without modifying applications. Modifying applications to emit data to message bus will have to be planned and implemented accordingly;
    4.      4.Message bus should have good performance. The automate Data Lake ingestion process does not have high performance requirements. However, this technology should enable streaming analytic and that is where performance becomes significant. I think, if we are selling Fanatical Support, the streaming analytic is a method that we can use to wow our customers;
    5.      5.Message bus technology has to be reliable and supportable.


Since event notifications have data that may be of interest for reporting purposes, especially when the event sourcing pattern is used (see above), we should consider how to get event notification data into the data warehouse.

One obvious way for this to happen is simply to create an ETL process within the data warehouse that acts as a consumer of event notifications on the appropriate event channel, and saves the event payload data into an appropriate data store.

![ETL job per event channel](../img/cookbook/event-driven-architecture/ingesting-events-into-data-warehouse-option-1.png)

Another possibility is to have an integration from the event channel to the data warehouse directly. In this scenario, the event notifications are published to the event channel by the event producer, and the event channel persists the event notification into a data store that the data warehouse is configured to integrate with.  This approach scales better as new event channels are added, since no additional ETL work is required by the data warehouse team. In fact, this is one of the primary considerations that leads us in a later section of this document to recommend Cloud Feeds as the preferred event channel.

![ETL job per event channel](../img/cookbook/event-driven-architecture/ingesting-events-into-data-warehouse-option-2.png)

# Good and Bad Practices

## Good Practices

1. Publish the event notification representation contract so that consumers can couple to this well-defined contract.
2. Follow the guidance found in [Selecting an Event Channel for Event-Driven Integration](../service-design/selecting-an-event-channel.md) to select the best technology to use to realize an event chanel in your application-to-application integration architecture.

## Bad Practices

None

# Unresolved Issues

None

# Frequently Asked Questions

The following are frequently asked questions whose answers are worth collecting in one place.

*When should I use events, and when should I prefer synchronous API requests?*

Synchronous requests are appropriate when the event source (or client) depends on the receiver (or server) to perform and complete a function as a component of its own process execution.  This is most often found in the case of reuse of existing business logic / application function.

*How much data should be communicated via event notifications?*
*What should I include in my event notification payload?*
*How is this different from database replication?*

The purpose of an event notification is to inform event consumers about  changes in the state of a resource. Therefore, only data that is meaningful to the event that transpired should be included in the event notification payload.

A good rule of thumb is to include just enough data to help event receivers decide how to react to the event. If event consumers require more data or functional interaction to complete their processes they can use alternative integration interfaces such as synchronous REST API calls to accomplish that part of the interaction.

*How can I know whether an event is consumed by a particular consumer?*

TODO

*Are there any patterns I should be aware of that fit into this messaging model?*

TODO: [dead letter channel](http://www.enterpriseintegrationpatterns.com/patterns/messaging/DeadLetterChannel.html)
TODO: [Asynchronous queuing](http://soapatterns.org/design_patterns/asynchronous_queuing)

*When is it better to use autonomous messages, and when is it better to use batch messages?*

TODO - address this note from Yogi

    There is a new model that is getting popular.
    There are batch modes of operations which orchestrate the batches in an asynchronous fashion.
    The APIs usually allow larger batches to be submitted, asynchronously process them and also provide feedback on the batches.
    The Biggest benefit is in terms of feedback to the actual API invoker in terms of number of events that got proceeded, the reasons they got failed etc.
    Many of the SAAS providers support these kind of Batch Modes, Ex: Sales Force Bulk API  Zuora Usage API
    How the batches are handled is internal to the Service Provider.
    There are less system hand offs and less chattiness between the application as this approach encourages direct integration and clear visibility around consumption.

*Are an event channel and a message bus the same thing? If not, what are the differences?*

TODO

# References
1. [Atom Publishing Protocol](https://tools.ietf.org/html/rfc5023)
2. [Atom Syndication Format](https://tools.ietf.org/html/rfc4287)
3. [Pipes and Filters Enterprise Integration Pattern](http://www.enterpriseintegrationpatterns.com/patterns/messaging/PipesAndFilters.html)
4. Microsoft Developer Network - [Pipes and Filters Pattern](https://msdn.microsoft.com/en-us/library/dn568100.aspx)
5. [Event Sourcing](http://martinfowler.com/eaaDev/EventSourcing.html)
6. Vernon, Vaughn. [Implementing Domain Driven Design](https://www.amazon.com/Implementing-Domain-Driven-Design-Vaughn-Vernon/dp/0321834577)
7. [Command and Query Responsibility Segregation (CQRS) Pattern](http://martinfowler.com/bliki/CQRS.html)
8. Microsoft Developer Network - [Command and Query Responsibility Segregation (CQRS) Pattern](http://martinfowler.com/bliki/CQRS.html)https://msdn.microsoft.com/en-us/library/dn568103.aspx)
9. Mircosoft Developer Network. The Architecture Journal. [Using Events in Highly Distributed Architectures](https://msdn.microsoft.com/en-us/library/dd129913.aspx)
10. [Selecting an Event Channel for Event-Driven Integration](../service-design/selecting-an-event-channel.md)

# Contributors

Ben Truitt
