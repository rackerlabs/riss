# Thoughts on SOA Standards

## What and Why

The reasons why we have chosen to establish standards:

1. Lack of standards result in services that have poor usability
2. Lack of standards contribute to slower service development
    - The same problems are solved over and over (and in different ways)
    - Clients require more cognitive loading to learn how to use existing services
    - Lots of bugs
3. Allows developers to worry about the hard stuff instead of re-inventing the wheel
4. Avoids standards existing only as an [oral tradition](https://en.wikipedia.org/wiki/Oral_tradition)
6. Education: explain what REST means to developers and where REST supports many possible solutions establishes what solution we've selected as a company

## Emergent Standards

Where possible we should look at our set of both internal and [OpenStack](http://www.openstack.org/) services and extract common solutions to use as our standards where:

1.  There is a concensus
2.  The concensus does prevent us from creating solutions required for internal services

We do this because:

1.  It leverages the thoughtfulness that has already applied to our existing services
2.  It reduces the amount of work needed to be done to our existing services to bring them up to date with standards
3.  It reduces the potential friction to overcome when evangelizing the SOA standards

## Basic REST documentation

We want to cover basic concepts in REST (such as HTTP verb usage) because we have observed developers misusing these basic concepts.

Not everyone has had the opportunity to learn REST the way we have. A key goal for us should be educating Rackspace developers about REST and how to correctly apply REST concepts in real-world code.

## Legacy Services

It would be foolish to require changes to legacy services once our standards are established. Instead, legacy services should be [grandfathered-in](https://en.wikipedia.org/wiki/Grandfather_clause). Legacy Services product owners should establish a roadmap to adopt service standards across one or more planned upcoming releases. Pragmaticism should play a significant role in this road map.

## Service Scoring

Our service standards should permit the creation of an Objective Scoring mechanism for a service. This scoring mechanism should take the contract for a service and in either an automated or manual way produce a report of variation between a service and the SOA standards and produce a prioritized work list of changes needed to reach standards compliance. Exceptions to the standards will likely be found and we will need to take approved exceptions into account when scoring a service.
