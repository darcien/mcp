# mcp builds

This fork publishes release binaries of [avelino/mcp](https://github.com/avelino/mcp)
for Linux and macOS. It is not affiliated with upstream. It is a temporary
distribution channel. Do not open issues or pull requests upstream from this fork.

The `dist` branch (this branch) contains only the release workflow and patches.
The `main` branch and the tags are upstream, without changes.

## How releases are made

1. Each day, [`release.yml`](.github/workflows/release.yml) finds the latest
   stable upstream release.
2. If this fork has no release for that tag, the workflow builds the upstream
   commit of the tag. It applies the files in `patches/` first, if there are any.
3. The workflow publishes a release with the same tag. The release contains
   the binaries, a `SHA256SUMS` file and build provenance attestations.
   Releases are immutable.

To build a specific upstream tag, run the workflow manually and set `tag`.

## Assets

| Asset | Platform |
|---|---|
| `mcp-aarch64-apple-darwin` | macOS, Apple silicon |
| `mcp-x86_64-apple-darwin` | macOS, Intel |
| `mcp-x86_64-unknown-linux-gnu` | Linux x86_64, glibc 2.28 or later |
| `mcp-aarch64-unknown-linux-gnu` | Linux arm64, glibc 2.28 or later |
| `mcp-x86_64-unknown-linux-musl` | Linux x86_64, static, any distribution |
| `mcp-aarch64-unknown-linux-musl` | Linux arm64, static, any distribution |

## Install with mise

```sh
mise use -g github:darcien/mcp
```

mise selects the asset for your platform. It verifies the download against
the SHA-256 digest from the GitHub API and against the build attestation.
Commit `mise.lock` to pin the version and the checksums.

## Verify a download manually

```sh
sha256sum -c SHA256SUMS --ignore-missing
gh attestation verify mcp-x86_64-unknown-linux-musl --repo darcien/mcp
```

## Known upstream behavior

When audit logging is on (the default), `mcp` downloads the ChronDB native
library from the `latest` release of `avelino/chrondb` at runtime. It does not
verify that download. The static musl builds cannot load this library, so on
those builds audit persistence and the tool cache are off. To prevent the
download on any build, set `MCP_AUDIT_ENABLED=false`.
