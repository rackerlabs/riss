# Pagination of Resource Collections

Paginated resources must use the following request parameters to specify a page of resources.

Request Parameter | Description | Type and Range | Defaults
------------------|-------------|----------------|----------
limit | Maximum number of records to return | Positive Integer. Range or acceptable values to be defined by Service. Services should document any limitations on this value including if the valid set of values is defined as a set of values. For example, a valid `limit` can be defined to be one of 25, 50, 75, 100. | Defined by the Service.
marker | Identifier of the last item on the previous page of the list | Type may vary by service (could be an Integer, UUID, or URN). | Null (which results in the first page)

##Example
`http://service.rackspace.com/customers?limit=10&marker=20`

Paginated collections never return itemNotFound (404) faults when the collection is empty — clients should expect an empty collection.

For convenience, collections contain atom "`next`" links and can optionally contain "`previous`" links. The last page in the collection will not contain a "`next`" link.

The following examples show pages in a collection of images.

To get the first page, issue a `GET` request to the following endpoint and set the `limit` parameter to the page size of a single item:

`http://service.rackspace.com/customers?limit=10`

Subsequent links honor the initial page size. A client can follow links to traverse a paginated collection.

## Cache-Ability

Consider the cache-ability of your resource. If too many options are provided, the chance of cache-hits are reduced which will have an impact on the scalability and performance of the Service.

## Survey of existing pagination schemes

These are the existing services that were evaluated to determine common pagination schemes.


Service           | Scheme                              
------------------|-------------------------------------
Cloud Control API | none                                
Autohost API      | none                                
CBS               | none                                
Dbaas             | none                                
Bigdata           | none                                
Support Service   | none                                
Usage API         | none                                
RackConnect       | none
CBU               | none
Customer          | none
Identity          | none
Cloud Feeds       | `limit`, `marker` & `direction`
AutoScale         | `limit` & `marker`
Cloud Queues      | `limit` & `marker`
Neutron           | `limit`, `marker` & `page_reverse`
Cloud DNS| `limit` & `offset` 
Cloud Images |`limit` & `marker`
Billing Service | `limit` & `marker`
Ticket Service (Meander) | none 
Ticket Service (Lefty) | `offset` & `count` 
Lbaas | `limit` & `marker`
Configuration Service | `limit` & `marker`
Offer Service | `limit` & `marker`
Promotion Service | `limit` & `marker`
Orchestration | `limit` & `marker`
Maas | `limit` & `marker`
CDN | `limit` & `marker`
Cloud Files | `limit` , `marker` & `end_marker` 
Cloud Servers | `limit` & `marker`
Checkmate | `limit` & `offset`

##Summary of request parameters commonly in use
Type     |Parameter| Description                                             
---------|---------|---------------------------------------------------------
Start    |marker  | Indicates a specific resource, not subject to context 
Start    |offset  | Indicates a specific element of the list, subject to the context of the query
Start    |page    | Indicates a specific partition of the list, subject to context of the query and page_size
Stop     |count, limit| The number of elements to retrieve 
Stop | page_size, per_page| In conjunction with page, effects the start location and number of elements to retrieve
Stop    |end_marker| A specific resource not to traverse beyond. May be used in conjunction with count or limit
Direction|direction, page_reversal | Modifies the starting point relative to the marker
