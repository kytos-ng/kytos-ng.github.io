---
title: NApps release
parent: Procedures
---

# How to release NApps

## Overview

Currently, [kytos-ng](https://github.com/kytos-ng) NApps are hosted and released on GitHub, and each version follow this pattern `[year].[number].[patch]` that is set on a `git` tag. The quantity of releases depend on what is being planned for every year. The `[patch]` number should only be incremented when a patch is indeed needed.

## Finding NApps

If you're releasing a set of NApps of Kytos-ng that is about to be shipped, you can use GitHub web search filters or [ghs](https://github.com/amlight/ghs) to find which NApps have a specific label. For instance, if you were to filter for all Kytos-ng repos urls that had issues closed on Kytos-ng version `2022.3`:

```
ghs search 'org:kytos-ng is:issue label:2022.3 is:closed' | jq '.items[].html_url' | sed 's/issues.*/"/g' | sed 's/"//g' | sort | uniq
```

Alternatively, you could also search for kytos-ng repos that had PRs merged between certain dates, let's say for instance from Aug 22, 2022 to Dec 31 2022:

```
ghs search 'org:kytos-ng is:pr is:closed merged:2022-08-22..2022-12-31' | jq '.items[].html_url' | sed 's/pull.*/"/g' | sed 's/"//g' | sort | uniq
```

Once you have the repos you can visit the `CHANGELOG.rst` file to review the summary of the changes that haven't been released yet.

## Releasing NApps

When a NApp is ready to be released, you have to complete the following steps:

- Create a new PR consolidating the changes of the respective version in the `CHANGELOG.rst`, and updating the version in the `kytos.json` file, and ask for a review from core developers. For example, [here's](https://github.com/kytos-ng/flow_manager/pull/102) a previous PR that bumped a version of `flow_manager`.
- Once the PR is approved and gets merged, create a new release on GitHub in the respective repo. Also, make sure to create a new `git` tag with the same name as the version when publishing the release targeting `master` branch. For instance, [here's](https://github.com/kytos-ng/flow_manager/releases/tag/2022.2.0) an example of `flow_manager`.

## Announcing

Typically, the announcement of an release is submitted on kytos-ng Slack once the NApps and the core libraries that compose the entire version are released on GitHub.
