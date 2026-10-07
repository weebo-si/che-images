# che-images

Builds the weebo-si forks of Eclipse Che: container images pushed to GHCR, and other artifacts
(the JetBrains Gateway plugin) published as GitHub releases. Everything is signed with Sigstore
(cosign keyless).

Project bootstrapped from the [weebo-base](https://github.com/batleforc/weebo-base) template.

| Image | Source |
|-------|--------|
| `ghcr.io/weebo-si/che-server` | [weebo-si/che-server](https://github.com/weebo-si/che-server) |
| `ghcr.io/weebo-si/che-operator` | [weebo-si/che-operator](https://github.com/weebo-si/che-operator) |
| `ghcr.io/weebo-si/che-dashboard` | [weebo-si/che-dashboard](https://github.com/weebo-si/che-dashboard) |
| `ghcr.io/weebo-si/che-code` | [weebo-si/che-code](https://github.com/weebo-si/che-code) |

| Artifact | Where | Source |
|----------|-------|--------|
| JetBrains Gateway plugin (`devspaces-gateway-plugin.zip`) | Release `devspaces-gateway-plugin-<ref>` of this repository | [weebo-si/devspaces-gateway-plugin](https://github.com/weebo-si/devspaces-gateway-plugin) |

## Quick start

```bash
task init                 # tools + git hooks
task images:build         # trigger a build of develop (images and plugin)
task images:watch         # follow it
task images:verify        # check the cosign signatures (images and plugin)
```

See [docs/readme.md](docs/readme.md) for tags, triggers and CheCluster usage.

## License

These are **unofficial builds** of [Eclipse Che](https://eclipse.dev/che/), not endorsed by or
affiliated with the Eclipse Foundation. "Eclipse Che" is a trademark of the Eclipse Foundation.

- The images contain Eclipse Che code under the [Eclipse Public License 2.0](https://www.eclipse.org/legal/epl-2.0/).
  The source of each image is the fork commit in its `org.opencontainers.image.source` and
  `org.opencontainers.image.revision` labels (also the `sha-<short>` tag).
- `che-code` also contains VS Code (Code-OSS) under the MIT license; its notices ship in the image.
- The Gateway plugin is EPL-2.0, based on the Red Hat OpenShift Dev Spaces plugin and renamed
  (own ID, name and vendor) so it is not taken for it. Each release links the fork commit it was
  built from and ships the license.
- Third-party components keep their own licenses and notices, as shipped by the upstream builds.
- The build scripts in this repository are under the [Apache License 2.0](LICENSE.md).
