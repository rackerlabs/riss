# Issue Guide - Rackspace Internal Service Standards

## Overview
For this project, issues on GitHub represent actionable work that should be done at some future point. It may be as simple as a small typo fix or as large as tracking the need to create a new guideline, standard or recipe. However, it should be work that falls under the [mission statement of the project](https://github.com/rackerlabs/riss/README.md).

## Issue format & content
For issues that are meant to request an enhancement or addition (see Labels, below), we'd like the issue to contain the following details:
1. A short description of the proposed change
2. Rationale: A paragraph or two justifying why the change will benefit the project
3. Details (optional): if you want to do so, you can also provide additional details that may find their way into a pull request once the issue is approved (see Labels, below).
4. Open Questions (optional): any questions that you feel should be discussed on the issue.
5. Pull Request (optional): If a pull request is created, you can link to it from the issue in this section
6. Updates: As you update an existing issue, keeping a running change log in this section is desirable.

## When we close issues
There are few major reasons that we might close an issue:

1. Issues unrelated to the [mission statement of the project](https://github.com/rackerlabs/riss/README.md).
2. Issues that are specific to OpenStack API guidelines. Since this project is targeting APIs that are not related to OpenStack, we'll direct you to the [OpenStack API Working Group](http://specs.openstack.org/openstack/api-wg/).
3. Nebulous issues. Issues that are not sufficiently specific or clear will be closed.
4. Sometimes after debate, we'll decide an issue isn't a good fit for the project. In that case, we'll also close it. Because of this, we ask that you don't start working on an issue until it's tagged with "up for grabs" or "issue approved". We'd hate for you to spend time and effort working on a change we'll ultimately be unable to take.

## Labels

We use GitHub labels on our issues in order to classify them. We have the following categories per issue:

* **Type**: These labels classify the type of issue. We use the following types:
  * [formatting](https://github.com/rackerlabs/riss/labels/formatting): Formatting or typo related issues
  * [enhancement](https://github.com/rackerlabs/riss/labels/enhancement): Issues related to an existing guideline, standard or recipe that improves it, but do not add new documents
  * [addition](https://github.com/rackerlabs/riss/labels/addition): Issues related to a request for a new guideline, standard or recipe. If approved, at least one new document will be created.
* **Ownership**: These labels are used to specify who owns specific issue. Issues without an ownership tag are still considered "up for discussion" and haven't been approved yet. We have the following different types of ownership:
  * [up for grabs](https://github.com/rackerlabs/riss/labels/up-for-grabs): Small sections of work which we believe are well scoped. These sorts of issues are a good place to start if you are new. Anyone is free to work on these issues.
  * [approved](https://github.com/rackerlabs/riss/labels/approved): Larger scale issues. Like up for grabs, anyone is free to work on these issues, but they may be trickier or require more work.
  * [grabbed by community](https://github.com/rackerlabs/riss/labels/grabbed-by-community): Someone outside the Rackspace Enterprise Architecture team has assumed responsibility for addressing this issue and is working on a fix. The comments for the issue will call out who is working on it. You shouldn't try to address the issue without coordinating with the owner.
  * [grabbed by assignee](https://github.com/rackerlabs/riss/labels/grabbed-by-assignee): Like grabbed by community, except the person the issue is assigned to is making a fix. This will be someone on the Rackspace Enterprise Architecture team.

## Assignee
We assign each issue to a Rackspace Enterprise Architecture team member. In most cases, the assignee will not be the one who ultimately fixes the issue (that only happens in the case where the issue is tagged "grabbed by assignee"). The purpose of the assignee is to act as a point of contact between the Rackspace Enterprise Architecture team and the community for the issue and make sure it's driven to resolution. If you're working on an issue and get stuck, please reach out to the assignee (just at mention them) and they will work to help you out.
