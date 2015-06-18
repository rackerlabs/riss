# Representation Design

-   [Considerations for Designing a Representation](#RepresentationDesign-ConsiderationsforDesigningaRepresentation)
-   [Thinking about the fundamental resource](#RepresentationDesign-Thinkingaboutthefundamentalresource)
-   [Start to map out the representation](#RepresentationDesign-Starttomapouttherepresentation)
-   [Think about Media Type and Representation Format](#RepresentationDesign-ThinkaboutMediaTypeandRepresentationFormat)
-   [Entity Headers](#RepresentationDesign-EntityHeaders)
-   [More Details](#RepresentationDesign-MoreDetails)

## Considerations for Designing a Representation

Think about designing your representation from several perspectives in order to best create a representation that works for today's needs as well as one that can work in the future.

Try thinking in these terms about the resource.

1.  Decomposition:
    1.  What are the essential attributes for the resource in isolation from current implementations and business rules/logic?
    2.  What are the items that are implementation specific and therefore may need to support various concurrent incarnations?
    3.  Where might changes occur in the resource?

2.  Relationships:
    1.  What are the other resources that are logically children of this resource
    2.  What is/are the parent(s) or owner(s) of this resource
    3.  What are other relationships between this resource and other resources (potentially in other services)

3.  Action:
    1.  What are the actions that the consumer can execute against this resource

## Thinking about the fundamental resource

Many times (though not exclusively), a RESTful resource will take the form of one of three common patterns:

1.  Collection Resource
2.  Object Resource
3.  Key-Value Resource

Thinking about resources that match one of these patterns can allow you to leverage some commonality in your implementation and make your services more consistent and easier to use.

See more: [Common Resource Patterns](common-resource-patterns.md)

## Start to map out the representation

See more here: [HATEOAS](hateoas.md), [Versioning](versioning-representations.md), and [Schema Design](schema-design.md)

## Think about Media Type and Representation Format

We've chosen JSON as our standard base representation format. You should consider creating a media type for either the resource type, or for your application. Doing so gives you the ability to:

1.  Version a resource (if necessary) representation requested by/responded to clients via media type negotiation instead of [URL Versioning](/pages/createpage.action?spaceKey=RISS&title=About+URL+Versioning&linkCreation=true&fromPageId=116578302).
2.  Easily support more than one representation concurrently.
3.  Provide additional documentation about your resources via external documentation.

See [Media Types](media-types.md) and the [Using Media Types](using-media-types.md) cookbook recipe for more information.

## Entity Headers

The entity headers are the HTTP headers that the service will respond with.

These headers are:

-   Allow
-   Content-Encoding
-   Content-Language
-   Content-Length
-   Content-Location
-   Content-MD5
-   Content-Range
-   Content-Type
-   Expires
-   Last-Modified
-   extension-header

See [HTTP Headers](/pages/createpage.action?spaceKey=RISS&title=HTTP+Headers&linkCreation=true&fromPageId=116578302) for more information.

## More Details

-   [HATEOAS](hateoas.md)
-   [JSON Representations](json-representations.md)
-   [Media Types](media-types.md)
-   [Schema Design](schema-design.md)
-   [Versioning Representations](versioning-representations.md)
-   [XML Representations](xml-representations.md)
