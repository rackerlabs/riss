# Review Process - Rackspace Internal Service Standards

We generally review every single PR (Pull Request). This page discusses how we conduct PR reviews.

## Process Goals

The key goals are:

* **Designed for GitHub**. In order to be sustainable and not be a hurdle for contributors the review process must feel natural to folks familiar with GitHub.

* **Transparency**. We use the same process for both internal as well as external contributors. This allows contributors to benefit from the results of reviews even if the implementer isn't external.

## Overall Process

GitHub is generally based around the pull-request model. The idea is that contributors perform their changes in their own fork and submit a pull request against our repository.

For trivial code changes, such as typo fixes, we want folks to directly submit a pull request rather than opening an issue. However, for bug fixes or feature work, we want contributors to first start a discussion by creating an issue.

[[img/Review-Process.png]]

## Steps

* **Contributor opens an issue**. The issue description should contain the details described in the [Issue Guide](Issue-Guide.md), under "*Issue format & content.*"

* **Community discusses the proposal**. If changes are necessary, the contributor is encouraged to edit the issue description. This allows folks joining later to understand the most recent proposal. To avoid confusion, the contributor should maintain a tiny change log, like a bolded "Updates:" followed by a bullet point list of the updates that were being made.

* **Issue is tagged as "Up For Grabs" or "Approved"**. Once the contributor and project owner agree on the overall shape and direction, the project owner tags the issue as either [up for grabs](https://github.com/rackerlabs/riss/labels/up-for-grabs), for small pieces of work which we believe are well scoped, or [approved](https://github.com/rackerlabs/riss/labels/approved) for larger efforts.

* **Changes made**. The contributor implements the changes as discussed. Minor deviations are OK, but if during the implementation the document being created starts to take a major shift away from what was discussed on the issue, the contributor is encouraged to go back to the issue and raise concerns regarding the current proposal.

* **Pull request created**. Once the contributor believes the implementation is ready for review, she creates a pull request, referencing the issue created in the first step. In order to call dibs, you can also create the PR before it's completely ready. Use checkboxes to indicate which areas are still missing so that we know it's not ready for review yet. [Here is a good example](https://github.com/dotnet/corefx/pull/316).

* **Pull request reviewed**. The community reviews the pull request. Once at least two project owners give their OK, the PR is considered good to go.

* **Pull request is merged**. When there are no issues - or the issues were addressed by the contributor, the PR is merged.
