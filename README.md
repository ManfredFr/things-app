# things-app

A **Claude Skill** that lets you control [Things 3](https://culturedcode.com/things/) — the macOS task manager by Cultured Code — directly from Claude using natural language.

Create tasks, manage projects, query your lists, reschedule, complete, and delete — all without leaving your Claude conversation.

---

## What it does

`/things-app` bridges Claude and Things 3 via **AppleScript** (`osascript`). This means Claude can both **write** (create, update, move, delete tasks) and **read** (query lists, return task data back into the conversation) — making it suitable for automation workflows, not just one-way commands.

It also fills two gaps Things 3 doesn't natively support:
- **Import from Apple Reminders** _(deprecated)_ — copy a Reminders list into Things as a project (flat list only; Reminders' AppleScript API exposes no sections)
- **YAML templates** — define reusable project structures as `.yaml` files and import them into Things on demand

It is intentionally a building block. On its own it manages Things. Combined with other Claude tools (Gmail, Calendar, file access) it becomes a powerful automation layer.

---

## Requirements

- **Claude Code running on macOS** — the skill executes `osascript` (AppleScript) in the local shell, so that shell must be on the same Mac as Things.
- [Things 3](https://culturedcode.com/things/) installed

> **Not supported in Cowork.** Cowork runs shell commands in a remote Linux sandbox that has no AppleScript and no connection to your Mac's Things app, so the skill's commands can't execute there. Use it with Claude Code on your Mac.

---

## Installation

Clone the repository into your Claude skills directory:

```bash
git clone https://github.com/ManfredFr/things-app.git ~/.claude/skills/things-app
```

Claude Code automatically discovers skills from `~/.claude/skills/`. Restart Claude Code to load a newly installed skill — discovery runs at startup. The skill is then available as `/things-app`.

> **Install a real clone, not a symlink.** Cloning (a regular directory) is what skill discovery expects. A symlink — e.g. pointing at a working copy in Dropbox/iCloud — may not be followed, and sandboxed apps can be blocked from reading the link's target.

> **About the `.skill` release asset.** Each tagged release attaches a `things-app.skill` file (built by [`.github/workflows/package-skill.yml`](.github/workflows/package-skill.yml)) for the desktop app's one-click install. It installs, but the skill still **won't function in Cowork** for the sandbox reason above — it's provided for completeness, not as a supported path.

### Updating

Pull the latest into the installed copy:

```bash
git -C ~/.claude/skills/things-app pull
```

Then restart Claude to pick up the changes.

---

## Usage

Invoke the skill directly:

```
/things-app add a task "Call the bank" to Today
```

Or trigger it naturally — Claude will invoke it automatically when context makes it clear you're working with Things 3.

---

## Examples

Examples progress from simple single-step commands to multi-step workflows.

### 1. Add a task to the Inbox

```
/things-app add "Buy milk" to my inbox
```

### 2. Add a task to Today

```
/things-app add "Send weekly report" to Today
```

### 3. Add a task with a deadline

```
/things-app add "Submit tax return" with a deadline of June 30
```

### 4. Show what's due today

```
/things-app show me what's due today
```

Returns a formatted list of your Today tasks with deadlines directly in the Claude response.

### 5. Mark a task complete

```
/things-app mark "Buy milk" as done
```

### 6. Reschedule a task

```
/things-app move "Call dentist" to next Monday
```

### 7. Create a project with tasks

```
/things-app create a project called "Website Redesign" with tasks:
- Write content brief
- Review designs
- Approve final mockups
```

### 8. Add a task with notes and tags

```
/things-app add "Review lease agreement" with notes "Focus on exit clause section 4" 
tagged "legal, property" due Friday
```

### 9. Query and filter tasks

```
/things-app list all tasks in my Inbox that contain "invoice"
```

Returns matching tasks with their details in the conversation.

### 10. Move all inbox tasks tagged "work" to a project

```
/things-app move everything in my inbox tagged "work" into the project "Q3 Work"
```

### 11. Gmail → Things inbox workflow

This example combines `/things-app` with Gmail MCP tools — it is not built into the skill but shows how it composes with other tools:

```
For every email in Gmail labelled "Follow Up":
- Create a Things inbox task using the email subject as the title
- Add a link to the email thread in the task notes
```

Claude will use the Gmail MCP to read the emails and `/things-app` to create the tasks in a single conversation turn.

### 12. Daily review routine

```
/things-app show me everything due today and anything overdue, 
grouped by project
```

---

## Importing from Apple Reminders (deprecated)

> **⚠️ Deprecated.** Kept for backward compatibility only. Reminders' AppleScript API cannot expose sections, so this import is always a **flat list** and loses any structure the source list had. For a structured project, use a [YAML template](#yaml-templates) instead. This feature may be removed in a future release.

Things 3 has no built-in import from Reminders. This skill reads a Reminders list via AppleScript and recreates it in Things as a project.

**How to do it:**

```
Copy my Reminders list "Packing List" into Things as a project
```

Claude will:
1. Read all items from the named Reminders list via AppleScript
2. Create the Things project with those tasks

**Sections are not preserved — the import is a flat list.**  
Apple's Reminders AppleScript API has no section/group support: it returns **only the reminders**, and section headers are omitted from the output entirely (they are not even returned as ordinary items). There is no AppleScript-only way to recover them. The only alternatives — reading the Reminders SQLite store (requires granting Full Disk Access) or reading the app's on-screen UI — are not portable, so this skill deliberately does not use them.

If you want sections in the resulting Things project, add the headings yourself afterwards, or import from a [YAML template](#yaml-templates) instead, which supports sections by design.

---

## YAML Templates

Things 3 has no native template support. This skill introduces a simple YAML format for reusable project structures.

### Template format

```yaml
name: Project Name
type: project

sections:

  - title: Section Heading
    tasks:
      - Task one
      - Task two

  - title: Another Section
    tasks:
      - Task three
```

### Importing a template

```
/things-app import /path/to/template.yaml
```

Claude reads the YAML file, converts it to the Things JSON format, and creates the project with all headings and tasks in one call.

**Example:**

```
/things-app import ~/.claude/skills/things-app/templates/cairns-trip.yaml
```

Creates a *Cairns Trip* project with 5 sections and 80 tasks.

### Bundled templates

| Template | Description |
|----------|-------------|
| [`templates/cairns-trip.yaml`](templates/cairns-trip.yaml) | International travel packing list with sections for carry-on, check-in luggage, electronics, personal care, and pre-departure tasks |

You can add your own templates anywhere on disk and import them by path.

---

## How it works

The skill is defined in [`SKILL.md`](SKILL.md) — a Markdown file with YAML frontmatter that Claude reads to understand when and how to invoke the skill.

At runtime, Claude generates AppleScript and executes it via `osascript` in Bash. Things 3 launches automatically if it isn't already running.

**Why AppleScript over the Things URL scheme?**  
The Things URL scheme (`things:///`) is write-only — it can trigger actions but cannot return data. AppleScript supports full bidirectional communication: Claude can query your tasks and use the results in its response or chain them into further actions.

**One exception — headings:**  
Headings inside projects are not in the Things 3 AppleScript dictionary and cannot be created via AppleScript. For any operation that requires headings (creating a project with sections, importing a template), the skill falls back to the `things:///json` URL scheme, which does support them.

**Documentation sources:**
- [Things AppleScript Overview](https://culturedcode.com/things/support/articles/2803572/)
- [Things AppleScript Technical Reference](https://culturedcode.com/things/support/articles/4562654/)
- [Things URL Scheme Reference](https://culturedcode.com/things/support/articles/2803573/)

---

## Repository structure

| Path | Purpose |
|------|---------|
| [`SKILL.md`](SKILL.md) | Skill definition — instructions Claude follows |
| [`templates/`](templates/) | Reusable YAML project templates |
| [`reference/`](reference/) | Reference docs loaded on demand |
| [`LICENSE`](LICENSE) | MIT |

---

## Contributing

Issues and pull requests welcome.

When editing `SKILL.md`, keep the `description` frontmatter field precise — it is the primary signal Claude uses to decide when to trigger the skill automatically.

---

## License

[MIT](LICENSE) © Manfred Friedrich
