# Pagination of Resource Collections

Paginated resources must use the following request parameters to specify a page of resources.

Request Parameter | Description | Type and Range | Defaults
------------------|-------------|----------------|----------
limit | Maximum number of records to return | Positive Integer. Range or acceptable values to be defined by Service. Services should document any limitations on this value including if the valid set of values is defined as a set of values. For example, a valid `limit` can be defined to be one of 25, 50, 75, 100. | Defined by the Service.
marker | Identifier of the last item on the previous page of the list | Type may vary by service (could be an Integer, UUID, or URN). | Null (which results in the first page)

Example: `http://service.rackspace.com/customers?limit=10&marker=20`

## Cache-Ability

Consider the cache-ability of your resource. If too many options are provided, the chance of cache-hits are reduced which will have an impact on the scalability and performance of the Service.

## Survey of existing pagination schemes

These are the existing services that were evaluated to determine common pagination schemes.

Service           | Scheme
------------------|---------
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
AutoScale         | `limit` & `marker`
Cloud Queues      | `limit` & `marker`
Neutron           | `limit`, `marker` & `page_reverse`

## Selected Pagination Scheme

Name     | Type                 | Description
---------|----------------------|------------------------------------
`marker` | String *(Optional)*  | Specifies the name of the last queue received in a previous request, or none to get the first page of results.
`limit`  | Integer *(Optional)* | Specifies the number of queues to return. The default value for the number of queues returned is 10. If you do not specify this parameter, the default number of queues is returned.

## Pagination of requests

To reduce load on the service, list operations return a maximum number of items at a time. To help you navigate the collection, you can set the `limit`, `marker`, and `page_reverse` parameters in the URI.

For example, the following request returns up to 100 pages, starting with the item with ID=1234:

```
?limit=100&marker=1234&page_reverse=False
```

The `marker` parameter is the ID of the last item in the previous list. The `limit` parameter sets the page size. The `page_reverse` parameter sets the page direction. These parameters are optional. If the client requests a limit beyond the maximum limit configured by the deployment, the server returns the number of items equal to the maximum limit.

For convenience, list responses contain `next` links and `previous` links. The last page in the list requested with `page_reverse=False` does not contain a `next` link, and the last page in the list requested with `page_reverse=True` does not contain a `previous` link. The following examples illustrate two pages with three items.

## Cloud DNS - `limit` & `offset`

Pagination is the ability to limit the size of the returned data as well as retrieve a specified subset of a large data set. Pagination has two key concepts: `limit` and `offset`. *Offset* is the starting point for the return data. For example, an `offset` of 50 specifies that the items that are returned should start with item number 51 (since the numbering is one-based) in the collection.

It is important to note that `offset` *must* be a multiple of the `limit` (or zero), otherwise a Bad Request Exception will be thrown. Both `limit` and `offset` are specified via request parameters on the URI. The parameters are named `limit` and `offset` respectively, and both apply only to `GET` calls. If unspecified, they default to `limit=100` and `offset=0`. See the examples that follow.

*Example 3.36. Examples of Limits and Offsets for Paging Calls*

&nbsp; | Example | Explanation
---|---------|------------
1. | `....\domains?limit=50` | returns the first 50 domains, that is: 1 - 50
2. | `....\domains?limit=50&offset=50` | returns the domains 51-100
3. | `....\domains?limit=25&offset=50` | returns the domains 51-75
4. | `....\domains?limit=25` | returns the domains 1-25
5. | `....\domains?limit=25&offset=5` | returns Bad Request Exception; offset must be a multiple of the limit or 0
6. | `....\domains?offset=5` | returns Bad Request Exception; offset must be a multiple of the limit or 0
7. | `....\domains?offset=200` | returns back the 201-300th domains if they exist (default limit of 100 applies)
8. | `....\domains` | returns the current maximum items allowable (currently 100)

## Cloud Images - `limit` & `marker`

Name | Type | Description
-----|------|-------------
`limit` | String *(Optional)* | Requests a specific page size. Expect a response to a limited request to return between zero items and the number specified. The typical pattern for using the `limit` and `marker` parameters is to make an initial limited request and then to use the ID of the last image from the response as the `marker` parameter in a subsequent limited request.
`marker` | String *(Optional)* | Specifies the ID of the last-seen image. The typical pattern for using the `limit` and `marker` parameters is to make an initial limited request and then to use the ID of the last image from the response as the `marker`parameter in a subsequent limited request.

## Servers - `limit` & `marker`

To reduce load on the service, list operations return a maximum number of items at a time. The maximum number of items returned is 1000.

To navigate the collection, you can set the `limit` and `marker` parameters in the URI request. For example:

```
?limit=100&marker=1234
```

The `marker` parameter is the ID of the last item in the previous list. Items are sorted by create time in descending order. When a create time is not available, the items are sorted by ID. A `marker` with an ID that is not valid returns an itemNotFound (404) fault.

The `limit` parameter sets the page size. If the client specifies a `limit` value that is greater than the supported limit, an overLimit (413) fault might be thrown.

Both parameters are optional.

*Note*

Paginated collections never return itemNotFound (404) faults when the collection is empty — clients should expect an empty collection.

For convenience, collections contain atom "`next`" links and can optionally contain "`previous`" links. The last page in the collection will not contain a "`next`" link.

The following examples show pages in a collection of images.

To get the first page, issue a `GET` request to the following endpoint and set the `limit` parameter to the page size of a single item:

```
http://dfw.servers.api.rackspacecloud.com/v2/010101/images?limit=1
```

Subsequent links honor the initial page size. A client can follow links to traverse a paginated collection.

## Lbaas - `limit` & `marker`

 Paginated collections

To reduce load on the service, list operations return a maximum limit of 100 items at a time. This is referred to as *pagination*. If a request supplies no limit or one that exceeds the configured default limit, the default is used instead.

Pagination provides the ability to limit the size of the returned data as well as retrieve a specified subset of a large data set. Pagination has two key concepts: limit and marker. *Marker* is a reference to an object's id and is in the list of paged results at that particular resource. In this case, the resource is a load balancer, so the marker is the load balancer id at which to begin the list of the paged results. To navigate the collection, the `limit` and `marker` parameters can be set in the URI. For example, `?limit=10&marker=1234` displays a maximum of 10 load balancers in the paginated results, beginning with the load balancer whose id is 1234.

There is also an `offset` parameter that can be used as a count of the number of objects from where the paginated list is started.

If a marker beyond the end of a list is given, an empty list is returned. Note that list operations never return 404 (itemNotFound) faults.

## Configuration Service - `limit` & `marker`

Paginated Collections

To reduce load on the service, list operations will return a maximum number of items at a time. The maximum number of items returned is determined by the service. To navigate the collection, the parameters *`limit`* and *`marker`* can be set in the URI (For example, *`limit`*=100&*`marker`*=10). The *`marker`* parameter is the index value of the last item in the previous list. Items are sorted by update time. When an update time is not available they are sorted by ID. The *`limit`* parameter sets the page size. Both parameters are optional. If the client requests a *`limit`* beyond that which is supported by the deployment an overLimit (404) fault.

*Note*

Paginated collections never return itemNotFound (404) faults when the collection is empty — clients should expect an empty collection.

In the XML representation, for convenience, collections contain atom "`next`" and "`previous`" links. The first page in the list will not contain a "`previous`" link, the last page in the list will not contain a "`next`" link.

In the JSON representation, paginated collections contain a `link` property that contains the "`next`" and "`previous`" links.

## Offer Service - limit & marker

Name | Style | Type | Description
-----|-------|------|-------------
`limit` | Query | String | An integer between 1 and 100 to specific the number of records to return. A string value of "infinite" indicates the API to return all of the products. The clients should be aware about the performance constraints using "infinite" could potentially pose with respect to the size of the response. The `limit` parameter is optional.
`marker` | Query | String | The starting point for the return data. This parameter is used to control the pagination. For example, an marker value of 10 specifies that the product offerings that are returned should start with item number 11. The `marker` parameter is optional.

## Promotinon Service - `limit` & `marker`

Paginated Collections

To reduce load on the service, list operations will return a maximum number of items at a time. The maximum number of items returned is determined by the service. To navigate the collection, the parameters *`limit`* and *`marker`* can be set in the URI (For example, *`limit`*=100&*`marker`*=10). The *`marker`* parameter is the index value of the last item in the previous list. Items are sorted by update time. When an update time is not available they are sorted by ID. The *`limit`* parameter sets the page size. Both parameters are optional. If the client requests a *`limit`* beyond that which is supported by the deployment an overLimit (404) fault.

*Note*

Paginated collections never return itemNotFound (404) faults when the collection is empty — clients should expect an empty collection.

In the XML representation, for convenience, collections contain atom `next` and `previous` links. The first page in the list will not contain a `previous` link, the last page in the list will not contain a `next` link.

In the JSON representation, paginated collections contain a link property that contains the `next` and `previous` links.

## Orchestration - `limit` & `marker`

Pagination

Pagination provides the ability to limit the size of the returned data in the response body as well as retrieve a specified subset of a large data set. Pagination has two key concepts: `marker`.

* Limit is the restriction on the maximum number of items for that type that can be returned.

* Marker is the ID of the last item in the previous list returned.

    The ID is the UUID in the case of stacks. For example, a query could request the next 10 stacks after the stack "1234" as follows: `?limit=10&marker=1234`. Items are displayed sorted by ID.

If the content returned by a call is paginated, the response includes a structured link with the basic structure `{"href": "<url>", "rel": "next"}`. Any response that is truncated by pagination will have a *next* link is returned.

Pagination applies only to the calls listed in the following table:

Verb  | URI        | Description
------|------------|-------------
`GET` | `/stacks/` | Lists the information for all stacks.

See the example for a paged List Stacks call that follows.

*Example List Stacks Paged Request: JSON*

```
GET /v1/622dd71147164a92bf3915f5cbdfe261/stacks?limit=2 HTTP/1.1

Accept: application/json
Accept-Encoding: gzip, deflate, compress
Host: 10.0.2.15:8004
User-Agent: HTTPie/0.7.2
X-Auth-Token: 286dbf02498f457399ee9b3db95ce65d
```

Notice that the paged request example above sets the limit to 2 (`?limit=2`), so the response that follows shows 2 stacks and returns a `marker` set to the UUID of the last stack in the returned list (`?marker=5d0daddb-2a24-475b-853d-4ab4e1522c63`). Also a link is provided to retrieve the next 2 results (`limit=2`) in the link element identified by the attribute `"rel":"next"`:

## Maas - `limit` & `marker`

Paginated collections

To reduce load on the service, list operations will return a maximum number of items at a time. To navigate the collection, the parameters `limit` and `marker` can be set in the URI (e.g. `?limit=200&marker=enCCCCCC`). The marker parameter is the ID of a first item in the next page. This item can be found in the metadata object under the `next_marker` tag. Items are sorted by the ID name in a lexicographic order. The `limit` parameter sets the page size. It defaults to `100`items per page and the maximum value is `1000`. Both parameters are optional. If the client requests a `limit` beyond that which is not supported an (400) fault may be thrown.

For convenience, collections contain a link to the next page (the `next_href` attribute in the metadata object). If there are no more objects, the `next_marker` and `next_href` attributes will be empty. The following examples illustrate two pages in a collection of entities. The first page was retrieved via a `GET` to https://monitoring.api.rackspacecloud.com/v1.0/entities?limit=1. In these examples, the limit parameter sets the page size to a single item. Subsequent `next_href`link will honor the initial page size. Thus, a client may follow this link to traverse a paginated collection without having to input the `limit` parameter.

## Billing Service - `limit` & `marker`

Paginated Collections

To reduce load on the service, list operations will return a maximum number of items at a time. The maximum number of items returned is determined by the service. To navigate the collection, the parameters *`limit`* and *`marker`* can be set in the URI (For example, *`limit`*=100&*`marker`*=10). The *`marker`* parameter is the index value of the last item in the previous list. Items are sorted by update time. When an update time is not available they are sorted by ID. The *`limit`* parameter sets the page size. Both parameters are optional. If the client requests a *`limit`* beyond that which is supported by the deployment an overLimit (404) fault.

*Note*

Paginated collections never return itemNotFound (404) faults when the collection is empty — clients should expect an empty collection.

In the XML representation, for convenience, collections contain atom `next` and `previous` links. The first page in the list will not contain a `previous` link; the last page in the list will not contain a `next` link.

In the JSON representation, paginated collections contain a link property that contains the `next` and `previous` links.

## CDN - limit & marker

Name       | Type               | Description
---------|----------------------|-------------
`marker` | String *(Optional)*  | Specifies the `service_id` that represents the last service listed.
`limit`  | Integer *(Optional)* | Specifies the number of resources to list. The default maximum limit is 20, but is configurable by the operator.

## Files - `limit` , `marker` & `end\_marker`

Name     | Type             | Description
---------|------------------|-------------
`limit`  | Int *(Optional)* | For an integer value n, limits the number of results to n values.
`marker` | String *(Optional)* | Given a string value x, returns container names greater in value than the specified marker. Only strings using UTF-8 encoding are valid. Using `marker`provides a mechanism for iterating through the entire list of containers.
`end_marker` | String *(Optional)* | Given a string value x, returns container names lesser in value than the specified end marker. Only strings using UTF-8 encoding are valid.

## Checkmate - `limit` & `offset`

https://github.rackspace.com/checkmate/checkmate/wiki/API-Docs

-   offset
    -   Number to offset deployment results by
    -   optional
-   limit
    -   Number to limit deployment results by
    -   optional

## Ticket Service (Meander) - none

## Ticket Service (Lefty) - `offset` & `count`

URL Parameters

-   *offset (optional)*: ticket offset for the start of a page (default: 0)

-   *count (optional)*: number of tickets to return in a page (default: 50)

-   *sort (optional)*: Returns tickets by the specified sort field and order

         default:
             sort_field : status
             sort_order : desc (Allowed : [desc,asc])

         usage: sort=sort_field|sort_order
         Examples :
             case a) ?sort=last_updated|desc
                  This returns tickets sorted by last_updated in descending order
             case b) ?sort=last_updated|desc&sort=account_id|asc
                  This returns tickets sorted by last_updated in descending order and account_id in
                  ascending order
          Note: Tickets can have multiple sorts cascaded as in case b)

Example:

https://github.rackspace.com/lefty/ticket_service/wiki/Ticket-API#example-request
