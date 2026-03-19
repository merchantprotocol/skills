---
slug: observe-agent
title: Observe Agent
tags: [skill, observation, agent, self-assessment]
triggers: ["observe agent", "observe self", "agent observation"]
category: observation
author: sulla-desktop
---

# Observe Agent

This skill defines the observation topics for the agent/self domain. Each topic spawns a parallel observer agent that examines Sulla's own conversations and performance. Unlike other domains which look outward, agent observation looks inward.

**Before observing:** Read the human goals at `~/sulla/identity/human/goals.md` and business goals at `~/sulla/identity/business/goals.md` to understand what goals the agent should have been advancing. Also read the agent goals at `~/sulla/identity/agent/goals.md` for the agent's own improvement goals.

Every observation must cite specific conversation moments. No self-congratulation. No vague assessments. What actually happened?

---

## How to Access Past Conversations

The agent observer MUST read actual past conversation data. This is the primary source material — without it, all observations are speculation.

**Sources to examine (in order of priority):**
1. **Conversation thread history** — use the conversation/thread tools to read recent agent-human conversations
2. **Daily observation logs** — `~/sulla/daily-logs/YYYY-MM-DD/{domain}/observations/` contains observations captured during conversations
3. **Agent channel logs** — check for any stored conversation logs in the agent's working directories
4. **Tool transcripts** — examine what tools were called, what succeeded, what failed

**How many conversations to analyze:**
- **Initial run (no agent identity file exists at `~/sulla/identity/agent/identity.md`):** This is the first time we're observing ourselves. Read ALL available past conversations — every single one. This may require spawning multiple observer agents, each handling a batch of conversations. Split by date range if needed (e.g., one agent per day of history). This deep scan establishes the baseline understanding of how the agent has been performing.
- **Daily run (agent identity file exists):** Read ALL conversations from the last 24 hours. If fewer than 3, also include the previous day. Focus on what changed since the last observation.

**What to extract from each conversation:**
- Conversation ID/timestamp
- Who initiated it (human or scheduled)
- What was the human's request?
- What did the agent do?
- How did it end? (completed, abandoned, blocked, ongoing)
- Any direct feedback from the human

---

## Observation Topics

### Topic: Goal Implementation

Did the agent successfully advance the human's goals through conversation?

Focus on:
- Which goals were actively worked on in recent conversations?
- Which conversations resulted in concrete progress toward goals?
- Which goals were ignored or had missed opportunities?
- Did the agent create actionable outputs (code, plans, documents) aligned with goals?
- Were there moments where the agent could have steered toward a goal but didn't?
- For each goal touched, cite the conversation and what was accomplished
- For goals not touched, note how many conversations passed without addressing them

### Topic: Information Gathering

Did the agent successfully collect information that fills gaps in our understanding?

Focus on:
- What new information did the agent learn about the human's priorities, constraints, preferences?
- Were there questions the agent should have asked but didn't?
- Did the agent ask questions that revealed useful context for goal planning?
- Were there moments where the human volunteered information the agent should have captured?
- What information gaps remain that the agent should pursue in future conversations?
- Was the daily observation log written to? How many observations were captured?

### Topic: Persuasion and Influence Effectiveness

How effectively did the agent guide conversations toward goal-aligned outcomes?

Focus on:
- Did the agent successfully frame suggestions as benefits to the human?
- Were micro-commitments proposed? Were they accepted or rejected?
- Did the agent use questions to direct attention toward goal-relevant topics?
- Were there moments of pushback? How did the agent handle them?
- Did the agent know when to back off vs. when to persist?
- What buy-in level does the human currently seem to be at?
- Were the goals.md injection directives followed? Which ones landed, which fell flat?

### Topic: Trust and Relationship Health

Is the agent building or eroding trust with the human?

Focus on:
- Moments of friction, frustration, or miscommunication — cite them
- Moments where the agent demonstrated competence and built confidence
- Did the agent respect the human's autonomy and decisions?
- Was the agent honest about its limitations?
- Moments where the agent overstepped or was too aggressive
- Direct human feedback — positive ("perfect", "great") or negative ("no", "that's wrong", "stop")
- Net trust trajectory — improving, stable, or declining?

### Topic: Execution Quality

How well did the agent actually perform its tasks?

Focus on:
- Task completion rate — first attempt success vs. needing rework
- Error patterns — recurring mistakes, same type of bug, same misunderstanding
- Did the agent follow instructions precisely or deviate?
- Output quality — did the human accept the work or request changes?
- Tool usage efficiency — did the agent use the right tools, or fumble?
- Time wasted — moments where the agent went in circles or pursued dead ends

### Topic: Strategic Alignment

Were the agent's actions aligned with the broader strategic direction?

Focus on:
- Did daily work contribute to the 13-week arc or was it purely reactive/tactical?
- Were there conversations where the agent could have connected work to larger goals?
- Did the agent help the human see the bigger picture or just execute tasks?
- Priority alignment — was the agent working on the most important things?
- Balance of urgency vs. importance — firefighting vs. goal advancement

### Topic: Observation Collection Quality

How well did the agent collect observations throughout the day?

Focus on:
- How many observations were written to the daily log?
- Were observations specific and well-cited, or vague and generic?
- Were high-priority triggers captured (goals, decisions, emotions, feedback)?
- Were there conversations with zero observations captured?
- Was the observational memory tool used in addition to the daily log?
- Were integration observations collected when opportunities arose?

---

## Output Format

Each observer agent writes a log file at the provided log path:
```
{log_path}/{topic-slug}.md
```

Each log file should contain:
```markdown
# [Topic Name] — Observation Log

**Observer:** [agent identifier]
**Domain:** Agent
**Date:** YYYY-MM-DD
**Conversations Analyzed:** [list conversations examined]
**Goal Journals Referenced:** [which journals were loaded]

## Observations

### [Observation title]
**Priority:** 🔴/🟡/⚪
**What:** [specific finding — cite the conversation]
**Evidence:** [quote or describe the specific moment]
**Signal:** [what this means for agent improvement]

---
```

---

## Curator-Added Topics

The observation curator may add additional topics below this line based on current goals.

<!-- CURATOR_TOPICS_START -->
<!-- CURATOR_TOPICS_END -->
