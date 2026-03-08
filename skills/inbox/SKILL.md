---
name: inbox
description: "Use when capturing ideas, thoughts, tasks, links, or voice dumps to save them as structured files in the personal knowledge inbox."
---

# Inbox — Universal Capture to Knowledge OS

You capture items into the Knowledge OS inbox. Your job is to take ANY raw input — voice transcripts, pasted links, messy notes, mixed languages — and turn it into clean, trackable inbox files with auto-detected types and project context.

**Core principle: CAPTURE FIRST, ORGANIZE LATER. Never lose a thought to friction.**

Input: $ARGUMENTS

## Step 1: Accept Raw Input

Take whatever the user dumped. It can be:
- Voice transcript (messy, incomplete sentences, mixed languages)
- Pasted links with commentary
- Stream-of-consciousness notes
- A single sentence
- Multiple items mashed together

If the input is empty or completely nonsensical (no discernible content), ask the user to clarify. Otherwise, proceed — even partial thoughts are worth capturing.

## Step 2: Detect Input Format

Determine what kind of raw input this is:
- **Text** — default. Proceed normally.
- **Audio file path** (e.g., `.m4a`, `.mp3`, `.wav`) — log "Audio transcription not yet supported. Please paste the transcript text." and stop.
- **URL** (e.g., `https://...`) — log "URL content fetching not yet supported. Capturing the URL as-is." and proceed with the URL as the content.

Set `source` to `voice` if the text appears to be a voice transcript (broken sentences, filler words, stream-of-consciousness style). Otherwise set `source` to `text`.

## Step 3: Clean Up

For each item, clean up the text into readable prose:
- Fix voice transcription artifacts (broken words, missing punctuation, repeated phrases)
- Structure into coherent sentences
- Preserve the original meaning and voice — don't over-formalize
- Keep both languages if the user mixed them (don't translate unless unintelligible)

## Step 4: Split Into Distinct Items

Identify separate items in the dump. Signals for splitting:
- Different topics or domains
- "Also..." / "Another thing..." / "Oh and..." transitions
- Unrelated links or references
- Context switches mid-paragraph

A single item → 1 file. Multiple items → multiple files. When in doubt, split — the user can merge later.

## Step 5: Detect Item Type

For each item, infer its type from the content:

- **Task** — Has a clear action to take. Keywords: "need to", "should", "must", "fix", "write", "create", "set up", "figure out", "document". The item describes something TO DO.
- **Idea** — A concept, hypothesis, inspiration, "what if" scenario, creative thought. No immediate action required — it's something to explore or consider.
- **Note** — Information, observation, reference, fact. Not actionable, not creative — just worth remembering.
- **Report** — Summary, status update, findings from an activity. Describes what WAS DONE or what IS happening.

When in doubt, default to **Idea** (same as the old behavior).

## Step 6: Infer Project Context

Try to link the item to a known project:

1. Check if the user is currently in a git repository. If yes, get the remote URL: `git remote get-url origin 2>/dev/null`
2. If a remote URL is found, scan all `~/Documents/Knowledge/Projects/*.md` files for a matching `git:` frontmatter field.
3. If a match is found, set `project: "[[Project Name]]"` (where Project Name is the filename without `.md`).
4. If no match, leave `project:` empty — don't guess.

Also: if the content explicitly mentions a project by name and that project file exists in `~/Documents/Knowledge/Projects/`, set the project field.

## Step 7: Capture Raw Mentions

Scan the cleaned-up content for obvious person or company names:
- Proper nouns that look like person names (first name, full name)
- Company names (especially if they match files in `~/Documents/Knowledge/People/` or `~/Documents/Knowledge/Companies/`)

Add them to the `mentions:` field as a simple list of strings. Do NOT try to resolve them to existing entities — just capture the raw text. The triage agent will handle resolution later.

If no obvious mentions, omit the `mentions:` field entirely.

## Step 8: Create Inbox Files

For each item:

### 8a: Determine Next ID

Scan existing files in `~/Documents/Knowledge/Inbox/` matching pattern `I????-*.md`. Find the highest existing ID number and increment by 1. If no files exist, start at `I0001`.

If there's an ID collision (e.g., parallel session created a file), increment until the filename is unique.

### 8b: Generate Slug

Create a kebab-case slug from the item's title/topic. Keep it short (2-5 words). Examples:
- `youtube-ai-limitless`
- `mcp-security-policy`
- `research-factory-sandbox`

### 8c: Write the File

Create `~/Documents/Knowledge/Inbox/I{NNNN}-{slug}.md` with this format:

```markdown
---
type: {Idea|Task|Note|Report}
id: I{NNNN}
status: new
created: {YYYY-MM-DDTHH:MM}
project: "[[Project Name]]"
mentions: ["Name1", "Name2"]
source: {voice|text}
---

# {Item Title}

{Cleaned up content — readable prose, 1-3 paragraphs typically}

{If there's a source (link, video, person), include it:}
Source: {source description}
```

Use the current date and time for `created`.

**Frontmatter rules:**
- Always include: `type`, `id`, `status`, `created`, `source`
- Include `project` only if inferred (Step 6). Omit if empty.
- Include `mentions` only if found (Step 7). Omit if empty.

## Step 9: Regenerate INDEX.md

After creating all inbox files, regenerate `~/Documents/Knowledge/Inbox/INDEX.md`:

1. Scan ALL `I????-*.md` files in `~/Documents/Knowledge/Inbox/`
2. For each file, read the frontmatter (`type`, `id`, `status`, `created`, `project`, `mentions`) and the first heading (title)
3. Sort by ID ascending
4. Write the index:

```markdown
# Inbox

| ID | Title | Type | Status | Created | Project | Mentions |
|----|-------|------|--------|---------|---------|----------|
| I0001 | {title} | {type} | {status} | {created date} | {project or empty} | {mentions joined or empty} |
```

## Step 10: Show Summary

Display a brief summary of what was created:

```
Captured {N} item(s):
- I0004 [Task]: {title}
- I0005 [Idea]: {title}

Files saved to ~/Documents/Knowledge/Inbox/
```

## Step 11: Do NOT Commit

Do not run `git add`, `git commit`, or any git operations. The Knowledge directory may not be a git repo, and commit timing is governed by IP protection rules. Files are simply created on disk.

## Common Mistakes — Do NOT:

- **Dedup at capture.** Never skip creating a file because "a similar item exists." Dedup is a triage operation, not a capture operation. Voice transcripts especially are unreliable for matching.
- **Auto-commit.** Never commit. IP protection rules govern when commits happen.
- **Over-template.** Don't add tags, priority, effort, or extra fields at capture time. Those are added during triage. Keep frontmatter to the schema above.
- **Over-formalize.** The cleaned-up prose should sound like the user, not like a technical document. Preserve voice and casual tone.
- **Refuse messy input.** If you can extract even one item from the mess, do it. Only ask for clarification if there's truly nothing to work with.
- **Translate.** If the user mixes Russian and English, keep both. Don't translate unless the original is unintelligible.
- **Resolve mentions.** Just capture raw names. Don't try to match to existing entity files or create wiki-links. The triage agent does that.
- **Force project context.** Only set project if you have a confident match (git remote or explicit mention). Never guess.

## Example

**User input (from a git repo with remote matching `[[MCP Engineering]]`):**
```
so I was watching this David Gaut video about how AI makes you limitless right and it got me thinking about feedback loops like higher order loops where you go from asking AI simple questions to asking AI how to approach questions to building systems that track and cross-link questions like our research system. Also we need to figure out MCP security policy for DataArt infosec department they keep asking about it Sergei mentioned it again yesterday. Oh and the research factory idea — what if the agent sandbox becomes a factory of researchers, like you spin up specialized research agents on demand
```

**Result:** 3 files created:

`I0004-ai-feedback-loops.md`:
```markdown
---
type: Idea
id: I0004
status: new
created: 2026-03-09T14:30
source: voice
---

# AI feedback loops — from questions to systems

Feedback loops in AI usage. Higher order loops: asking AI simple questions → asking AI how to approach questions → building systems that track and cross-link questions → research system like we built.

Source: David Gaut YouTube video about AI making you limitless
```

`I0005-mcp-security-policy.md`:
```markdown
---
type: Task
id: I0005
status: new
created: 2026-03-09T14:30
project: "[[MCP Engineering]]"
mentions: ["Sergei"]
source: voice
---

# Figure out MCP security policy for DataArt InfoSec

DataArt InfoSec department keeps asking about MCP security policy. Sergei mentioned it again yesterday. Need to figure out and document a clear policy for them.
```

`I0006-research-factory-sandbox.md`:
```markdown
---
type: Idea
id: I0006
status: new
created: 2026-03-09T14:30
source: voice
---

# Research factory — agent sandbox as researcher factory

What if the agent sandbox becomes a factory of researchers? Spin up specialized research agents on demand instead of running everything through a single agent.
```

**INDEX.md** regenerated with all entries. Summary shown to user.
