---
title: HATEOAS in JSON
layout: article
tags: json hateoas links relationships
authors:
  - name: Mark Morga
    title: Principal Architect
    email: mmorga@rackspace.com
---
# Hypertext as the Engine of Application State (HATEOAS) in JSON

## Problem

Hypertext as the Engine of Application State (HATEOAS) is essential for RESTful APIs. How do you effectively implement HATEOAS in a RESTful JSON API?

## Guidelines

1. Resource *SHOULD* include links to related resources in the `_links` object
2. Compact URIs (CURIEs, see below) describing links relations to resource operations *SHOULD* describe the HTTP method of the operation (POST, DELETE, PUT, etc.)
3. Related resources that are included in the representation (rather than linked) *SHOULD* be included in an `_embedded` object (see below)

## Standards

1. A Resource *MUST* include a `self` link in the `_links` object
2. A Resource *MUST* include a set of links to operations on itself in the `_links` object
3. A Resource's links *MUST* be used to describe the relationship between itself and the linked resource or operation
    1. If the link relationship can be described by a standard [IANA link relations](https://www.iana.org/assignments/link-relations/link-relations.xhtml) then the IANA Link Relation *MUST* be used
    2. If the link relationship is a custom link relation, then the link relation *MUST* be described by a `curies` entry (as discussed below)
4. CURIE links *MUST* be resolvable and must return a human readable document describing the relation.

## Discussion

There is a lot of value in selecting a consistent HATEOAS implementation. After an evaluation of many alternatives, we have selected [JSON Hypertext Application Language](http://tools.ietf.org/html/draft-kelly-json-hal) (HAL).

### HAL explained

A HAL representation has the following basic structure:

```js
{
    "_links": { // links to operations on this resource and related resources
        "self": { // link relation (rel) required
            "href": "/example-resource" // required
        }
    },
    ... // resource properties
    "_embedded": { // optional embedded resources
    }
}
```

The `_links` object contains the set of links to related resources and operations on this resource.

Following the `_links` object, the resource's attributes are included.

Finally, the optional `_embedded` object contains any related resources that have representations embedded in this resource.

### Anatomy of a link by example

A basic link requires only a relation name (`self` in this case) object with an `href` URI attribute:

```json
{
  "_links": {
    "self": {
      "href": "http://example.com/myself",
    }
  }
}
```

A templated link indicates that the `href` of the link object is a URI Template.

```json
{
  "_links": {
    "ea:find": {
      "href": "/orders?status={status}",
      "templated": true
    }
  }
}
```

`name` can be used as a secondary key for links that share a common relation. In this case both Fred and Kate are admins, but Fred is *primary* admin and Kate is *backup* admin. The `title` attribute can be used to provide a human-readable identifier.

```json
{
  "_links": {
    "ea:admin": [{
      "href": "/admins/2",
      "name": "ea:primary",
      "title": "Fred"
    }, {
      "href": "/admins/5",
      "name": "ea:backup",
      "title": "Kate"
    }]
  }
}
```

Other optional attributes:

* `type` can be provided as a hint for the media-type of the target resource
* `deprecation` can be provided on deprecated links with a URI describing the deprecation
* `hreflang` can be provided as a hint as to the language of the target resource
* `profile` can be provided as a means of specifying a [profile](https://tools.ietf.org/html/rfc6906) for the representation

### A self documenting API with CURIEs

CURIEs in HAL are used to document the properties of links.

```json
{
  "_links": {
    "curies": [{
      "name": "ea",
      "href": "http://example.com/docs/rels/{rel}",
      "templated": true
    }],
    "ea:find": {
      "href": "/orders?status={status}",
      "templated": true
    },
    "ea:admin": [{
      "href": "/admins/2",
      "title": "Fred"
    }, {
      "href": "/admins/5",
      "title": "Kate"
    }]
  }
}
```

In the above example, the `curies` section defines a `name` "ea" which is a prefix used on other links (`ea:find` and `ea:admin`). It specifies that those link relationships are documented at the templated link `http://example.com/docs/rels/{rel}` (`http://example.com/docs/rels/find` and `http://example.com/docs/rels/admin`).

There can be multiple CURIEs specified in your representation and there likely will be a CURIE defined for standard Rackspace relations and one defined for unique relations for the service.

CURIEs should be valid and should respond with a human readable document containing a description of the relation. Relations for operations must specify the HTTP method to be used for the operation.

### An example

The following example models an orders collection consisting of:

* Links
    - A `self` link
    - A `next` link
    - A link to a `find` operation
    - A list of `admin` relation links
* Properties
    - `currentlyProcessing`: 14
    - `shippedToday`: 20
* Embedded Resources:
    - A list of two order resources:
        + Order Links
            * A `self` link
            * A link to a related `basket` resource
            * A link to a related `customer` resource
        + Order Properties
            * `total`: 30.00
            * `currency`: "USD"
            * `status`: "shipped"

A full HAL representation example:

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
      "href": "/orders?status={status}",
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

## Good and Bad Practices

### Good Practices

1. Including `self` links
2. Including links to resource operations
3. Providing documenting CURIE links to link relations
4. Spend time to make your relation pages (referenced by CURIEs) clear and meaningful.

### Bad Practices

1. Not including links in representations
2. Linking to everything - choose an appropriate set of legitimate links to include

## Unresolved Issues

1. HAL media-type
    1. specifies a media-type of `application/hal+json`. If we choose to use this, then the decision has an impact on our current proposed use of media-types. See: [Using Media-Types](../cookbook/using-media-types.md) and ['profile' Link Relation Type](https://tools.ietf.org/html/rfc6906)
    2. Profile link relations may provide a solution to bridge the problem of specifying the schema of a representation without a proliferation of media-types.
2. Standard CURIE site
    1. A standard service to house CURIE relations should be established. This service could be nothing more than a CDN or cloud files. It would require a means for developers to add and update relations over time in a low ceremony way.
    2. The standard CURIE base URI for Rackspace should provide relations for standard operations (post, put, get, delete, etc.) with a recommended name of "rax"
    3. A template should be produced to show what is expected on a CURIE relation page

## Frequently Asked Questions

The following are frequently asked questions whose answers are worth collecting in one place.

None.

## References

1. [How to implement HATEOAS](../cookbook/how-to-implement-hateoas.md) Recipe
2. [JSON Hypertext Application Language Draft 7](https://tools.ietf.org/html/draft-kelly-json-hal-07)
3. [HAL - Hypertext Application Language](http://stateless.co/hal_specification.html)
4. [IANA Link Relations](https://www.iana.org/assignments/link-relations/link-relations.xhtml)
5. [Library support for HAL](https://github.com/mikekelly/hal_specification/wiki/Libraries) for: Ruby, Python, Javascript, Java, Scala, Go
6. [Web Linking](https://tools.ietf.org/html/rfc5988) Describes link relations and attributes of web links
7. [CURIE Syntax 1.0](hhttp://www.w3.org/TR/CURIE/) W3C standard defining CURIEs.
