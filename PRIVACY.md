**English** | [日本語](PRIVACY.ja.md)

# Privacy Policy

Last updated: 2026-03-17

## Overview

DJ Mix Analyzer is a fully offline desktop application. All user data is stored locally on your machine and is never transmitted externally.

## Technical Basis

The following statements are verified facts based on the source code implementation.

### 1. Network Communication: None

| Check | Result |
|:---|:---|
| HTTP client libraries in Rust dependencies | **Not included** (no reqwest, hyper, ureq, etc. in Cargo.toml) |
| External communication code in frontend | **None** (no fetch(), XMLHttpRequest, etc.) |
| External connections allowed by CSP | **Not allowed** (`connect-src ipc: http://ipc.localhost` only) |
| Network permitted by Tauri capabilities | **Not permitted** (`core:default` and `dialog:default` only) |

### 2. File Access

**Files read:**
- DJ software session/library files explicitly selected by the user via the file dialog
- The app's own SQLite database

**Files written:**
- `dj_history.db` (SQLite database in the app data folder)
- `dj_history.db-wal`, `dj_history.db-shm` (SQLite WAL mode temporary files)
- CSV export files to a directory explicitly specified by the user

**Files never modified:**
- DJ software original files (Serato, rekordbox, Traktor library and session files) are accessed read-only and are never modified

### 3. External Processes

No subprocesses are launched. All processing is self-contained within the application.

### 4. Telemetry

No usage data collection, crash reporting, or analytics tracking is implemented.

### 5. Stored Data

Data stored in the SQLite database:

- Session information (filename, date/time, track count)
- Track information (title, artist, BPM, Key)
- Track play order and duration within sessions
- User settings (Key notation, theme, font size, export path)
- Saved setlists

No personally identifiable information (name, email address, account information, etc.) is stored.

### 6. Data Storage Location

| OS | Path |
|:---|:---|
| macOS | `~/Library/Application Support/com.kohadachan.dj-mix-analyzer/dj_history.db` |
| Windows | `%APPDATA%\com.kohadachan.dj-mix-analyzer\dj_history.db` |

### 7. Complete Data Removal

Uninstalling the app does not automatically delete the data folder above. To completely remove all data, manually delete the folder at the path shown above.

## Unverified Items

- Whether the Tauri framework itself performs any internal network communication is outside the scope of this verification. Please refer to the official Tauri documentation. No such behavior has been observed, but we do not guarantee the internal behavior of the framework in its entirety.

## Contact

For privacy-related questions, please reach out via [Issues](../../issues).
