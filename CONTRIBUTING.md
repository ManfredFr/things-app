# Contributing

Contributions are welcome. All changes go through a pull request and require review before merging into `main`.

## How to contribute

1. **Fork** the repository
2. **Create a branch** for your change — use a descriptive name, e.g. `fix/typo-in-readme` or `feat/add-tag-examples`
3. **Make your changes**
4. **Open a pull request** against `main` with a clear description of what you changed and why
5. **Wait for review** — the maintainer will review and either approve, request changes, or decline

## What makes a good contribution

- **Bug fixes** — something in the skill doesn't work as documented
- **New examples** — a useful AppleScript pattern not yet covered
- **Documentation improvements** — clearer explanations, better wording
- **Edge case handling** — things the skill should handle but currently doesn't

## Guidelines

- Keep changes focused — one concern per pull request
- Test your AppleScript snippets in Things 3 before submitting
- If you're adding a new operation, add a matching example to `README.md`
- Do not modify the `description` frontmatter in `SKILL.md` without discussion — it controls when Claude triggers the skill automatically
- **Never use angle brackets (`<...>`) anywhere in the `SKILL.md` frontmatter.** The Cowork skill validator parses them as XML tags and rejects the `.skill` file (use `[brackets]` for placeholders, e.g. `import [path]`). The release CI enforces this and will fail the build if any are present.

## Questions

Open an issue if you're unsure whether a change is appropriate before investing time in it.
