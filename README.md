# Rackspace Internal Service Standards

This project contains the set of standards, guidelines, and cookbook recipes for developing RESTful services at Rackspace.

## Definitions

*Standards*

Standards are requirements that services must meet to by compliant with the Rackspace SOA.

*Guidelines*

Guidelines are strongly suggested to be supported by services to increase the conformity of service APIs to ease usability.

*Recipes*

Recipes are documents that discuss common service patterns and provide the standardized means to implement those service patterns.

## Organization

This project is organized as follows:

```
+ https://github.com/rackerlabs/riss
|   \—service-design   - Standards and Guidelines for overall service design
|   \—resource-Design  - Standards and Guidelines for resource design
|                        Resource types, routes (URLs), nesting,
|                        proper use of HTTP
|   \—representation-design
|                      - Standards and Guidelines for HATEOAS, schema design,
|                        JSON & XML design, media types, and versioning
|   \—documentation
|                      — Standards and Guidelines for RAML documentation,
|                        documentation generation, where to put documentation,
|                        communication and planning
|   \—non-functional-requirements
|                      - Standards and Guidelines for performance, monitoring,
|                        security, quality
|   \—language-framework
|                      - Standards Guidelines specific to programming
|                        languages and frameworks
|   \—cookbook         - Recipes for common service patterns
```

## Contributing

We welcome contributions to our standards. Please follow these guidelines to make it easier to accept your contributions.

1. Place your contribution file in an appropriate sub-directory of this project (see Organization above).
2. Name your file meaningfully in lowercase with dashes and `.md` as extension.
3. Use GitHub Flavored Markdown for your file format.
4. Include diagrams where appropriate. Diagrams are preferred in the SVG format.
5. For new pages, use other pages as models to follow for your contributions.
6. For new cookbook recipes, use the [cookbook recipe template](cookbook/recipe-template.md)
