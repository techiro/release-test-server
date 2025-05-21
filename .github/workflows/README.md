# GitHub Actions Workflows

This directory contains GitHub Actions workflows for syncing content between repositories.

## Workflow Files

### 1. `sync-to-release-test.yml`

This workflow is triggered when changes are made to the `release-server.md` file in the `techiro/release-test-server` repository. It sends a repository_dispatch event to the `techiro/release-test` repository.

**Requirements:**
- A secret named `REPO_ACCESS_TOKEN` must be set in the repository settings with a GitHub personal access token that has the `repo` scope and access to both repositories.

### 2. `receive-server-updates.yml` (to be placed in techiro/release-test repository)

This workflow is triggered by the repository_dispatch event from the `techiro/release-test-server` repository. It:
1. Runs the `make sync server-release` command
2. Commits any changes to the `release-server.md` file
3. Creates a pull request with these changes

**Requirements:**
- The repository must have a Makefile with a `sync server-release` target that fetches the latest `release-server.md` from the server repository.
- GitHub Actions must have permission to create branches and pull requests.

## Setup Instructions

1. Add the `REPO_ACCESS_TOKEN` secret to the `techiro/release-test-server` repository settings.
2. Place the `receive-server-updates.yml` file in the `.github/workflows` directory of the `techiro/release-test` repository.
3. Ensure the `techiro/release-test` repository has a Makefile with the appropriate sync command.