# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

A collection of Raycast Script Commands (macOS) for focusing/switching browser tabs in the Comet browser. The primary system is a code generator that produces focus scripts from a JSON config.

## Key Workflow

To add or modify a focus script:
1. Edit `focus-configs.json` — add/modify an entry with `name`, `title`, `icon`, `url_patterns`, `default_url`, and `use_history`
2. Run `./generate-history-scripts.sh` to regenerate all scripts in `focus/`
3. Never hand-edit files in `focus/` — they are generated and will be overwritten

See `example-config.json` for documented config format.

## Architecture

**Generator (`generate-history-scripts.sh`):** Reads `focus-configs.json`, produces one bash script per entry in `focus/`. Each generated script embeds AppleScript to control Comet browser windows/tabs. Two modes:
- `use_history: true` — queries Comet's SQLite history DB (`~/Library/Application Support/Comet/Default/History`) for the most recently visited matching URL, falls back to `default_url`
- `use_history: false` — always opens the hardcoded `default_url`

**Generated focus scripts (`focus/`):** Each script checks if Comet is running. If running, it cycles through tabs matching `url_patterns` (round-robin if current tab already matches). If not running, it launches Comet, waits for tab restore, then focuses a matching tab.

**Standalone scripts (`other/`):** One-off Raycast scripts not managed by the generator (e.g., `google-meet.sh` is a toggle that shows/hides a Meet window).

**Graveyard (`graveyard/`):** Deprecated/experimental scripts in various languages (Ruby, Python, AppleScript, Swift). `graveyard/spec.md` contains a detailed behavior spec for ChatGPT toggle logic.

## Raycast Script Metadata

All scripts use Raycast's comment-based metadata format:
```
# @raycast.schemaVersion 1
# @raycast.title Script Title
# @raycast.mode silent
# @raycast.icon 🔗
# @raycast.author Anders Bekkevard
```

## Runtime Dependencies

- macOS with Raycast installed
- Comet browser (Chromium-based, at `/Applications/Comet.app`)
- `python3` (used by the generator to parse JSON)
- `sqlite3` (used by history-enabled scripts to query browser history)
- `osascript` (AppleScript, ships with macOS)
