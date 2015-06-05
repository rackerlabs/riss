# Using Media Types

# Problem

You have a resource representation ready, but how do you associate a media type with it.

# Solution

Services providing business data should provide a primary representation using XML with a media type with "application" type and a subtype taken from the vendor tree, with "rackspace" as the vendor, a document specific identifier that matches the XML Schema's version, and the "+xml" suffix (following RFC 3023). Additionally, "application/xml" should be supported for clients (such as browsers) that are not aware of the representation semantics or that cannot do content negotiation. Alternative representations such as JSON should use equivalent media types, with a modified suffix such as "+json".

# Discussion

Media types are of central importance to RESTful web architectures and are key to the correct interpretation of a representation. Media types define the semantics of the representation. Media types are standardized and so clients can reliable interpret what service responses mean when they declare a media type that the client is familiar with. Media types are also the cornerstone of content negotiation. Rackspace will use custom media types, but these will generally extend well known media types like "application/xml". We may register some of our media types with IANA, but generally media types will be governed internally.

RFC 4288 covers media type specifications. A media type generally takes the form \`type/subtype\`. Following RFC 4288, section 4.2.5, the "application" type should be used "for data to be processed by some type of application program" and is the most appropriate type.

The subtypes are described in section 3, and generally take the form "\*tree.subtree.specific-subtype\*". For Rackspace services, the vendor tree described in section 3.2 is the most appropriate because our documents are "associated with commercially available products" and we want to assure that "registration formally belongs to the vendor" (namely Rackspace). We do not want media types to change if and when any particular capability is exposed to Rackspace customers or partners so we adopt this up front for all media types.

Specifically "vnd.rackspace.\*specific-subtype\*" is typical. It may be appropriate in some instances to use a different vendor subtree that "rackspace". For example, media types used by openstack could use "vnd.rackspace.\*specific-subtype\*". Reseller programs that desire to use a "white label" support model could use a generic vendor subtree. For example, email and apps white label support is associated with the domain emailsrvr.com and could use "vnd.emailsrvr.\*specific-subtype\*". Generally, though, you should use "rackspace" unless there is a business reason not to.

The specific-subtype should generally be named after the entity described. When XML is used, this should match the document name of the XSD associated with it, including version number (use v1 if the XSD omits it), but sans the ".xsd" extension. XML should use the "+xml" suffix, per RFC 3023. Eg: if we describe a customer defined in customer.xsd, the full media type would be "application/vnd.rackspace.customer-v1+xml". If we release customer-v2.xsd, the media type for a document with this representation would be "application/vnd.rackspace.customer-v2+xml". Versioning compatability requirements will be defined in a separate cookbook entry.

Alternate representations such as JSON, should use an equivalent extension, e.g. "+json". There is no RFC making this formal, yet we adopt it anyway as a Rackspace convention.

# Good and Bad Practices

## Good Practices

1.  Use the "application" type, so that all representations of business data match "application/\*".
2.  For the primary media type, use the vendor tree, with "rackspace" as the vendor: "application/vnd.rackspace.\*"
3.  Name the subtype after the XML Schema, with version number and "+xml": "application/vnd.rackspace.customer-v1+xml"
4.  Accept generic "application/xml" as a secondary media type, for browsers and clients with poor content negotiation.
5.  For alternate representations, use the same format but change the suffix: "application/vnd.rackspace.customer-v1+json"

## Bad Practices

1.  Don't only use "application/xml".
2.  Don't leave off the schema version number. Media types MUST include it. Proxies will rely on it to route to the correct versioned URI and caches will depend on it. The permalink strategy depends on it.

# Unresolved Issues

None.

# Frequently Asked Questions

The following are frequently asked questions whose answers are worth collecting in one place.

*Why bother with the custom vendor subtypes? Why can't we just use "application/xml"?*

Several reasons: First, to differentiate and specialize them as a form of type checking. Second to allow conventions to be specified per media type (eg: default and maximum page sizes for paged records) that differ between types. Third, to allow content negotiation to take place with specificity, including even version awareness. This will allow permalinks to resources to be defined so that clients can globally change a single property that influences the version of the schema used. This will be described more fully in the versioning cookbook recipe.

*Why are we putting the version number in the media type? We're already putting it at the base of URI path.*

You can't serve two different representations under the banner of an identical media type. The media type must fully specify a representation bit for bit. Also, the versioning plan will include permalinks that use URIs with no version in the path. Without permalinks, resource represenations that need to refer to each other would either do so with versioned URI, which will result in "link hell", or an extra HATEOAS based version resolution step would be required. After much discussion, the consensus is to use versioned media types and support optional content negotiation.

# References

1.  [RFC 4288](http://tools.ietf.org/html/rfc4288), Media Type Specifications and Registration Procedures .
2.  [RFC 3023](http://tools.ietf.org/html/rfc3023), XML Media Types.
3.  Wikipedia page about an [Internet Media Type](http://en.wikipedia.org/wiki/Internet_media_type).

# Contributors

Bryan Taylor

Mark Morga
