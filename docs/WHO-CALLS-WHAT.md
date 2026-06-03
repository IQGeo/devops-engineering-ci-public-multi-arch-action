# Who Calls What

## Scope

This document shows the inbound and outbound dependency relationships for `devops-engineering-ci-public-multi-arch-action`.

## Matrix

| Direction | Component | Relationship | Notes |
| --- | --- | --- | --- |
| Called by | `devops-engineering-ci-public-build-multi-arch-workflow` | Inbound | Publishes manifests for module-style image builds |
| Called by | `devops-engineering-ci-public-build-platform-specialised-image-workflow` | Inbound | Publishes manifests for specialised platform and DevDB QA image builds |
| Consumes | Architecture-specific images in ACR | Internal | Expects amd64 and arm64 images to already exist before manifest creation |
| Writes to | Azure Container Registry and Harbor | Outbound | Creates version tags and applies additional engineering and release tags |

## Notes

- This repo is the manifest and retagging fan-in layer for multiple build flows.
- It depends on successful upstream architecture-specific image builds rather than source repositories.