# che-images

Builds the weebo-si forks of Eclipse Che and pushes them to GHCR, signed with Sigstore (cosign keyless).

Project bootstrapped from the [weebo-base](https://github.com/batleforc/weebo-base) template.

| Image | Source |
|-------|--------|
| `ghcr.io/weebo-si/che-server` | [weebo-si/che-server](https://github.com/weebo-si/che-server) |
| `ghcr.io/weebo-si/che-operator` | [weebo-si/che-operator](https://github.com/weebo-si/che-operator) |
| `ghcr.io/weebo-si/che-dashboard` | [weebo-si/che-dashboard](https://github.com/weebo-si/che-dashboard) |

## Quick start

```bash
task init                 # tools + git hooks
task images:build         # trigger a build of feat/forgejo-gitservice
task images:watch         # follow it
task images:verify        # check the cosign signatures
```

See [docs/readme.md](docs/readme.md) for tags, triggers and CheCluster usage.
