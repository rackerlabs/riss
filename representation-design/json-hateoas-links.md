# JSON HATEOAS Link Representation Comparison

## Goals

In order of diminishing preference:

* Find an existing standard that satisfies our requirements
* Find an existing standard that can be trimmed down to satisfy our requirements
* Build our own standard (using OpenStack as a base) that satisfies our requirements

## HATEOAS requirements

1. *Must* support “self" links
2. *Must* support links to related resources
    1. *Must* contain relationship information to indicate the relationship to this resource
    2. *Should* contain information by which the relationship description can be loaded (via URL)
3. Must* support links to operations that can be performed on this resource
    1. *Must* contain a relationship that describes the operation
    2. *Should* contain information by which the relationship description can be loaded (via URL)
    3. *Should* indicate the HTTP method of the operation (POST, DELETE, PUT, etc.)

## Information & Scoring Criteria

1. Standards Link
    1. Status
2. Acceptance and use
    1. Amount of tooling around technology
    2. References on GitHub
    3. Articles found on Google
3. Media-Type
4. Boilerplate: requirements for representation format
5. Scope: does standard cover more or less HATEOAS links in representations?
6. Example representation (see description below)
7. Link capabilities
    1. "self" link
    2. Link to related resource, list of links to related resources.
    3. Link to operation on resource
    4. Relationship indicator for link
    5. Relationship indicator description (reference or link)
    6. Indicating a templated link
    7. HTTP method specification for operation link
    8. Embedded resources

## The Contenders

1. [HAL - Hypertext Application Language](json-hateoas-link-standards/hal.md)
2. [JSON-LD](json-hateoas-link-standards/json-ld.md)
3. [Hydra](json-hateoas-link-standards/hydra.md)
4. [JSON API](json-hateoas-link-standards/json-api.md)
5. [JSON Hyper-Schema: Hypertext definitions for JSON Schema](json-hateoas-link-standards/json-hyper-schema.md)
6. [Collection+JSON - Hypermedia Type](json-hateoas-link-standards/json-collection.md)
7. [SIREN](json-hateoas-link-standards/siren.md)

## Example Representation

In each linking standard, an example is given followed by examples for the kinds of link information in the HATEOAS Requirements. In the sub-sections that follow, references to parts of the examples use [JSON Pointer](http://tools.ietf.org/html/rfc6901) notation (extended with an `*` to indicate wildcards).

The example models an orders collection consisting of:

* Links
    - A "self" link
    - A "next" link
    - A link to a "find" operation
    - A list of links to admins
* Properties
    - currentlyProcessing: 14
    - shippedToday: 20
* Embedded Resources:
    - A list of two order resources:
        + Links
            * A "self" link
            * A link to a related "basket" resource
            * A link to a related "customer" resource
        + Properties
            * total: 30.00
            * currency: "USD"
            * status: "shipped"



## TODO

1. Add scoring criteria

