# Contract Versioning

# Problem

How should changes to a service contract be identified so that artifacts and resources can be differentiated over time, while keeping client disruption minimal.

# Solution

Contracts and their related resources should be versioned using major version identifiers to differentiate non-backwards compatible changes and minor version numbers to differentiate contract changes that are backwards compatible. Contract artifacts such as the WADL and XSDs should expose both major and minor verison numbers. The major version of the WADL defines the major version of the service contract as a whole. The WADL will define the XSD grammars and media types used to access specific capabilities of the contract, which will have their own versions that are independent of the contract version written against a RESTful API should work if it uses a major version number that matches deployed contract and therefore media types and versioned URI's should only use the major version number. Resources should be accessible by un-versioned permalink URIs that support content negotiation on major version number aware media types. Accessing the permalink without specifying the media type should result in a version document being returned linking the accessible versioned representations.

# Discussion

The versioning solution presented here depends on notions of compatibility. We generally follow versioning terminology defined by the [W3C Technical Architecture Group](http://www.w3.org/2001/tag/) documented in their draft finding [Extending and Versioning Languages: Terminology](http://www.w3.org/2001/tag/). Types of compatibility are specifically defined in [section 1.1.1 Compatibility](http://www.w3.org/2001/tag/doc/versioning#iddiv371153984).

Note that there are two distinct types of language used by a RESTful service:

-   the HTTP request language
-   the media type representation language

A language change is *backwards compatible* if consumers of the revised language can correctly process all instances of the unrevised language, so that old messages don't have their meaning redefined. When discussing the HTTP requests defined in a WADL, the producer of these requests is the service client and the consumer is the service. A change to resource locations such as those in a WADL is backwards compatible if the new implementation can correctly process old HTTP requests. In particular all old resources must continue to exist at their previous URIs and all old HTTP methods must still work. In other words, when considering URIs, backwards compatibility means no broken links.

When discussing the media type representations and their corrseponding XML Schema Documents (XSD's), the producer could be either the service or the client. For example, for simple GET reqeusts, the service is the producer. For POST and PUT, the service client may be the producer. A change to a media type is backwards compatible if document parsers for the new media type can correctly process instances of the old media type. Said differently, representations in the old media type remain valid if they are considered documents of the new media type. When the media type is an XML format with an XSD, this says that the new XSD still validates old XML instance documents.

The following table shows how versions are exposed in different places:

Construct | Versioning Type | Discussion
----------|-----------------|-----------
WADL | v(MAJOR.)MINOR | Defines the overall version of a service contract. References subordinate XSD and media type versions. Each active WADL will have its version number shown in the versions part of the service profile.
XSD | v(MAJOR.)MINOR | Can vary independently of the WADL, but it's referenced in \<grammars\> element within the WADL.
media type | v(MAJOR) | Matches the corresponding XSD's major version number. Backwards compatible changes to the service do not affect the media type's version, which is defined for each resource representation in the WADL.
xml namespace | v(MAJOR) | Namespaces serve to give the xml elements and attributes non-ambiguous meanings within the media type. Backwards compatible changes may not redefine previously existing elements or attributes, but may add new ones.
versioned URI | v$MAJOR | Matches the corresponding WADL's major version number (and NOT the resource's XSD version number).
permalink URI | N/A | Does not contain version information. A GET must pass an accepts header to do content negotation on a valid media type as defined by the WADL. Absent content negotiation, a version document will be returned linking valid versions.

According to the TAG group cited above, a language change is *forwards compatible* if consumers of the old language can correctly process all instances of the new language. When dealing with media types, this says that code that worked on old documents continues to work when given instances of the new media type. For XSD derived media types, this says that new XML validates against the old XSD. This might be achieved by having the old XSD explicitly offer extension points, and assuring that new additions always occur via these extension points.

In terms of the HTTP requests, forward compatibility contemplates old versions of the service whose navigation paths continues to work with new clients. Since REST relies fairly heavily on HATEOAS this simply requires new clients not to change the semantics of the links within representations and not use new entry points into the service.

Forwards compatibility in representations has no affect on version identifiers when the representation xsd changes, but it does give us another option to form a composition where we add a second new schema to represent the change instead of modifying the existing one. This is especially valueable when XSDs are shared by services as a change to a shared artifact has greater impact, and is essential when different services may desire different extensions.

# Good and Bad Practices

## Good Practices

1.  Try hard not to break backwards compatibility. Uprev minor versions often, and major versions rarely or never.
2.  Decouple XSD versions from contract/WADL versions. Reuse XSDs as often as possible, including from other services.
3.  Allow clients to have permalinks that will never change. Achieve backwards compatibility by leveraging permalinks systematically.
4.  Support easy browser inspection of older versions of resources by using versioned URIs as variants of the primary resource.
5.  The WADL and XSD should use major and minor revision numbers so that compatbile but different feature sets in the contract are differentiated.
6.  Use [atom:link](http://atomlink) elements with rel="bookmark" for permalinks and rel="self" for versioned URIs. Clients should only store the former.
7.  Use content negotiation on permalinks, with accepts headers set to the appropriate version of the media type.
8.  Plan ahead to try to achieve forwards compatability. When making a change, be mindful of the forward compatability strategy.
9.  Leverage forwards compatability for representations to allow composite extensions instead of changing existing schemas. Do this especially when multiple services may share use of an XSD.

## Bad Practices

1.  It is bad to include the minor revision number in versioned URIs or media types, since clients should not have to be modified at all to continue to work when backwards compatible changes are made.
2.  It is wrong to fail to bump the major revision number if a change that breaks existing clients is introduced.
3.  It is wrong to expect contract versions to coincide with the version of the implementation deployment artifact.
4.  It is bad to persist versioned URIs as a client.

# Unresolved Issues

# Frequently Asked Questions

The following are frequently asked questions whose answers are worth collecting in one place.

*What is the preferred way to identify a major or minor version?*

Any string can work for a major or minor number, though it's clearly better if the natural progression is evident somehow. One common idiom is to denote major version by prepending "v" to an integer which ascends from 1 each time it changes. Minor versions are separated by a dot "." and also are an integer that ascends from 1. An example would be "v2.1", where the major version in "v2" and the minor version is "1".

*We already have the major and minor versions visible in our versioned URIs, what should we do?*

Actually, you don't. Reinterpret this as using a longer major version number. Eg: if your versioned URIs contain "/v1.2" in the path, then your major version number is "v1.2". Simply bump this to either "v1.3" or "v2.0" if it changes again.

*Is it expected to run major versions in parallel?*

Each major version will have its own lifecycle. It's somewhat harsh to clients to suddenly stop supporting the service implementation they work with. It's good to generally give clients time to migrate by running both versions concurrently for a while.

*Why bother with versioning via media types and using content negotiation?*

It's more RESTful, which translates into tangible benefits for clients. Each resource should have a URI that truly identifies it. If this URI has version numbers in it, then the URI will change when we change the represenation of the resource. But then we'll have two different URIs that refer to the same thing, but we don't have a way to name the thing itself so that code can understand both refer to the same thing. Storing versioned URIs causes great pain for clients who have persisted or cached URIs. Making such changes requires out-of-band information and can't scale as the variety of clients can increase until it is impractical to find them all. A RESTful service should guide its clients through application state changes with links whose meaning and validity doesn't change.

*What happens when I need to make a change where the data model itself changes so that running new and old version concurrently is a challenge?*

A resource can be expected to use only those representations that are valid. For example, if we change a single valued relationship to a many valued one and a particular resource actually has multiples, it may be impossible to represent it in the old format, so we simply don't offer the old format, and if it's explicity requested using content negotiation, we can return a 404 NOT FOUND. Generally, you might need to make data model and data modifications to support concurrent versions. For example, in this example, if we also add a way to flag one of the child resources, we could support the old representation with it.

# References

1.  [Extending and Versioning Languages: Terminology](http://www.w3.org/2001/tag/)

# Contributors

* Bryan Taylor
* Mark Morga

## Comments:

1. As we are thinking about updating this cookbook, let's consider not versioning URIs. See the [video](http://www.infoq.com/presentations/rest-misconceptions?utm_campaign=infoq_content&amp;utm_source=infoq&amp;utm_medium=feed&amp;utm_term=global&amp;utm_reader=feedly) that Mark Morga pointed us to, at minute marker 26-34 or so.

    -- Ben Truitt

2. I agree with not versioning URIs, however, how should we handle existing APIs? Do we apply this rule to new APIs only?

    -- Hector Munoz

3. Our policy in general has been to grandfather-in existing APIs. We don't want to trigger widespread change. As existing APIs are improved, moving to a version-less approach should be considered.

    -- Mark Morga
