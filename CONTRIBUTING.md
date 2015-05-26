# Contributing Guide - Rackspace Internal Service Standards

## Workflow

We welcome contributions to our standards. Please follow these guidelines to make it easier to accept your contributions. We use and recommend the following workflow:

1. Create an issue for your work.
    - You can skip this step for trivial changes.
    - Reuse an existing issue on the topic, if there is one.
    - Get agreement from the team and the community that your proposed change is a good one.
    - If your change adds a new guideline, standard or recipe, follow the [Review Process](Review-Process.md).
    - Clearly state that you are going to take on implementing it, if that's the case. The issue filer and the implementer don't have to be the same person.
2. Create a personal fork of the repository on GitHub (if you don't already have one).
3. Create a branch off of master (`git checkout -b mybranch`).
    - Name the branch so that it clearly communicates your intentions, such as issue-123 or githubhandle-issue.
    - Branches are useful since they isolate your changes from incoming changes from upstream. They also enable you to create multiple PRs from the same fork.
4. Make and commit your changes.
    - Place your contribution file in an appropriate sub-directory of this project (see Project Organization below).
    - Name your file meaningfully in lowercase with dashes and `.md` as extension.
    - Use GitHub Flavored Markdown for your file format.
    - Include diagrams where appropriate. Diagrams are required to be in the PNG or JPG format but we encourage the inclusion of SVG format to make editing easier.
    - For new pages, use other pages as models to follow for your contributions.
    - For new cookbook recipes, use the [cookbook recipe template](cookbook/recipe-template.md)
    - Please following our commit messages guidance, below.
5. Create a pull request (PR) against the upstream repository's **master** branch.
    - Push your changes to your fork on GitHub (if you haven't already).

Note: It is OK for your PR to include a large number of commits. Once your change is accepted, you will be asked to squash your commits into one or some appropriately small number of commits before your PR is merged.

## Project Organization

This project is organized as follows:

```
+ https://github.com/rackerlabs/riss
|   \-api-review       - API Review Guide and Scorecard
|   \-communication-and-planning
|                      - Required communication and planning roadmap
|   \—cookbook         - Recipes for common service patterns
|   \—documentation
|                      — Standards and Guidelines for RAML documentation,
|                        documentation generation, where to put documentation,
|                        communication and planning
|   \-img              - diagrams and images
|   \—language-framework
|                      - Standards Guidelines specific to programming
|                        languages and frameworks
|   \—monitoring       - Standards for service monitoring
|   \—performance      - Standards for service performance
|   \—quality          - Standards for service quality and testing
|   \—references       - References
|   \—representation-design
|                      - Standards and Guidelines for HATEOAS, schema design,
|                        JSON & XML design, media types, and versioning
|   \—resource-Design  - Standards and Guidelines for resource design
|                        Resource types, routes (URLs), nesting,
|                        proper use of HTTP
|   \—security         - Standards for service security and compliance
|   \—service-design   - Standards and Guidelines for overall service design
|   \—templates        - Templates for new pages
```

## Issues

If you are looking at getting your feet wet with some simple (but still beneficial) changes, check out our [up for grabs Issues](https://github.com/rackerlabs/riss/labels/up-for-grabs). You do not need to file an issue for trivial changes (e.g. typo fixes). Just send us
a PR if it's tiny.

Don't feel obliged to follow every issue with a PR. Simply filing issues for problems you
encounter is a great way to contribute too!

### DOs and DON'Ts

* **DO** keep the discussions focused. When a new or related topic comes up it's often better to create new issue than to side track the discussion.
* **DO** blog and tweet (or whatever) about your contributions, frequently!
* **DON'T** surprise us with big pull requests. Instead, file an issue and start
  a discussion so we can agree on a direction before you invest a large amount
  of time.
* **DON'T** submit PRs that alter licensing related files or headers. If you believe there's a problem with them, file an issue and we'll be happy to discuss it.
* **DON'T** add guideline, standard or recipe additions without filing an issue and discussing with us first. See [Review Process](Review-Process.md).

## Making a change

There are several issues to keep in mind when making a change.

### Typos

Typos are embarrassing! We will accept most PRs that fix typos. In order to make it easier to review your PR, please focus on a given document with your fixes or on one type of typo across the entire repository. If it's going to take 30 mins to review your PR, then we will probably ask you to chunk it up.

### Style

We strive to ensure that our standards, guidelines and recipes conform to a consistent style.  Therefore, please use the appropriate [template](/templates) as your starting point when creating a new document.

#### Commit Messages

Please format commit messages as follows (based on this [excellent post](http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)):

```
Summarize change in 50 characters or less

Provide more detail after the first line. Leave one blank line below the
summary and wrap all lines at 72 characters or less.

If the change fixes an issue, leave another blank line after the final
paragraph and indicate which issue is fixed in the specific format
below.

Fix #42
```

Also do your best to factor commits appropriately, i.e not too large with unrelated things in the same commit, and not too small with the same small change applied N times in N different commits. If there was some accidental reformatting or whitespace changes during the course of your commits, please rebase them away before submitting the PR.

## PR Feedback

Team and community members will provide feedback on your change. Community feedback is highly valued. You will often see the absence of team feedback if the community has already provided good review feedback.

Based on the size and impact of the change, and your history with this project, 1-2 core members will review every PR prior to merge.
