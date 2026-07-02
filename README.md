# skopeo-binary

[![Build](https://github.com/norrs/skopeo-binary/actions/workflows/build.yml/badge.svg?branch=main)](https://github.com/norrs/skopeo-binary/actions/workflows/build.yml)

Statically linked [skopeo](https://github.com/podman-container-tools/skopeo) binaries,
built from the official upstream source in GitHub Actions and published as
releases. These binaries are consumed by the
[skopeo-mise-tool](https://github.com/norrs/skopeo-mise-tool) mise plugin.

Release tags mirror the upstream skopeo release versions.

## Build details

Binaries are built with `CGO_ENABLED=0` and the build tags
`exclude_graphdriver_devicemapper exclude_graphdriver_btrfs containers_image_openpgp`
to produce portable, statically linked executables.

## Platforms

| OS     | Architectures         |
| ------ | --------------------- |
| linux  | amd64, arm64, ppc64le |
| darwin | amd64, arm64          |

## Usage

Each release provides one binary per platform/architecture plus a matching
`.sha256` (and `.md5`) checksum file.

```bash
version=v1.16.1
arch=amd64
os=linux
curl -fsSL -o skopeo \
  "https://github.com/norrs/skopeo-binary/releases/download/${version}/skopeo-${os}-${arch}"
chmod +x skopeo
```

## Building releases

The [`build.yml`](.github/workflows/build.yml) workflow builds and publishes
releases. It runs:

- **On a schedule** — weekly (Mondays 01:00 UTC) to pick up new upstream
  releases automatically.
- **On push** to `main`.
- **Manually** via the *Run workflow* button (`workflow_dispatch`), or from the
  CLI with `gh`.

A normal run builds **all** pending upstream tags within the candidate window
(the latest patch plus the last four `.0` minor releases). Tags are skipped if
they are already recorded in `version.txt` or already have a GitHub release, so
existing releases are never rebuilt.

### Forcing a rebuild of a single version

To rebuild a version that has already been published (e.g. after a build-tooling
change), use the `rebuild` input. It targets a single tag, bypasses both skip
checks, and deletes the existing release (and its tag) before republishing.

From the CLI:

```bash
gh workflow run build.yml --repo norrs/skopeo-binary -f rebuild=v1.16.1
```

Or from the GitHub UI: **Actions → Build → Run workflow**, then enter the tag
(e.g. `v1.16.1`) in the **rebuild** field. Leave the field empty for a normal
run.

## Credits

The build tooling in this repository is based on
[lework/skopeo-binary](https://github.com/lework/skopeo-binary), which pioneered
the GitHub Actions workflow for building and publishing statically linked skopeo
binaries. Many thanks to [@lework](https://github.com/lework) for the original
work.

## License

The build tooling in this repository is licensed under [MIT](LICENSE). skopeo
itself is distributed under its own upstream license.
