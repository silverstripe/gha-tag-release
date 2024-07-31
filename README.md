# GitHub Actions - Tag release

Create a tag and an optional release

## Usage

**workflow.yml**
```yml
permissions: {}

jobs:
  tagrelease:
    # ...
    permissions:
      contents: write
    steps:
      - name: Create tag and release
        uses: silverstripe/gha-tag-release@v1
        with:
          skip_gauge_release: true
          tag: 1.2.3
          release: false
```

### Inputs

### Latest local sha
Required if not skipping gauge release.

The result of `$(git rev-parse HEAD)`.

`latest_local_sha: f22dbc6ec6118096c8ccccee1ca0074bfb2f2291`

### Skip gauge release
Whether to skip gauging the release. Gauging the release will only allow tagging if all of the following are true:

- The branch this action is run against is a patch branch (e.g. `1.2`)
- The `latest_local_sha` input matches the `github.sha` GitHub actions variable
- The `latest_local_sha` input matches the current latest commit sha on this branch
- There's an existing stable semver patch tag for this branch already (i.e. for branch `1.2` there must be a `1.2.<digit>` tag)
- There are commits on the branch that warrant a new patch release and weren't included in the latest patch release for this branch

Gauging the release also identifies the correct next patch tag, and uses that to tag the release. Default is false, enable with:

`skip_gauge_release: true`

#### tag
Required if skipping gauge release. Cannot be provided if _not_ skipping gauge release.

The tag to create e.g. `1.2.3`.

#### delete_existing
Cannot be provided if _not_ skipping gauge release.

Whether to delete any existing tags or releases that match tag if they exist. Default is false, enable with:

`delete_existing: true`

#### release
Whether to create a coresponding release that matches tag

#### release_description
The description text used for the release - format with markdown

#### release_auto_notes
Whether to use the github API to auto generate the release which will be appended to `release_description`. Default is false, enable with:

`release_auto_notes: true`

## Why there is no SHA input parameter when not using gauge release

Creating a tag for a particular SHA, either via the GitHub API or via CLI (i.e. git tag) in an action is strangely blocked. The error is "Resource not accessible by integration" which is a permissions error.

However, tags can be created with the following methods:

- Using `${{ github.sha }}` which is the latest sha in a context instead of historic sha
- Creating a release via GitHub API, which will also create a tag. While it's tempting to just use this and then delete the release, it's seems possible that this may stop working in the future

The following methods have been attempted:

- Using third party actions to create tags
- Passing in `permissions: write-all` from the calling workflow
- Passing in a github token from the calling workflow

It's likely that `${{ github.sha }}` will be good enough though - the intention is that this action will be used to tag the _most recent commit_ on a given branch, e.g. after a pull request is merged.
