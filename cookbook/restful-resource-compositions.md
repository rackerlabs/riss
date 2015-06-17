# RESTful Resource Compositions

# Problem

When and how should a service combine resources to form a new composite resource.

# Solution

Consider client usage patterns, the likelihood of reuse, and performance and latency needs, and if these factors suggest it, form a new resource that aggregates other resources to reduce the number of client/server round trips.

# Discussion

Composition is a fundamental

# Good and Bad Practices

## Good Practices

1.  Composite representations should reuse existing schemas, leveraging XML extensibility and namespaces.
2.  Composed elements should include their `<atom:link ref="" href="">` elements with appropriate rel and href attributes, including self references so that clients can do piecewise refreshes.

## Bad Practices

# Unresolved Issues

None.

# Frequently Asked Questions

The following are frequently asked questions whose answers are worth collecting in one place.

*Where should a composition be hosted: in a separate service or as a new capability on one of the composed services?*

Either way is acceptable. Use a separate task service if the composition is complex or involves operations that don't fit within the functional scope of an existing service. If, however, the composition extends an entity that is clearly within the scope of one service, it may be appropriate to add a capability to that service to provide the composition.

# References

1.  RESTful Web Services Cookbook, Subbu Allamaraju. [2.4 When to Combine Resources into Composites](http://search.safaribooksonline.com/9780596809140/recipe-when-to-combine-resources-into-composites), via Safari.

# Contributors

Bryan Taylor

Mark Morga
