---
summary: 'Reusable macOS release playbook (Sparkle, notarization, GitHub releases)'
---

# Releasing macOS Apps (Sparkle + GitHub)

Reusable checklist distilled from recent VibeTunnel, Trimmy, and CodexBar releases. Adapt the script names and paths to the target repo before running anything.

> Must read: this master file lives at `~/Projects/agent-scripts/docs/RELEASING-MAC.md`. Open it alongside any repo-local release doc and reconcile differences before starting.

## Scope & Assumptions
- Swift/SwiftUI macOS app shipped outside the App Store, updated via Sparkle (stable + optional prerelease feed).
- Artifacts distributed through GitHub Releases; appcast XML is committed in the repo root.
- Signing uses Developer ID Application cert and ed25519 Sparkle keys; notarization uses App Store Connect API keys.
- Long-running steps (build, notarization, release scripts) should run in tmux/screen to avoid timeouts.

## Prerequisites
- Tools: Xcode 16.4+ (or project minimum), `swift` toolchain, `notarytool`, Sparkle CLI (`sign_update`, `generate_appcast`), `gh`.
- Credentials in env (export once per session):
  - `APP_STORE_CONNECT_KEY_ID`, `APP_STORE_CONNECT_ISSUER_ID`, `APP_STORE_CONNECT_API_KEY_P8`
  - `SPARKLE_PRIVATE_KEY_FILE` (file-based ed25519 key) and, if used, `SPARKLE_ACCOUNT`
- Repo clean and on main; fast, stable internet for notarization; Apple Developer services green.
- Keep the previous public build installed in `/Applications/<App>.app` to verify the Sparkle update path.

## Versioning Rules
- Single source of truth (e.g., `version.xcconfig` or `Info.plist`): set both `MARKETING_VERSION` and `CURRENT_PROJECT_VERSION`.
- Build numbers **must** monotonically increase; Sparkle compares `CFBundleVersion`, not the marketing string. Always bump the build before tagging/publishing and keep the appcast `sparkle:version` in sync.
- Pre-release suffixes (beta/rc) belong in the source-of-truth version **before** running release scripts—avoid double-suffix mistakes.
- For npm/pnpm packages, every beta/rc publish must use a new semver with a suffix (e.g., `1.2.3-beta.1`); npm will not let you overwrite an existing version/tag.
- If there are sibling surfaces (e.g., web UI), align their versions with the macOS app before releasing.

## Prep: Review History & Changelog
- Verify the latest published release/tag on GitHub (ensure assets are present and match the appcast) **before starting any new release work**.
- Read all commits since that tag (including merges) and skim the diff to capture user-visible changes.
- Curate the changelog before anything else:
  - Focus on user-facing changes only; omit other projects, tests, or internal tweaks unless they materially affect users.
  - Order entries from most interesting/impactful to least.
  - If a feature was added and then removed within the release window, don’t mention it (never shipped).
  - If anything is unclear or contentious, ask the user whether to add/remove it.
- After curation, resort the changelog section for the new version so it matches the above guidance.

## Pre-flight
1) Sync + sanity
```bash
git checkout main && git pull --rebase
git status
```
2) Open the repo’s release doc (if any) and this master file (`~/Projects/agent-scripts/docs/RELEASING-MAC.md`); resolve any conflicts in favor of the current project owner’s direction.
3) Update version + changelog (changelog is the release-notes source).
4) Run the project’s lint/typecheck/tests (e.g., `swiftformat .`, `swiftlint --strict`, `swift test`).
5) Ensure Sparkle key file exists and do a quick test sign:
```bash
echo test > /tmp/sparkle.txt
sign_update -f "$SPARKLE_PRIVATE_KEY_FILE" /tmp/sparkle.txt --account "${SPARKLE_ACCOUNT:-default}"
rm /tmp/sparkle.txt
```
6) Clear stuck DMG volumes if needed:
```bash
for v in /Volumes/*; do [[ $v == */<App>* ]] && hdiutil detach "$v" -force; done
```

## Build, Sign, Notarize
- Prefer the repo’s scripted entry point. Common options:
  - `./scripts/release.sh stable|beta <n>` (handles build → notarize → appcast → release; supports `--resume`/`--status`)
  - `./Scripts/sign-and-notarize.sh` (SwiftPM apps; produces `<App>-<ver>.zip` and staples)
- Typical expectations:
  - Release (or universal) configuration, arm64 at minimum; some projects also ship universal binaries.
  - Sign all nested frameworks/XPCs; use the script’s flags—do **not** change `--deep` usage unless the script requires it.
  - Notarization via `notarytool` with the exported API key; staple after success.
  - Avoid `unzip` when testing locally; use `ditto -x -k <zip> /Applications` to prevent `._*` files that break signatures.

## Sparkle Signing & Appcast
**Policy:** Ship full updates only (no deltas). Remove any `<sparkle:deltas>` blocks before publishing the appcast.
1) Generate signature for the shipping artifact (DMG or ZIP):
```bash
sign_update -f "$SPARKLE_PRIVATE_KEY_FILE" path/to/<App>-<ver>.dmg --account "${SPARKLE_ACCOUNT:-default}"
```
2) Update the correct appcast (stable vs prerelease). Ensure:
   - `sparkle:shortVersionString` == marketing version
   - `sparkle:version` == build number (unique and increasing)
   - `sparkle:edSignature` matches the signature you just generated
3) If scripts exist (`generate-appcast.sh`, `make_appcast.sh`, etc.), use them; otherwise edit appcast XML carefully using the existing entry as a template.
4) Validate signatures and feed:
   - Run any helper (`./scripts/validate-sparkle-signature.sh`) if provided.
   - `sign_update -p <zip>` output matches `sparkle:edSignature` in the appcast.
   - `curl -I "<enclosure-url>"` returns 200.
   - `curl "<appcast-url>" | head` shows the new build number/signature/length.
   - (Optional) Verify update flow using the previous installed build and Sparkle UI.

## GitHub Release & Tag
1) Tag the release after artifacts are ready: `git tag v<version>` (or let the release script tag).
2) Create the GitHub release (pre-release for betas), title `<App> <version>`, body = changelog section for that version.
3) Upload artifacts: DMG/ZIP (and appcast if published via Releases). Ensure enclosure URLs in the appcast point to the uploaded assets and return 200/OK.
4) Release notes correctness:
   - Header **must be exactly** `<App> <version>` — no prefixes/suffixes.
   - Body must be a copy of the curated changelog for that version (user-facing items only, same order).
   - Confirm every bullet from the changelog is present; nothing extra.
5) Push tags/commits once appcast and release notes are correct.

## Verification (Definition of Done)
- Download the published artifact, install via `ditto`, launch, and verify:
  - `spctl --assess --type execute --verbose <App>.app`
  - `codesign --verify --deep --strict --verbose <App>.app`
  - `stapler validate <App>.app`
- Check Sparkle update path from the previous installed build (stable and prerelease if applicable).
- Curl the appcast and enclosure URL; confirm the new entry, correct build number, and non-404 asset.
- Spot-check artifact size against recent releases to catch bundled dev files.
- For multi-surface apps, confirm version strings match across app UI and companion surfaces.
- GitHub release notes verified: header is `<App> <version>` only; body matches changelog bullets and order.
- Sparkle verification complete: signatures match, appcast entry correct, update tested from prior build when possible.
- Appcast/cache note: if Sparkle still reports the old version, relaunch/wait briefly (feed caching) before retagging; fix build/appcast first. The appcast is the single source of truth for published versions—keep it authoritative; avoid parallel “tracking tables.”

## Final Sign-Off (agent handoff)
- Re-read the GitHub release page: title exactly `<App> <version>`; body matches the curated changelog (no missing/extra bullets).
- Re-open the appcast in a browser/`curl` to confirm the new entry, signature, and enclosure URL are present and 200/OK.
- Confirm local checks: `spctl`, `codesign`, `stapler` on the downloaded artifact; app launches; update path works from previous build.
- Log any deviations or manual fixes (e.g., regenerated signature) in the task notes before handing off.

## Recovery / Resume
- If a scripted release stops mid-flight, rerun with `--resume` or consult any `.release-state` the script writes.
- After notarization success but before publish, you can recover manually:
  1) Create DMG/ZIP if missing (`./scripts/create-dmg.sh` or `./Scripts/package_app.sh`).
  2) Sign with Sparkle (`sign_update -f …`).
  3) Manually edit appcast with the new signature.
  4) Create/repair the GitHub release and push appcast changes.

## One-Page Checklist
- [ ] Opened repo-local release doc and this master guide (`~/Projects/agent-scripts/docs/RELEASING-MAC.md`); resolved any conflicts.
- [ ] Version + build number updated in the single source of truth (and synced to any sibling surfaces).
- [ ] Changelog entry authored for this version.
- [ ] Lint/typecheck/tests green.
- [ ] Sparkle key verified with test sign_update.
- [ ] Release script run (or build + sign + notarize completed).
- [ ] Sparkle signature generated with `-f` and applied to appcast; build number unique.
- [ ] Tag + GitHub release created; assets uploaded; URLs in appcast resolve (200/OK).
- [ ] After publishing, bump `CHANGELOG.md`: move the shipped notes under the released version, increment its patch number, and start a new `Unreleased` section for the next patch.
- [ ] Downloaded artifact passes `spctl`, `codesign`, `stapler`; no `._*` files.
- [ ] Update flow validated from a previous version.
- [ ] Appcast shows the correct notes (single-version chunk), and artifact size looks sane.
- [ ] GitHub release notes header is `<App> <version>` and body matches changelog bullets exactly.
- [ ] Manual Sparkle verification done (signature comparison, appcast curl, optional live update test).
