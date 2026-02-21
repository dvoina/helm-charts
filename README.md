# helm-charts
Some general purpose helm charts 

## Publishing charts to GHCR (OCI)

This repository includes a GitHub Actions workflow that packages the `ckan` chart and publishes it to GitHub Container Registry (GHCR) as an OCI artifact: [.github/workflows/helm-publish.yml](.github/workflows/helm-publish.yml)

Prerequisites
- Helm 3.12.0+ installed locally for manual testing (matches CI).
- If you have an older Helm, either upgrade (recommended) or set `HELM_EXPERIMENTAL_OCI=1` when using older Helm builds.
- The workflow uses the repository `GITHUB_TOKEN` (no extra secret required) and requires the `packages: write` permission for Actions. The workflow file already sets this permission.

Quick local test (optional)
```sh
# If needed, install or upgrade Helm to match CI (v3.12.0):
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod +x get_helm.sh
./get_helm.sh --version v3.12.0

# Package and push the chart (replace <OWNER> and credentials):
helm package ckan --destination ./charts
VERSION=$(grep '^version:' ckan/Chart.yaml | awk '{print $2}')
echo "$GHCR_TOKEN" | helm registry login ghcr.io -u "<USER>" --password-stdin
helm chart save ckan oci://ghcr.io/<OWNER>/ckan:${VERSION}
helm chart push oci://ghcr.io/<OWNER>/ckan:${VERSION}
```

Notes
- The workflow runs on pushes to `main` that change files under `ckan/` or when manually triggered.
- If you need to publish under a different GHCR repository or name, update the workflow reference in `.github/workflows/helm-publish.yml`.
