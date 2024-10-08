# GitHub Automatic Releases

This action simplifies the GitHub release process by automatically uploading assets, generating changelogs, handling pre-releases, and so on.

Original work by marvinpinto, updated and refactored by crowbarmaster and then modified for EMS-ESP.

## Contents

1. [Usage Examples](#usage-examples)
1. [Supported Parameters](#supported-parameters)
1. [Event Triggers](#event-triggers)
1. [Versioning](#versioning)
1. [How to get help](#how-to-get-help)
1. [License](#license)

## Usage Examples

### Automatically generate a pre-release when changes land on master

This example workflow will kick in as soon as changes land on `master`. After running the steps to build and test your project:

1. It will create (or replace) a git tag called `latest`.
1. Generate a changelog from all the commits between this, and the previous `latest` tag.
1. Generate a new release associated with the `latest` tag (removing any previous associated releases).
1. Update this new release with the specified title (e.g. `Development Build`).
1. Upload `LICENSE.txt` and any `jar` files as release assets.
1. Mark this release as a `pre-release`.

You can see a working example of this workflow over at [crowbarmaster/GHactions](https://github.com/crowbarmaster/GHactions/releases/tag/latest).

```yaml
---
name: "pre-release"

on:
  push:
    branches:
      - "master"

jobs:
  pre-release:
    name: "Pre Release"
    runs-on: "ubuntu-latest"

    steps:
      # ...
      - name: "Build & test"
        run: |
          echo "done!"

      - uses: "emsesp/GH-Automatic-Releases@latest"
        with:
          repo_token: "${{ secrets.GITHUB_TOKEN }}"
          automatic_release_tag: "latest"
          prerelease: true
          title: "Development Build"
          files: |
            LICENSE.txt
            *.jar
```

### Create a new GitHub release when tags are pushed to the repository

Similar to the previous example, this workflow will kick in as soon as new tags are pushed to GitHub. After building & testing your project:

1. Generate a changelog from all the commits between this and the previous [semver-looking](https://semver.org/) tag.
1. Generate a new release and associate it with this tag.
1. Upload `LICENSE.txt` and any `jar` files as release assets.

```yaml
---
name: "tagged-release"

on:
  push:
    tags:
      - "v*"

jobs:
  tagged-release:
    name: "Tagged Release"
    runs-on: "ubuntu-latest"

    steps:
      # ...
      - name: "Build & test"
        run: |
          echo "done!"

      - uses: "emsesp/GH-Automatic-Releases@latest"
        with:
          repo_token: "${{ secrets.GITHUB_TOKEN }}"
          prerelease: false
          files: |
            LICENSE.txt
            *.jar
```

## Supported Parameters

| Parameter               | Description                                                | Default   |
| ----------------------- | ---------------------------------------------------------- | --------- |
| `repo_token`\*\*        | GitHub Action token, e.g. `"${{ secrets.GITHUB_TOKEN }}"`. | `null`    |
| `draft`                 | Mark this release as a draft?                              | `false`   |
| `prerelease`            | Mark this release as a pre-release?                        | `true`    |
| `overwrite_tag`         | Do you aim to overwrite existing releases?                 | `true`    |
| `generate_notes`        | Do you wish to have release notes auto-generated?          | `true`    |
| `automatic_release_tag` | Tag name to use for automatic releases, e.g `latest`.      | `null`    |
| `title`                 | Release title; defaults to the tag name if none specified. | Tag Name  |
| `body`                  | Release body; defaults to changelog if none specified.     | Changelog |
| `files`                 | Files to upload as part of the release assets.             | `null`    |

## Outputs

The following output values can be accessed via `${{ steps.<step-id>.outputs.<output-name> }}`:

| Name                     | Description                                            | Type   |
| ------------------------ | ------------------------------------------------------ | ------ |
| `automatic_releases_tag` | The release tag this action just processed             | string |
| `upload_url`             | The URL for uploading additional assets to the release | string |
| `release_id`             | The ID that was returned for this release              | string |

### Notes

- Parameters denoted with `**` are required.
- The `files` parameter supports multi-line [glob](https://github.com/isaacs/node-glob) patterns, see repository examples.

## Event Triggers

The GitHub Actions framework allows you to trigger this (and other) actions on _many combinations_ of events. For example, you could create specific pre-releases for release candidate tags (e.g `*-rc*`), generate releases as changes land on master (example above), nightly releases, and much more. Read through [Workflow syntax for GitHub Actions](https://help.github.com/en/articles/workflow-syntax-for-github-actions) for ideas and advanced examples.

```yaml
- uses: "emsesp/GH-Automatic-Releases@<VERSION>"
```

## License

The source code for this project is released under the [MIT License](/LICENSE). This project is not associated with GitHub.
