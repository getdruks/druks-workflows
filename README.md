# druks-workflows

The shared GitHub workflows that Druks repositories call.

## release-app

`release-app` releases a Druks app from a version tag. The release holds the wheel
of the app and its `.sha256`.

The workflow does these steps:

1. It builds the wheel of the app.
2. It makes sure that the tag is `v` followed by the version in `pyproject.toml`.
3. It installs the wheel with its dependencies in a new environment, then runs
   `druks check-app`.
4. It creates the GitHub release with the wheel and its `.sha256`.

Call it from `.github/workflows/release.yml` in the app repository:

```yaml
name: Release

on:
  push:
    tags: ["v*"]

permissions:
  contents: write

jobs:
  release:
    uses: getdruks/druks-workflows/.github/workflows/release-app.yml@v1
    with:
      app: site_builder
```

`app` is the name that `druks check-app` takes.

Pin `v1`. We move the `v1` tag on each compatible change.

## house-rules

`house-rules` holds a pull request to the rules of `druks-house-rules`. It
installs the package from its repository, then runs `druks-house-rules check`
against the base branch with the pull request title and body. The check reads
`druks-house-rules.toml` in the repository root.

Call it from `.github/workflows/house-rules.yml` in the repository:

```yaml
name: House rules

on:
  pull_request:

permissions:
  contents: read

jobs:
  house-rules:
    uses: getdruks/druks-workflows/.github/workflows/house-rules.yml@v1
```

`ref` is the git ref of `druks-house-rules` to install; it defaults to the
current release tag.
