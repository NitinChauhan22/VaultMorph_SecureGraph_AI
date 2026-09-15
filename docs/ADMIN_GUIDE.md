# SecureGraph AI — Admin Guide

## 1. Installation & licensing

- **Desktop Community edition**: single binary/installer (MSI and NSIS builds), one edition, a
  binary registered/unregistered state (no tiers, no trial expiry on desktop).
- **Registration**: Settings → License → Register. Free, non-expiring, requires only an email
  address. Registering sends your email and a one-way device fingerprint to `api.vaultmorph.org`
  for license issuance — see the Privacy & consent section below and the full Privacy Policy.
- **What registration unlocks**: a small set of governance-dashboard extras. It does **not** gate
  scanning, reports, or exports in the current Community model — those work fully unregistered.
- **License server**: `api.vaultmorph.org` (FastAPI) issues and verifies Ed25519-signed
  registration tokens; device fingerprinting binds a registration to one machine.
- **Uninstalling**: use the Start Menu uninstaller or Windows Settings/Control Panel → Apps —
  both trigger the same registered uninstaller entry. Neither touches your per-user app data
  (`%APPDATA%\securegraph\securegraph\`), including scan history and the consent record, which
  persists across reinstalls by design (see §7). If the uninstaller doesn't run from the Start
  Menu shortcut but does from Control Panel, try running it elevated (as Administrator) and make
  sure no `SecureGraph AI` process (including a `tauri dev` instance) is still running — a locked
  executable is the most common cause of a silent NSIS uninstall failure.

## 2. Installing the scanner engines & their dependencies

SecureGraph AI itself is one self-contained installer, but most of its scan engines drive a
**separate, external command-line tool** that has to be installed on the machine independently —
the app does not silently bundle or auto-install these. Two engines (SAST's OpenGrep is standalone
below; Taint Analysis) need no separate install for Taint Analysis specifically — everything else
below does.

### Semgrep (SAST — primary engine)

1. Install Python 3 from [python.org/downloads](https://python.org/downloads) — on the first
   setup screen, tick **"Add python.exe to PATH"**. (Skip this step if Python is already installed.)
2. Open PowerShell or Command Prompt and run: `pip install semgrep`
3. Verify: `semgrep --version` should print a version number.
4. Usually no further step needed — SecureGraph AI auto-detects pip-installed tools via Python
   itself (Settings → Scanner Tool Locations → **Auto-detect**), it doesn't rely on `PATH` alone.

### Checkov (IaC Security)

Same pattern as Semgrep: `pip install checkov`, verify with `checkov --version`, Auto-detect finds
it the same way.

### OSV-Scanner (SCA / Dependencies)

- **Recommended**: `winget install --id Google.OSVScanner -e`
- **Fallback** (no winget, or an air-gapped machine): download the Windows binary from
  [github.com/google/osv-scanner/releases](https://github.com/google/osv-scanner/releases) and
  keep it somewhere permanent, e.g. `C:\Tools\osv-scanner\osv-scanner.exe`.
- Verify: `osv-scanner --version`
- If you used winget, the app finds it on `PATH` automatically. If you used the manual download,
  paste the full `.exe` path into **Settings → Scanner Tool Locations → OSV-Scanner**.
- Then run the one-time database bootstrap below before your first real scan.

### Gitleaks (Secrets Scanning)

- **Recommended**: `winget install --id Gitleaks.Gitleaks -e`
- **Fallback**: download `gitleaks_<version>_windows_x64.zip` from
  [github.com/gitleaks/gitleaks/releases](https://github.com/gitleaks/gitleaks/releases), extract
  it, and keep `gitleaks.exe` somewhere permanent.
- Verify: `gitleaks version`
- Set the path in Settings → Scanner Tool Locations if it isn't found automatically.

### Trivy (Container Security)

- **Recommended**: `winget install --id AquaSecurity.Trivy -e`
- **Fallback**: download `trivy_<version>_windows-64bit.zip` from
  [github.com/aquasecurity/trivy/releases](https://github.com/aquasecurity/trivy/releases), unzip,
  and keep `trivy.exe` somewhere permanent.
- Verify: `trivy --version`
- Set the path in Settings if needed, then run the one-time database bootstrap below.

### OpenGrep (SAST — second engine)

- **No winget or pip package exists for this one** — download `opengrep_windows_x86.exe` directly
  from [github.com/opengrep/opengrep/releases](https://github.com/opengrep/opengrep/releases) and
  move it somewhere permanent, e.g. `C:\Tools\opengrep\opengrep.exe`.
- Windows SmartScreen will very likely warn on first run of a freshly downloaded `.exe` — click
  "More info" → "Run anyway" (only after confirming the download came from the official
  `opengrep/opengrep` GitHub repo).
- Verify: run it once from its folder (`.\opengrep.exe --version`).
- Because OpenGrep is never Python-auto-detected and rarely lands on `PATH` by itself, you will
  almost always need to paste its exact path into **Settings → Scanner Tool Locations → OpenGrep**
  — this is expected, not a sign something is broken.

### OWASP ZAP (DAST)

- **Recommended**: `winget install --id ZAP.ZAP -e`
- **Fallback**: download the Windows installer from
  [zaproxy.org/download](https://www.zaproxy.org/download/) and run it.
- Some ZAP versions bundle their own Java runtime and some need a separate JRE — check the exact
  requirement listed on the download page for the version you're installing.
- **Known false positive**: some ZAP installer builds have been flagged by Microsoft
  Defender/SmartScreen as a threat — this has been reported as a false positive against the
  official installer. If it happens, confirm the download came from zaproxy.org and check the file
  hash on the download page before proceeding, rather than assuming it's genuinely malicious.
- After install, point Settings → Scanner Tool Locations → ZAP at it if not found automatically
  (the DAST screen also has its own Auto-detect for ZAP specifically).

### General Windows install notes

- **Admin rights**: Semgrep/Checkov's `pip install` uses `--user` and needs no admin rights.
  `winget` can install per-user or machine-wide; the manual-download fallbacks need no admin
  rights either, as long as you place the binary somewhere you can write to.
- **`winget` not recognized**: it ships as the "App Installer" component, preinstalled on most
  current Windows 10/11 machines. If your terminal doesn't recognize `winget`, install "App
  Installer" from the Microsoft Store, or use the manual-download fallback instead.
- **SmartScreen warnings are expected**, not necessarily a red flag: most of these are open-source
  CLI tools without a paid code-signing certificate, so a freshly downloaded `.exe`/`.zip` from
  GitHub Releases routinely triggers "Windows protected your PC." Confirm the URL is the project's
  official GitHub repo before clicking through.
- **Corporate network/proxy**: `winget`, `pip`, and GitHub downloads all need outbound HTTPS. If
  your organization blocks these, download the binaries on a machine that does have access and
  copy them over, then set the explicit path for each in Settings.

### One-time database bootstrap (OSV-Scanner & Trivy)

OSV-Scanner and Trivy each need a **local vulnerability database** before their first real scan —
this is separate from installing the binary itself, and is the single network-touching step in an
otherwise fully offline scanning pipeline:

- **OSV-Scanner**: `osv-scanner scan source --offline-vulnerabilities --download-offline-databases -r <a directory>`
  with `OSV_SCANNER_LOCAL_DB_CACHE_DIRECTORY` set to where you want the DB cached.
- **Trivy**: `trivy image --download-db-only --cache-dir <cache directory>`

Settings shows a **DB status panel** for both (with the exact command above shown next to each),
flags the DB as stale after 14 days (advisory only — it never blocks a scan), and has a
**"Download/Update DB now"** button (capped at 15 minutes) that runs the same command for you.

### Verifying everything worked

- Settings → **Scanner Tool Locations** shows, per tool: any override you've set, the path the app
  actually resolved, and whether that resolution fell through to the bare-`PATH` fallback (worth
  fixing with an explicit path if so — see Troubleshooting below).
- The most reliable end-to-end check: enable every engine and run one scan. Each engine should
  report findings (or "no findings") rather than a "binary not usable on PATH" error.

### How the app finds each tool (resolution order)

1. **An explicit path you've set** in Settings → Scanner Tool Locations, if one is configured.
2. **For Semgrep and Checkov only** (the two pip-installed tools), the app asks Python itself
   where it puts console scripts and checks there — this is what the **Auto-detect** button does.
   It never runs automatically or during a scan, only on that explicit click.
3. **The bare tool name on your system `PATH`** — today's fallback, same as running `semgrep` from
   a terminal yourself.

A **Repair** button is available for Semgrep/Checkov only — it runs
`pip install --force-reinstall --user <tool>` (capped at 6 minutes) and needs a working Python on
`PATH` first.

## 3. Configuring scans

- Engine selection and scope are configured per-project in **Settings → Scan Engines**.
- CI/CD: invoke the `securegraph` CLI with the same `--scope`/engine flags as the GUI; the CLI
  produces the same report fields, so pipeline output matches what you'd see locally. The same
  external-tool prerequisites in §2 apply in CI — install them in your pipeline image.
- Scans can be triggered manually, on a schedule, or from a CI pipeline step. DAST is
  consent-gated and typically reviewer-initiated rather than run on every commit.
- **Custom SAST rules**: Settings → Rule Management (also its own top-level nav item) lets you
  enable/disable rules, pin specific versions, roll back a rule change, and import/export rule
  packages — useful for tuning noisy rules or adding org-specific detections without waiting on a
  product release.

## 4. Troubleshooting scan/engine failures

| Symptom | What it means | Fix |
|---|---|---|
| `"<tool> binary not usable on PATH: ..."` | The app tried its resolved path (override, or bare name on `PATH`) and the process failed to launch or run | Confirm the tool is actually installed (`<tool> --version` in a terminal); if it works there but not in the app, set an explicit path in Settings → Scanner Tool Locations rather than relying on `PATH` |
| Semgrep/Checkov work in a terminal but fail from the app, especially after `npx tauri dev` | A known root cause: a `pip install --user` console-script launcher can land in a per-user Scripts directory that isn't on every shell's `PATH` — the terminal you launched `tauri dev` from may lack it even though another shell has it | Click **Auto-detect** in Settings (asks Python directly, not `PATH`), or set the exact path manually |
| OSV-Scanner: `"local OSV vulnerability database not found at ..."` | The binary is installed and found, but its offline vulnerability DB hasn't been bootstrapped yet | Run the one-time OSV-Scanner command from §2 (or click "Download/Update DB now" in Settings) |
| Trivy: DB-missing message with a `trivy image --download-db-only ...` command shown | Same as above, for Trivy's DB | Run the shown command once, or use the Settings button |
| OpenGrep: `"rule pack not found at ..."` | This is a packaging defect in the app itself, **not** a missing external tool — OpenGrep's bundled rules didn't ship correctly | Reinstall/repair the SecureGraph AI app itself; this is not something reinstalling OpenGrep fixes |
| DAST/ZAP not detected | ZAP is never auto-installed — this is expected until you install it | Install from zaproxy.org/download (or use the Docker image alternative), then set its path in Settings, or click DAST's own "Auto-detect" |
| Repair/Reinstall button does nothing or times out | Reinstall only supports Semgrep and Checkov (the two pip tools), and needs a working Python on `PATH`; it's capped at 6 minutes | Verify Python installs and runs on its own first; install it with "Add python.exe to PATH" checked on Windows |
| NSIS installer/uninstaller issue | See §1's uninstall note — usually a locked running process or a non-elevated attempt | Close all SecureGraph AI processes first; run the (un)installer as Administrator |

## 5. AI Context Firewall & AI Audit

- **Local-only mode** is supported and is the default: no source code, secrets, or findings leave
  the machine unless you explicitly configure and enable an external AI provider.
- **AI providers**: OpenAI, Anthropic, a generic OpenAI-compatible endpoint (including a local
  LLM), or a CLI-based provider (Codex CLI, Claude Code CLI). CLI providers run as a local process
  on this machine, but the CLI tool itself typically calls a remote cloud API using its own
  separate credentials — running the CLI locally is **not** the same as local model inference, and
  the Settings screen discloses this explicitly next to the provider picker.
- **AI Context Firewall policy**: configured in Settings → AI Governance; controls exactly which
  categories of content (source snippets, metadata, etc.) are allowed to reach an external
  provider, independent of which provider is enabled.
- **AI Audit Engine mode** — three levels, configured in Settings, applied to new AI interactions:
  - **Mode 0 — Metadata only**: only interaction metadata (timestamps, provider, feature used) is
    logged, not any content.
  - **Mode 1 — Security context**: logs security-relevant context about what was sent, without the
    full raw content.
  - **Mode 2 — Full sanitized**: logs the full interaction content, after sanitization.
  Review actual logged interactions in the **AI Audit Log** screen (also its own top-level nav
  item) and in the report's Audit & AI Governance sections.

## 6. Secrets validation policy

Settings lets you opt in, per secret-scanning provider, to live-validating a discovered credential
(checking whether it's actually active) rather than only pattern-matching it. This is off by
default per provider; enabling it discloses exactly where the validation check is sent.

## 7. Reports — admin-level notes

- Reports render as HTML with print-ready CSS (A4 page size, exact-color printing, repeating table
  headers) — use the browser's Print to PDF for a PDF copy; a dedicated PDF renderer is a possible
  future addition, not required today.
- Ten report types are available (Executive, Application, Security, Scan, Developer, Vulnerability,
  SBOM, CBOM, Audit, AI Governance) and six export formats (HTML, JSON, CSV, SARIF, CycloneDX,
  SPDX) — see the User Guide §8 for what each covers.
- **Report branding**: a persisted default (logo, etc.) applied to visual reports, configurable in
  Settings.
- The Application security score formula and its four bands, and the "Stale" (30-day) threshold,
  are documented in each report's own Definitions section — auditable without engineering access.
- Sample/golden reports live under `doc/sample-reports/` for internal QA reference only — never
  treat them as live data.

## 8. Privacy & consent

- A first-run, non-dismissible **Privacy Notice** screen must be accepted before registration is
  possible — it discloses exactly what's collected (nothing, until you register; then email +
  device fingerprint) and covers GDPR/India DPDP Act 2023/CCPA rights.
- The decision is stored locally and per-user at
  `%APPDATA%\securegraph\securegraph\config\consent.json` — **not** tied to the app installation,
  so it survives reinstall/uninstall and is shared identically between an installed build and
  `npm run tauri dev`. To re-trigger the notice for testing, delete that file (the app must be
  closed first).
- Full policy: `doc/10_USER_DOCS/PRIVACY_POLICY.md`, also published at
  vaultmorph.org/securegraph/privacy. To review or withdraw consent as a user, unregister via
  Settings → License → Unregister and/or contact contact@vaultmorph.org.

## 9. SIEM integration & Enterprise deployment

- **SIEM Integration** (its own top-level nav item, permission-gated) streams findings/events to
  an external SIEM.
- **Enterprise Deployment** (its own top-level nav item, permission-gated) covers multi-seat/
  organization deployment settings, separate from the free Desktop Community registration flow
  described in §1.

## 10. Data & storage

- Findings, scan history, and the graph are stored locally in a SQLite database under the app's
  data directory (`Settings` shows the exact `config_dir`/`data_dir`/`log_dir` paths, or run the
  in-app `doctor` diagnostic); back these up as part of normal machine backup.
- SBOM/CBOM exports follow the CycloneDX 1.6 schema.
- Config file: `<config_dir>\securegraph.toml`, editable via Settings or directly on disk (a
  malformed file is treated as a hard error on load, not silently ignored, since it could mask a
  security-relevant setting like an accidental `ai_enabled = true`).

## 11. Support & escalation

- Product homepage: vaultmorph.org. Documentation: vaultmorph.org/securegraph/docs.
- License/registration issues: api.vaultmorph.org admin tooling.
- Privacy requests: contact@vaultmorph.org (see §8 and the full Privacy Policy for response times).
- Internal engineering docs (architecture, phase history) live under `doc/09_PHASES/` and
  `doc/01_ARCHITECTURE/` for the dev team — not for end users.
