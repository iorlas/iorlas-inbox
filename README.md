# iorlas-idea

Fast idea capture for personal knowledge management. Dump thoughts, links, voice transcripts — they become structured, trackable idea files.

## Quick Install

Via [iorlas-marketplace](https://github.com/iorlas/iorlas-marketplace):

```bash
claude mcp add-from-marketplace iorlas-idea
```

## Manual Install

Add to your Claude Code settings:

```json
{
  "permissions": {
    "allow": ["Bash(mkdir:*)","Bash(ls:*)","Read(*)", "Write(*)"]
  },
  "mcpServers": {},
  "projects": {
    "/": {
      "skills": ["~/Projects/iorlas-idea/skills"]
    }
  }
}
```

Or add the skills path to your project's `.claude/settings.json`.

## Usage

```
/idea so I was watching this video about feedback loops and AI and it
got me thinking we should build a research factory where agents spin up
on demand. Also need to write that MCP security doc for DataArt.
```

The skill will:
1. Clean up your raw input (fixes voice artifacts, structures sentences)
2. Split distinct ideas into separate files
3. Create numbered idea files in `~/Documents/Knowledge/Ideas/`
4. Regenerate the `BOARD.md` index
5. Show you a summary of what was captured

## File Format

Each idea is stored as a minimal markdown file:

```markdown
---
id: I0001
status: new
created: 2026-03-08T14:30
---

# Idea Title

Cleaned up idea content...

## Mentions
- 2026-03-08T14:30: Initial capture
```

### Statuses

- `new` — Just captured, unreviewed
- `active` — Being worked on
- `completed` — Done
- `promoted` — Became a research
- `discarded` — Won't pursue

## Storage

Ideas are stored in `~/Documents/Knowledge/Ideas/` — a global path that works from any repo and syncs via iCloud on macOS.

## License

MIT — Denis Tomilin
