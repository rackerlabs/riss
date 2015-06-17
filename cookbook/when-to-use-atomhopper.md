# When to Use AtomHopper

# Problem

1.  You are unsure if you should access your resources from your main service or from [Atom Hopper](http://atomhopper.org/).
2.  You have chosen Atom Hopper for your feeds, but you are unsure if you should self-host it or use the internal hosted service.

# Solution

## Problem 1

Ask yourself the following questions:

1.  Is the resource I want to expose consist of a sequential series of items that are added to over time?
2.  Can the items in the resource I want to expose expressible as XML or JSON?
3.  Do I need to expose this resource to an undetermined number of consumers?
4.  Do I want to decouple the consumers of this resource from the general consumers of my service?
5.  Do I want to off-load the access of this resource from the workload on my service?
6.  Is it preferable for my resource to be requested on demand (rather than pushed to listeners)?

If you have answered "Yes" to these questions, then Atom Hopper is the correct tool to use for your resource.

## Problem 2

Ask yourself the following questions:

1.  Am I willing to host Atom Hopper (in a servlet container) on my server's deployment systems (or provision systems to host it on)?
2.  Are my requirements such that hosted Atom Hopper is inappropriate (SLA requirements, due to data retention policies, schema requirements or other)?

If you answered "Yes" to both of those questions then you should consider hosting Atom Hopper. Otherwise, you should use the internal hosted offering.

# Discussion

Atom Hopper use within Rackspace is emerging as a [REST](https://en.wikipedia.org/wiki/REST)ful [Message Queue](https://en.wikipedia.org/wiki/Message_queue). Atom Hopper implements [AtomPub](http://www.atompub.org/) (itself a RESTful API).

The advantage of using Atom Hopper over other message queues (such as AMQP) is the fact that Atom Hopper:

-   Supports Rackspace standard RESTful interaction.
-   Supports Rackspace authentication via Repose.
-   Is language agnostic and can be freely used by any programming language and framework.

## About Hosted Atom Hopper

The Hosted Atom Hopper team has a particular target customer in mind. If your proposed use of Atom Hopper differs too much from this intended customer you might be better off hosting your own Atom Hopper instance. The target customer of Hosted Atom Hopper has (see note below):

-   Several consumers of the feed.
-   Atom entries are represented as XML.
-   The entry XML has a strong explicit schema and that schema does not change often.
-   The entry representation does not have a large number of variations.
-   The consumers of the feed need no more than three days of history in the feed.

This information is based on conversations with Joe Savak on 5/8/2014 and is subject to change over time.

# Good and Bad Practices

## Good Practices

1.  Be explicit with the schema you are exposing as the body of your Atom entries (define a schema).
2.  Off-load stream oriented resources to Atom Hopper.

## Bad Practices

1.  Using Atom Hopper for a list of items that change over time (for example, earlier entries change in place or are removed from the stream).
2.  Using Atom Hopper for streams that are only consumed internally by a service or have requirements for extremely high speed delivery or extremely high event rate.
3.  Using Atom Hopper for streams that need to be consumed via push (synchronous) rather than pull (subscribed).

# Unresolved Issues

None.

# Frequently Asked Questions

*What is Atom Hopper/AtomPub?*

Atom Hopper is a stand-alone feed service that implements the AtomPub Protocol. AtomPub is a RESTful protocol that implements a RESTful API to interact with [Atom](http://tools.ietf.org/html/rfc4287) feeds.



# References

1.  [Atom Hopper](http://atomhopper.org/) (public site)
2.  [The Atom Syndication Format](http://tools.ietf.org/html/rfc4287)
3.  [Atom Publishing Protocol](http://www.atompub.org/)
4.  [Cloud Feeds FAQ](http://www.rackspace.com/knowledge_center/article/cloud-feeds-faq)

# Contributors

* Mark Morga
* Marco Cuellar
