# things-app

A **Claude Skill** that lets you control [Things 3](https://culturedcode.com/things/) — the macOS task manager by Cultured Code — directly from Claude using natural language.

Create tasks, manage projects, query your lists, reschedule, complete, and delete — all without leaving your Claude conversation.

---

## What it does

`/things-app` bridges Claude and Things 3 via **AppleScript** (`osascript`). This means Claude can both **write** (create, update, move, delete tasks) and **read** (query lists, return task data back into the conversation) — making it suitable for automation workflows, not just one-way commands.

It is intentionally a building block. On its own it manages Things. Combined with other Claude tools (Gmail, Calendar, file access) it becomes a powerful automation layer.

---

## Requirements

- macOS (Things 3 and AppleScript are macOS-only)
- [Things 3](https://culturedcode.com/things/) installed
- Claude Code or Claude Cowork

---

## Installation

Clone the repository into your Claude skills directory:

```bash
git clone https://github.com/ManfredFr/things-app.git ~/.claude/skills/things-app
```

Claude automatically discovers skills from `~/.claude/skills/`. The skill is immediately available as `/things-app`.

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
- Remove the "Follow Up" label from the email
```

Claude will use the Gmail MCP to read the emails and `/things-app` to create the tasks in a single conversation turn.

### 12. Daily review routine

```
/things-app show me everything due today and anything overdue, 
grouped by project
```

---

## How it works

The skill is defined in [`SKILL.md`](SKILL.md) — a Markdown file with YAML frontmatter that Claude Code reads to understand when and how to invoke the skill.

At runtime, Claude generates AppleScript and executes it via `osascript` in Bash. Things 3 launches automatically if it isn't already running.

**Why AppleScript over the Things URL scheme?**  
The Things URL scheme (`things:///`) is write-only — it can trigger actions but cannot return data. AppleScript supports full bidirectional communication: Claude can query your tasks and use the results in its response or chain them into further actions.

**Documentation sources:**
- [Things AppleScript Overview](https://culturedcode.com/things/support/articles/2803572/)
- [Things AppleScript Technical Reference](https://culturedcode.com/things/support/articles/4562654/)
- [Things URL Scheme Reference](https://culturedcode.com/things/support/articles/2803573/)

---

## Repository structure

| Path | Purpose |
|------|---------|
| [`SKILL.md`](SKILL.md) | Skill definition — instructions Claude follows |
| [`scripts/`](scripts/) | Helper scripts (currently unused) |
| [`reference/`](reference/) | Reference docs loaded on demand |
| [`LICENSE`](LICENSE) | MIT |

---

## Contributing

Issues and pull requests welcome.

When editing `SKILL.md`, keep the `description` frontmatter field precise — it is the primary signal Claude uses to decide when to trigger the skill automatically.

---

## License

[MIT](LICENSE) © Manfred Friedrich
