#Sorting
When sorting a collection, the following request parameters must be specified.

Request Parameter | Description | Type and Range | Defaults
------------------|-------------|----------------|----------
sortKey | The name of the field to sort by.| String | Specified by the Service.
sortOrder | The order in which to sort by. asc or desc | String | Specified by the Service.

##Example
`http://service.rackspace.com/customers?sortkey=name&sortOrder=asc`

##Survey of existing services
Service| Format
-------|------
Cloud Servers | none
First Gen Servers | none
Cloud Images | `sort_key&sort_dir`
Cloud Loadbalancer | none
Cloud DNS | none
Cloud Networks | none
Cloud Block Storage | none
Cloud Files | none
Cloud CDN | none
Cloud Databases | none
Cloud Bigdata | none
Cloud Monitoring | none
Rackspace Tickets | none
Cloud Metrics | none
Twitter | `sort=property-direction`
Yelp | `sort=enum`
Ebay | `sort=enum`
eventful | `sort_order=property&sort_direction=direction`
paypal | `sort_by=property&sort_order=direction`
    
##Known formats
* `sort=property|direction`
* `sort=property:direction`
* `sort=property-direction`
* `sort_property=property&sort_direction=direction`
* `sort=property+direction,property2+direction`
* `sort=property&property.dir=direction`
* `orderby.direction=property`