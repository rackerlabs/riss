# Service Hostname Conventions

# Problem

You are implementing a service and need to register the hostnames which will host resources owned by the service.

# Solution

The root service hostname should match the form: \`some-service.api.rackspace.com\`, where "\*some-service\*" is singular and matches the exact name given to the service in the service registry. When the service inventory name is more than one word, spaces should be mapped to hyphens. The service has autonomy of the entire namespace of subdomains under the root and may expose resources anywhere within this namespace. We adopt the convention that the top level subdomain under \*some-service\* should name the environment, except for production, when the environment name can be omitted.

# Discussion

Each service should have its root hostname defined by a DNS "A record" constructed from the canonical service name used in the service inventory. The server may have other CNAME entries as aliases, but it has only one canonical hostname. All resources exposed by the service should be dereferenced by URIs whose hosts end with the service root hostname.

A service is autonomous over its runtime environment, and it may use any number of network nodes structured underneath its root hostname. For example, a general hostname under the a generic service named "some-service" would look like:

`node.some-service.api.rackspace.com`

Each service will name hosts within its subdomain based on a variety of concerns. This can include adding additional subdomains if desired. For example:

1.  To differentiate environments: e.g. `test.some-service.api.rackspace.com`. The convention is to omit the environment for the production environment, but to include it as the top level below *service*.
2.  To optionally differentiate server roles: e.g. `db{1,2}.service.api.rackspace.com` and `web{1,2,3,4}.service.api.rackspace.com`. There may or may not be resources exposed by these, and if not, this might be done purely for system administrative convenience.
3.  To shard data or behavior owned by the service, perhaps geographically. E.g: `shard-{DFW,LON,IAD}.service.api.rackspace.com`, or by other means.
4.  To differentiate logical subsystems of the service. E.g.: `module{A,B,C}.service.api.rackspace.com`.
5.  To segregate capabilities in other ways, such as for partners or administrative access. E.g.: `admin.service.api.rackspace.com`

Because the service is autonomous over the hostname substructure under the \`service.api.rackspace.com\` subdomain, care should be taken not to leak these implementation details into the client contract. Specifically, clients should only be expected to know about the service hostname root. Their entry point is always at `service.api.rackspace.com` and links should direct the client from there, so that they are protected from coupling

The canonical hostname should be singular and should not use abbreviations or acronyms. This will be automatically true when the hostname matches the service name in the service inventory. By not using abbreviations, we assure that users and developers will not have to guess which shortened form of the noun.

The hostnames hosting resources of the service will each be reachable at an IP that may resolve differently depending on the client location. For example, when the client is on the Rackspace network, the hostname would typically resolve to a 10.x.x.x address,

This is an IPv4 oriented statement - in IPv6, the concept of private IPs is less used.

while an external client might see a different routable, world visible IP. Services with no externalized resources may not have this IP. All services should have an internal IP.

Must be protected by an appropriate firewall.

By keeping hostnames in tight correspondence with the governed name of a service, we bolster both. The URLs are generally visible to users examining a specific representation, so the hostname part of the URL will be seen, thereby reinforcing the service names we use. Conversely, a user who only knows the service name in the service inventory can immediately navigate to the service.

# Good and Bad Practices

## Good Practices

1.  The primary hostname of a service should match the service inventory service name.
2.  The primary service hostname should be a singular noun, possibly with other descriptive words.
3.  The primary service hostname should not use abbreviations or "lingo".
4.  The primary service hostname should replace spaces with dashes if it is multiple words.
5.  The primary service hostname should use only lower case characters.
6.  The primary service hostname should be the unique DNS A record entry.
7.  It is OK to create aliases as CNAME entries, but links to service resources should not use such hostname aliases within their href URIs.
8.  Non-production environments should be the top level below the service name. Omit the environment name for production.

Bad Practices

1.  Do not use system names for hostnames as services may outlive the systems they encapsulate.
2.  Service hostnames should not include the word "service" or "information" or "system" as it is redundant.
3.  Service names should NOT imply a broader entire functional scope than what the service is for. E.g: A `monitoring-event.api.rackspace.com` service hostname makes more sense than \`event.api.rackspace.com\` if the functional scope is confined to monitoring.
4.  Do NOT use anything besides the primary service hostname as links in a resource representation: no CNAME aliases, no raw IPs.
5.  Be careful that individual resources do not end up with aliased URIs when hostname substructure is used.

# Unresolved Issues

The following questions remain unresolved:

1.  Will we use "split horizon" hostname resolution that resolve to different IP's when accessed internally vs externally?
2.  Can we standardize more common cases besides environment?
3.  Are administrative API's accessing "different" resources? How should they be named? Using an admin subdomain? Or perhaps a fixed port standard like 8443?

# Frequently Asked Questions

*What does a typical URL link look like?*

A typical link to a particular resource on a service called "some-service" would be of the form `https://some-service.api.rackspace.com/some-resource/123`.

*If I have a load balancer and I can access a resource (resource.xml in this example) at `https://some-service.api.rackspace.com/resource.xml` and `https://web1.some-service.api.rackspace.com/resource.xml`, am I violating the no aliasing rule by exposing the same resource at two URIs?*

No. They are not quite the same resource (though they are clearly related). The former is the "real" resource, and the latter is a resource derived from the former by accessing it via a particular network node. Links generally use the former unless we are interested in clarifying how the internals of the service infrastructure are behaving. For example, we might monitor the web1 URI to test if web1 is functioning properly, but in general clients should never couple to such infrastructure details.

# References

1.  RESTful Web Services Cookbook, Subbu Allamaraju. [Recipe 4.1](http://search.safaribooksonline.com/9780596809140/recipe-how-to-design-uris), "How to Design URIs".

# Contributors

Bryan Taylor

Mark Morga
