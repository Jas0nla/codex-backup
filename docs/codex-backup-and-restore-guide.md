# Codex: Backup and Restore Guide

This guide is for local Codex backup, restore, and post-restore validation.

It is written to avoid the most common mistake:

- assuming chats are lost when they still exist
- but the project/workspace registration in the Codex UI is missing

## Scope

This guide covers:

- what to back up
- how to restore safely
- how to verify whether chats are truly missing
- what to do when `Chronological list` works but `By project` does not

## Main Local Data Roots

Codex uses two important local data roots:

1. App state
   - macOS path:
     `/Users/jason/Library/Application Support/Codex`
   - purpose:
     UI state, cache, local runtime state, cookies, Electron storage

2. Codex data root
   - macOS path:
     `/Users/jason/.codex`
   - purpose:
     sessions, sqlite state, session index, logs, skills, runtime metadata

If you restore only one of them, you may get partial recovery.

## What To Back Up

Before any reset or risky repair, back up both:

- `/Users/jason/Library/Application Support/Codex`
- `/Users/jason/.codex`

Recommended backup structure:

```text
Codex/
  full-reset-backup/
    Application-Support-Codex-YYYYMMDD-HHMMSS/
    dot-codex-YYYYMMDD-HHMMSS/
    RESTORE_CODEX.md
```

## Full Restore

Use this when you want to restore the full local Codex state.

1. Quit Codex completely.
2. Move or remove the current live directories.
3. Restore both backup roots.
4. Re-open Codex.

Example commands:

```bash
osascript -e 'quit app "Codex"'
rm -rf '/Users/jason/Library/Application Support/Codex'
rm -rf '/Users/jason/.codex'
rsync -a '/Users/jason/Library/CloudStorage/OneDrive-Jasonchenloans/Codex/full-reset-backup/Application-Support-Codex-20260417-214507/' '/Users/jason/Library/Application Support/Codex/'
rsync -a '/Users/jason/Library/CloudStorage/OneDrive-Jasonchenloans/Codex/full-reset-backup/dot-codex-20260417-214507/' '/Users/jason/.codex/'
open -a Codex
```

## Safer Restore Order

Do not jump to full rollback first unless you need to.

Recommended order:

1. Restore or reset only `Application Support/Codex` if the problem looks UI-only.
2. Re-test Codex.
3. Restore or inspect `~/.codex` only if session/state data is actually missing.
4. Use full restore only when both layers must be reverted together.

## First Validation After Restore

After reopening Codex, check these in order:

1. Can `Chats -> Chronological list` still see the expected sessions?
2. Can `Chats -> By project` see them?
3. Is the expected project root still added in the Codex UI?

This order matters.

If `Chronological list` still works, do not immediately treat the situation as chat loss.

## Important Special Case

### Symptom

- `Chronological list` still shows old sessions
- `By project` is empty or missing expected sessions
- this happened after backup restore or local reset

### What It Usually Means

This usually means:

- the underlying thread/session data still exists
- but the workspace root is no longer registered in Codex UI
- so Codex has nothing to group those sessions under in `By project`

### Correct Fix Order

1. Open `Chats -> Chronological list`.
2. If the missing sessions are visible there, stop treating this as data loss.
3. Check whether the expected project/workspace root is still present in Codex UI.
4. If the project root is missing, add it back in the UI.
5. Re-check `Chats -> By project`.

### Do Not Jump To These First

Do not first assume:

- `.codex` is corrupted
- `session_index.jsonl` must be replaced
- sqlite must be rebuilt
- full backup rollback is required

Those may be unnecessary if the real issue is missing workspace registration.

## Known Working Example

Observed on this machine:

- `Chronological list` still had the sessions
- `By project` was missing them
- manually adding `/Users/jason/Documents/Playground` back into the Codex UI restored the sessions immediately

That means:

- the chats were not gone
- the missing piece was workspace registration in the UI

## Fast Re-Add Method Without Mouse Input

If a project root is missing from Codex and you want to re-register it without using mouse clicks, a working method on this machine was:

```bash
open -a Codex "/full/project/path"
```

Examples:

```bash
open -a Codex "/Users/jason/Documents/Project/AgentOperatingLayer"
open -a Codex "/Users/jason/Documents/Project/appraisal-skill"
open -a Codex "/Users/jason/Documents/Project/Claw-code"
```

This was more reliable than:

- editing sqlite first
- editing global state first
- trying to repair `By project` indirectly

Practical interpretation:

- if the chats still exist
- and the project root itself still exists on disk
- then directly opening that folder in Codex can cause Codex to re-register the workspace and restore the `By project` grouping

Use this before deeper metadata repair whenever possible.

## Useful Local Files For Debugging

If deeper debugging is needed, inspect these:

- `~/.codex/state_5.sqlite`
- `~/.codex/session_index.jsonl`
- `~/.codex/.codex-global-state.json`
- `/Users/jason/Library/Application Support/Codex`

Useful interpretation:

- `state_5.sqlite`:
  thread metadata
- `session_index.jsonl`:
  indexed session list
- `.codex-global-state.json`:
  saved workspace roots, project ordering, and global UI-related state
- `Application Support/Codex`:
  Electron app/UI cache and local app state

## Practical Recovery Checklist

Use this checklist before escalating:

1. Verify whether the missing chats still appear in `Chronological list`.
2. Verify whether the expected workspace root still exists in Codex UI.
3. Re-add the missing workspace root if needed.
4. Re-test `By project`.
5. Only then consider cache reset or backup restore.
6. Only after that consider deeper index/database repair.

## Recommended Workspace Root Re-Add List

If `By project` is incomplete after restore, re-check whether these commonly used local project roots are still registered in the Codex UI.

High priority:

- `/Users/jason/Documents/Project/AgentOperatingLayer`
- `/Users/jason/Documents/Project/appraisal-skill`
- `/Users/jason/Documents/Project/Claw-code`

Lower frequency but still worth re-adding:

- `/Users/jason/Documents/Project/Vercel`
- `/Users/jason/Documents/Project/MLO`
- `/Users/jason/Documents/Project/property-report-studio`
- `/Users/jason/Documents/Project/PASEO UPLOAD FILE PATCH`

Already confirmed important:

- `/Users/jason/Documents/Playground`

If these are missing from the Codex UI workspace list, `By project` may hide the related sessions even when the chats still exist locally.

## Summary

When restore appears to succeed but `By project` is empty:

- check `Chronological list` first
- do not assume chat loss
- re-add the missing project root in the Codex UI
- only use deeper restore/debug steps if that fails
