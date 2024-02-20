# Versions Repository
Welcome to the Versions repository, where we keep track of version streams for various upstream projects. This repository is instrumental in managing and automating the lifecycle of software versions, ensuring that projects are up-to-date and secure.

## What is a Version Stream?
A version stream consists of a set of versions that an upstream project maintains concurrently. For instance, Java maintains version streams like Java 8, 11, and 17, with some versions eventually reaching end-of-life (EOL), such as Java 14 and 15.

## How it Works
We have a daily cron job that checks for version stream specifications in this repository and fetches related version data from the endoflife.date API. This automation helps us maintain the latest and most accurate version information for various software, contributing to the broader opensource ecosystem's health and security.

## Adding a Version Stream Specification
Contributing to the repository involves adding a version stream specification. Here's how you can add a new spec:

```yaml
apiVersion: versions.wolfi.dev/v1
kind: VersionStream
metadata:
  name: <project-name>
spec:
  endOfLife:
    identifier: <eol-identifier>
  versionStreamFormat: ${{name}}-${{versions.major}}.${{versions.minor}}
```

Replace <project-name> with the name of the project and <eol-identifier> with the corresponding product name used when querying the endoflife.date API.

## Alternatives
Manual Update: If the version data cannot be automatically fetched, a manual specification can be used, requiring manual updates to the `.status` fields.
```yaml
kind: VersionStream
metadata:
  name: <project-name>
spec:
  manual: true
  versionStreamFormat: ${{name}}-${{versions.major}}.${{versions.minor}}
```
URI Override: For fetching data from an alternative end-of-life server, uriOverride can be specified.
```yaml
kind: VersionStream
metadata:
  name: <project-name>
spec:
  endOfLife:
    identifier: <eol-identifier>
    uriOverride: https://customeol.date
  versionStreamFormat: ${{name}}-${{versions.major}}.${{versions.minor}}
```

## Cron Jobs
### Version Data Update
A cron job runs daily to replace version stream status fields with data from endoflife.date or manually if specified.

### Version Stream Monitoring
Another cron job checks for missing new version stream versions against package names in Wolfi, creating issues and PRs for new version streams or when they're nearing or have reached EoL.

## Customisations
To tell the Version Stream Monitoring bot to ignore creating issues for missing version streams use `ignoreMissingVersions`:
```yaml
spec:
  endOfLife:
    identifier: python
  ignoreMissingVersions:
  - "3.8"
  - "3.9"
```
To tell the Version Stream Monitoring bot to ignore creating EOL issues, useful for when EOL packages can live in wolfi, for say bootstrapping, use `ignoreEolVersions`:
```yaml
spec:
  endOfLife:
    identifier: redhat-build-of-openjdk
  ignoreEolVersions:
  - "7"
```
## Example Contributions
Adding a missing product to endoflife.date: [Example PR](https://github.com/endoflife-date/endoflife.date/pull/4256/files)

Manually updating version data: [Example PR](https://github.com/endoflife-date/endoflife.date/pull/4568/files)