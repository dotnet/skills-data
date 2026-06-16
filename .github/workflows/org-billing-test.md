---
name: "Org Billing Test"
description: >
  Dummy agentic workflow used to verify that organization-level Copilot billing
  is enabled for the dotnet org. It runs on same-repo pull requests using the
  built-in GITHUB_TOKEN (no personal access token / PAT pool), relying solely on
  the `copilot-requests: write` permission for org-billed authentication.
on:
  pull_request:
    types: [opened, synchronize, reopened]

# Belt-and-suspenders: never run on forked-repo PRs. With `pull_request`, gh-aw
# already blocks forks by default (forks: same-repo only), but a forked PR would
# also lack the read-write GITHUB_TOKEN this test depends on.
if: ${{ !github.event.pull_request.head.repo.fork }}

permissions:
  contents: read
  pull-requests: read
  # The key line under test: granting `copilot-requests: write` lets the Copilot
  # engine authenticate with ${{ github.token }} and bill inference directly to
  # the organization — no PAT required. If org-level billing is NOT enabled for
  # the dotnet org, the agent job should fail to authenticate / be billed.
  copilot-requests: write

engine:
  id: copilot

safe-outputs:
  add-comment:
    max: 1
---

# Org Billing Test

You are a dummy test agent. Your only purpose is to confirm that this agentic
workflow was able to run and authenticate to Copilot using **organization-level
billing** via the built-in `GITHUB_TOKEN` (no personal access token).

## Task

1. Read the title and number of the pull request that triggered this workflow.
2. Post a single comment on the pull request that confirms the agent executed
   successfully. The comment must include:
   - A short confirmation that org-level Copilot billing appears to be working
     (because the agent ran without a PAT).
   - The PR number and title you read.
   - The current UTC date/time as reported by the run.

Keep the comment brief and friendly. Do not take any other action.
