# Fork Changes

This fork has been modified from the upstream `microsoft/playwright-dotnet` repository with the following changes:

## Purpose
The purpose of this fork is to:
1. Update the base Docker image to use .NET 9 SDK (noble)
2. Publish Docker images to GitHub Container Registry (GHCR) instead of Azure Container Registry
3. Disable all other GitHub Actions workflows to focus only on Docker builds

## Changes Made

### 1. Dockerfile Update
- **File**: `utils/docker/Dockerfile.noble`
- **Change**: Updated base image from `mcr.microsoft.com/dotnet/sdk:8.0-noble` to `mcr.microsoft.com/dotnet/sdk:9.0-noble`

### 2. GitHub Actions Workflows

#### Disabled Workflows
The following workflows have been disabled by changing their trigger branches to `disabled-branch-never-runs`:
- `tests.yml` - Main test suite
- `code-style.yml` - Code style checks
- `nuget-package-tests.yml` - NuGet package smoke tests
- `validate-nuget-packages.yml` - NuGet package validation
- `tests_harness.yml` - Test harness
- `publish_docker.yml` - Original Azure Container Registry publishing

#### Active Workflow
- **File**: `.github/workflows/test_docker.yml`
- **Renamed to**: "Build and Publish Docker Image to GHCR"
- **Changes**:
  - Updated to use .NET 9.0.x
  - Builds only the `noble` flavor (Ubuntu 24.04)
  - Publishes to GitHub Container Registry (`ghcr.io/abartrim/playwright-dotnet`)
  - Tags images as:
    - `ghcr.io/abartrim/playwright-dotnet:noble-latest`
    - `ghcr.io/abartrim/playwright-dotnet:latest`
  - Removed test execution steps (only builds and publishes)
  - Triggers on:
    - Push to main branch (when Dockerfile or related files change)
    - Pull requests to main
    - Manual workflow dispatch

## Usage

### Building Locally
To build the Docker image locally:
```bash
cd utils/docker
bash build.sh --amd64 noble playwright-dotnet:localbuild-noble
```

### Pulling from GHCR
To use the published image:
```bash
docker pull ghcr.io/abartrim/playwright-dotnet:latest
# or
docker pull ghcr.io/abartrim/playwright-dotnet:noble-latest
```

## Notes
- The workflow will only publish images on pushes to the `main` branch
- Pull requests will build the image but not publish it
- The workflow can be triggered manually via GitHub Actions UI (workflow_dispatch)
