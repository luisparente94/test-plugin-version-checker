# Plugin version checker

This repository hosts static JSON files so each plugin can read its latest version and platform-specific download URLs over HTTPS.

## Manifest URLs

Each plugin has its own file: `<pluginId>-plugin-version.json`.

### GitHub Pages (recommended)

If [GitHub Pages](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site) is enabled for this repo (e.g. deploy from `main` / root):

`https://luisparente94.github.io/test-plugin-version-checker/<pluginId>-plugin-version.json`

Examples:

- `https://luisparente94.github.io/test-plugin-version-checker/davinci-plugin-version.json`
- `https://luisparente94.github.io/test-plugin-version-checker/quark-plugin-version.json`

### Raw GitHub (alternative)

Same files via raw content (no Pages required):

`https://raw.githubusercontent.com/luisparente94/test-plugin-version-checker/main/<pluginId>-plugin-version.json`

Examples:

- `https://raw.githubusercontent.com/luisparente94/test-plugin-version-checker/main/davinci-plugin-version.json`
- `https://raw.githubusercontent.com/luisparente94/test-plugin-version-checker/main/quark-plugin-version.json`

If you rename the repo, change the default branch, or move files, update these URLs accordingly.

## Manifest format

One JSON object per plugin file (e.g. `davinci-plugin-version.json`, `quark-plugin-version.json`):

```json
{
  "version": "1.0.0",
  "downloads": {
    "darwin": "https://example.com/file.pkg",
    "win32": "https://example.com/file.exe"
  }
}
```

| Field | Meaning |
|--------|---------|
| `version` | Latest version string for that plugin |
| `downloads.darwin` | macOS installer/download URL |
| `downloads.win32` | Windows installer/download URL |

## Plugins in this repo

| Plugin id | File |
|-----------|------|
| `davinci` | `davinci-plugin-version.json` |
| `quark` | `quark-plugin-version.json` |

## Client workflow

1. Build the manifest URL for your plugin (Pages or raw), using your fixed `pluginId` and the `-plugin-version.json` suffix.
2. `GET` that URL (or cache it with a short TTL).
3. Parse JSON and compare the running plugin version to `version` (use your project’s semver or string rules).
4. If an update is needed, pick `downloads.darwin` or `downloads.win32` based on the OS.

## Publishing a new version

1. Upload release binaries and copy the final public download URLs (GitHub Release assets are a good fit).
2. Edit **that plugin’s** file only (e.g. `davinci-plugin-version.json`): set `version` and each `downloads.*` URL you support.
3. Commit and push to `main`. Allow a short delay for GitHub/CDN to serve the new JSON.

## Keeping downloads working

- Do not delete old release files if older plugin builds still point at those URLs.
- Prefer immutable URLs (one URL per file) instead of reusing the same URL for a different build.

## Optional hardening

- Add per-platform `sha256` and verify after download.
- Add `releasedAt` (ISO-8601) or `minSupportedVersion` if you need rollout or forced-upgrade rules.
