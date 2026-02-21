# helm-charts
Some general purpose helm charts 

## Publishing charts to GHCR (OCI)

This repository includes a GitHub Actions workflow that packages the `ckan` chart and publishes it to GitHub Container Registry (GHCR) as an OCI artifact: [.github/workflows/helm-publish.yml](.github/workflows/helm-publish.yml)

Prerequisites
- Helm 3.8+ installed locally for manual testing.
- The workflow uses the repository `GITHUB_TOKEN` (no extra secret required) and requires the `packages: write` permission for Actions. The workflow file already sets this permission.

Quick local test (optional)
```sh
export HELM_EXPERIMENTAL_OCI=1
helm package ckan --destination ./charts
VERSION=$(grep '^version:' ckan/Chart.yaml | awk '{print $2}')
helm chart save ckan oci://ghcr.io/<OWNER>/ckan:${VERSION}
helm chart push oci://ghcr.io/<OWNER>/ckan:${VERSION}
```

Notes
- The workflow runs on pushes to `main` that change files under `ckan/` or when manually triggered.
- If you need to publish under a different GHCR repository or name, update the workflow reference in `.github/workflows/helm-publish.yml`.
