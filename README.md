Developer Connect
## Where is the book repo?
https://github.com/pivotal-cf/docs-book-devconnect

The book repo uses these branches:

* **edge** builds from the **master** content branch in this repo.
Pipeline [here]().

* **master** builds from the published content branches in this repo.
Pipeline [here](https://concourse.run.pivotal.io/teams/cf-docs/pipelines/cf-services?group=devconnect).

## Branches in this (content) repo

All documentation for the next unreleased version of Developer Connect is in `master`.

Always make changes you want carried forward in the master branch. This includes:

* Unreleased features
* Doc bug fixes
* Doc reorganization or enhancement

| Branch Name| Use for… |
|------------| ---------|
| master (current work)    | v0.1 (staged here: http://docs-pcf-staging.cfapps.io/developer-connect/0-alpha/|

When the latest alpha version is ready to publish, merge the master branch into an alpha branch.
Keep all the alpha versions of the release notes stacked in the [Release Notes for Pivotal Developer Connect](http://docs-pcf-staging.cfapps.io/developer-connect/0-n/release-notes.html) page.

1. Always cherry-pick any changes to live branches into **master** if you want those changes carried forward.
2. If necessary, immediately cherry-pick/copy changes from **master** that you want to push immediately to production into the appropriate live branch above."

### Style Sheet

We need to decide on product name short forms:

| Term or product name | Notes |
|----------------------|-------|
| Developer Connect | `product_full`, the longest name |

## Pipelines

[//]: # "**Edge Pipeline**<br>
The `master` branch builds to the <br> <strong>cf-services-edge > developer-connect-edge</strong> pipeline, and does not go to production until release time: [Edge pipeline](). <br>"

**Production Pipeline**<br>
All live branches build to the <strong>cf-services > developer-connnect</strong> pipeline,
and are manually pushed to production as needed: [Production pipeline](https://concourse.run.pivotal.io/teams/cf-docs/pipelines/cf-services?group=devconnect).

## How to Cherry-Pick from one Branch to Another
1. Make changes in the first branch (usually `master`), commit them, and then push them to the repo.
2. Copy part of the SHA for the above commit. To find this, you can do a `git log`, or look at the list of commits in the github repo.
3. Checkout the second branch, where you want to copy the changes you made in the first branch.
4. Run this command, using the SHA snippet you copied above:
    `git cherry-pick <SHA_SNIPPET>`<br><br>
    For example: `git cherry-pick 5dc22fe00`

    Do the cherry-pick immediately to lessen the chances of conflicts.
    Otherwise, you may need to resolve conflicts in order to complete the cherry-pick.

5. Do a `git push` after the cherry-pick is complete.<br><br>
