---
name: app-store-changelog
description: Generate user-facing App Store release notes from git history since the last tag. Use when preparing an iOS / iPadOS / visionOS / macOS / watchOS App Store submission, drafting "What's New in Version X" notes, translating commit messages into customer-friendly bullets, or producing release notes for App Store Connect.
---

# App Store Changelog

## Overview

Generate a comprehensive, user-facing changelog from git history since the last tag, then translate commits into clear App Store release notes.

## Workflow

### 1) Collect changes

- Run `scripts/collect_release_changes.sh` from the repo root to gather commits and touched files.
- If needed, pass a specific tag or ref: `scripts/collect_release_changes.sh v1.2.3 HEAD`.
- If no tags exist, the script falls back to full history.

### 2) Triage for user impact

- Scan commits and files to identify user-visible changes.
- Group changes by theme (New, Improved, Fixed) and deduplicate overlaps.
- Drop internal-only work (build scripts, refactors, dependency bumps, CI).

### 3) Draft App Store notes

- Write short, benefit-focused bullets for each user-facing change.
- Use clear verbs and plain language; avoid internal jargon.
- Prefer 5 to 10 bullets unless the user requests a different length.

### 4) Validate

- Ensure every bullet maps back to a real change in the range.
- Check for duplicates and overly technical wording.
- Ask for clarification if any change is ambiguous or possibly internal-only.

### 5) Check App Review rules

Release notes are App Store metadata, so App Review checks them.

- **List every significant change (§2.3.12).** New features and product changes must appear in "What's New". A generic line like "Bug fixes and performance improvements" is only acceptable when the release really is just bug fixes, security updates, or performance work.
- **Only describe what ships in this build (§2.3.1(a)).** Don't announce features that are still behind a disabled flag or rolling out later, and don't promise upcoming features.
- **Match the App Review notes (§2.3.1(a), §2.5.19).** Every user-visible feature in the notes should also be described specifically in App Store Connect's Notes for Review, with how to reach it. Features that ship hidden or undisclosed count as hidden or dormant features, which since October 16, 2026 can lead to app removal and termination of the developer account.
- **Keep it clean.** Don't use other apps' or brands' names to borrow attention (§2.3.7, §4.1). Avoid quoting prices or discounts, which can differ by storefront and become a "false price" (§2.3.1(a)), and avoid unverifiable claims such as "#1" or "fastest".
- If a commit adds a feature that is off by default or server-controlled, flag it to the user instead of silently dropping or including it.

## Commit-to-Bullet Examples

The following shows how raw commits are translated into App Store bullets:

| Raw commit message | App Store bullet |
|---|---|
| `fix(auth): resolve token refresh race condition on iOS 17` | Fixed a login issue that could leave some users unexpectedly signed out. |
| `feat(search): add voice input to search bar` | Search your library hands-free with the new voice input option. |
| `perf(timeline): lazy-load images to reduce scroll jank` | Scrolling through your timeline is now smoother and faster. |

Internal-only commits that are dropped (no user impact):

- `chore: upgrade fastlane to 2.219`
- `refactor(network): extract URLSession wrapper into module`
- `ci: add nightly build job`

## Example Output

**What's New in Version 3.4**

- Search your library hands-free with the new voice input option.
- Scrolling through your timeline is now smoother and faster.
- Fixed a login issue that could leave some users unexpectedly signed out.
- Added dark-mode support to the settings screen.
- Improved load times when opening large photo albums.

## Output Format

- Title (optional): "What's New" or product name + version.
- Bullet list only; one sentence per bullet.
- Stick to storefront limits if the user provides one.

## Resources

- `scripts/collect_release_changes.sh` — Collect commits and touched files since last tag.
- `references/release-notes-guidelines.md` — Language, filtering, and QA rules for App Store notes.
