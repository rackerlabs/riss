# Paging Event Collections

# Problem

You have collection of change events to publish and you need an implementation for paging that follows good RESTful practices.

# Solution

Use either atom or raw collections, with navigation among pages with a bounded size defined in relation to "marks". The service will need to distinguish three types of resources: entry, before, and after, each with distinguishable URIs. Each page is a resource of one of these types whose representations are contiguous subsets of events order by time. The entry resource doesn't depend on any mark and displays the latest events, bounded by the pagesize. The "before" resources contain the events immediately prior to the marked event. The "after" resource contains the events immediately following the marked event.

Each page representation should display newest events first and use semantic linking for page navigation using standard link relations: first, next, previous, etc... . Page order is newest first, whereas event order is event publication time order, so the "next" links on a page navigate to the "before" resource using the original pages oldest event as the mark. Similarly, "previous" links navigate to the "after" resource whose mark is the newest event on the original page. The "first" link goes to the entry resource. Navigation to empty pages is appropriate, and any non-empty page must contain "first", "next", and "previous" links.

# Discussion

Each event collection should be given a name, and accessed by going to the entry resource whose URI includes this name. The connection between the types of page resources, a typical URI implementation structure, the link relations within page representations that use such resources, and the resource meaning is shown in the following table.

*Page Resources for Event Collections*

Resource Type
Resource URI
Link Relation
Meaning
Entry Page
`/{eventCollectionName}`
first
The newest events
Before Page
`/{eventCollectionName}?before={unique-mark-identifier}`
next
Events immediately before the marked event
After Page
`/{eventCollectionName}?after={unique-mark-identifier}`
previous
Events immediately after the marked event

We follow the conventions of AtomPub, so the entry page at the entry URI is a resource of the latest events in descending publication order. The result is that the page order defined by "next" and "previous" links results in a "newest first" page order that is opposite to the event order. Because of the time inversion between pages and events, we use "after" resources for "previous" link relations, and "before" resources for "next" link relations.

# Good and Bad Practices

## Good Practices

1.  Use a fixed bound for the maximum number of events within each page.
2.  Follow AtomPub conventions so that page representations show newest entries first, in publication time order.
3.  Follow AtomPub conventions so that page order moves older in time, so that "next" pages contain older events, and "previous" pages contain younger events.
4.  Differentiate three types of resources: entry, before, and after resources.
5.  There should be a unique entry resource for each collection and has a static URI that is independend of any mark.
6.  Marks should be identified in the URI by a uniquely identifying event attribute.
7.  The entry page should include "previous" link, even though until events arrive it will be empty. Clients can poll this link until it changes to reliably obtain new events in order.

## Bad Practices

1.  Allowing client configuration of pagesize bounds hinders caching.
2.  Never allow any client request to return unbounded data sets. Allowing this is a denial of service vulnerability for both the client and server.
3.  Do not invent different and competing conventions for page entry ordering in paged collections to those of AtomPub.
4.  Do not use stateful paging mechanisms like pageNumber or offset unless they are immutable during concurrent event arrivals.
5.  Do not expect clients to construct or understand the URI structure.

# Unresolved Issues

None.

# Frequently Asked Questions

The following are frequently asked questions whose answers are worth collecting in one place.

*Does this recipe comport with the decision tree within the Atom Feed Paging and Archiving cookbook?*

Yes. Whether we use Atom or not, the paging styles of RFC 5005 are relevant and we follow the paging strategy selection criteria from the Atom Feed Paging and Archiving cookbook. We apply the decision criteria to context of append only collections of immutable events. Following the decision tree within that recipe, we rule out complete archives, and rule out updating older items. Then we arrive at the question "Will clients need access to historic entries and expect pages do not change over time". Clients definitely need access historic entries. Because entries are append only and immutable, the benefits of eventually freezing the entry set on a page are less important, so we answer "no" to fixed page boundaries and arrive at paged collections per RFC 5005. However, the solution of this recipe could be adapted to support a blend of the paging and archived strategy.

*What makes a good mark identifier?*

The mark identifier should be some truly unique attribute of an event. A sequence id or a GUIDs works well. Timestamps might work if they are high resolution. It's critical that given the mark identifying, a single event record is guaranteed to be retrieved. This should be enforced by data store unique constraints. It's also important that the mark identifier allow efficient retrieval of the mark record, such as by using an indexed lookup.

*How should I implement efficient page retrievals using a database?*

Implement an events table. Use application logic or database triggers to populate it. The events table should have a unique key on the mark identifier and an index on fields that define the publication order. The other fields of the table should be sufficent to produce the event payload in representations. A simpifying strategy is to use an \`event\_id\` sequence that is assigned in event publication order.

The follow table illustrates how the server would translate page resource types into SQL statements. Not all databases use LIMIT syntax, but most have some equivalent.

*Resource SQL Examples*

Page Resource Type
SQL
Entry
`SELECT * FROM events ORDER BY event_id DESC LIMIT {pagesize}`
Before
`SELECT * FROM events WHERE event_id < {mark_event_id} ORDER BY event_id DESC LIMIT {pagesize}`
After
`SELECT * FROM events WHERE event_id > {mark_event_id} ORDER BY event_id DESC LIMIT {pagesize}`

Other data models can work. We may have to write queries that first look up the mark record and then retrieve a pagesize of data using a different total ordering. For efficient retrieval, we need efficient unique key retrieval of marks, an index on the columns that define the total ordering, and the ability to limit the records returned by the query.

# References

1.  Atom Feed Paging and Archiving Cookbook Recipe
2.  RFC 5005 - Feed and Archive Paging
3.  RFC 5023 - The Atom Publishing Protocol

# Contributors

Bryan Taylor

Mark Morga
