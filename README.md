# SquidStack Release

CloudBees Unify release workflow for SquidStack application.

## Overview

This repo contains the Unify release workflow that orchestrates deployments of all SquidStack components. The workflow is triggered by the modular StackSphere release flow when a component build completes on main.

## Components

The SquidStack application consists of 8 components:

- **squid-auth** - Authentication service (Postgres + Liquibase)
- **squid-ui** - User interface (React app with Feature Management)
- **squid-reef-asia** - Asia region service
- **squid-reef-eu** - EU region service
- **squid-feeds** - Feeds service
- **squid-reef-na** - North America region service
- **squid-assets** - Assets service
- **squid-catalog** - Catalog service

## Release Flow

1. Component CI completes (e.g., squid-ui on main branch)
2. Component triggers `application-release` workflow
3. Application-release runs modular actions:
   - **release-resolver**: Discovers baseline artifact versions
   - **manifest-renderer**: Builds Unify manifest with resolved versions
   - **release-executor**: Creates and executes Unify release
4. Unify release workflow (this repo) deploys all components

## Workflow

`.cloudbees/workflows/release.yaml` - Unify release workflow

### Inputs

- `environment`: Target deployment environment (dev, integ, preprod, prod)
- `manifest`: JSON manifest with component versions and artifacts

### Stages

1. **Deploy Components**: Parallel deployment of all 8 components

## Usage

This workflow is triggered automatically by the release-executor action. Manual trigger:

```yaml
environment: integ
manifest: |
  {
    "squid-auth": {
      "version": "2026.03.10.1-abc123",
      "artifactId": "...",
      "digest": "sha256:..."
    },
    "squid-ui": {
      "version": "2026.03.12.1-def456",
      "artifactId": "...",
      "digest": "sha256:..."
    },
    ...
  }
```

## Architecture

Part of the StackSphere ARCH 0.5 modular release design:
- **Repo-per-component** model
- **Baseline + Bump** version resolution
- **Unify-native** release orchestration
