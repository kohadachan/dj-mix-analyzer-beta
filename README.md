**English** | [日本語](README.ja.md)

# DJ Mix Analyzer — Beta Release

> **Beta**: This is a limited beta release for testing.
> Please report bugs or feedback via [Issues](../../issues).

DJ Mix Analyzer imports play history from Serato DJ / rekordbox / Traktor, and provides track usage trends, transition analysis, setlist building, and more. Built with [Tauri](https://tauri.app/) (Rust + React).

---

## Download

Download the latest version from the [Releases page](../../releases).

| Platform | File | Notes |
|:---:|:---|:---|
| **macOS** (Apple Silicon) | `DJ_Mix_Analyzer_0.2.5_aarch64.dmg` | macOS 12+ |
| **Windows** (64-bit) | `DJ_Mix_Analyzer_0.2.5_x64-setup.exe` | Windows 10+ (recommended) |
| **Windows** (64-bit) | `DJ_Mix_Analyzer_0.2.5_x64_en-US.msi` | For enterprise environments |

### File Verification (SHA-256)

You can verify the integrity of your download:

```
f69866e4fcf43515bc9f1239bbbdc0810c79444f8af5df3635f628c68e90aa4c  DJ_Mix_Analyzer_0.2.5_aarch64.dmg
c08a6921d86e785674653d8745fbd06ffb58019f66c162fd87defa39a7e0b7b4  DJ_Mix_Analyzer_0.2.5_x64-setup.exe
82bfa552e2fd309e2abb138fc76a624f01958508e40b920db0e692739b1691e6  DJ_Mix_Analyzer_0.2.5_x64_en-US.msi
```

**macOS:**
```bash
shasum -a 256 ~/Downloads/DJ_Mix_Analyzer_0.2.5_aarch64.dmg
```

**Windows (PowerShell):**
```powershell
Get-FileHash "$env:USERPROFILE\Downloads\DJ_Mix_Analyzer_0.2.5_x64-setup.exe" -Algorithm SHA256
```

---

## Installation

### macOS

1. Open the `.dmg` file
2. Drag "DJ Mix Analyzer" to the "Applications" folder
3. On first launch, you'll see an **"unidentified developer"** warning:
   - Click the **?** button in the top-right corner of the dialog
   - Go to **System Settings** → **Privacy & Security** → click **"Open Anyway"**
   - Or right-click the `.app` → "Open" → "Open"

> **Why the warning?** The app is not signed with an Apple Developer certificate. See the "Privacy & Safety" section below for details on the app's security.

### Windows

1. Run the `.exe` file (NSIS installer)
2. If **Windows SmartScreen** appears:
   - Click "More info" → "Run anyway"
3. Follow the installer instructions

> **Why the warning?** The app is not signed with a Microsoft-certified code signing certificate. This is common for beta software from independent developers.

> **Verification tips:**
> - Confirm the download came from this GitHub repository's Releases page
> - Verify the SHA-256 hash matches the values above
> - Check that the file size is reasonable (~3.5 MB for .exe, ~5 MB for .msi)

### Uninstall

- **macOS**: Move "DJ Mix Analyzer" from `Applications` to Trash. To also remove data: delete `~/Library/Application Support/com.kohadachan.dj-mix-analyzer/`
- **Windows**: Settings → Apps → "DJ Mix Analyzer" → Uninstall. To also remove data: delete `%APPDATA%\com.kohadachan.dj-mix-analyzer\`

---

## Usage

### First Launch

The app opens with an empty dashboard. Import your DJ play history to get started.

### Importing Data

1. Go to the **"Library"** tab → **"Import"** sub-tab
2. Check the in-app **"How to export from your DJ software"** guide
3. Import files by:
   - **Drag & drop** files or folders onto the drop zone
   - Or click **"Browse Folder..."** to select a folder
4. After import, check the **"Dashboard"** tab for your stats

#### Import vs. Enrich

| | **Import** (Session Import) | **Enrich** (BPM / Key) |
|:---|:---|:---|
| **Purpose** | Register play history (sessions + tracks) to the database | Add BPM & Key metadata to existing tracks |
| **Data flow** | Files → new sessions & tracks created | Library → existing tracks' BPM/Key columns updated |
| **Location** | Library → Import drop zone | Library → Import, Enrich section below |

#### Recommended Import Workflow by DJ Software

Here's the most efficient way to get your data into DJ Mix Analyzer for each DJ software.

<details>
<summary>Serato DJ — 2 Steps (Import → Enrich)</summary>

Serato stores play history and track metadata in **separate files**, so importing requires two steps.

**Step 1: Import (Play History)**

Serato DJ automatically saves a session file (`.txt`) every time you play. Import the entire folder at once:

- **Mac**: `~/Music/_Serato_/History/Sessions/`
- **Windows**: `Music\_Serato_\History\Sessions\`

Drag & drop the Sessions folder to import all past sessions at once.
Duplicate detection prevents the same sessions from being imported twice, so it's safe to re-import.

> **Session files do not contain BPM/Key data.** Serato's session files only record track name, artist, play time, and deck info. BPM and Key are not included. The next step will fill those in.

**Step 2: Enrich (Add BPM/Key)**

Serato's internal database (`database V2`) stores all track metadata — including BPM and Key — regardless of your UI column display settings. The Enrich feature reads this database and automatically fills in BPM/Key for your imported tracks.

1. Go to **"Library"** → **"Import"**, scroll down to the Enrich section
2. Your Serato library will be auto-detected — select it and click **"Enrich"**

> **About CSV export**: You can also export CSV from Serato's History tab, but the columns included in the CSV **depend on what columns you have displayed in the UI** at the time of export (e.g., if Key is not shown, it won't be in the CSV). Session files (`.txt`) + Enrich is the more reliable method.

</details>

<details>
<summary>rekordbox — 1 Step (just master.db)</summary>

rekordbox stores both play history and track metadata in a single `master.db` file, so **one import does everything**.

1. Locate your `master.db`:
   - **Mac**: `~/Library/Pioneer/rekordbox/master.db`
   - **Windows**: `%APPDATA%\Pioneer\rekordbox\master.db`
2. Drag & drop `master.db`, or select it via "Browse"

This imports **play history + BPM + Key + Genre** all at once. No Enrich step needed.

> **Note**: master.db is accessed read-only. Your rekordbox data will not be modified.

> **About XML export**: rekordbox's "Export collection in xml format" can also be imported, but XML does **not** contain play history (HISTORY). Use it only as an Enrich source for BPM/Key.

</details>

<details>
<summary>Traktor — 1 Step (just collection.nml)</summary>

Traktor also stores both play history and track metadata in a single `collection.nml` file, so **one import does everything**.

- **Mac**: `~/Documents/Native Instruments/Traktor X.X.X/collection.nml`
- **Windows**: `Documents\Native Instruments\Traktor X.X.X\collection.nml`

Drag & drop collection.nml, or select it via "Browse".
Play history + BPM + Key are all imported at once.

</details>

#### Summary: Import Flow by DJ Software

| DJ Software | Steps | Import Source | Enrich Needed? |
|:---|:---|:---|:---:|
| **Serato DJ** | 2 steps | `Sessions/` folder → Enrich | **Yes** |
| **rekordbox** | 1 step | `master.db` | No |
| **Traktor** | 1 step | `collection.nml` | No |

### BPM / Key Enrichment (Enrich)

Adds BPM & Key metadata to previously imported tracks. Primarily used by **Serato users** (rekordbox / Traktor tracks get this data automatically during import).

1. Go to **"Library"** tab → **"Import"** sub-tab, scroll to the **Enrich** section
2. DJ software libraries are auto-detected (if not found, use "Add Source..." to specify manually)
3. Select a source and click **"Enrich"**

| Source | Data | Notes |
|:---|:---|:---|
| **Serato** `_Serato_/` | BPM, Key | database V2 + streaming Metadata |
| **rekordbox** `master.db` | BPM, Key | Not needed if already imported via master.db |
| **rekordbox** XML | BPM, Key | Fallback when master.db is unavailable |
| **Traktor** `collection.nml` | BPM, Key | Not needed if already imported via collection.nml |

### Features

| Tab | Description |
|:---|:---|
| **Dashboard** | Track/session counts, top tracks/artists, day-of-week & hourly analysis |
| **Tracks** | Full track list with Key/BPM/Tag filters, inline detail expansion, page size control |
| **Transitions** | Transition stats, top pairs, N-track chains, artist transitions, deck analysis |
| **Insights** | Hub tracks, entropy map, bridge tracks, set diversity, escape routes |
| **Flow** | Visualize before/after transitions for a selected track |
| **Network** | Transition network graph with Key & BPM displayed inside nodes |
| **Progression** | Per-session BPM & Key change analysis |
| **Escapes** | Suggest alternative transitions from well-worn patterns (with track search) |
| **Rediscover** | Find tracks you used to play frequently but haven't played recently |
| **Explorer** | Manual setlist builder with real-time next-track suggestions |
| **Generator** | Auto-generate setlists based on transition history |
| **Setlists** | Manage saved setlists, export to CSV |
| **Settings** | Key notation (Camelot/Musical), font size, theme, export directory |

---

## Privacy & Safety

The following is based on the source code implementation.

### Network Communication

**This app makes no internet connections whatsoever.**

- No HTTP client libraries (reqwest, hyper, etc.) are included
- Frontend external URL connections are restricted by CSP (Content Security Policy)
- Tauri capabilities do not permit network APIs

### Telemetry

**No usage data collection, crash reporting, or analytics tracking of any kind.**

### File Access

- **Read**: Only DJ software files explicitly selected by the user via the file dialog
- **Write**: Only the SQLite database (`dj_history.db`) in the app data folder, and CSV files to user-specified export destinations
- **No modification**: Your DJ software's original files are never modified or deleted (read-only access)

### Enrich Auto-Detection

When detecting Enrich sources, the app checks for the existence of these directories (file contents are not read):

- **macOS**: External drives under `/Volumes/`
- **Windows**: Drives D: through Z:

This is used solely to locate DJ software library folders. No files are read until the user explicitly runs "Enrich".

### Data Storage

All data is stored locally:

| OS | Path |
|:---|:---|
| macOS | `~/Library/Application Support/com.kohadachan.dj-mix-analyzer/` |
| Windows | `%APPDATA%\com.kohadachan.dj-mix-analyzer\` |

See [PRIVACY.md](PRIVACY.md) for full details.

---

## Known Limitations

- No macOS Intel build available (Apple Silicon only)
- The app is not code-signed, so OS warnings will appear on first launch
- Serato session files (.txt) do not include BPM/Key — use the Enrich feature to add them from Serato's database
- Serato 4.0.x CSV exports include BPM & Key directly (Japanese locale also supported)
- Beta software — unexpected behavior may occur

## Feedback

Please report bugs or feature requests via [Issues](../../issues).
For bug reports, please use the [template](../../issues/new?template=bug_report.yml).

---

## Disclaimer

This software is provided "AS IS" without warranty of any kind. The author shall not be liable for any damages arising from the use or inability to use this software, including but not limited to data loss, equipment malfunction, or any other direct or indirect damages. As beta software, unexpected behavior may occur. Please back up important data before use.

By installing or using this software, you agree to this disclaimer.

## License

All rights reserved. The source code of this software is proprietary.
Installers distributed through this repository are licensed for personal use only.
