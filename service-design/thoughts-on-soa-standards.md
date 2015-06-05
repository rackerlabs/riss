# Thoughts on SOA Standards

-   [What and why](#ThoughtsonSOAStandards-Whatandwhy)
-   [Emergent Standards](#ThoughtsonSOAStandards-EmergentStandards)
-   [Basic REST documentation](#ThoughtsonSOAStandards-BasicRESTdocumentation)
-   [Standards vs Cookbooks](#ThoughtsonSOAStandards-StandardsvsCookbooks)
    -   [Basic standards](#ThoughtsonSOAStandards-Basicstandards)
    -   [Recipes](#ThoughtsonSOAStandards-Recipes)
-   [Legacy Services](#ThoughtsonSOAStandards-LegacyServices)
-   [Service Scoring](#ThoughtsonSOAStandards-ServiceScoring)

## What and Why

The reasons why we need to do this are:

1.  Our lack of standards have resulted in a set of services that are difficult to use by clients
2.  Our lack of standards contribute to our slow speed of development of new services
    -   We solve the same problems over and over (in different ways)
    -   Clients take longer to learn how to use existing services
    -   It creates lots of bugs

3.  Developers are asking for it
4.  Much of our SOA standards exist only as an [oral tradition](https://en.wikipedia.org/wiki/Oral_tradition)
5.  Our current "Cookbook" approach leaves out too much
6.  The average (or even exceptional) developer at Rackspace doesn't understand REST

## Emergent Standards

Where possible we should look at our set of both internal and open stack services and extract common solutions to use as our standards where:

1.  There is a concensus
2.  The concensus does not have fundamental flaws that would weaken our SOA

We do this because:

1.  It leverages the thoughtfulness that has already applied to our existing services
2.  It reduces the amount of work needed to be done to our existing services to bring them up to date with standards
3.  It reduces the potential friction to overcome when evangelizing the SOA standards

## Basic REST documentation

We want to cover basic concepts in REST (such as HTTP verb usage) because we have observed developers misusing these basic concepts.

Not everyone has had the opportunity to learn REST the way we have. A key goal for us should be educating Rackspace developers about REST and how to correctly apply REST concepts in real-world code.

## Standards vs Cookbooks

I assert that rather than a cookbook we need a kitchen manual. A cookbook contains a set of recipes on how to cook specific dishes, but not the skills needed to cook (for example: how do you *sauté*?). We need to have a manual for building SOA services for the reason that a restaurant needs a set of standards for how dishes are cooked specific to that restaurant.

Cookbook?

Shouldn't the articles we've written be called recipes rather than cookbooks? The set of our *recipes* would be the cookbook.

So our standards should be broken up into the following categories:

### Basic standards

These are the raw materials of building services. To continue our cooking analogy, these are the standards that define the raw materials (or ingredients) of Rackspace services. These kinds of standards should be the same for any service - exceptions should be few and for very good reasons. 

We choose standards if there is a significant cost incurred when all services do not follow one way of doing something. And we should be able to articulate that cost for a service in an objective way.

These are the kinds of things that are covered in our basic standards:

1.  Use of HTTP verbs
2.  *TBD*

### Recipes

These take the ingredients of the basic standards and combine them into patterns to provide solutions for common problems.

A recipe contains one (or more) recommended way(s) of solving a certain problem. The intended use is to disseminate knowledge about good practices, tried and tested solutions for a problem.

## Legacy Services

It would be foolish to require changes to legacy services once our standards are established. Instead, legacy services should be [grandfathered-in](https://en.wikipedia.org/wiki/Grandfather_clause). Legacy Services product owners should establish a roadmap to adopt service standards across one or more planned upcoming releases. Pragmaticism should play a significant role in this road map.

## Service Scoring

Our service standards should permit the creation of an Objective Scoring mechanism for a service. This scoring mechanism should take the contract for a service and in either an automated or manual way produce a report of variation between a service and the SOA standards and produce a prioritized work list of changes needed to reach standards compliance. Exceptions to the standards will likely be found and we will need to take approved exceptions into account when scoring a service.

## Comments:

1. I like the word 'Recipe'.

    We should call something a standard if there is a significant cost incurred when all services do not follow one way of doing it. And we should be able to articulate that cost.

    In my opinion a recipe is a recommended way of solving a certain problem. The intended use is to the disseminate knowledge about good practices, likely tried and tested solutions when others are trying to solve the same problem.

    --Midhun Chandran

2. I agree that "recipe" is a better word to describe specific instructions on how to implement a standard.  However, I think in this case, the recipe should have more than the right way of implementing it.  It should also list the wrong way of implementing a standard, and the reasoning behind why it should be avoided.

    --Marco Cuellar

3. Is it possible to  put something for "what"? This will give some idea to developers and new entrants to understand what standard team is discussing here (like Architecture, Service, Governance etc.)

    -- diby1641
