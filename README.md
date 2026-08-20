# renovate-config

Shared [Renovate](https://docs.renovatebot.com/) presets for compscidr repos.

## Presets

| Preset | Extend with | Use for |
|---|---|---|
| `default.json` | `github>compscidr/renovate-config` | Any repo, any language |
| `android.json` | `github>compscidr/renovate-config:android` | Android repos |
| `android-junit5.json` | `github>compscidr/renovate-config:android-junit5` | Android repos using `de.mannodermaus.junit5` |

Each extends the one above it, so pick the most specific and you get the rest.

## Usage

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>compscidr/renovate-config:android"]
}
```

Repo-specific rules (`ignoreDeps`, one-off pins) still go in the repo's own
`renovate.json` alongside the `extends`.

## What `android` adds

Tracks `compileSdk` against the latest released Android API level, using the
`apiVersion` field of the [endoflife.date Android feed](https://endoflife.date/api/android.json).

Renovate has no built-in Android SDK datasource, and `compileSdk` is a bare
integer rather than a dependency coordinate, so this is a custom manager plus a
custom datasource. It exists because androidx releases regularly start requiring
a newer `compileSdk`, which fails `checkAarMetadata` on every open Renovate PR at
once, with an error that talks about `minSdk` and is easy to misread as a device
compatibility problem. It is not: `compileSdk` only selects which APIs are
available at compile time.

`minSdk` and `targetSdk` are deliberately left alone. Those genuinely do change
device compatibility and runtime behavior.

## Why `android-junit5` is separate

The `junit-bom < 6` hold applies to the mannodermaus runner, not to Android in
general. A repo whose only Jupiter usage is in a plain JVM module is unaffected,
and pinning it there would silently freeze `junit-bom` with no explanation.
The pin travels with the runner that needs it.
