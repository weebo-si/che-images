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
| che-code | `build/dockerfiles/assembly.Dockerfile` | Separate jobs, as upstream: `linux-musl`, `linux-libc-ubi8` and `linux-libc-ubi9` are built in parallel, then assembled. `linux/amd64` only |

### Other artifacts

| Artifact | Build | Published as |
|----------|-------|--------------|
| JetBrains Gateway plugin | `./gradlew buildPlugin` (JDK 21) in `weebo-si/devspaces-gateway-plugin` | Pre-release `weebo-gateway-plugin-<ref>` of this repository, replaced on every build: `weebo-gateway-plugin.zip`, its cosign bundle `weebo-gateway-plugin.zip.sigstore.json`, the exact source built `weebo-gateway-plugin-source.tar.gz`, `LICENSE`, `THIRD-PARTY-NOTICES.txt` and `THIRD-PARTY-LICENSES.md` |

The plugin's `develop` merges `chore/weebo-branding`: own plugin ID `io.github.weebo-si.gateway`,
name and vendor, no Red Hat icon, artifact `weebo-gateway-plugin`, and the third-party notices in
the jar (`META-INF/third-party`). The source archive is attached because `develop` is force-pushed:
the commit it was built from can disappear from the fork. Install it from disk in Gateway, after uninstalling the Red Hat "OpenShift Dev
Spaces" plugin: both handle the same Gateway links.

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

Plugin:

```bash
gh release download weebo-gateway-plugin-develop -R weebo-si/che-images
cosign verify-blob weebo-gateway-plugin.zip \
  --bundle weebo-gateway-plugin.zip.sigstore.json \
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

che-code is not set in the CheCluster: it is the image of the editor definition (the
`che-code-injector` init container and the editor runtime). Point a custom editor definition at
`ghcr.io/weebo-si/che-code:develop`.

The operator image replaces the one in the `che-operator` Deployment (Helm `image` value or OLM subscription override).

## First run

GHCR packages created by `GITHUB_TOKEN` start **private**. After the first build, set each
package to public (org → Packages → package → Settings) or give the cluster a pull secret.
