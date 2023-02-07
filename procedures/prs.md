---
title: Pull requests
parent: Procedures
---

# How to open a PR (pull request)

## Prerequisites

- If you don't have a [GitHub](https://www.github.com) account, you'll need to create one
- If haven't contributed to Kytos-ng before, you'll need to <a href="https://docs.google.com/forms/d/1N-Cuq4L904j7fbxjJMpNY1s4JJMLAifq21Uwi7NwMKY">digitally sign</a> the <a href="../license/cla.html">CLA</a>
- Make sure that there's an existing aligned issue that will be linked in the PR

## General guidelines

- Contributions are more than welcome
- Small PRs are encouraged instead of large ones
- Breaking changes, architectural or significant changes that can impact Kytos-ng ecosystem should be discussed and aligned first with core members
- The branch name typically follows this pattern `[feature|fix|hotfix|doc|release|misc]/<name>`

## Submitting a PR

### PR checklist:

- Make sure linters and unit tests are passing with `tox`
- Summarize the PR title and add a tag `[feature|fix|hotfix|doc|release|misc] <title>` accordingly
- Optionally, depending on the impact of your change, it might be helpful to also run [kytos-end-to-end-tests](https://github.com/amlight/kytos-end-to-end-tests) and include a summary of the results in a `### End-to-end Tests` subsection.

The following template is encouraged to be used:

```
Closes #issue_number

### Summary

See updated changelog file and/or add any other summarized helpful information for reviewers

### Exploratory Tests

### End-to-End Tests
```

- Closes `#<issue_number` is for automatically closing the issue that this PR addresses
- `### Summary` will typically be `See updated changelog file` or also include additional helpful information for reviewers
- `### Exploratory Tests` subsection is for documenting and explaining how you've validated and explored your change locally with `kytosd` in addition to shipping unit tests
- `### End-to-end Tests` subsection is optional depending on the impact of the change

That's it. Once your PR is submitted make sure to keep an eye on your PR notifications for any follow ups, once it's approved, a core team member will merge it.

Thanks for contributing to Kytos-ng.
