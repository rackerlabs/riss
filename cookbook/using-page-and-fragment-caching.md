# Using Page and Fragment Caching

# Problem

You want to know how use page and fragment caching in your RESTful service.

# Solution

This form of caching is best used when multiple representations share common fragments, when representations are shared between resources and any time that rendering is expensive (i.e. Profiling indicates that rendering is a significant portion of the overall service call cost).

## Java applications

Java applications that use Hibernate to connect to a persistent data store should use a Hibernate Second Level Cache to cache persistent objects (Database caching is the topic of [Using Database Caching](using-database-caching.md). Hibernate caching almost always eliminates the need for page and fragment caching.

Therefore, postpone adding this type of caching to your Java / Hibernate application until profiling indicates that your application would be likely to benefit from this type of caching.

(TODO: update this section based on whatever datagrid technology we end up recommending. This is just a placeholder paragraph for now.)

If you determine that page and fragment caching must be used, then use [Infinispan](http://www.jboss.org/infinispan) in distributed mode as your programmatic cache so that all of the application servers in your cluster can take advantage of the cached data in a reliable and scalable manner. Use the Memcached interface for Infinispan to allow out-of-the-box support for Rails applications.

It is important that the cache be clustered among all of the application servers for your application so that these servers can ramain consistent with one another.

## Rails applications

TODO: Get clarification from Mark about what the most recent version of Rails supports for ORM caching. This section may be incorrect.

Rails does not have an out-of-the-box ORM caching solution similar to Hibernate's Second Level Cache. Therefore, even persistent data often needs to be cached in a programmatic cache. Happily, Rails supports several programmatic caching mechanisms. See the following table for details about each type of programmatic caching supported by Rails:

*Types of Programmatic Caching in Rails*

Type
Applicability
Page Caching
Use page caching for resources that can be shared among clients (i.e. do not depend upon authentication) and
that do not vary based on URL parameters.
Action Caching
Use Action Caching to cache full responses that require a before filter to be run, such as an authorization
filter.
Fragment Caching
Use Fragment Caching for dynamically generated response bodies where different parts of the reponse body need
to be cached and expired differently.

 for details about these types of caching mechanisms.

# Discussion

Page and Fragment caching may also be referred to as programmatic caching or side-caching. It is a caching technique in which the application developer is directly responsible for managing data in a cache. The cache contains full or partial contents of data returned by the application. We consider it to be distinct from database caching because with database caching, the ORM is responsible for automatically managing the cache content. For origin servers, page and fragment caching is also different from HTTP expiration-based caching because that type of caching is oriented toward describing whether and how an HTTP response may be cached, rather than managing cache entries directly.

For applications that are able to take advantage of a database cache between requests (notably Java applications using Hibernate), there is typically little or no benefit to page and fragment caching. The ORM will almost always be able to do a better job of managing the contents of the cache and will do so with very little development effort. Page and fragment caching tends to require at least some development and can be error-prone if not correctly implemented.

However, there are some scenarios where it makes sense to employ a programmatic cache in addition to a database cache. Notably it may make sense to programmatically cache non-persistent data that is costly to calculate and that is likely to benefit from caching because of frequent access. Rendering the representation can expensive in both Java and Ruby and for particularly expensive renders there is value in doing a fragment cache over and above what the database cache can provide.

Page Caching is an approach to caching where the entire output of an action is stored as a HTML file that the web server can serve for subsequent requests as a static file. A reverse proxy cache is able to perform this same function, and is not hampered by the short-comings of the Rails page caching approach (see FAQ below for details). In general developers should not concern themselves with page caching. Instead, delegate this functionality to a reverse proxy server situated between your origin server and your clients.

Action Caching works like Page Caching except that the incoming web request is handled by the Rails stack and Action Pack so that "before filters" can be run on it before the cached content is served. This allows authentication and other restrictions to be applied while still leveraging cached content for improved performance.

Fragment Caching is used for response bodies made up of dynamically generated content fragments that need to be expired or cached differently from one another.

For each of these types of programmatic caching in Rails, developers are responsible for expiring entries in the cache at appropriate times, like when the data is updated by a mutating operation. Rails provides a mechanism called Cache Sweeping to make it relatively easy to support clearing caches appropriately. Cache sweeping provides an elegant solution to prevent the need to implement a ton of cache expiration calls throughout your code. It does this by moving all the work required to expire cached content into an `ActionController::Caching::Sweeper` subclass. This class is an observer and looks for changes to an object via callbacks, and when a change occurs it expires the caches associated with that object in an around or after filter. Developers should prefer to use Cache Sweepers to keep code clean.

# Good and Bad Practices

## Good Practices

1.  In Rails apps, don't use page caching for resources that vary depending on URL parameters since Rails page caching does not support this use-case. Instead, delegate this work to a reverse proxy cache.
2.  In Rails apps, use Cache Sweepers to keep your code clean and reduce the likelihood of cache related errors.

## Bad Practices

1.  Don't use a local programmatic cache when you have more than one application server in a cluster. Instead, use a clustered cache so that mutating operations performed against one application server can invalidate cache entries shared by all servers in the cluster. Using only local caches will result in some caches becoming out of date, leading to unexpected behavior.

# Unresolved Issues

# Frequently Asked Questions

The following are frequently asked questions whose answers are worth collecting in one place.

*What are the short-comings of Rail's page caching that are mentioned in the discussion section above?*

There are three major reasons to prefer a reverse proxy cache instead of page caching in Rails. These are:

1.  A reverse proxy is designed for serving static cached content. Modern reverse proxy caches are highly optimized and battle tested.
2.  Rails Page Caching is only available to unauthenticated, static pages where all visitors are treated the same.
3.  Rails Page Caching ignores all parameters. For example /products?page=1 will be written out to the filesystem as products.html with no reference to the page parameter. Thus, if someone requests \`/products?page=2\` later, they will get the cached first page.

# References

1.  [Glossary of caching terminology in Confluence](https://confluence.core.rackspace.com/display/EArch/Glossary+of+caching+and+data+grid+terminology).
2.  [Caching with Rails: An overview](http://guides.rubyonrails.org/caching_with_rails.html).
3.  [Rails API documentation for ActionController::Caching::Pages](http://api.rubyonrails.org/classes/ActionController/Caching/Pages.html).

# Contributors

Benjamin Truitt
Mark Morga
Dave Slotnick

Mark Morga
