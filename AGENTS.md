# AGENTS.md — layer-android-apidemos

Standalone candy repo for the `android-apidemos` layer. The whole candy lives in
`charly.yml` at the repo root: an `apk:` package section pointing at the
committed file `tests/data/ApiDemos-debug.apk`, and a deploy-scope `plan:` of
`adb:` checks. There is no source tree, no container install, and no service —
the `apk:` step runs only on a `target: android` deploy.

**No dedicated owning skill exists for this candy.** The applicable procedures are
the `kind: android` schema/deploy skill and the `adb:` check-verb skill, both
listed below. If a future change adds a `skill:` entity, project it as
`/charly-check:<name>` here.

Canonical files:

- `charly.yml` — the `android-apidemos:` candy entity (the `apk:` committed-file
  path and the `adb:` plan checks).
- `tests/data/ApiDemos-debug.apk` — the committed APK fixture; the `apk:` path is
  project-root-relative to the candy's own source tree, so this file must ship in
  the repo.
- `.github/workflows/deploy.yml` — the manifest gate.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-check:android` — the `kind: android` schema and the `target: android`
  deploy substrate (in-pod emulator or a remote/physical adb endpoint). Load
  before editing or troubleshooting the candy.
- `/charly-check:adb` — the `adb:` check verb (`shell`, `pm`, `resolve-activity`)
  the plan uses. Load before changing the plan checks.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `apk:` package format, `plan:` step verbs incl. `check:`/`eventually:`). Load
  before editing any entity field or plan step.

## Build / validate / test

- `charly box validate` at the repo root — the same structural gate CI runs.
  The CI pin lives in `.github/workflows/deploy.yml`; keep the `version:` schema
  stamp within the pinned charly's supported range (do not migrate the stamp
  past the pin).
- `.github/workflows/deploy.yml` — builds the pinned charly from a CI-time
  checkout and runs `charly box validate`. This is the merge gate.
- The runtime evidence is deploy-scope: the candy's `plan:` `adb:` checks run
  against a booted `kind: android` device, not in CI. The R10 bed for the
  endpoint-device path is `check-android-emulator-pod` in `opencharly/charly`.

## Modify this repo

- There is no embedded `skill:` entity to mirror here; the candy `description:`
  and the `plan:` `adb:` checks are the usage source. If a dedicated skill is
  added later, add it as a `skill:` entity in the same change.
- The APK fixture is the production contract: its `apk:` path is
  project-root-relative to this candy's source tree, so a path change must move
  the file in the same commit. Refresh the fixture only with its canonical md5.
- Keep `version:` at the schema stamp the pinned CI charly supports.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
