---
name: inbox
description: "Captures ideas, thoughts, tasks, voice dumps, links, or notes into structured inbox files in ~/Documents/Knowledge/Inbox/. Use when the user says 'capture this', 'save this thought', 'inbox', or dumps raw text/voice transcript to process."
---

# Inbox — Universal Capture

**CAPTURE FIRST, ORGANIZE LATER. Never lose a thought to friction.**

You capture raw input — voice transcripts, pasted links, messy notes, mixed languages — into clean, trackable inbox files with auto-detected types and project context. Split multi-topic dumps into separate files. Clean voice artifacts into readable prose while preserving the user's voice and mixed languages.

Input: $ARGUMENTS

## Unsupported Inputs

- **Audio file path** (`.m4a`, `.mp3`, `.wav`) — say "Audio transcription not yet supported. Please paste the transcript text." and stop.
- **URL only** — say "URL content fetching not yet supported. Capturing the URL as-is." and proceed.

## Output Template

Create `~/Documents/Knowledge/Inbox/I{NNNN}-{slug}.md`:

```markdown
---
type: {Idea|Task|Note|Report}
id: I{NNNN}
status: new
created: {YYYY-MM-DDTHH:MM}
context: {full cwd path}
project: "[[{folder name or matched project}]]"
mentions: ["Name1", "Name2"]
source: {voice|text}
---

# {Item Title}

{Cleaned up content}

Source: {if applicable — link, video, person}
```

**ID sequence:** scan `~/Documents/Knowledge/Inbox/I????-*.md`, find highest ID, increment. Start at I0001 if none exist.

**Frontmatter:** always include type, id, status, created, context, project, source. Include `mentions` only if names found; omit if none.

## Project Context

Run: `basename "$(git rev-parse --show-toplevel 2>/dev/null || pwd)"`

- `context:` — full cwd path
- `project:` — `"[[{result}]]"` as wiki-link (dangling OK)

If `~/Documents/Knowledge/Projects/{result}.md` exists and a Projects/ file has a matching `git:` remote URL in frontmatter, use that file's name (without .md) as the link target instead.

## Constraints

- **No dedup at capture.** Never skip because "a similar item exists." Dedup is triage, not capture.
- **No git operations.** No add, commit, push. IP protection governs commit timing.
- **No extra fields.** No tags, priority, effort. Those come at triage.
- **Preserve voice.** Keep mixed languages. Don't translate. Don't over-formalize.
- **Mentions = raw strings.** Capture names as plain text. No wiki-links, no entity resolution.
- **Source field:** set to `voice` if input looks like voice transcript, otherwise `text`.

## Example

**Input** (cwd: `/Users/user/Projects/mcp-tools`, git repo, Projects/ file matches remote -> `[[MCP Engineering]]`):
```
so I was watching this David Gaut video about how AI makes you limitless right and it got me thinking about feedback loops. Also we need to figure out MCP security policy for DataArt infosec department Sergei mentioned it again yesterday. Oh and the research factory idea — what if the agent sandbox becomes a factory of researchers
```

**Result:** 3 files created:

`I0004-ai-feedback-loops.md`:
```markdown
---
type: Idea
id: I0004
status: new
created: 2026-03-09T14:30
context: /Users/user/Projects/mcp-tools
project: "[[MCP Engineering]]"
source: voice
---

# AI feedback loops — from questions to systems

Feedback loops in AI usage. Higher order loops: asking AI simple questions -> asking AI how to approach questions -> building systems that track and cross-link questions -> research system like we built.

Source: David Gaut YouTube video about AI making you limitless
```

`I0005-mcp-security-policy.md` — type: Task, mentions: ["Sergei"]
`I0006-research-factory-sandbox.md` — type: Idea

Summary shown to user.
