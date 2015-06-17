# Using Database Caching

# Problem

You want to know how to use database caching in your RESTful service implementation.

# Solution

Use a database cache in your application's data access layer to reduce traffic between your application and the database by reusing data already loaded from the database.

## Hibernate

In Java applications, use Hibernate's second level cache to automatically manage moving data into and out of the cache between transactions. Use Hibernate's query cache to cache the results of individual queries.

(TODO: update this section based on whatever datagrid technology we end up recommending. This is just a placeholder paragraph for now.)

Use [Infinispan](http://www.jboss.org/infinispan) as your second level cache provider.

## Rails

Make use of the built-in query cache that stores result sets within a single request. Consider using page and fragment caching as described in [Using Page and Fragment Caching](using-page-and-fragment-caching.md).

# Discussion

A properly configured application can dramatically reduce its level of interaction with its underlying database by making use of a database cache. Modern ORMs make it fairly easy to take advantage of a cache of data from the database.

## Hibernate

A Hibernate Session is a transaction-scoped cache of persistent data. Data accessed via the Session is cached in the Session for the duration of the current transaction, and then is cleared. No specific configuration is required to take advantage of this first level cache in Hibernate.

A Second Level Cache is an optional cluster or JVM-level cache whose contents are maintained beyond the life of a transaction and can be shared across transactions. Use of a Second Level Cache is configured as part of the configuration of the Hibernate SessionFactory. If a Second Level Cache is enabled, caching of an instance of a particular entity class or of results of a particular query can be configured on a class-by-class, collection-by-collection and query-by-query basis. See the [Hibernate Reference Documentation](http://docs.jboss.org/hibernate/core/3.6/reference/en-US/html/performance.html#performance-cache) for more on Second Level Cache basics and how to configure entity classes, collections and queries for caching.

If your application needs to read, but not modify, instances of a persistent class, a read-only cache can be used. This is the simplest and best performing strategy, and is the most safe for use in a cluster.

If the application needs to update data, a read-write cache might be appropriate. If you want to use this strategy in a cluster, you should ensure that the underlying cache implementation supports locking. Note that Memcached does not support locking, which is one of several reasons that Inifinispan is the preferred clustered cache implementation to use.

Query result sets can also be cached. This is only useful for queries that are run frequently with the same parameters.

In some cases, there is an external application (i.e. outside of Hibernate's control) that can modify data in the database. Generally, a Second Level Cache should not be used in this case, since data in the cache can become out of date with respect to the database. But sometimes application designers can tolerate having out of date data in the cache. In this situation, use an least-recently-used (LRU) eviction policy with a fairly short eviction time. This will ensure that out-of-date data eventually gets flushed from the cache.

When you application is deployed in a clustered deployment topology such that more than one instance of the application serves data from the same underlying database, you must use a clustered cache so that changes on one application server are propagated appropriately to the caches on the other servers in the cluster. Using Infinispan with the default cluster settings is the recommended approach for a clustered cache.

## Rails

Since Rails 2.0, (J)Ruby on Rails has included a fairly basic query cache that is enabled by default in ActiveRecord. Query caching is a Rails feature that caches the result set returned by each query so that if Rails encounters the same query again for that request, it will use the cached result set as opposed to running the query against the database again. An ActiveRecord query cache lives only for the duration of a particular action (i.e. request). Mutating operations (i.e. INSERT, UPDATE and DELETE) cause the query cache to be flushed.

In addition to this fairly basic Query cache, Rails supports page, action and fragment caches. Rails 3 introduced lazy loading of queries for relations. As a result the rendering of a collection in a fragment (which can be cached) can save the call of the query. Page and fragment caching is the topic of [Using Page and Fragment Caching](using-page-and-fragment-caching.md). Please refer to it for additional detail about these caching options in Rails.

# Good and Bad Practices

## Good Practices - Hibernate

1.  If your application connects to a database, configure your application to use Hibernate's second level cache and query cache.
2.  Configure your application to use a clustered cache for its database cache. Specifically, use Infinispan in Distributed Mode with at least two copies of each cached item. This provides the benefits of scalability, reliability, and high performance for the entire cluster.
3.  Use a read-only cache for data that will not change to optimize how the data is cached.
4.  Set an appropriate expiration time for your data if it is possible for another application or process to make changes to the underlying persistent store.

## Bad Practices

1.  Don't cache everything always. Instead, use a profiling tool to guide you in your decision-making process regarding what queries, objects and collections should be cached. Queries that aren't repeated with the exact same parameters can fill up the query result cache with no benefit. Similarly, data that is unlikely to be accessed again before the expiration time is reached is not a good candidate for caching.
2.  Don't roll your own caching solution for database caching in your application. Use Infinispan in Distributed Mode as Hibernate's second level cache and query cache. This solution is fast, reliable and scalable, and has a far lower cost compared with custom development.
3.  Don't use a local database cache when you have more than one application server in a cluster. Instead, use a clustered cache so that mutating operations performed against one application server can invalidate cache entries shared by all servers in the cluster. Using only local caches will result in some caches becoming out of date, leading to unexpected behavior.

# Unresolved Issues

Need to get clarification from Mark about whether I've mis-stated anything related to Rails ORM caching

# Frequently Asked Questions

The following are frequently asked questions whose answers are worth collecting in one place.

*In Hibernate, what is a Timestamp Cache?*

The above discussion about query caches is a bit over-simplified to be fully accurate. Timestamp caching is an internal detail of query caching. As part of each query result, Hibernate stores the timestamp of when the query was executed. There is also a special area in the cache (the timestamps cache) where, for each entity class, the timestamp of the last update to any instance of that class is stored. When a query result is read from the cache, its timestamp is compared to the timestamps of all entities involved in the query. If any entity has a later timestamp, the cached result is discarded and a new query against the database is executed. See [section 2.1.4 of Hibernate Caching](http://docs.jboss.org/jbossclustering/hibernate-caching/3.3/en-US/html_single/index.html) for more details.

## Doesn't Rails offer Entity and Collection Caching?

In a word, no. However, there are a few plugins for Rails that claim to offer several similar features to Hibernate's second level cache. Notably, [Cache Money](https://github.com/nkallen/cache-money), a cache-library that uses memcached for a clustered, automatic write-through cache that was developed originally for Twitter then open-sourced. However, Cache Money [does not currently support Rails 3](https://github.com/nkallen/cache-money/issues/17).

# References

1.  [Glossary of caching terminology in Confluence](https://confluence.core.rackspace.com/display/EArch/Glossary+of+caching+and+data+grid+terminology)
2.  [Infinispan](http://www.jboss.org/infinispan)
3.  [Hibernate "Improving Performance" Guide](http://docs.jboss.org/hibernate/core/3.6/reference/en-US/html/performance.html#performance-cache).
4.  [Caching with Rails: An overview, section 1.5](http://guides.rubyonrails.org/caching_with_rails.html)
5.  [Cache Money](https://github.com/nkallen/cache-money)

# Contributors

Benjamin Truitt
Mark Morga
Dave Slotnick

Mark Morga
