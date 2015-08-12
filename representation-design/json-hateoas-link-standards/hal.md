## [HAL - Hypertext Application Language](http://stateless.co/hal_specification.html)

1. Standards: [IETF Draft Standard](https://tools.ietf.org/html/draft-kelly-json-hal-07)
    1. Status: IETF Internet Draft Expires January 23, 2016
2. Acceptance and use
    1. [Amount of tooling around technology](https://github.com/mikekelly/hal_specification/wiki/Libraries)
        * Ruby
        * Python
        * Javascript
        * Java
        * Scala
        * Go
    2. [References on GitHub](https://github.com/search?utf8=%E2%9C%93&q=HAL+JSON&type=Repositories&ref=searchresults) 84 results.
    3. [Articles found on Google](https://www.google.com/?gws_rd=ssl#q=HAL+JSON) 321,000
3. Media-Type: `application/hal+json`
4. Boilerplate: requirements for representation format
    ```json
    {
        "_links": { // optional
            "self": { // link relation (rel) required
                "href": "/example-resource" // required
            }
        },
        ... // resource properties
        "_embedded": { // optional embedded resources
        }
    }
    ```
5. Scope: Covers primarily HATEOAS links with support for embedded resources.
6. Example collection representation
    ```json
    {
      "_links": {
        "self": {
          "href": "/orders"
        },
        "curies": [{
          "name": "ea",
          "href": "http://example.com/docs/rels/{rel}",
          "templated": true
        }],
        "next": {
          "href": "/orders?page=2"
        },
        "ea:find": {
          "href": "/orders{?id}",
          "templated": true
        },
        "ea:admin": [{
          "href": "/admins/2",
          "title": "Fred"
        }, {
          "href": "/admins/5",
          "title": "Kate"
        }]
      },
      "currentlyProcessing": 14,
      "shippedToday": 20,
      "_embedded": {
        "ea:order": [{
          "_links": {
            "self": {
              "href": "/orders/123"
            },
            "ea:basket": {
              "href": "/baskets/98712"
            },
            "ea:customer": {
              "href": "/customers/7809"
            }
          },
          "total": 30.00,
          "currency": "USD",
          "status": "shipped"
        }, {
          "_links": {
            "self": {
              "href": "/orders/124"
            },
            "ea:basket": {
              "href": "/baskets/97213"
            },
            "ea:customer": {
              "href": "/customers/12369"
            }
          },
          "total": 20.00,
          "currency": "USD",
          "status": "processing"
        }]
      }
    }
    ```
7. Link capabilities
    1. "self" link
        1. `#/_links/self`
    2. Link to related resource, list of links to related resources.
        1. `#/_links/ea:admin`
        1. `#/_embedded/ea:order/*/ea:basket`
        1. `#/_embedded/ea:order/*/ea:customer`
        1. Embedded resources: `#/_embedded/ea:order`
    3. Link to operation on resource. The link relationship uses `"curies"`. Curies provide an `href` to describe the relationship and a `name` to namespace the link names used in the `_links` object.
        1. `#/_links/ea:find` - a curies link to find operation
        1. `#/_links/next`
    4. Relationship indicator for link. Link relationship (rel) is specified by link name. Standard `rel` names "self", "next" correspond to standard [link-relations](https://www.iana.org/assignments/link-relations/link-relations.xhtml)
        1. `#/_links/self` - a "self" link relation
        1. `#/_links/next` - a "next" link relation
        1. `#/_links/ea:find` - a curies link relation
        1. `#/_links/ea:admin` - a curies link relation
    5. Relationship indicator description (reference or link)
        1. `#/_links/curies/0/href` - link to curies description
    6. Indicating a templated link
        1. `#/_links/curies/0/template` - indicates curies is a templated link
    7. HTTP method specification for operation link
        1. HAL doesn't specify this directly, but HTTP method information can be stored for "curies" type links in the "curies" HREF link.
    8. Embedded resources
        1. `#/_embedded/ea:order`
