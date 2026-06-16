# things-app

A [Claude](https://claude.com/claude-code) **Skill** to interact with
[Cultured Code Things](https://culturedcode.com/things/) — the macOS/iOS task
manager — from Claude. Create to-dos and projects, file tasks into lists and
areas, and query existing items.

> **Status:** Scaffold. The repository structure is in place; the working skill
> body is being finalised.

## What's in here

| Path | Purpose |
| --- | --- |
| [`SKILL.md`](SKILL.md) | The skill definition — frontmatter (`name`, `description`) plus instructions Claude follows. |
| [`scripts/`](scripts/) | Helper scripts the skill calls (Things URL scheme / AppleScript bridges). |
| [`reference/`](reference/) | Reference docs loaded on demand (URL scheme, AppleScript API). |
| [`LICENSE`](LICENSE) | MIT. |

## Installing

This skill works with [Claude Code](https://docs.claude.com/en/docs/claude-code).
Clone it into your personal skills directory:

```bash
git clone https://github.com/ManfredFr/things-app.git \
  ~/.claude/skills/things-app
```

Claude discovers the skill from its `SKILL.md`. Invoke it with `/things-app` or
just describe a Things task in natural language.

## Requirements

- macOS with the [Things](https://culturedcode.com/things/) app installed.
- For reads via the URL scheme: enable **Things → Settings → General → Enable
  Things URLs** and generate an auth token.

## Contributing

Issues and pull requests welcome. Keep the `SKILL.md` frontmatter
`description` precise — it's what Claude uses to decide when to trigger the skill.

## License

[MIT](LICENSE) © Manfred Friedrich
