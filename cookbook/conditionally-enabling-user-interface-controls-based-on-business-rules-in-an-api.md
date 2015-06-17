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

*Can I use HTTP headers for the HATEOAS links?*

TODO

*Instead of using HATEOAS links, what about HTTP OPTIONS verb?*

TODO

# References

1. [Hypermedia as the Engine of Application State (HATEOAS)](how-to-implement-hateoas.html)

# Contributors

Ben Truitt
