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
task images:build         # trigger a build of develop
task images:watch         # follow it
task images:verify        # check the cosign signatures
```

See [docs/readme.md](docs/readme.md) for tags, triggers and CheCluster usage.

## License

These are **unofficial builds** of [Eclipse Che](https://eclipse.dev/che/), not endorsed by or
affiliated with the Eclipse Foundation. "Eclipse Che" is a trademark of the Eclipse Foundation.

- The images contain Eclipse Che code under the [Eclipse Public License 2.0](https://www.eclipse.org/legal/epl-2.0/).
  The source of each image is the fork commit in its `org.opencontainers.image.source` and
  `org.opencontainers.image.revision` labels (also the `sha-<short>` tag).
- Third-party components keep their own licenses and notices, as shipped by the upstream builds.
- The build scripts in this repository are under the [Apache License 2.0](LICENSE.md).
