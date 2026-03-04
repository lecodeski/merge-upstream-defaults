# Merge Upstream

Merge changes from an upstream repository branch into a current repository branch. For example, updating changes from the repository that was forked from. If branch arguments are omitted it uses the default branches as configured in the respective repositories.

Current limitations:

- only merge only selected branch
- only work with public upstream Github repository
- merge fast forward only (--ff-only)

To merge multiple branches, create multiple jobs.

To run action for another repository, create a [personal access token (PAT)](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token)

```yaml
      - name: Merge Upstream with Defaults
        uses: lecodeski/merge-upstream-defaults@v1
        with:
          upstream: ${{ github.event.inputs.upstream }}
          upstream-branch: ${{ github.event.inputs.upstream-branch }} # Optional: If omitted, it will use the default branch as configured in the upstream repo. 
          branch: ${{ github.event.inputs.branch }} # Optional: If omitted, it will use the default branch as configured in the target repo.
          token: ${{ secrets.TOKEN }} # Optional: If omitted, defaults to ${{ github.token }}.
```

## Usage

### Set up for scheduled trigger

```yaml
name: Scheduled Merge Remote Action
on:
  schedule:
    - cron: '0 0 * * 1'
    # scheduled for 00:00 every Monday

jobs:
  merge-upstream-defaults:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          ref: upstream             # set the branch to merge to
          fetch-depth: 0
      - name: Merge Upstream
        uses: lecodeski/merge-upstream-defaults@v1
        with:
          upstream: owner/repo      # set the upstream repo
          upstream-branch: master   # set the upstream branch to merge from, optional
          branch: upstream          # set the branch to merge to, optional

  # set up another job to merge another branch
  merge-upstream-defaults-another-branch:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          ref: another-branch       # set the branch to merge to
          fetch-depth: 0
      - name: Merge Upstream
        uses: lecodeski/merge-upstream-defaults@v1
        with:
          upstream: owner/repo              # set the upstream repo
          upstream-branch: another-branch   # set the upstream branch to merge from, optional
          branch: another-branch            # set the branch to merge to, optional

```

Reference:

- [Triggering a workflow with events](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#triggering-a-workflow-with-events)
- [Creating a composite run steps action](https://docs.github.com/en/actions/creating-actions/creating-a-composite-run-steps-action)

## How to run this action manually

This action can trigger manually as needed.

1. Go to `Actions` at the top of your Github repository
2. Click on `Manual Merge Upstream Action` (or other name you have given) under `All workflows`
3. You will see `Run workflow`, click on it
4. Fill in the upstream repository and branch to merge from, and the branch to merge to (⚠️ double check all are correct)
5. Click `Run workflow`
6. Check your branch commit history

### Set up for manual trigger

copy and commit this to `.github/workflows/merge-upstream-defaults.yml` in your default branch of your repository.

```yaml
name: Manual Merge Remote Action
on:
  workflow_dispatch:
    inputs:
      upstream:
        description: 'Upstream repository owner/name. Eg. lecodeski/merge-upstream-defaults'
        required: true
        default: 'owner/name'       # set the upstream repo
      upstream-branch:
        description: 'Upstream branch to merge from. Eg. master'
        required: false
        default: 'master'           # set the upstream branch to merge from, optional
      branch:
        description: 'Branch to merge to'
        required: false
        default: 'upstream'         # set the branch to merge to, optional

jobs:
  merge-upstream-defaults:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          ref: ${{ github.event.inputs.branch }}
          fetch-depth: 0
      - name: Merge Upstream
        uses: lecodeski/merge-upstream-defaults-defaults@v1
        with:
          upstream: ${{ github.event.inputs.upstream }}
          upstream-branch: ${{ github.event.inputs.upstream-branch }}
          branch: ${{ github.event.inputs.branch }}
          token: ${{ secrets.TOKEN }}
```
