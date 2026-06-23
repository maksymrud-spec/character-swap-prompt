# character-swap-prompt

Claude skill for generating Nano Banana 2 character swap prompts.

## What this skill does

Takes two image packs and generates a single English prompt that instructs Nano Banana 2 to replace characters in a scene:
- **Pack 1** — reference characters on white background with `@name` identifiers
- **Pack 2** — scene photos where original characters get replaced

## How it's used in Claude

This skill is loaded dynamically at the start of each Claude session via a `curl` call to this repo. Claude reads the latest `SKILL.md` and follows its instructions for that session.

## Files

```
SKILL.md          ← the skill itself (this is what Claude reads)
README.md         ← you are here
CHANGELOG.md      ← version history
```

## Updating the skill

1. Edit `SKILL.md` locally
2. `git add SKILL.md`
3. `git commit -m "describe what changed"`
4. `git push`

Claude will pick up the new version automatically on the next session.

## Versioning convention

Use simple semantic notes in commit messages:
- `fix: ...` — corrected wrong behavior
- `add: ...` — new rule or example added
- `remove: ...` — removed outdated instruction
- `refactor: ...` — restructured without changing behavior
