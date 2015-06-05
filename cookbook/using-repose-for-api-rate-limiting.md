# Using Repose for API Rate Limiting

Early draft - not official

This is a first draft, and has not been reviewed or accepted by the Enterprise Architecture team. It is not yet official or recommended.

# Problem

You want to know how to use Repose to achieve rate limiting in your API implementation.

# Solution

### Deployment Topology

Repose can be deployed in several ways. Please refer to the [repose documentation](https://repose.atlassian.net/wiki/display/REPOSE/Installation#Installation-Deploymentoptions) for details on the various options [1]. For rate limiting purposes, it does not matter which deployment option you choose to use.

To get the maximum benefit for the [repose rate limiting filter](https://repose.atlassian.net/wiki/display/REPOSE/Rate+Limiting+filter), use [distributed rate limiting](https://repose.atlassian.net/wiki/display/REPOSE/Rate+Limiting+filter#RateLimitingfilter-Distributedratelimitingconfiguration)  [2, 3].  This allows your repose instances to share limits, offering the best protection to your underlying compute resources.

### Rate Limit Types

The Repose Rate Limiting Filter offers several types of rate limits.  Please refer to the [documentation](https://repose.atlassian.net/wiki/display/REPOSE/Rate+Limiting+filter) for details [2].

Use at least a combination of one or more [global rate limits](https://repose.atlassian.net/wiki/display/REPOSE/Rate+Limiting+filter#RateLimitingfilter-Globalratelimits), one or more user-level rate limits.  Optionally, add one or more group level rate limits, IP address level rate limits, etc.

## Discussion

The purpose of rate limiting it to protect the compute resources consumed by your application so that they are not overwhelmed by incoming requests, and to ensure a fair distribution of responsiveness among users of your application.

Combining global and user-level rate limits provides the basic ability to support these goals.

Global level rate limits ensure that the application will not see more requests per unit of time than it is prepared to handle.  The specific thresholds and configuration values for your global rate limit(s) should be determined by performance testing and application monitoring analysis.

Global rate limits on their own will prevent the application from becoming generally unresponsive, but will not ensure a fair distribution of resources amongst your users. To achieve the goal of fairness, you'll need to use user level rate limits.  This will ensure that one aggressive or misbehaving user is not hogging the compute resources of your application.

You may wish to add additional rate limit features to your repose configuration such as group level rate limits, IP address rate limits, etc.  For discussion about the benefits of these options rate limit configurations, please refer to the documentation for the [repose rate limiting filter](https://repose.atlassian.net/wiki/display/REPOSE/Rate+Limiting+filter).  Here is a high-level summary of the currently available features:

Rate Limit Option
Used To...
User-level rate limits
Prevent any one user from hogging resources, starving other users from benefitting from the application's capabilities in a fair way.
Group-level rate limits
Prevent a group of users from hogging resources
Global rate limits
Prevent the application from receiving more requests per unit of time than it is capable of processing
IP-level rate limits

-   Rate limit a particular client that cannot otherwise be uniquely identified (e.g. because multiple clients share a user)
-   Rate limit a group of clients that share an incoming IP address

Query-parameter rate limits
limit queries that place a large burden on your backend systems

# Good and Bad Practices

## Good Practices

1.  Configure your repose instances to do distributed rate limiting so that rate limits are shared.
2.  Configure repose to return HTTP 429 response code (rather than 413), because [RFC 6585](http://tools.ietf.org/html/rfc6585) created this code specifically for rate limiting purposes.
3.  Always configure a global rate limit based on performance data to ensure that your application cannot become overwhelmed by incoming requests.
4.  Create user-level rate limits to ensure fair distribution of responsiveness of your application among your user base.
5.  Consider adding additional rate limit features as dictated by the specifics of your application (e.g. consider using the query-parameter rate limiter option to prevent too many expensive queries from being requested in a short period of time)

## Bad Practices

# Unresolved Issues

1.  We should provide guidance about where to put the rate limit filter in the list of repose filters, since it depends on other repose filters having been run first.

# Frequently Asked Questions

The following are frequently asked questions whose answers are worth collecting in one place.

*Example Question?*

Example answer.

# References

1. https://repose.atlassian.net/wiki/display/REPOSE/Installation#Installation-Deploymentoptions
2. https://repose.atlassian.net/wiki/display/REPOSE/Rate+Limiting+filter
3. https://repose.atlassian.net/wiki/display/REPOSE/Rate+Limiting+filter#RateLimitingfilter-Distributedratelimitingconfiguration
4. https://repose.atlassian.net/wiki/display/REPOSE/Rate+Limiting+filter#RateLimitingfilter-Globalratelimits

# Contributors

Ben Truitt

## Attachments:

[repose-toplogy.png](../images/repose-topology.png) (image/png)
