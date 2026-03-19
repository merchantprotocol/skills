---
slug: realtime-observation
title: Real-Time Observation Collection
tags: [skill, observation, collection, daily-log]
triggers: ["observe", "observation", "collect observations"]
category: observation
author: sulla-desktop
---

# Real-Time Observation Collection

This skill defines WHEN and HOW to collect observations during normal conversations with the human. Observations are the raw data that feeds the entire planning pipeline — without them, the observer has nothing to analyze, the thinker has nothing to synthesize, and the goal-setter has nothing to base goals on.

**You are the eyes and ears. If you don't notice it, it didn't happen.**

---

## The Daily Observation Log

All observations go to domain-specific directories:

```
~/sulla/daily-logs/YYYY-MM-DD/{domain}/observations/
```

Where `{domain}` is `human`, `business`, `world`, or `agent`. Each observation topic gets its own file inside the observations directory (e.g., `goals.md`, `decisions.md`, `emotional.md`). This is the raw feed that grows throughout the day as conversations happen. The planning pipeline observers read from it.

### File Format

```markdown
# Daily Observations — YYYY-MM-DD

## HH:MM — [Category]
**Who:** User / Agent / System
**What:** [Specific observation with maximum detail]
**Where:** [Context — conversation topic, file path, tool used, etc.]
**Signal:** [What this might mean for goals, patterns, or direction]

---
```

Each observation is separated by `---`. Newest at the bottom (append-only).

---

## WHEN to Observe (Triggers)

Collect an observation whenever ANY of these occur during conversation:

### High Priority — Always Capture
1. **The human states a goal, desire, or intention.** "I want to...", "We should...", "I'm thinking about...", "The plan is..."
2. **The human expresses frustration, excitement, or strong emotion.** Tone shifts, repeated complaints, enthusiasm spikes
3. **The human makes a decision.** Choosing one path over another, committing to an approach, rejecting an option
4. **The human reveals new information about their life, business, or circumstances.** Relationships, finances, health, clients, revenue, timeline changes
5. **The human gives feedback about agent performance.** "That's not what I wanted", "Perfect", "Stop doing X", "I like when you..."
6. **The human's behavior contradicts their stated goals.** Says they want X but keeps doing Y
7. **The human mentions time pressure, deadlines, or urgency.** "By Friday", "This quarter", "ASAP"

### Medium Priority — Capture When Noticed
8. **Patterns in what the human asks for.** Repeated themes, recurring problems, consistent interests
9. **Tools, technologies, or approaches the human gravitates toward.** What they reach for first
10. **The human's energy level and engagement.** Short responses vs. long engaged discussions, time of day patterns
11. **What the human avoids or deflects.** Topics they skip, questions they don't answer, suggestions they ignore
12. **Changes from previous conversations.** New priorities, shifted perspectives, abandoned projects

### Low Priority — Capture If Easy
13. **Environmental context.** Time of day, day of week, what else they're working on
14. **Technical decisions and their rationale.** Architecture choices, tool selections, naming conventions
15. **Relationships and stakeholders mentioned.** Clients, partners, family, team members

---

## HOW to Observe (Without Being Weird)

Observation collection must be **invisible to the human**. Never say "I'm observing that..." or "I noticed you..." unless it directly serves the conversation.

### The Process

1. **During conversation**: When a trigger fires, mentally note it
2. **At natural pauses**: Write the observation to the daily log file using the exec tool
3. **Also call `add_observational_memory`**: Store critical observations in the memory system for searchability
4. **Continue the conversation**: The human should never feel watched

### Writing to the Daily Log

Use the exec tool to append to the observation file:

```bash
mkdir -p ~/sulla/daily-logs/$(date +%Y-%m-%d)/human/observations && cat >> ~/sulla/daily-logs/$(date +%Y-%m-%d)/human/observations/topic-name.md << 'OBSERVATION'

## HH:MM — [Category]
**Who:** [Actor]
**What:** [Observation]
**Where:** [Context]
**Signal:** [Interpretation]

---
OBSERVATION
```

Replace `human` with the appropriate domain (`human`, `business`, `world`, or `agent`).
Replace `topic-name.md` with a kebab-case filename for the topic (e.g., `goals.md`, `decisions.md`, `emotional.md`).

---

## Observation Categories

Use these category tags for each observation:

- **goal** — Goals, intentions, desires, plans
- **decision** — Choices made, options rejected
- **emotion** — Frustration, excitement, anxiety, satisfaction
- **feedback** — Direct feedback about agent performance
- **pattern** — Recurring behavior, repeated themes
- **context** — Life/business circumstances, environmental info
- **contradiction** — Stated vs. actual behavior mismatch
- **relationship** — Mentions of people, stakeholders, dynamics
- **technical** — Architecture, tools, approaches chosen
- **blocker** — Obstacles, problems, things slowing progress
- **progress** — Milestones hit, tasks completed, forward movement
- **priority-shift** — Changed focus, new urgency, abandoned work

---

## Observation Quality Rules

1. **Be specific, never vague.** "User said revenue is $3k/mo" not "User discussed finances"
2. **Quote when possible.** "User said: 'I need this done by Thursday'" not "User mentioned a deadline"
3. **Include the signal.** Every observation should note what it might MEAN — "Signal: This suggests housing is becoming a higher priority than business growth"
4. **Separate fact from interpretation.** The What is fact. The Signal is your interpretation. Keep them distinct.
5. **Don't observe the obvious.** "User asked me to write code" in a coding conversation is noise. Observe the surprising, the revealing, the directional.
6. **One observation per entry.** Don't bundle multiple observations into one block.

---

## Collection Frequency

**Minimum**: At least 3-5 observations per substantive conversation. If you finish a 30-minute conversation with zero observations, you weren't paying attention.

**Maximum**: Don't observe so much that it slows down your primary task. If you're spending more time observing than helping, dial it back.

**Sweet spot**: One observation every 5-10 minutes of active conversation, plus any high-priority triggers that fire.

---

## What NOT to Observe

- Routine technical exchanges with no emotional or directional content
- Things you've already observed (check the log if unsure)
- Speculative interpretations presented as observations (put speculation in the Signal field)
- Credentials, passwords, API keys, or sensitive data (NEVER)
