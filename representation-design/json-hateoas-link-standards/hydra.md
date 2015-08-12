## [Hydra](https://www.w3.org/community/hydra/)

[Hydra W3C Community Group](https://www.w3.org/community/hydra/)

Hydra provides a grammar for JSON-LD with information more suited to RESTful API description. In fact, [one Hydra use case](http://www.markus-lanthaler.com/hydra/spec/latest/core/#documenting-a-web-api) would be a suitable substitute for RAML.

1. Standards: [Hydra W3C Community Group Public Working Draft](http://www.hydra-cg.com/spec/latest/core)
    1. Status: Unofficial Draft 19 January 2015
2. Acceptance and use
    1. [Tooling](http://json-ld.org/) for JSON-LD applies.
        * Ruby
        * Python
        * Javascript
        * Java
        * [Go](https://github.com/kazarena/json-gold)
    2. [References on GitHub](https://github.com/search?utf8=%E2%9C%93&q=hydra+JSON-LD&type=Repositories&ref=searchresults) 9 results.
    3. [Articles found on Google](https://www.google.com/?gws_rd=ssl#q=json-ld) 12,400
3. Media-Type: `application/ld+json` (same as JSON-LD)
4. Boilerplate: requirements for representation format. None.
5. Scope:
    1. Adding affordances to JSON representations (HATEOAS links)
    2. Web API documentation
    3. API Discovery
6. Example collection representation
    ```json
    {
      "@context": {
        "@vocab": "http://www.w3.org/ns/hydra/context.jsonld",
        "find": "hydra:IriTemplate",
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
      "@type": "PagedCollection",
      "nextPage": "/orders?page=2",
      "find": "/orders{?id}",
      "admin": [{
        "@id": "/admins/2",
        "title": "Fred"
      }, {
        "@id": "/admins/5",
        "title": "Kate"
      }],
      "operation": [
        {
          "@id": "/orders",
          "@type": "CreateResourceOperation",
          "method": "POST"
        }
      ],
      "currentlyProcessing": 14,
      "shippedToday": 20,
      "member": [{
        "@id": "/orders/123",
        "@type": "order",
        "basket": "/baskets/98712",
        "customer": "/customers/7809",
        "total": 30.00,
        "currency": "USD",
        "status": "shipped"
      }, {
        "@id": "/orders/124",
        "@type": "order",
        "basket": "/baskets/97213",
        "customer": "/customers/12369",
        "total": 20.00,
        "currency": "USD",
        "status": "processing"
      }]
    }
    ```
7. Link capabilities
    1. "self" link
        1. `#/@id`
    2. Link to related resource, list of links to related resources.
        1. `#/admin`
        1. `#/orders/*/basket`
        1. `#/orders/*/customer`
        1. Embedded resources: `#/orders/*`
    3. Link to operation on resource.
        1. `#/comments`
        2. `#/operation[0]`
    4. Relationship indicator for link. Link relationship (rel) is specified by information in the `@context` or `@vocab`. There is nothing directly in the representation that indicates a link relation, but it can be described via a graph.
        1. `#/@id` - an implicit "self" link relation
        1. `#/next` - a "next" link relation defined by `#/@context/next`
        1. `#/find` - a curies link relation defined by `#/@context/find`
        1. `#/admin` - a curies link relation defined by `#/@context/admin`
    5. Relationship indicator description (reference or link)
        1. `#/@context/next/@id` - link to description of the next node.
    6. Indicating a templated link. JSON-LD does not appear to support a concept of a templated link directly. A vocabulary could describe a templated link as a type.
    7. HTTP method specification for operation link
        1. `#/operation[0]`
    8. Embedded resources. JSON-LD doesn't have a dedicated concept of embedded resources, but they can be represented as follows:
        1. `#/orders/*`
