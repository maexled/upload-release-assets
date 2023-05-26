# GitHub Action - Releases API

**Please note:** This is just to fix the original action for my specific use cases.
Feel free to use it as you wish or open an issue if you want/need additional behaviors.

If you want to make a pr be careful that it's not going to the original repo.

**This is meant to act as almost a direct replacement for the original that can take multiple files.**

If you are having any troubles, you can enable additional logs and info by setting `ACTIONS_STEP_DEBUG` to `true` in your secretes

This GitHub Action (written in JavaScript) wraps the [GitHub Release API](https://developer.github.com/v3/repos/releases/), specifically the [Upload a Release Asset](https://developer.github.com/v3/repos/releases/#upload-a-release-asset) endpoint, to allow you to leverage GitHub Actions to upload release assets.

[![Tests](https://github.com/maexled/upload-release-assets/actions/workflows/ci.yml/badge.svg)](https://github.com/maexled/upload-release-assets/actions/workflows/ci.yml)

## Usage

### Pre-requisites

Create a workflow `.yml` file in your repositories `.github/workflows` directory. An [example workflow](#example-workflow---upload-a-release-asset) is available below. For more information, reference the GitHub Help Documentation for [Creating a workflow file](https://help.github.com/en/articles/configuring-a-workflow#creating-a-workflow-file). You also will need to have a release to upload your asset to.

### Inputs

For more information on these inputs, see the [API Documentation](https://developer.github.com/v3/repos/releases/#input-2)

- `upload_url`: The URL for uploading assets to the release, which could come from the release trigger (`${{ github.event.release.upload_url }}`)
- `asset_path` can now take wildcards as well as an array of paths. Uses [@actions/glob](https://github.com/actions/toolkit/tree/main/packages/glob) so the same as [actions/upload-artifact](https://github.com/actions/upload-artifact)
- `asset_name` Will be ignored if there are multiple files
- `asset_content_type` is now optional

### Outputs

For more information on these outputs, see the [API Documentation](https://developer.github.com/v3/repos/releases/#response-for-successful-upload) for an example of what these outputs look like

- `browser_download_url`: An array of URLs users can navigate to in order to download the release assets. i.e. `https://github.com/octocat/Hello-World/releases/download/v1.0.0/example.zip`

### Example workflow - upload a release asset

On every `release` which was published [upload a release asset](https://developer.github.com/v3/repos/releases/#upload-a-release-asset).


If you want to create a release and upload artifacts to this release this action may fit better to you: [softprops/action-gh-release](https://github.com/softprops/action-gh-release)

```yaml
on:
  release:
    types:
      - published

name: Upload Release Asset

jobs:
  build:
    name: Upload Release Asset
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3
      - name: Build project # This would actually build your project, using zip for an example artifact
        run: |
          zip --junk-paths my-artifact README.md
      - name: Upload Release Asset
        id: upload-release-asset
        uses: maexled/upload-release-assets@v1.2.0
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          upload_url: ${{ github.event.release.upload_url }}
          asset_path: ./my-artifact.zip
          asset_name: my-artifact.zip
          asset_content_type: application/zip
```

This will upload a release artifact to an existing release, outputting the `browser_download_url` for the asset which could be handled by a third party service, or by GitHub Actions for additional uses. For more information, see the GitHub Documentation for the [upload a release asset](https://developer.github.com/v3/repos/releases/#upload-a-release-asset) endpoint.

## Contributing

We would love you to contribute to `maexled/upload-release-assets`, pull requests are welcome! Please see the [CONTRIBUTING.md](CONTRIBUTING.md) for more information.

## Acknowledgment

I would like to acknowledge the original repository, [sekwah41/upload-release-assets](https://github.com/sekwah41/upload-release-assets), from which this project was forked.

In this forked version, I have updated the dependencies, including the upgrade of `@actions/core` to version 1.10.0. This update addresses the deprecation message related to the `set-output` command, which will be disabled soon. For more information, please refer to the [GitHub Blog Changelog](https://github.blog/changelog/2022-10-11-github-actions-deprecating-save-state-and-set-output-commands/).

## License
The scripts and documentation in this project are released under the [MIT License](LICENSE)
