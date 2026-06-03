# devops-engineering-ci-public-multi-arch-action

Reusable composite GitHub Action for creating and retagging multi-architecture image manifests.

## Overview

This action takes the architecture-specific images produced earlier in a workflow, creates a version-tagged multi-arch manifest, then applies the additional engineering and release tags that should point at that manifest.

It is the fan-in step used after separate amd64 and arm64 image builds complete.

For inbound and outbound dependency relationships, see [docs/WHO-CALLS-WHAT.md](docs/WHO-CALLS-WHAT.md).

## Action

### `action.yml`

Key responsibilities:

- Logs in to Azure Container Registry and Harbor.
- Creates the version-tagged manifest from the amd64 and arm64 image tags.
- Pushes the version manifest to ACR.
- Mirrors the engineering tag to Harbor.
- Retags the manifest for any additional engineering tags.
- Retags release repositories when `is_release` is `true` and the module is included in `release_modules`.

## Important inputs

- `version`: final version tag for the manifest.
- `updated_tags`: comma-separated tags to apply after the version manifest is created.
- `amd_tag` and `arm_tag`: architecture-specific image tags to combine.
- `module`: image repository path below the registry prefix.
- `release_modules`: comma-separated list used to decide whether release retagging should occur.
- `acr` and `harbor`: registry hosts.
- `registry_username` and `registry_password`: ACR credentials.
- `harbor_username` and `harbor_cli_secret`: Harbor credentials.
- `engineering_prefix` and `releases_prefix`: target registry prefixes.
- `is_release`: release gate for release retagging.

## Typical flow

```text
arch-specific images already exist
	-> create version manifest in ACR
	-> mirror engineering manifest to Harbor
	-> apply extra engineering tags
	-> optionally apply release tags in ACR and Harbor
```

## How this repo fits the wider build stack

- Upstream: the action is usually called by `devops-engineering-ci-public-build-multi-arch-workflow` and `devops-engineering-ci-public-build-platform-specialised-image-workflow`.
- Downstream: it only depends on the previously built per-architecture images being present in the registry.
- Scope: this repo handles manifest creation and retagging, not Docker builds.