# A collection of reusable Github Actions and Workflows
A collection of reusable Github Actions and workflows used throughout GovWifi, so instead of maintaining 10 separate instances of code, we can reuse and maintain just the one copy.

## Ruby Updater Action
This workflow is designed to speed up the process of updating Ruby in our Docker containers.
It does this by trying to update the .ruby-version Dockerfile and GemfileLock, if this fails, it will remove the gemfile lock and recreate it.
We have about 10 repos that need constant updating, while this isn't 100% fool proof, it will fail if there are incompatibilities, it will at least help with the most common and easy updates.
This will create a branch and PR to review, fix and or merge as required.

use
```
# .github/workflows/upgrade-ruby.yml
name: Upgrade Ruby

on:
  workflow_dispatch:
  schedule:
    - cron: "0 0 15 * 0" # Runs monthly (in the middle to avoid any date clashes)

permissions:
  contents: write
  pull-requests: write

jobs:
  upgrade-ruby:
    runs-on: ubuntu-latest
    steps:
      - name: Check out code
        uses: actions/checkout@v4
      - name: run updater
        uses: govwifi/shared-actions-workflows/.github/actions/ruby-updater@main
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
```
## Checks not run.
PR's created by github tokens will not run further workflows, this is a design decision taken by Github.
See [Triggering Workflows](https://github.com/peter-evans/create-pull-request/blob/main/docs/concepts-guidelines.md#triggering-further-workflow-runs)
Workarounds include [PAT](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token), [SSH](https://github.com/peter-evans/create-pull-request/blob/main/docs/concepts-guidelines.md#push-using-ssh-deploy-keys) [Machine Account](https://github.com/peter-evans/create-pull-request/blob/main/docs/concepts-guidelines.md#push-pull-request-branches-to-a-fork) or [GitHub App](https://github.com/peter-evans/create-pull-request/blob/main/docs/concepts-guidelines.md#authenticating-with-github-app-generated-tokens)
At this stage these haven't been implemented by this action, we will need to make a decision on which direction to go if we want to do this
