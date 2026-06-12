# CLIProxyAPI Plugins Store

This repository is the lightweight official plugin registry for CLIProxyAPI.
It only maintains `registry.json`; plugin binaries, checksums, and release notes
must stay in each plugin author's own GitHub repository.

## Registry Format

`registry.json` must use this shape:

```json
{
  "schema_version": 1,
  "plugins": [
    {
      "id": "sample-provider",
      "name": "Sample Provider",
      "description": "Adds sample provider support.",
      "author": "author-name",
      "repository": "https://github.com/author-name/cliproxy-sample-provider",
      "logo": "https://raw.githubusercontent.com/author-name/cliproxy-sample-provider/main/logo.png",
      "homepage": "https://github.com/author-name/cliproxy-sample-provider",
      "license": "MIT",
      "tags": ["provider"]
    }
  ]
}
```

Required fields:

- `id`
- `name`
- `description`
- `author`
- `repository`

Optional fields:

- `version` (legacy; only used as a display fallback when the latest release
  cannot be queried)
- `logo`
- `homepage`
- `license`
- `tags`

## Validation Rules

- `schema_version` must be `1`.
- `id` must match CLIProxyAPI plugin ID rules: start with an ASCII letter or
  digit, then use only ASCII letters, digits, `.`, `_`, or `-`, up to 128
  characters total.
- `id` values must be unique.
- `version`, when present, must not start with `v`.
- `repository` must be exactly `https://github.com/{owner}/{repo}`.

## Release Requirements

Plugin authors publish binaries in their own GitHub Releases. CLIProxyAPI
reads the repository's latest release: the release tag defines the plugin
version and must be `v<version>` with a dotted numeric version, for example
`v0.1.0`. Publishing a new release is all it takes to ship an update; the
registry does not need to change.

Each release must include one `checksums.txt` and one zip per supported platform.
Asset names must use the release tag version without the leading `v`:

```text
<id>_<version>_<goos>_<goarch>.zip
checksums.txt
```

Examples:

```text
sample-provider_0.1.0_darwin_arm64.zip
sample-provider_0.1.0_linux_amd64.zip
sample-provider_0.1.0_windows_amd64.zip
checksums.txt
```

`checksums.txt` must use sha256sum format:

```text
<sha256>  sample-provider_0.1.0_darwin_arm64.zip
```

## Zip Layout

Each platform zip must contain the target dynamic library at the zip root:

- Darwin: `<id>.dylib`
- Linux: `<id>.so`
- Windows: `<id>.dll`

Nested dynamic libraries, absolute paths, zip-slip paths, mismatched filenames,
and multiple dynamic libraries are rejected by the CLIProxyAPI installer.

## Adding A Plugin

Open a pull request that updates only `registry.json` unless documentation also
needs clarification. The pull request should include:

- The plugin's GitHub repository URL.
- The latest release tag in `v<version>` form, for example `v0.1.0`.
- Evidence that the required zip asset and `checksums.txt` exist in the release.
- A short description of what capability the plugin adds.
