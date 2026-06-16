---
name: things-app
description: Interact with Cultured Code's Things app (macOS/iOS task manager) — create to-dos and projects, add tasks to lists, and query existing items. Use when the user wants to capture a task, plan a project, or work with their Things to-dos and areas.
---

# Things App

Interact with [Cultured Code Things](https://culturedcode.com/things/) — the macOS/iOS task manager — from Claude.

> **Scaffold notice:** This is the public repository structure for the `things-app`
> skill. The working skill body is maintained separately and will replace the
> placeholder instructions below. Keep the frontmatter `name` and `description`
> accurate as the implementation lands.

## When to use this skill

Use this skill when the user wants to:

- Capture a quick to-do or note into Things
- Create a project with multiple to-dos, headings, checklist items
- Add tasks to a specific list, area, or project (Inbox, Today, Upcoming, etc.)
- Read or query existing to-dos, projects, and areas

## How it works

Things exposes two integration surfaces on macOS:

- **URL scheme (`things:///`)** — the documented, write-oriented interface for
  adding and updating items. See [reference/url-scheme.md](reference/url-scheme.md).
- **AppleScript / `osascript`** — for richer reads and scripting. See
  [reference/applescript.md](reference/applescript.md).

Helper scripts live in [scripts/](scripts/).

## Instructions

<!-- Replace this section with the real skill workflow when content lands. -->

1. Determine whether the user wants to **create** (use the URL scheme) or
   **read/query** (use AppleScript) Things data.
2. Build the appropriate command, confirming destructive or bulk actions first.
3. Report back what was created or found, linking to the item where possible.

## Notes

- This skill targets the desktop Things app on macOS.
- Reads via the URL scheme require Things' "Enable Things URLs" setting and an
  auth token (Things → Settings → General → Enable Things URLs → Manage).
