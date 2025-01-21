[![GitHub Workflow - Build and Test Status](https://img.shields.io/github/actions/workflow/status/pyTooling/download-artifact/.github%2Fworkflows%2FArtifactsDownload.yml?branch=dev&logo=githubactions)](https://GitHub.com/pyTooling/download-artifact/actions/workflows/ArtifactsDownload.yml)
[![Sourcecode License](https://img.shields.io/badge/code-MIT%20License-green?longCache=true&style=flat-square&logoColor=fff)](LICENSE.md)

# Artifact Download Action with File Permission Preservation

This composite action, based on [`actions/download-artifact`](https://github.com/actions/download-artifact) and
extracting the artifact's content from a tarball, will preserve file attributes like **file permissions**. This
essential capability is not implemented by GitHub until now (requested on [05.12.2019](https://github.com/actions/upload-artifact/issues/38))
and still delayed and/or refused? to be implemented in the future. According to GitHub, the internal API doesn't allow
the implementation of such a feature, but this actions is demonstrating a working solution.

📤 See [pyTooling/upload-artifact](https://github.com/pyTooling/upload-artifact) for the matching upload action.


## Usage

```yaml
jobs:
  MyJob:
    steps:
      - name: 📥 Download artifact
        uses: pyTooling/download-artifact@v4
        with:
          name: binary

      - name: 📥 Download artifact
        uses: pyTooling/download-artifact@dev
        with:
          name: documentation
          path: public

      - name: 📥 Download artifact
        uses: pyTooling/download-artifact@dev
        with:
          pattern: unittest-*
          path: reports
```


### Input Parameters

| Parameter        | Required | Default                    | Description                                                                                                                                                                                                                                                                                                 |
|------------------|:--------:|----------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`           |    no    | `''`                       | Name of the artifact to download.<br>If unspecified, all artifacts for the run are downloaded.                                                                                                                                                                                                              |
| `path`           |    no    | `$GITHUB_WORKSPACE`        | Destination path. Supports basic tilde expansion.                                                                                                                                                                                                                                                           |
| `pattern`        |    no    |                            | A glob pattern to the artifacts that should be downloaded.<br>Ignored if `name` is specified.                                                                                                                                                                                                               |
| `merge-multiple` |    no    | `false`                    | When multiple artifacts are matched, this changes the behavior of the destination directories.<br> If true, the downloaded artifacts will be in the same directory specified by path.<br> If false, the downloaded artifacts will be extracted into individual named directories within the specified path. |
| `github-token`   |    no    |                            | The GitHub token used to authenticate with the GitHub API.<br> This is required when downloading artifacts from a different repository or from a different workflow run.<br> If unspecified, the action will download artifacts from the current repo and the current workflow run.                         |
| `repository`     |    no    | `${{ github.repository }}` | The repository owner and the repository name joined together by "/".<br> If github-token is specified, this is the repository that artifacts will be downloaded from.                                                                                                                                       |
| `run-id`         |    no    | `${{ github.run_id }}`     | The id of the workflow run where the desired download artifact was uploaded from.<br> If github-token is specified, this is the run that artifacts will be downloaded from.                                                                                                                                 |
| `tarball-name`   |    no    | [^1]                       |                                                                                                                                                                                                                                                                                                             |
| `investigate`    |    no    | `false`                    | If enabled, show the downloaded artifact's directory content as a tree.                                                                                                                                                                                                                                     |

[^1]: `'__pyTooling_upload_artifact__.tar'`


### Output Parameters

| Parameter       | Description                                          |
|-----------------|------------------------------------------------------|
| `download-path` | Absolute path where the artifact(s) were downloaded. |


## Fixed behavior compared to `actions/download-artifact`

1. **Preserve file permissions**  
   The artifact's content is collected in a tarball, which allows preserving file attributes like file permissions.
2. **Don't remove common prefix from files**  
   `actions/upload-artifact` removes the common prefix from all files before storing in an artifact. This is not a
   well-defined behavior. Slightly changing the list of collected files might drastically change the directory structure
   of the artifact.  
   This action defines a root directory from where the content of the tarball is constructed. This is independent of the
   list of provided file patterns.

## Further Features

* Accepts artifacts uploaded from `actions/upload-artifact` (legacy artifacts) and `pyTooling/upload-artifacts`.

## Dependencies

* [actions/download-artifact@v4](https://github.com/actions/download-artifact)

## Competing Actions

* [eviden-actions/download-artifact](https://github.com/eviden-actions/download-artifact)
* [nmerget/download-gzip-artifact](https://github.com/nmerget/download-gzip-artifact)
* [alehechka/download-tartifact](https://github.com/alehechka/download-tartifact)

## Contributors

* [Patrick Lehmann](https://GitHub.com/Paebbels) (Maintainer)
* [Sven Köhler](https://GitHub.com/skoehler)
* [and more...](https://GitHub.com/pyTooling/upload-artifact/graphs/contributors)

### Credits

**This action was inspired by and is based on:**
 * [actions/upload-artifact#38 - upload-artifact does not retain artifact permissions (08. Sep. 2024)](https://github.com/actions/upload-artifact/issues/38#issuecomment-2336484584)
 * [Gist: 
rcdailey/download-tar-action.yml](https://gist.github.com/rcdailey/cd3437bb2c63647126aa5740824b2a4f)


## License

This GitHub Composite Action (source code) licensed under [The MIT License](LICENSE.md).

---

SPDX-License-Identifier: MIT
