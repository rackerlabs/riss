# General XML Schema Guidance

# Problem

You need to make an XML Schema to define a representation of a service resource.

# Solution

Start with a conceptual data model in mind and use the basic building blocks of XML Schema to define a hierarchical view of the entity you wish to represent. Courser grained representations will traverse more of the relationships than finer grained counterparts. Each entity will either be a root node of some representation or will be subordinate to some other element. The relationships at the boundary of the representation will relate an entity in the representation to one that is not in it by sustituting a hyperlink for the related entity. Representations describing a RESTful resource will need to include links

# Discussion

In a contract first approach, you will not have created an object model or a database model (logical or physical). You will be writing XML Schemas by hand. Before you do this, you should do high level conceptual data modeling, probably using something like a URL class diagram, to define and name the basic entities and their relationships to each other. The schema you create will define a representation from the view of an instance of some "root" entity. The represenation might extend across some small set of relationships or it might be a single entity. This is a question of representation granularity

# Good and Bad Practices

## Good Practices

## Bad Practices

# Unresolved Issues

None.

# Frequently Asked Questions

The following are frequently asked questions whose answers are worth collecting in one place.

*Question*

Answer

# Contributors

Bryan Taylor

Mark Morga
