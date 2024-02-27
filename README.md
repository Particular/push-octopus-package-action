# push-octopus-package-action

Pushes a release package to Octopus Deploy.

## Prerequisites

Before this action runs:

* NuGet packages must have been created in the root-level `nugets` directory.
* Any additional assets to be included in the GitHub release (like app executables) must be created in the root-level `assets` directory.
* The following environment variables must have been set during the build by [Particular.Packaging](https://github.com/Particular/Particular.Packaging):
    * `MinVerVersion`
    * `MinVerMajor`
    * `MinVerMinor`

## Usage

### Basic usage

```yaml
    steps:
      - name: Deploy
        uses: Particular/push-octopus-package-action@v2.0.0
        with:
          octopus-deploy-api-key: ${{ secrets.OCTOPUS_DEPLOY_API_KEY }}
```

### Additional metadata

This is used primarily for [ServiceControl](https://github.com/Particular/ServiceControl/blob/master/.github/workflows/release.yml). One or more additional file patterns can be specified, one per line, that will be included in the root of the deployment package. Encoding this data as JSON files with `ConvertTo-Json` in the workflow and `ConvertFrom-Json` on the deployment server is recommended.

```yaml
    steps:
      - name: Deploy
        uses: Particular/push-octopus-package-action@v2.0.0
        with:
          octopus-deploy-api-key: ${{ secrets.OCTOPUS_DEPLOY_API_KEY }}
          additional-metadata-paths: metadata/*.json

```

```yaml
    steps:
      - name: Deploy
        uses: Particular/push-octopus-package-action@v2.0.0
        with:
          octopus-deploy-api-key: ${{ secrets.OCTOPUS_DEPLOY_API_KEY }}
          additional-metadata-paths: |
            metadata/*.json
            other-directory/*.txt
```

## How it works

This action abstracts the following steps:

1. Arranging NuGet packages and other assets on disk for packaging
1. Creating a metadata file to pass version information to Octopus Deploy
1. Packaging the content into a RepoName.Deploy package
1. Publishing an `octopus-package` artifact to GitHub Actions
1. Pushing the deployment package to Octopus
1. Creating the Octopus Deploy release

Because release workflows cannot be easily tested in each repo, combining these tasks into a single action allows running through CI using a fake "release" so that PRs can be merged with confidence.

## License

The scripts and documentation in this project are released under the [MIT License](LICENSE.md).
