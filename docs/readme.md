# Documentation

## How the build works

`.github/workflows/build.yml` runs one matrix job per image. Each job checks out
`weebo-si/<image>` at the requested ref, builds with the fork's own Dockerfile and pushes to
`ghcr.io/weebo-si/<image>`, then signs the digest with cosign (keyless, GitHub OIDC).

| Image | Dockerfile | Notes |
|-------|------------|-------|
| che-server | `build/dockerfiles/Dockerfile` | Maven assembly built first (`mvn install -DskipTests -Pfast`), then copied into the context |
| che-operator | `Dockerfile` | `SKIP_TESTS=true`, tests run in the fork's CI |
| che-dashboard | `build/dockerfiles/Dockerfile` | |

No build cache is used: the workflow publishes images, so caches are a poisoning vector.
Actions are pinned to commit SHAs (enforced by `task lint` / zizmor).

## Triggers

- Nightly at 03:00 UTC on `develop`
- Push to `main` touching the workflow
- Manual: `task images:build REF=<ref> PLATFORMS=linux/amd64,linux/arm64`

## Tags

- `<ref>` with `/` replaced by `-` (default: `develop`)
- `sha-<7 chars>`: commit of the fork that was built

## Verify a signature

```bash
cosign verify ghcr.io/weebo-si/che-server:develop \
  --certificate-identity-regexp '^https://github.com/weebo-si/che-images/' \
  --certificate-oidc-issuer https://token.actions.githubusercontent.com
```

## Use in a CheCluster

```yaml
spec:
  components:
    cheServer:
      deployment:
        containers:
          - image: ghcr.io/weebo-si/che-server:develop
    dashboard:
      deployment:
        containers:
          - image: ghcr.io/weebo-si/che-dashboard:develop
```

The operator image replaces the one in the `che-operator` Deployment (Helm `image` value or OLM subscription override).

## First run

GHCR packages created by `GITHUB_TOKEN` start **private**. After the first build, set each
package to public (org → Packages → package → Settings) or give the cluster a pull secret.
