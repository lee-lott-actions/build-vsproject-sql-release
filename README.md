# Build Visual Studio SQL Project Release Action

This GitHub Action builds a Visual Studio SQL project, creates a DACPAC, packages it into a zip file, and creates a GitHub release with a semantic version tag and release notes. The action streamlines the build and release automation for SQL Server Data Tools (`.sqlproj`) projects, with support for both standard and pre-release versioning.

---

## Features
- Builds a Visual Studio SQL project (`.sqlproj`) using MSBuild.
- Generates a DACPAC (`.dacpac`) and packages all build outputs into a versioned zip file.
- Creates a GitHub release with automated release notes and uploads the zip artifact.
- Supports pre-release versioning with a custom suffix.
- Adds a job summary with release details.
- Records the release date and time in Central Daylight Time (CDT).

---

## Inputs

| Name                | Description                                                                 | Required |
|---------------------|-----------------------------------------------------------------------------|----------|
| `project-file-path` | The path to the Visual Studio SQL project file (`.sqlproj`) to be built.    | Yes      |
| `prerelease`        | Mark the release as a pre-release (`true`/`false`).                         | Yes      |
| `prerelease-suffix` | Suffix to append to the version number for pre-releases. For non-pre-releases, the version is based on the latest pre-release with this suffix. | Yes      |
| `package-name`      | The base name for the zip file containing the build artifacts.              | Yes      |
| `configuration`     | The build configuration to use (e.g., Debug, Release).                      | Yes      |
| `token`             | The GitHub token used for authentication to create the release.             | Yes      |

---

## Outputs

| Name            | Description                                                                          |
|-----------------|--------------------------------------------------------------------------------------|
| `version-tag`   | The new tag created for the release (e.g., `v1.0.0`).                                |
| `version-number`| The new tag with the "v" prefix removed (e.g., `1.0.0`).                             |
| `zip-file-name` | The name of the zip file containing build artifacts (e.g., `my-package.1.0.0.zip`).  |
| `zip-file-path` | The path to the zip file containing the build objects.                               |
| `build-status`  | The build/release outcome: Release Created, No Release, or Release Failed.           |

---

## Usage

**Important**:  
- Run this action on a `windows-latest` runner (for MSBuild and Data Tools compatibility).
- Designed to be run on merged pull requests or other release triggers.

### Example Workflow

```yaml
name: Build and Release SQL Project

on:
  pull_request:
    types: [closed]

jobs:
  build-and-release:
    if: github.event.pull_request.merged == true
    name: Create Release
    runs-on: windows-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v6

      - name: Build SQL Project and Create Release
        uses: lee-lott-actions/build-vsproject-sql-release@v1
        with:
          project-file-path: 'src/MyDatabaseProject/MyDatabaseProject.sqlproj'
          prerelease: 'false'
          prerelease-suffix: 'beta'
          package-name: 'my-database'
          configuration: 'Release'
          token: ${{ secrets.GITHUB_TOKEN }}
```

---

## Notes

- The action creates a DACPAC and zips all published files for attachment to the release.
- The release tag and notes are automatically generated, with a summary added to the workflow run.
- For pre-releases, a git tag is created but no GitHub Release is published; for standard releases, both are created and the zip file is attached.
- The action records the release time in Central Daylight Time (CDT).
- Intended for standard Visual Studio SQL Server Database Projects.

---
