---
schemaversion: 1
slug: project-ideas
title: "Project Ideas Registry"
section: "Standard Operating Procedures"
category: "Project Management"
tags:
  - skill
  - project
  - ideas
  - ideation
  - planning
  - registry
order: 6
locked: true
author: seed
---

# Project Ideas Registry — Standard Operating Procedure

**Triggers**: Used by the daily project planning workflow during the Synthesize & Rank step. Not typically invoked manually.

## Overview

The **Project Ideas Registry** is a persistent file at `~/sulla/projects/PROJECT_IDEAS.md` that tracks every project idea the system has ever generated. It serves as institutional memory for the ideation process — recording what ideas keep surfacing, which ones the human approves, rejects, or ignores, and how the system's proposals evolve over time.

This registry is the key to learning the human's preferences. An idea rejected 3 times should stop being proposed. An idea that keeps surfacing independently across multiple runs signals genuine importance. An idea the human ignored (didn't explicitly reject) may just need better timing or framing.

## File Location

`~/sulla/projects/PROJECT_IDEAS.md`

Create the file if it doesn't exist. The file has no frontmatter — it starts with the heading.

## File Format

The file has two sections: a **Summary Stats** block at the top, and **Idea Entries** below.

```markdown
# Project Ideas Registry

**Last Updated**: YYYY-MM-DD
**Total Ideas**: N
**Accepted**: N | **Rejected**: N | **Ignored**: N | **Pending**: N

---

## {idea-slug}

- **Title**: Human-readable project title
- **Description**: One to two sentence description of what this project would produce
- **Status**: pending | accepted | rejected | retired
- **First Surfaced**: YYYY-MM-DD
- **Last Surfaced**: YYYY-MM-DD
- **Times Ideated**: N
- **Times Proposed**: N
- **Times Accepted**: 0
- **Times Rejected**: 0
- **Times Ignored**: 0
- **Source Lenses**: Goal Alignment, Pain Points, Quick Wins
- **Related Goals**: Which specific goals this advances
- **Retirement Reason**: (only if retired) Why this idea was retired

---
```

## Status Definitions

- **pending** — idea has been generated but not yet proposed, OR has been proposed but not explicitly accepted or rejected
- **accepted** — human approved this idea at least once; a project was created for it
- **rejected** — human explicitly said no to this idea (said "no", "not that one", "skip", etc.)
- **retired** — idea has been rejected 3+ times, or the goals it served are no longer active, or the agent decided it's no longer relevant. Retired ideas are kept in the file for history but are never proposed again unless goals change.

## How to Update the Registry

### During Synthesize & Rank (daily project planning):

1. **Read the existing registry** at `~/sulla/projects/PROJECT_IDEAS.md`. If it doesn't exist, create it with empty stats.

2. **Match new ideas to existing entries.** For each idea from the ideation lenses:
   - Search the registry by slug and by semantic similarity (same goal, same pain point, similar scope)
   - If a match exists: increment `Times Ideated`, update `Last Surfaced`, update `Source Lenses` if new lenses surfaced it, update `Related Goals` if goals evolved
   - If no match: create a new entry with `Times Ideated: 1`, `Times Proposed: 0`, all counters at 0

3. **Use registry data to inform ranking.** When scoring and ranking the top 3:
   - **Boost** ideas with high `Times Ideated` — the system keeps independently arriving at this idea, which signals genuine importance
   - **Penalize** ideas with `Times Rejected > 0` — reduce rank proportional to rejection count
   - **Neutral** on ignored ideas — the human may not have had time, don't punish
   - **Never propose** retired ideas (status: retired)
   - **Prefer fresh framing** — if an idea was ignored before, consider whether it can be reframed or scoped differently

4. **Update summary stats** at the top of the file.

### After Human Response (proposal approval step):

For each of the 3 proposals that were presented:

1. Increment `Times Proposed` on the matching registry entry

2. Based on the human's response:
   - **Accepted**: increment `Times Accepted`, set `Status: accepted`
   - **Explicitly rejected**: increment `Times Rejected`. If `Times Rejected >= 3`, set `Status: retired` with a `Retirement Reason`
   - **Ignored** (human picked a different proposal or said "none"): increment `Times Ignored`

3. If the human suggested their own idea (not one of the 3 proposals):
   - Create a new entry with `Status: accepted`, `Times Ideated: 0` (human-originated), `Times Proposed: 0`, `Times Accepted: 1`
   - This is valuable signal — the human's own ideas reveal what the system isn't yet ideating

## Retirement Rules

An idea should be retired when:
- It has been rejected 3 or more times (`Times Rejected >= 3`)
- The goals it was designed to advance are no longer in any active journal
- It has been ignored 5+ times without ever being accepted
- The agent determines it's obsolete (market changed, capability was built another way, etc.)

Set `Status: retired` and add a `Retirement Reason` explaining why.

**Reactivation**: A retired idea CAN be reactivated if the goals it serves come back into the journals. Change status back to `pending` and reset the rejection counter. Add a note in the entry.

## Preference Learning Signals

Over time, the registry reveals patterns:

- **Which lenses produce accepted ideas?** If Pain Point Solutions consistently wins, the human prioritizes removing friction over building new things.
- **What size projects get accepted?** If quick wins always win, the human prefers momentum over ambition.
- **What gets rejected?** Capability building ideas rejected repeatedly means the human wants direct output, not infrastructure.
- **Human-originated ideas**: Ideas with `Times Ideated: 0` (human suggested them) show what the system should learn to ideate on its own.

Log these patterns in the daily project planning log for the agent to learn from.

## Best Practices

1. **Slugs must be stable** — once an idea has a slug, don't change it. This is the key for tracking across days.
2. **Merge similar ideas** — if two ideas are essentially the same project scoped differently, merge them under one entry and note the scope variations.
3. **Be generous with matching** — "build a blog publishing pipeline" and "automate blog posts" are the same idea. Don't create duplicates.
4. **Preserve history** — never delete entries. Retired ideas stay in the file. The history is the learning.
5. **Update atomically** — read the file, make all changes, write it back. Don't partially update.
