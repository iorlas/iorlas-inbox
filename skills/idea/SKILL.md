---
name: idea
description: "Use when capturing ideas, thoughts, links, or voice dumps to save them as structured files in the personal knowledge inbox."
---

# Idea — Fast Capture to Knowledge Inbox

You capture ideas into structured files. Your job is to take ANY raw input — voice transcripts, pasted links, messy notes, mixed languages — and turn it into clean, trackable idea files.

**Core principle: CAPTURE FIRST, ORGANIZE LATER. Never lose an idea to friction.**

Input: $ARGUMENTS

## Step 1: Accept Raw Input

Take whatever the user dumped. It can be:
- Voice transcript (messy, incomplete sentences, mixed languages)
- Pasted links with commentary
- Stream-of-consciousness notes
- A single sentence
- Multiple ideas mashed together

If the input is empty or completely nonsensical (no discernible idea), ask the user to clarify. Otherwise, proceed — even partial ideas are worth capturing.

## Step 2: Clean Up

For each idea, clean up the text into readable prose:
- Fix voice transcription artifacts (broken words, missing punctuation, repeated phrases)
- Structure into coherent sentences
- Preserve the original meaning and voice — don't over-formalize
- Keep both languages if the user mixed them (don't translate unless unintelligible)

## Step 3: Split Into Distinct Ideas

Identify separate ideas in the dump. Signals for splitting:
- Different topics or domains
- "Also..." / "Another thing..." / "Oh and..." transitions
- Unrelated links or references
- Context switches mid-paragraph

A single idea dump → 1 file. Multiple ideas → multiple files. When in doubt, split — the user can merge later.

## Step 4: Create Idea Files

For each idea:

### 4a: Determine Next ID

Scan existing files in `~/Documents/Knowledge/Ideas/` matching pattern `I????-*.md`. Find the highest existing ID number and increment by 1. If no files exist, start at `I0001`.

If there's an ID collision (e.g., parallel session created a file), increment until the filename is unique.

### 4b: Generate Slug

Create a kebab-case slug from the idea's title/topic. Keep it short (2-5 words). Examples:
- `youtube-ai-limitless`
- `mcp-security-policy`
- `research-factory-sandbox`

### 4c: Write the File

Create `~/Documents/Knowledge/Ideas/I{NNNN}-{slug}.md` with this format:

```markdown
---
id: I{NNNN}
status: new
created: {YYYY-MM-DDTHH:MM}
---

# {Idea Title}

{Cleaned up idea content — readable prose, 1-3 paragraphs typically}

{If there's a source (link, video, person), include it:}
Source: {source description}

## Mentions
- {YYYY-MM-DDTHH:MM}: Initial capture
```

Use the current date and time for `created` and the Mentions entry.

## Step 5: Regenerate BOARD.md

After creating all idea files, regenerate `~/Documents/Knowledge/Ideas/BOARD.md`:

1. Scan ALL `I????-*.md` files in `~/Documents/Knowledge/Ideas/`
2. For each file, read the frontmatter (id, status, created) and the first heading (title)
3. Also read `tags` from frontmatter if present (will be empty for new captures)
4. Sort by ID ascending
5. Write the board:

```markdown
# Ideas Board

| ID | Title | Status | Created | Tags |
|----|-------|--------|---------|------|
| I0001 | {title} | {status} | {created date} | {tags or empty} |
| I0002 | {title} | {status} | {created date} | {tags or empty} |
```

## Step 6: Show Summary

Display a brief summary of what was created:

```
Captured {N} idea(s):
- I0001: {title}
- I0002: {title}
- I0003: {title}

Files saved to ~/Documents/Knowledge/Ideas/
```

## Step 7: Do NOT Commit

Do not run `git add`, `git commit`, or any git operations. The Knowledge directory may not be a git repo, and commit timing is governed by IP protection rules. Files are simply created on disk.

## Common Mistakes — Do NOT:

- **Dedup at capture.** Never skip creating a file because "a similar idea exists." Dedup is a triage operation, not a capture operation. Voice transcripts especially are unreliable for matching.
- **Auto-commit.** Never commit. IP protection rules govern when commits happen.
- **Over-template.** Don't add tags, priority, effort, or related fields at capture time. Those are added during triage. Keep frontmatter minimal: id, status, created.
- **Over-formalize.** The cleaned-up prose should sound like the user, not like a technical document. Preserve voice and casual tone.
- **Refuse messy input.** If you can extract even one idea from the mess, do it. Only ask for clarification if there's truly nothing to work with.
- **Translate.** If the user mixes Russian and English, keep both. Don't translate unless the original is unintelligible.

## Example

**User input:**
```
so I was watching this David Gaut video about how AI makes you limitless right and it got me thinking about feedback loops like higher order loops where you go from asking AI simple questions to asking AI how to approach questions to building systems that track and cross-link questions like our research system. Also we need to figure out MCP security policy for DataArt infosec department they keep asking about it. Oh and the research factory idea — what if the agent sandbox becomes a factory of researchers, like you spin up specialized research agents on demand
```

**Result:** 3 files created:

`I0001-youtube-ai-limitless.md`:
```markdown
---
id: I0001
status: new
created: 2026-03-08T14:30
---

# How AI makes you limitless — YouTube video idea

Feedback loops. Higher order loops. Migrating from asking AI simple questions
→ asking AI how to approach questions → building systems to track and cross-link
questions → research system like we built.

Source: David Gaut YouTube video "Limitless can actually transform your life"

## Mentions
- 2026-03-08T14:30: Initial capture (voice)
```

`I0002-mcp-security-policy.md`:
```markdown
---
id: I0002
status: new
created: 2026-03-08T14:30
---

# MCP security policy for DataArt InfoSec

DataArt InfoSec department keeps asking about MCP security policy. Need to figure
out and document a clear policy for them.

## Mentions
- 2026-03-08T14:30: Initial capture (voice)
```

`I0003-research-factory-sandbox.md`:
```markdown
---
id: I0003
status: new
created: 2026-03-08T14:30
---

# Research factory — agent sandbox as researcher factory

What if the agent sandbox becomes a factory of researchers? Spin up specialized
research agents on demand instead of running everything through a single agent.

## Mentions
- 2026-03-08T14:30: Initial capture (voice)
```

**BOARD.md** regenerated with all 3 entries. Summary shown to user.
