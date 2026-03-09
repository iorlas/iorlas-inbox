---
name: triage
description: "Processes inbox items: reviews new captures, resolves mentions to entities, links projects, updates status. Use when the user says 'triage inbox', 'process inbox', or 'review captures'."
---

# Inbox Triage

Process `status: new` items in `~/Documents/Knowledge/Inbox/`. For each item: review, resolve mentions, link entities, update status.

Input: $ARGUMENTS

## Process

### 1. Find untriaged items

```bash
grep -rl 'status: new' ~/Documents/Knowledge/Inbox/I????-*.md
```

If `$ARGUMENTS` specifies an ID (e.g., `I0005`), process only that item. Otherwise process all `status: new` items.

### 2. For each item

Read the file and:

**a. Resolve mentions** — match raw mention strings against existing entity files:
- Check `~/Documents/Knowledge/People/{name}.md` and `~/Documents/Knowledge/Companies/{name}.md`
- If a match exists, convert the mention to a wiki-link in the body text: `[[Person Name]]`
- If no match, ask the user: "Create entity for {name}? (Person/Company/Skip)"
- If user says create: make a stub entity file with minimal frontmatter

**b. Verify project link** — if `project` field is empty or `[[researches-cowork]]`-style generic:
- Check if content hints at a specific project
- Check `~/Documents/Knowledge/Projects/` for matches
- Ask user to confirm or skip

**c. Review type** — show the item to the user with detected type. Ask: "Correct? (y/change type/skip)"

**d. Update status** — change `status: new` to `status: triaged` in frontmatter

### 3. Re-embed

After processing all items:
```bash
npx @tobilu/qmd embed
```

### 4. Summary

```
Triaged N item(s):
- I0005 [Note]: Skill compression research paid off → triaged, project: [[researches-cowork]]
- I0006 [Task]: Review brainstorming skill → triaged, entity created: [[R045]]
```

## Constraints

- **Always ask before creating entities.** Never auto-create People or Company files.
- **Don't change item content.** Only update frontmatter and convert mention strings to wiki-links.
- **Don't delete or merge items.** Dedup is a separate decision the user makes.
- **No git operations.** IP protection governs commit timing.
- **One item at a time.** Show each item, get confirmation, move on. Don't batch silently.
