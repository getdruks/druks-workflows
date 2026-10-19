# druks-workflows

The shared GitHub workflows that Druks repositories call.

## release-app

`release-app` releases a Druks app from a version tag. The release holds the wheel
of the app and the wheels of its dependencies that Druks does not ship.

The workflow does these steps:

1. It builds the wheel of the app.
2. It makes sure that the tag is `v` followed by the version in `pyproject.toml`.
3. It resolves the requirements of the app against the lock of the Druks release.
4. It downloads the wheels of the packages that the lock does not have.
5. It installs the app from these wheels beside that Druks release, then runs
   `druks check-app`.
6. It creates the GitHub release with the wheel of the app, its `.sha256`, and the
   dependency wheels.

If a dependency has no wheel for Linux x86_64, the release fails.

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
      druks-version: "0.11.0"
```

`app` is the name that `druks check-app` takes. `druks-version` is the Druks release
that the app runs on.

Pin `v1`. We move the `v1` tag on each compatible change.
