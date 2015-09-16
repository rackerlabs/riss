---
layout: article
title: "Account and Device References"
tags: account device references uris
authors:
    - name: Mark Morga
    - title: Principal Architect
---
# Account and Device References

## Problem

It is desirable to reference Accounts and Devices in representations by identifier (rather than URL). How should those identifiers be represented.

## Guidelines

1. References to account and device entities should include a URL link to those entities in addition to the URI identifier as described here.

## Standards

### Account Number URIs

1. Account numbers are represented as a URI in the form: `urn:{account-namespace-identifier}:{account-tenant-number}`. For example: `urn:rax-dedicated:987349`.
2. The Account Namespace Identifier must correspond to one of the Namespace Identifiers in the table below.
 

## Discussion

### Account URI Namespace Identifiers

|Account Type | Namespace Identifier (NID) | Account Example | Device Example |
|-------------|----------------------------|-----------------|----------------|
|Rackspace Dedicated     | rax-dedicated    | urn:rax-dedicated:987349    | urn:rax-dedicated:987349:68763482 |
|Rackspace Public Cloud  | rax-public-cloud | urn:rax-public-cloud:987349 | urn:rax-public-cloud:987349:c931cdde-45da-4a4e-a1aa-13ba5f8424bd |
|Microsoft Azure         | azure            | urn:azure:8274343           | urn:azure:8274343:67834234 |
|Amazon Web Services     | aws              | urn:aws:2652398723          | urn:aws:2652398723:98634123 |
|OpenStack               | openstack        | urn:openstack:example.com:9283465  | urn:openstack:example.com:9283465:3455ae20-d856-4c70-8e8f-084535f4873f |

The NSS (namespace specific string) for accounts is simply the account (tenant) number for the account type.

An exception is OpenStack instances. Here, the NSS is the instance domain plus tenant number.

### Device Number URIs

Devices should be referred to by URI as well.

The NSS for devices is: `{account-nss}:{device-identifier}`. The device identifier is the (string, integer or UUID) as used in that system.

## Good and Bad Practices

### Good Practices

1. Use URIs to specify and account or device in APIs.
2. In response representations include URL links to accounts and devices in the representation in addition to URN style URI reference.

### Bad Practices

1. Referencing an account or device by a numeric value only.

## Unresolved Issues


## Frequently Asked Questions



## References

1. See: [RFC 2141](http://tools.ietf.org/html/rfc2141) for an explanation of URN style URIs.
