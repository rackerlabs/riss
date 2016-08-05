# Conditionally enabling user interface controls based on business rules in an API

Early draft - not official

This is a first draft, and has not been reviewed or accepted by the Enterprise Architecture team. It is not yet official or recommended.

# Problem

You want to know how your user interface can conditionally enable user interface controls depending on access control business logic found in a service you depend on.

# Solution

Use the presence or absence of HATEOAS links returned by an API to conditionally show / hide or enable / disable user interface controls.

## Discussion

So you've got a RESTful API that follows the [HATEOAS cookbook](how-to-implement-hateoas.md)'s good practices by explicitly providing hyperlinks for every application state transition relevant to the client.

Your user interface needs to present controls to your users to perform actions that result in calling the API to accomplish application state transition.

It'd be best to avoid duplicating business logic related to deciding whether a user can perform a given action.  This business logic already has to be implemented in the service since you may not be the only client of the API.

To avoid duplicating this business logic, and to avoid a need for out-of-band communication between the service implementor and your user interface team, you can simply leverage the HATEOAS links provided by the API to decide which user interface controls to enable.

In the representation for a given resource, an API that follows the [HATEOAS cookbook](how-to-implement-hateoas.md)'s good practices will return hyperlinks for every application state transition that the client is permitted to initiate.  Therefore, you can use the presence or absence of a given hyperlink in the representation to conditionally enable or disable the related user interface control.

### Examples

TODO

# Good and Bad Practices

## Good Practices

1.  Conditionally enable user interface controls based on the presence of the relevant HATEOAS hyperlink in the resource's representation returned from the API.

## Bad Practices

1.  Duplicate authorization business logic that is the responsibility of the API implementation.

2.  Display user controls for every user, even though some users may not have permission from the API implementation to initiate the corresponding state transition.

# Unresolved Issues

None

# Frequently Asked Questions

The following are frequently asked questions whose answers are worth collecting in one place.

*As a service provider, how can I use an HTTP intermediary to implement the authorization-conditioned business logic?*

Authorization business logic can be implemented in the origin service, in an HTTP intermediary within the API service boundary, or both.


| Business Logic Concern | Appropriate for Origin Service? | Appropriate for HTTP intermediary?|
|:--------------------------:|:---:|:---:|
| Coarse-grained permissions | Yes<sup>1</sup> | Yes |
| Fine-grained permissions   | Yes | Yes<sup>2</sup>  |
| Contextual business logic  | Yes  | No<sup>3</sup>  |

Notes on the table:

1. In cases where you are using an HTTP intermediary such as Repose to implement coarse-grained permissions (e.g. RBAC), it is preferable to implement this concern in the intermediary as well, since it keeps the coarse-grained permission business logic cohesive and encapsulated within one place for easier maintenance and testing. However, this requires the HTTP intermediary to inspect and modify the response body to remove the HATEOAS link for responses to clients with insufficient permission. You'll need to be sure your HTTP intermediary supports this functionality.
2. The same logic from the previous point applies for fine-grained permissions, but this will only make sense to do when your HTTP intermediary is concerned with fine-grained permissions.
3. The HTTP intermediary is not a good place to implement contextual business logic such as logic based on resource state, since this spreads out the business logic of your API implementation and increases maintenance burden.

*Instead of using HATEOAS links, what about HTTP OPTIONS verb?*

The HTTP OPTIONS verb is another way to solve the problem statement in this cookbook entry. However, we prefer the HATEOAS link approach because:

1. It is aligned to "Hypermedia as the engine of application state (HATEOAS)" constraint, an aspect of the "Uniform Interface" constraint of the RESTful architectural style.

# References

1. [Hypermedia as the Engine of Application State (HATEOAS)](how-to-implement-hateoas.md)
2. [Hypertext as the Engine of Application State (HATEOAS) in JSON](../representation-design/json-hateoas-links.md)
3. [Repose HTTP Intermediary](http://www.openrepose.org/)

# Contributors

- Ben Truitt
- Hector Munoz
- Mark Morga
- Josh Schairbaum
