---
name: things-app
description: Interact with the Things 3 task manager app on macOS using AppleScript. Use this skill whenever the user wants to create tasks, read tasks, list to-dos, manage projects, search Things, show a list, update or complete existing tasks, or do anything with Things 3 — even if they don't say "Things" explicitly but context makes it clear they're using it for task management.
compatibility:
  platform: macOS
  requires: Things 3 (must be installed)
---

# Things 3 — AppleScript Integration

Control Things 3 via AppleScript, executed through Bash using `osascript`. This approach supports both **reading** (querying tasks, projects, areas, tags) and **writing** (creating, modifying, moving, deleting items).

Run AppleScript inline:

```bash
osascript -e 'tell application "Things3" to ...'
```

Or as a heredoc for multi-line scripts:

```bash
osascript <<'EOF'
tell application "Things3"
  ...
end tell
EOF
```

Things 3 will launch automatically if not running.

---

## Objects & Properties

### To-Do
- `name` — title
- `notes` — body text
- `due date` — deadline (date or `missing value`)
- `activation date` — the "when" date (scheduled date)
- `completion date` — when it was completed
- `cancellation date`
- `creation date`, `modification date`
- `status` — `open`, `completed`, or `canceled`
- `tag names` — comma-separated string, e.g. `"work, urgent"`
- `project` — parent project (or `missing value`)
- `area` — parent area (or `missing value`)

### Project
- `name`, `notes`, `due date`, `tag names`, `area`
- `status` — `open`, `completed`, or `canceled`
- Contains `to dos`

### Area
- `name`, `tag names`
- Contains `to dos` and `projects`

### Tag
- `name`, `parent tag`

### Built-in Lists
Access by name: `"Inbox"`, `"Today"`, `"Anytime"`, `"Upcoming"`, `"Someday"`, `"Logbook"`, `"Trash"`

---

## Reading Data

### Get today's tasks
```applescript
tell application "Things3"
    to dos of list "Today"
end tell
```

### Get inbox tasks
```applescript
tell application "Things3"
    to dos of list "Inbox"
end tell
```

### Get tasks in a project
```applescript
tell application "Things3"
    to dos of project "Work"
end tell
```

### Get all projects
```applescript
tell application "Things3"
    projects
end tell
```

### Get all areas
```applescript
tell application "Things3"
    areas
end tell
```

### Get currently selected tasks
```applescript
tell application "Things3"
    selected to dos
end tell
```

### Read properties of tasks (example — list Today with deadlines)
```bash
osascript <<'EOF'
tell application "Things3"
    set todayTasks to to dos of list "Today"
    set output to ""
    repeat with t in todayTasks
        set taskName to name of t
        set dl to due date of t
        if dl is missing value then
            set output to output & taskName & "\n"
        else
            set output to output & taskName & " [deadline: " & (dl as string) & "]\n"
        end if
    end repeat
    return output
end tell
EOF
```

---

## Writing Data

### Create a to-do in Inbox
```applescript
tell application "Things3"
    make new to do with properties {name:"Buy groceries"}
end tell
```

### Create a to-do in Today with a deadline
```applescript
tell application "Things3"
    set newTask to make new to do with properties {name:"Submit report", due date:date "20 June 2026"}
    schedule newTask for date "today"
end tell
```

### Create a to-do in a specific project
```applescript
tell application "Things3"
    tell project "Work"
        make new to do with properties {name:"Review PR", notes:"Check auth changes"}
    end tell
end tell
```

### Create a project (optionally in an area)
```applescript
tell application "Things3"
    make new project with properties {name:"Q3 Planning", notes:"Goals for Q3"}
end tell
```

### Create a project inside an area
```applescript
tell application "Things3"
    set a to area "Personal"
    make new project with properties {name:"Home Renovation", area:a}
end tell
```

### Create a project with child tasks
```bash
osascript <<'EOF'
tell application "Things3"
    set p to make new project with properties {name:"New Blog Post"}
    tell p
        make new to do with properties {name:"Research options"}
        make new to do with properties {name:"Write draft"}
        make new to do with properties {name:"Review and publish"}
    end tell
end tell
EOF
```

---

## Modifying Existing Items

### Update a task's title and notes
```applescript
tell application "Things3"
    set t to to do named "Buy groceries"
    set name of t to "Buy groceries and cook dinner"
    set notes of t to "Don't forget milk"
end tell
```

### Set a deadline
```applescript
tell application "Things3"
    set due date of to do named "Submit report" to date "20 June 2026"
end tell
```

### Reschedule a task (set "when")
```applescript
tell application "Things3"
    schedule to do named "Call dentist" for date "tomorrow"
end tell
```

### Add tags
```applescript
tell application "Things3"
    set tag names of to do named "Buy groceries" to "personal, errands"
end tell
```

### Mark a task complete
```applescript
tell application "Things3"
    set status of to do named "Buy groceries" to completed
end tell
```

### Mark a task canceled
```applescript
tell application "Things3"
    set status of to do named "Old idea" to canceled
end tell
```

### Move a task to a project
```applescript
tell application "Things3"
    move to do named "Write tests" to project "Work"
end tell
```

### Move a task to a list
```applescript
tell application "Things3"
    move to do named "Someday idea" to list "Someday"
end tell
```

### Delete a task
```applescript
tell application "Things3"
    delete to do named "Old task"
end tell
```

---

## UI & Navigation

### Show a specific list
```applescript
tell application "Things3"
    show list "Today"
end tell
```

### Show a project
```applescript
tell application "Things3"
    show project "Work"
end tell
```

### Show a specific task (and bring Things to front)
```applescript
tell application "Things3"
    show to do named "Buy groceries"
end tell
```

### Open the Quick Entry panel
```applescript
tell application "Things3"
    show quick entry panel
end tell
```

### Open Quick Entry pre-filled
```applescript
tell application "Things3"
    show quick entry panel with properties {name:"Call dentist"} autofill yes
end tell
```

---

## Workflow

1. **Understand intent** — determine whether this is a read (list, query, search) or write (create, update, complete, move, delete) operation.
2. **Choose the right object** — to do, project, area, or tag; in a list, project, or area.
3. **Run the AppleScript** via `osascript` using Bash.
4. **For reads** — format the returned data clearly in the response (table, list, etc.).
5. **For writes** — confirm what was done in plain language.

### Accessing items by name vs. iteration
- `to do named "X"` — use when the name is known exactly (throws an error if not found).
- `to dos of list "Today"` + `repeat` — use when iterating or searching by property.

### Resolving a list/project/area by name
When the user says "add to X" and X is not a built-in list, try in this order:
1. `tell project "X"` — if that errors, fall back to
2. `tell area "X"` — if that also errors, tell the user X was not found

Built-in list names (`"Inbox"`, `"Today"`, `"Anytime"`, `"Upcoming"`, `"Someday"`, `"Logbook"`, `"Trash"`) use `list "X"` directly and do not need this fallback.

### Dates
Always use unambiguous date formats to avoid locale misinterpretation: `date "20 June 2026"`. Do not use ISO format (`date "2026-06-20"`) — it gets parsed incorrectly depending on system locale. Natural language works too: `date "today"`, `date "tomorrow"`.

### Task placement
- A task with no `activation date` stays in the **Inbox** (or whichever list/project it was created in).
- Setting `activation date` schedules the task and moves it to **Today** or **Upcoming**.
- `due date` is the deadline — setting it alone does not move the task out of the Inbox.
- Unless the user explicitly asks to schedule a task, never set `activation date`.

### Limitations
Not all Things features are available via AppleScript. If something isn't documented here, it's likely not possible via scripting.
