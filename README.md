# Indy Shared GHA Components

This repository contains reusable workflows and actions used by the Indy projects.

## Actions

To use an action from this repository specify the `uses:` in the following manner:

Example:

```yaml
- name: get-release-info
   id: get-release-info
   uses: hyperledger-indy/indy-shared-gha/.github/actions/get-release-info@v0.1
   with:
      versionString: "${{ github.event.pull_request.body }}"
```

## Workflows

To use an workflow from this repository specify the `uses:` in the following manner:

Example:

```yaml
jobs:

  ...

  lint:
    name: Lint
    needs: [release-infos]
    if: needs.infos.outputs.isVersionBump == 'true'
    uses: hyperledger-indy/indy-shared-gha/.github/workflows/lint.yaml

  build-docker-image:
    name: Create Builder Image
    needs: [lint, release-infos]
    if: needs.infos.outputs.isVersionBump == 'true'
    uses: hyperledger-indy/indy-shared-gha/.github/workflows/buildimage.yaml
    with:
      CACHE_KEY_BUILD: ${{ needs.workflow-setup.outputs.CACHE_KEY_BUILD }}
      GITHUB_REPOSITORY_NAME: ${{ needs.workflow-setup.outputs.GITHUB_REPOSITORY_NAME }}
      UBUNTU_VERSION: ${{ needs.workflow-setup.outputs.UBUNTU_VERSION }}

  build_packages:
    name: Build Packages
    needs: [release-infos, plenum_tests]
    if: needs.infos.outputs.isVersionBump == 'true'
    uses: hyperledger-indy/indy-shared-gha/.github/workflows/buildpackages.yaml
    with:
      GITHUB_REPOSITORY_NAME: ${{ needs.workflow-setup.outputs.GITHUB_REPOSITORY_NAME }}
      UBUNTU_VERSION: ${{ needs.workflow-setup.outputs.UBUNTU_VERSION }}
      isDev: 'false'
      isRC: '${{ needs.infos.outputs.isPreRelease }}'
```
