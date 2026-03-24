# Plugin version checker

This repository hosts a static JSON manifest so plugins can read the latest version and platform-specific download URLs over HTTPS.

## Manifest URL

Plugins should fetch:

`https://luisparente94.github.io/test-plugin-version-checker/<pluginId>-plugin-version.json`

If you rename the repo or change the default branch, update this URL accordingly.

## Manifest format

File: `plugin-versions.json`

```json
{
  "<pluginId>": {
    "version": "1.0.0",
    "downloads": {
      "darwin": "https://example.com/file.pkg",
      "win32": "https://example.com/file.msi"
    }
  }
}
```

| Field | Meaning |
|--------|---------|
| `<pluginId>` | Stable id for the plugin (e.g. `davinci`, `quark`) |
| `version` | Latest version string for that plugin |
| `downloads.darwin` | macOS installer/download URL |
| `downloads.win32` | Windows installer/download URL |

## Plugins in this manifest

- `davinci`
- `quark`

## Client workflow

1. `GET` the raw GitHub URL above (or cache it with a short TTL).
2. Look up the object for your `pluginId`.
3. Compare the running plugin version to `version` (use your project’s semver or string rules).
4. If an update is needed, pick `downloads.darwin` or `downloads.win32` based on the OS.

## Publishing a new version

1. Upload release binaries and copy the final public download URLs (GitHub Release assets are a good fit).
2. Edit `plugin-versions.json`: set `version` and each `downloads.*` URL you support.
3. Commit and push to `main`. The manifest URL above will serve the new JSON after GitHub updates (CDN caching can add a short delay).

## Keeping downloads working

- Do not delete old release files if older plugin builds still point at those URLs.
- Prefer immutable URLs (one URL per file) instead of reusing the same URL for a different build.

## Optional hardening

- Add per-platform `sha256` and verify after download.
- Add `releasedAt` (ISO-8601) or `minSupportedVersion` if you need rollout or forced-upgrade rules.
