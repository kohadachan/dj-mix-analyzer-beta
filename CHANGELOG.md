# Changelog

All notable changes to DJ Mix Analyzer are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [0.2.6] - 2026-06-12

### Added
- SQLite `PRAGMA user_version` schema tracking with migration tests.
- Pre-migration DB backup for existing databases before schema upgrades.
- Parser fixture tests for Serato TXT, Traktor, rekordbox TSV/XML, and Soundiiz imports.
- Frontend Vitest setup and tests for pure utilities.
- `scripts/release.sh` for version bumping, checks, macOS app build, ad-hoc re-signing, create-dmg packaging, checksums, and release notes scaffolding.

### Changed
- Aligned `package.json`, `Cargo.toml`, and `tauri.conf.json` versions to `0.2.6`.
- Enrichment now parses large library files before acquiring the DB mutex, then writes in short batches.
- Project documentation now reflects the Rust + TypeScript app with Python removed.
- Release procedure now uses `codesign` and `create-dmg` instead of manual `hdiutil` steps.

### Fixed
- Startup and DB initialization errors now propagate as setup failures instead of panicking.
- Import worker DB-open failures are reported through import progress errors instead of panicking the thread.
- DB migrations now run in a transaction and only tolerate/log duplicate-column legacy cases.
- React hook dependency warnings are fixed without suppressions.
- DMG and release output artifacts are ignored by git.

### Removed
- Legacy Python package, CLI, `pyproject.toml`, and `uv.lock`.

## [0.2.5] - 2026-03-09

### Added
- Serato 4.0.x CSV import support, including Japanese locale headers.

### Fixed
- rekordbox `master.db` decryption and enrich logging.

## [0.2.4] - 2026-03-06

### Added
- Direct rekordbox `master.db` import.

### Fixed
- Practice date calculation.
- Duplicate session handling.
- CSV injection risk in exports.
- Lint issues.

## [0.2.3] - 2026-03-06

### Fixed
- rekordbox XML import issues.
- Table header display issues.
- Generator suggestion behavior.

## [0.2.2] - 2026-03-06

### Added
- Per-app import guide with export instructions for Serato, rekordbox, and Traktor.
- Onboarding hints and empty states.

### Changed
- Improved explorer fit badges, table header alignment, select styling, and data guidance.
- Restored Dashboard sections for top artists, play-count distribution, freshness, and opener/closer analysis.
- Improved Tag Mapping editor layout and inline editing.

## [0.2.1] - 2026-03-05

### Fixed
- JSX namespace build error by using the ReactNode type.

## [0.2.0] - 2026-03-03

### Added
- Full Rust backend and React UI for the Tauri desktop app.
- Analysis features, export formats, and unified error handling.
- Track filters, inline detail, and network graph enhancements.
- App icon updates and improved DJ library detection.

### Fixed
- Serato TXT parser wide-character extraction.
- Poisoned DB lock recovery.
- Windows CI cache behavior for icon changes.
