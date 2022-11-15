# action-delete-release-candidates

GitHub Action to delete release candidates and corresponding artefacts.
This is a composite action that combines other actions, like:

- wow-actions/delete-stale-releases
- aws-actions/configure-aws-credentials

This action is used to automate the removal of release candidates that are typically created from pull requests.

## Inputs

| Input                 | Description                                                                                                                                                                                                      | Required | Default        |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|----------------|
| release-identifier    | A string that will be used to identify the releases to be deleted (eg. "pr1234").                                                                                                                                | true     |                |
| ecr-name              | The name of an ECR where to look for container images tagged with the releases we are deleting. The name is not the full URL, it is just the last bit of the URL.                                                | false    |                |
| s3-bucket             | The name of a S3 bucket where to look for artefacts tagged with the releases we are deleting.                                                                                                                    | false    |                |
| s3-object-key-prefix  | The S3 object key prefix used to look for artefacts tagged with the releases we are deleting.                                                                                                                    | false    |                |
| aws-access-key-id     | The AWS access key ID providing access to delete artefacts from either or both ECR and S3 Bucket.                                                                                                                | false    |                |
| aws-secret-access-key | The runtimes where to deploy the release.                                                                                                                                                                        | false    |                |
| aws-region            | The AWS region.                                                                                                                                                                                                  | false    | 'eu-central-1' |

## Outputs

| Output   | Description                                    |
|----------|------------------------------------------------|
| releases | The releases that were identified and deleted. |

## Usage

The main use-case for this action is to remove releases from GitHub.
In addition to that the action can also remove related objects from S3 and container images from ECR.

### Remove GitHub Releases

The following workflow configuration removes GitHub releases when a pull request is closed.

```yaml
name: Delete Release Candidates


on:
  pull_request:
    types: [ closed ]


jobs:
  delete-release-candidates:
    name: Delete Release Candidates
    runs-on: ubuntu-latest
    steps:
      - uses: GRESB/action-delete-release-candidates
        with:
          release-identifier: '-pr${{ github.event.number }}-rc'
```

## Remove GitHub Releases and S3 Artefacts

The following workflow configuration removes GitHub releases and corresponding artefacts from S3 when a pull request is
closed.

```yaml
name: Delete Release Candidates and S3 Artefacts


on:
  pull_request:
    types: [ closed ]


jobs:
  delete-release-candidates:
    name: Delete Release Candidates
    runs-on: ubuntu-latest
    steps:
      - uses: GRESB/action-delete-release-candidates
        with:
          release-identifier: '-pr${{ github.event.number }}-rc'
          s3-bucket: 'my-bucket'
          s3-object-key-prefix: 'my-app/packages'
          aws-access-key-id: ${{ secrets.aws-access-key-id }}
          aws-secret-access-key: ${{ secrets.aws-secret-access-key }}
```

## Remove GitHub Releases and ECR Container Images

The following workflow configuration removes GitHub releases and corresponding container images from ECR when a pull
request is closed.

```yaml
name: Delete Release Candidates and ECR Container Images


on:
  pull_request:
    types: [ closed ]


jobs:
  delete-release-candidates:
    name: Delete Release Candidates
    runs-on: ubuntu-latest
    steps:
      - uses: GRESB/action-delete-release-candidates
        with:
          release-identifier: '-pr${{ github.event.number }}-rc'
          ecr-name: 'my-app-registry'
          aws-access-key-id: ${{ secrets.aws-access-key-id }}
          aws-secret-access-key: ${{ secrets.aws-secret-access-key }}
```
