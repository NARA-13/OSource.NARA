# OSource

A free, local-first Windows application for discovering open-source software, managing application references, and exploring useful websites.

**Version 0.4.0 — functional preview.** This distribution includes a real Windows x64 desktop executable and a real NSIS installer. It is not just a website. It is also **not a production-certified implementation of every feature in the original product specification**. Read the capability boundaries and test report below.

![OSource Home interface](docs/OSource-home.png)

*Actual running UI captured in the Linux development preview, not a Windows runtime screenshot.*

## Start on Windows

### Installer
1. Use `OSource-Setup-0.4.0-x64.exe` from the release directory.
2. Select an installation folder and optional Start Menu/desktop shortcuts.
3. Open OSource. No account or administrator rights are needed for OSource itself.
4. If the Microsoft Edge WebView2 Evergreen Runtime is missing, OSource offers to open Microsoft's official download page. Install the runtime from Microsoft and reopen OSource.

### Portable host
Extract `OSource-Portable-0.4.0-x64.zip` and run `OSource.exe`. Keep the included license notices with the executable when redistributing it. No OSource installation is required; **the system WebView2 Runtime is still required**.

The default data directory is `%LOCALAPPDATA%\OSource`, separate from the executable. For a custom data directory:

```powershell
.\OSource.exe --data "D:\OSourceData"
```

This makes the database/profile location configurable, but does not bundle WebView2 or automatically relocate the download folder. Set the download location in OSource Settings.

### Requirements and distribution status
- Target: **Windows 10 and Windows 11, x64**. 32-bit Windows is not supported by these release artifacts.
- Microsoft Edge WebView2 Evergreen Runtime. Official URL: https://developer.microsoft.com/microsoft-edge/webview2/
- Internet access for live metadata and downloads; cached results and library references remain available offline.
- A writable local data directory. One OSource instance per data directory.
- **The executables are unsigned.** A signing certificate and public release identity were not supplied. Windows may show a warning. Never disable SmartScreen, antivirus, or other system protections.
- **Cross-compiled on Linux; not executed or installed on Windows 10/11 in this environment.** Native smoke testing remains required before wider distribution. See `docs/TEST-REPORT.md`.

## What actually works

### What changed in 0.4.0
- Added **VibeTopList · RPG**: two public RPG pages, robots-aware fetching, local search and 12-hour caching.
- Added **Foundplay** using its documented public games API, with source attribution and its 25-result query limit. No bulk HTML scrape.
- Added **Awesome AI Games** from `aviaryan/awesome-ai-games`: actual README browser-game links, locally searchable and cached for 12 hours.
- Added the requested **r/Piracy wiki** as an explicitly external-only reference. Automated access was denied; no wiki contents are imported and no bypass is attempted.
- Sources appear in Store and Websites → Live directory, with shortcuts from Explore. New-source bookmarks retain provenance and appear in global search.
- The app now has **eight catalog adapters and one external reference**, not nine live imported catalogs. Pricing, licenses, rights and Windows compatibility are not inferred from being listed. See `docs/SOURCES-0.4.md`.

### Previous changes in 0.3.0
- Cleaner navigation and card layouts; live-source counts collapse into a compact disclosure.
- Short, finite page/card entrances, hover/press feedback, image fades and save-button feedback. No decorative glow, particles, ambient loops or forced scrolling. App and OS reduced-motion preferences are respected.
- **Websites → Explore:** 28 website picks from public Reddit recommendation threads, grouped into Useful, Play, Make, Learn and Explore. Instant filtering, progressive loading and a Surprise me preview.
- **Store → Community picks:** 12 desktop projects recommended in public threads, with official GitHub release access through the existing consent-based download flow.
- Source links on every pick; recommendation attribution is retained when adding a pick to My websites. Existing personal websites/settings are not overwritten or replaced.
- This small bundled collection is **curated, not live-scraped or automatically synced from every Reddit comment**. The five live-source adapters remain available separately. See `docs/COMMUNITY-PICKS.md`.

### Previous changes in 0.2.0
- Accent-derived backgrounds, navigation, cards, controls and branding; blue no longer leaves green surfaces. Settings saves are serialized to preserve rapid selections.
- Removed the promotional Home hero and shortened page copy.
- Real website thumbnails with a privacy toggle and local cache clearing.
- Five public-source adapters, pagination, source counts and saved discoveries.

### Catalog scale — what “200,000+” means
The app connects to searchable sources with a combined reach well beyond 200,000 records. It does **not** ship 200,000 reviewed Windows installers or preload that many items. GitHub reports broad repository matches (including libraries, documentation and non-Windows projects), not a verified app inventory. Counts are source-scoped, overlapping and sometimes approximate. No summed “verified apps” badge is shown. Reddit searches only the fetched recent feed, not all Reddit. See `docs/DISCOVERY.md` for scopes, limits and observed live results.

### Six main sections
1. **Home:** real GitHub metadata for an editorial set of actual projects, locally favored app ordering, recent activity, website favorites, collection entry points and shortcuts.
2. **All Apps:** import `.exe` / `.lnk` using a native Windows file picker; best-effort detection from uninstall-registry display icons and Start Menu shortcuts; launch, reveal folder, favorite, group into a named collection, search, sort, grid/list views and reference removal. Entries explicitly say Imported or Detected. Existence checks detect missing referenced files.
3. **Store:** GitHub, FMHY, Reddit RSS, Open VSX, FreeToGame, VibeTopList, Foundplay and Awesome AI Games adapters, plus an external-only wiki reference, source-specific counts/status, query/kind/source filters, pagination and persistent saved discoveries. GitHub details retain release assets, architecture/package filters and explicit download consent.
4. **Websites:** curated community picks, saved websites and a searchable live directory, categories/bookmarks/custom entries, browser opening, and actual website thumbnails (thum.io capture, with public publisher-image fallback). Failed captures show Unavailable, not fabricated artwork.
5. **Settings:** light/dark/system theme, accent, density, radius, interface/text scale, reduced motion, sidebar collapse, installed-font enumeration, grid/list preference, download folder/concurrency, optional local history, local recommendations, startup section, optional synthesized sound, cache/history clearing, JSON export/settings import, logs and storage usage.
6. **About & Credits:** mission, version, actual dependencies, data-source credits, privacy/security information, changelog and explicit limitations.

### Real downloads, not simulated progress
- GitHub release assets only; backend re-resolves the selected asset ID from release metadata.
- Original HTTPS download URL is visible before you approve a download.
- Background streaming; real received byte count, speed and estimated remaining time.
- Bounded concurrency, queue, pause/resume, cancel/retry and persisted history.
- Range resume uses `If-Range` when validators exist. A server that ignores Range causes a safe restart, not an append.
- Size checks, basic file-header validation and SHA-256 calculation. A supplied GitHub release SHA-256 digest must match.
- Windows Authenticode inspection where applicable and Internet-zone marking on supported Windows filesystems.
- **No automatic execution.** Saved executable files are rehashed and require a native confirmation before Windows opens them. Archives are shown in their folder for manual extraction.
- Closing while transfers are active asks for confirmation. Partial files survive reopening; resume is a deliberate action.

### Search and privacy
`Ctrl + K` opens grouped search. Arrow keys select results, Enter opens them, Esc closes. Search includes loaded/cached store data, live public repository search, library references, websites, categories and local recent activity. Typing `favorites` finds saved entries. Remote lookups are debounced and cached.

No accounts, telemetry implementation, advertising SDKs, subscriptions or cloud synchronization. Local data lives in a transactional bbolt database. Third-party sites may independently contain advertisements or require accounts for their own features; OSource labels free services separately from open-source projects.

## Important boundaries

- **Downloading is not installing.** OSource does not silently install, infer an installer exit status, resolve package dependencies or promise to detect the resulting app. After installing, use Find installed apps or Add an app.
- There is **no universal uninstall engine**. Use Windows Settings → Apps for safe supported removal. Removing an OSource reference does not delete the app or user files.
- No automatic app update tracking. A project's latest stable GitHub release is shown, but installed versions are not reliably compared against it.
- No proof that every discovered repository supports Windows. Executable/MSI assets and explicitly Windows-named archives are identified by filename heuristics; architecture can be Unknown. GitHub search can return unrelated or unsafe projects. Review the original project before downloading.
- License labels reflect GitHub's detected SPDX identifier, not a source-code audit. Known open-source SPDX licenses are distinguished from unrecognized/source-available projects.
- Project avatars are generally repository-owner avatars, not guaranteed official app icons. Library apps use neutral icons; native executable icon extraction is not implemented.
- Website thumbnails are optional cached still images, not embedded live pages. Some sites deny access or yield no usable image. Publisher images may be promotional rather than page screenshots. Public URLs are sent to thum.io; turn this off in Settings → Privacy.
- GitHub, FMHY, Reddit public RSS, Open VSX and FreeToGame are implemented discovery adapters. Only GitHub release assets enter the managed download pipeline. Other records open their original source sites. AlternativeTo, SourceForge and F-Droid remain external references; F-Droid is Android-specific.
- No tray mode, startup registration, arbitrary proxy/source configuration, embedded third-party website browsing, background/transparency effects, system notifications, arbitrary shortcut remapping or window-position persistence.
- A public OSource repository, issue tracker, security mailbox, update feed and signing identity were not supplied. The UI says so; it does not invent endpoints or claim to have checked for updates.
- Discovery loads 12 records per source on All sources, or 30 for a single source, with Load more. GitHub exposes at most 1,000 results per query, often less under rate limits. Narrow queries to reach other repositories. Global quick search remains a smaller result preview. Discovery and metadata caches have source-specific TTLs and stale fallback; 403/429 cooldowns persist across restarts.
- App detection is best effort and may produce duplicate references or miss applications. `.lnk` checks verify the shortcut file exists, not its resolved target. Registry DisplayIcon paths are launch candidates, not authoritative launch commands.
- Clearing records retains files. Cancelling removes the partial download, not an installed app. Do not clear history if you still need a paused transfer's resume record.

See `docs/FEATURE-MATRIX.md` for an explicit specification-to-implementation summary.

## Development

### Stack
- Go desktop/backend, `go-webview2` Windows host and Windows APIs.
- Embedded vanilla HTML/CSS/JavaScript; **no Node runtime or browser window is required by the shipped desktop app**.
- bbolt transactional embedded database.
- NSIS Modern UI per-user installer.
- No npm dependencies in the application, no frontend CDN, no embedded credentials.

The Go toolchain used here is `go1.27.3`; the exact minimum/toolchain target is pinned by `go.mod`. Install that toolchain or a compatible newer one. NSIS 3 is needed only to rebuild the installer. Dependencies are locked in `go.sum`.

### On Windows
```powershell
go mod download
go test ./...
powershell -File scripts\build.ps1
# Optional: with NSIS's makensis on PATH
makensis packaging\installer.nsi
```
Review the build scripts before running them. If your normal script policy prevents execution, run the listed build commands directly rather than changing Windows security protections.

### Cross-build from Linux
```bash
bash scripts/build.sh
# With NSIS installed:
makensis packaging/installer.nsi
```

### UI/backend development preview
```bash
go run . --preview --port 8787 --data /absolute/path/to/test-data
```
Open `http://127.0.0.1:8787`. Preview binds to all interfaces for proxied development environments; **do not expose it on an untrusted network or use personal production data**. Native import, detection, launch, installer execution and folder-opening are disabled in preview. The catalog and downloads are real. The release Windows host instead binds only to an ephemeral loopback address and checks its Host header, request token and Origin.

The frontend is embedded at build time; rebuild/restart after changing `ui/`.

### Tests
```bash
go test ./...
go test -race ./...                    # Requires a race-capable toolchain/C compiler
OSOURCE_LIVE_TEST=1 go test -run TestLiveGitHubDownload -v .
```
The opt-in live test fetches `jqlang/jq`'s latest stable release and downloads its actual Windows x64 executable to a temporary test directory. **It never executes the file.** On PowerShell, set `$env:OSOURCE_LIVE_TEST='1'` first.

Browser checks (preview server running):
```bash
npm install --no-save playwright
npx playwright install chromium
node tests/browser.cjs
node tests/discovery-browser.cjs
node tests/community-browser.cjs
node tests/game-sources-browser.cjs
# Optional actual-file browser test: set OSOURCE_DOWNLOAD_DIR to a disposable absolute folder
node tests/download-browser.cjs
```
The browser test exercises real local persistence. Use a fresh throwaway preview database. It may need public GitHub access and a non-exhausted API quota.

## Source layout
```
main.go                    Local state, HTTP API, process lifecycle
auth through handler()     Per-process token, Origin and desktop Host checks
catalog.go                 GitHub project/releases adapter and network policy
discovery.go               Discovery routing, pagination and source caches
game_sources.go            New game sources, reference and robots policy
previews.go                Optional real website captures/publisher images
downloads.go               Queue, transfer, resume, integrity and explicit launch
platform_windows.go        Native host, dialogs, registry, shell, signatures
platform_other.go          Development-preview stubs; no non-Windows launch
core_test.go               Unit/security/persistence/download/live tests
discovery_test.go          Adapter/cache/rate-limit/thumbnail-security tests
community_test.go          Bundled references and bookmark provenance tests
game_sources_test.go       Source parsing, API limits, robots and reference tests
ui/                        Embedded interface, font and original artwork
branding/                  SVG/PNG/ICO marks and installer artwork
packaging/installer.nsi     Branded per-user installer/uninstaller
scripts/                   Reproducible build commands
rsrc_windows_amd64.syso     Windows icon, version and GUI manifest resources
```

## Uninstall and data removal
The installer registers a per-user Windows uninstall entry. Uninstall removes OSource's installed files and selected shortcuts. It intentionally retains `%LOCALAPPDATA%\OSource`, downloads and other applications. Use Settings to export/erase local records first if desired. After closing OSource, delete its data directory to remove the database, logs and WebView profile too. Downloaded files remain in your chosen download folder.

## License and credits
OSource source and original branding/artwork are provided under MIT. Dependencies retain their original licenses; see `THIRD-PARTY-NOTICES.md` and `docs/licenses/`. Software listings, names and avatars belong to their original developers. OSource is independent and not affiliated with the directories or projects it references.

### Package source and portable distributions
After building the executable and installer with Go/NSIS, run `python scripts/package.py` (Python 3). This creates versioned portable/source ZIPs and a relative-filename `release/SHA256SUMS.txt`. No application data, credentials, caches or downloaded third-party executables are packaged. The source ZIP contains the dependency lockfile, original branding, modern source files, tests, documentation and resource/build scripts.

### Maintain the community collection
Edit `docs/research/community-picks.json`, keeping each actual recommendation URL, original summary, pricing/license caveat and curation date. Run `python scripts/update-community.py`, then rebuild the executable. The generator does not scrape threads or certify safety. Tests validate structure and source URL handling, not the correctness of every third-party claim.
