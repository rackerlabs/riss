## [JSON-LD](http://json-ld.org/)

1. Standards: [W3C Recommendation](http://www.w3.org/TR/json-ld/)
    1. Status: W3C Recommendation 16 January 2014
2. Acceptance and use
    1. [Tooling](http://json-ld.org/)
        * Ruby
        * Python
        * Javascript
        * Java
        * [Go](https://github.com/kazarena/json-gold)
    2. [References on GitHub](https://github.com/search?utf8=%E2%9C%93&q=JSON-LD&type=Repositories&ref=searchresults) 183 results.
    3. [Articles found on Google](https://www.google.com/?gws_rd=ssl#q=json-ld) 476,000
3. Media-Type: `application/ld+json`
4. Boilerplate: requirements for representation format. None.
5. Scope: General purpose linking in JSON documents - not HATEOAS specific. See [Hydra](hydra.md).
6. Example collection representation
    ```json
    {
      "@context": {
        "next": "http://schema.org/url",
        "find": "http://schema.org/url",
        "admin": {
          "@id": "adminRef",
          "@container": "@list"
        },
        "adminRef": {
          "@id": "http://schema.org/url",
          "title": "http://schema.org/name"
        },
        "currentlyProcessing": "https://schema.org/Integer",
        "shippedToday": "https://schema.org/Integer",
        "orders": {
          "@id": "order",
          "@container": "@list"
        },
        "order": {
          "@id": "http://schema.org/url",
          "basket": "http://schema.org/url",
          "customer": "http://schema.org/url",
          "total": "https://schema.org/amount",
          "currency": "https://schema.org/currency",
          "status": "https://schema.org/OrderStatus"
        }
      },
      "@id": "/orders",
      "next": "/orders?page=2",
      "find": "/orders{?id}",
      "admin": [{
        "@id": "/admins/2",
        "title": "Fred"
      }, {
        "@id": "/admins/5",
        "title": "Kate"
      }],
      "currentlyProcessing": 14,
      "shippedToday": 20,
      "orders": [{
        "@id": "/orders/123",
        "basket": "/baskets/98712",
        "customer": "/customers/7809",
        "total": 30.00,
        "currency": "USD",
        "status": "shipped"
      }, {
        "@id": "/orders/124",
        "basket": "/baskets/97213",
        "customer": "/customers/12369",
        "total": 20.00,
        "currency": "USD",
        "status": "processing"
      }]
    }
    ```

    JSON-LD context documents can also be referenced (via URI) from a JSON representation rather than included within the representation. Further, contexts can be simplified by specifying a [default vocabulary](http://www.w3.org/TR/json-ld/#default-vocabulary).

7. Link capabilities
    1. "self" link
        1. `#/@id`
    2. Link to related resource, list of links to related resources.
        1. `#/admin`
        1. `#/orders/*/basket`
        1. `#/orders/*/customer`
        1. Embedded resources: `#/orders/*`
    3. Link to operation on resource.
        1. `#/find` - a curies link to find operation
        1. `#/next`
    4. Relationship indicator for link. Link relationship (rel) is specified by information in the `@context` or `@vocab`. There is nothing directly in the representation that indicates a link relation, but it can be described via a graph.
        1. `#/@id` - an implicit "self" link relation
        1. `#/next` - a "next" link relation defined by `#/@context/next`
        1. `#/find` - a curies link relation defined by `#/@context/find`
        1. `#/admin` - a curies link relation defined by `#/@context/admin`
    5. Relationship indicator description (reference or link)
        1. `#/@context/next/@id` - link to description of the next node.
    6. Indicating a templated link. JSON-LD does not appear to support a concept of a templated link directly. A vocabulary could describe a templated link as a type.
    7. HTTP method specification for operation link
        1. JSON-LD doesn't specify this directly, but HTTP method information can be stored in the context link (example: `#/@context/image/@id`) for the item. See: [Hydra](hydra.md).
    8. Embedded resources. JSON-LD doesn't have a dedicated concept of embedded resources, but they can be represented as follows:
        1. `#/orders/*`
