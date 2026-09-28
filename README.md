# android-apidemos

The committed ApiDemos APK fixture for OpenCharly's Android deploy and check path.

The `android-apidemos` candy ships the canonical `ApiDemos-debug.apk`
(`io.appium.android.apis`) and installs it onto a `kind: android` device through
the `apk:` package format's **committed-file** path (`apk: <path>`, not an
`apkeep` download). It is pushed with the goadb sync protocol, so it works
against a remote or physical adb endpoint with no host `apkeep`/`adb` binaries —
which is exactly what exercises the endpoint-device path.

The candy installs nothing into a container image: the `apk:` step runs only on a
`target: android` deploy, so its observable effect is the package present on the
booted device.

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `android-apidemos` |
| Package | `io.appium.android.apis` |
| Format | `apk:` committed-file (`tests/data/ApiDemos-debug.apk`) |
| Deploy target | `target: android` (only) |
| Service / port | none |
| Environment | none |

## How to use it

Compose the layer into the project that deploys to your Android device:

```yaml
my-android-project:
  candy:
    base: fedora
    candy:
      - '@github.com/opencharly/layer-android-apidemos:<tag>'
```

The package is installed on the booted device at deploy time; the candy's own
`plan:` then verifies it:

```bash
adb shell pm list packages io.appium.android.apis
adb shell cmd package resolve-activity --brief io.appium.android.apis
```

## Layout

- `charly.yml` — the `android-apidemos:` candy entity (the `apk:` committed-file
  path and the deploy-scope `adb:` checks).
- `tests/data/ApiDemos-debug.apk` — the committed APK fixture (md5 `f968ec5b`).
- `.github/workflows/deploy.yml` — the manifest gate (`charly box validate`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skills: `/charly-check:android` (the `kind: android` schema and deploy) and `/charly-check:adb` (the `adb:` check verb)
- Check framework: `/charly-check:check`
- Device automation: `/charly-check:appium`
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
