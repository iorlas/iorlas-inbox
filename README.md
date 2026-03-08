# iorlas-inbox

Universal capture for personal knowledge OS. Dump thoughts, tasks, links, voice transcripts — they become structured, trackable inbox files with auto-detected types and project context.

## Quick Install

Via [iorlas-marketplace](https://github.com/iorlas/iorlas-marketplace):

```bash
claude mcp add-from-marketplace iorlas-inbox
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
      "skills": ["~/Projects/iorlas-inbox/skills"]
    }
  }
}
```

Or add the skills path to your project's `.claude/settings.json`.

## Usage

```
/inbox so I was watching this video about feedback loops and AI and it
got me thinking we should build a research factory where agents spin up
on demand. Also need to write that MCP security doc for DataArt.
```

The skill will:
1. Clean up your raw input (fixes voice artifacts, structures sentences)
2. Split distinct items into separate files
3. Auto-detect type (Idea, Task, Note, Report)
4. Infer project context from git remote
5. Capture person/company mentions
6. Create numbered inbox files in `~/Documents/Knowledge/Inbox/`
7. Regenerate the `INDEX.md`
8. Show you a summary of what was captured

## File Format

Each inbox item is stored as a markdown file with rich frontmatter:

```markdown
---
type: Task
id: I0042
status: new
created: 2026-03-09T14:30
project: "[[MCP Engineering]]"
mentions: ["Sergei"]
source: text
---

# MCP security policy for DataArt InfoSec

Need to document a clear MCP security policy for the InfoSec department.
```

### Item Types

- `Idea` — Concepts, inspirations, hypotheses
- `Task` — Actionable items, TODOs
- `Note` — Information, observations, references
- `Report` — Summaries, status updates, findings

### Statuses

- `new` — Just captured, unreviewed
- `triaged` — Tagged and linked by triage agent
- `in-progress` — Being worked on
- `done` — Completed
- `archived` — No longer relevant

## Storage

Items are stored in `~/Documents/Knowledge/Inbox/` — part of the Knowledge OS folder structure that works from any repo and syncs via iCloud on macOS.

## License

MIT — Denis Tomilin
