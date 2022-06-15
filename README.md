# GitHub Actions - Tag release

Create a tag and an optional release

## Usage

**workflow.yml**
```yml
steps:
  - name: Create tag and release
    uses: silverstripe/gha-tag-release@v1
    with:
      tag: 1.2.3
      release: true
      delete_existing: true
      release_description: "Some description about the 1.2.3 release"
      is_prerelease: false
```

## Why there is no SHA input paramater

Creating a tag for a particular SHA, either via the GitHub API or via CLI (i.e. git tag) in an action is strangely blocked. The error is "Resource not accessible by integration" which is a permissions error.

However, tags can be created with the following methods:
- Using `${{ github.sha }}` which is the latest sha in a context instead of historic sha
- Creating a release via GitHub API, which will also create a tag. While it's tempting to just use this and then delete the release, it's seems possible that this may stop working in the future

The following methods have been attempted:
- Using third party actions to create tags
- Passing in `permissions: write-all` from the calling workflow
- Passing in a github token from the calling workflow

It's likely that `${{ github.sha }}` will be good enough though - the intention is that this action will be used to tag the _most recent commit_ on a given branch, e.g. after a pull request is merged.
