# Using Caching in a RESTful Service Implementation

# Problem

You want to know how to use caching throughout your RESTful service implementation.

# Solution

Use a mix of caching techniques throughout the layers of your application. Refer to the following table for a list of recommended techniques and their applicability.

## Recommended caching techniques

### [Using Database Caching](using-database-caching.md)

A database cache is designed to reduce traffic between your application and the database by reusing data already loaded from the database. Database access is necessary only when retrieving data that is not currently available in the cache. A library such as Hibernate should be used to transparently handle cache management on behalf of your application.

### [Using Page and Fragment Caching](using-page-and-fragment-caching.md)

With this mechanism, the application is responsible for maintaining the data in the cache. The cache contains full or partial contents of data returned by the application. This form of caching is best used when multiple representations share common fragments, when representations are shared between resources and any time that rendering is expensive (i.e. Examining rendering times indicates that rendering is a significant portion of the overall service call cost).

### [Using HTTP Headers for Expiration Caching](using-http-headers-for-expiration-caching.md)

The goal of this caching technique is to eliminate the need to send requests in many cases. Allowing proxy caches and client-side caches to reuse a previously retrieved representation of an entity reduces the number of network round-trips required for many operations. This results in reduced end-user perceived latency and improved reliability.

### [Supporting HTTP Cache Validation](supporting-http-cache-validation.md)

This caching technique is complimentary to using HTTP headers for Expiration caching. The goal of this technique is to eliminate the need to send full responses in many cases, which reduces network bandwidth requirements. Furthermore, some resources that are costly to generate may be relatively cheap to validate, resulting in reduced end-user perceived latency.

### [Using HTTP Proxy Caching](using-http-proxy-caching.md)

To fully benefit from HTTP caching, implementors of RESTful services in the Rackspace Foundation SOA should strive to deploy a reverse-proxy cache in the request-reply chain for its clients.

# Frequently Asked Questions

The following are frequently asked questions whose answers are worth collecting in one place.

*When should I use caching in my application? How do I know which caching technique(s) to apply?*

The caching techniques listed here add to the design, implementation, testing, and support costs for your application. Therefore, these techniques should be applied in a targeted manner. If your initial implementation does not meet the SLA required by your clients, use performance testing to identify performance hot-spots where the caching techniques listed here may provide a benefit. Decide which specific technique(s) to apply by using an analytical approach that proves the benefit outweighs the costs involved.

*Will my write-mostly service benefit from caching?*

It depends. In general, the more often the resources offered by your implementation are mutated, the less likely your service is to benefit from caching. Having said that, your specific use-cases may or may not benefit from the techniques listed here. Therefore, use profiling tools to guide your decision about whether and how to apply caching in your application.

*Are there any special caching considerations relevant to my deployment process?*

Yes. In general, you should plan to purge all cached content from database, page and fragment caches when you deploy a new version of your application. HTTP caches should not require purging, provided you follow the service versioning cookbook entry, since resources will be cached in a version-specific manner in any HTTP proxy caches in your request-reply chain. There may be certain cases where it is safe to not purge cached content at deploy-time. The technical lead for the project is responsible for identifying and planning for these special circumstances.

# References

1.  [RESTful Web Services Cookbook](http://search.safaribooksonline.com/9780596809140), Subbu Allamaraju. via Safari, especially Chapter 9, recipe 9.3.
2.  [Glossary of caching terminology in Confluence](https://confluence.core.rackspace.com/display/EArch/Glossary+of+caching+and+data+grid+terminology).
3.  [Using HTTP Headers for Expiration Caching](using-http-headers-for-expiration-caching.md)
4.  [Supporting HTTP Cache Validation](supporting-http-cache-validation.md)
5.  [Using HTTP Proxy Caching](using-http-proxy-caching.md)
6.  [Using Database Caching](using-database-caching.md)
7.  [Using Page and Fragment Caching](using-page-and-fragment-caching.md)

# Contributors

Benjamin Truitt
Mark Morga
Dave Slotnick

Mark Morga

 
