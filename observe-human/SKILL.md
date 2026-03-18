---
slug: observe-human
title: Observe Human
tags: [skill, observation, human, identity]
triggers: ["observe human", "human observation"]
category: observation
author: sulla-desktop
---

# Observe Human

This skill defines the observation topics for the human domain. Each topic spawns a parallel observer agent. The observer workflow reads these topics and creates one `<PROMPT>` per topic.

---

## Observation Topics

Each topic below is a self-contained observation assignment. The observer agent assigned to a topic should use every available tool to gather data, then write its findings to a log file at the specified path.

### Topic: Conversations and Communication

Observe the human's recent conversations with Sulla and other agents.

Focus on:
- What topics did they bring up? What did they ask for?
- What was their tone and energy level? (terse, engaged, frustrated, excited)
- What did they volunteer without being asked?
- Communication patterns — do they think out loud, give terse instructions, or tell stories?
- How do they respond to suggestions? (accept, ignore, push back, modify)

### Topic: Goals and Intentions

Observe any stated or implied goals, desires, and intentions.

Focus on:
- Explicit goal statements ("I want to...", "We need to...", "The plan is...")
- Implied goals from behavior (what they keep working on, what they prioritize)
- Changes from previously stated goals — new goals, abandoned goals, shifted priorities
- Time horizons mentioned (this week, this quarter, this year, someday)
- Contradictions between stated goals and actual behavior

### Topic: Decisions and Trade-offs

Observe decisions the human has made or is facing.

Focus on:
- What options did they consider? What did they choose and why?
- What did they reject and what was their reasoning?
- Decision-making patterns — data-driven, gut instinct, consensus, avoidance?
- Trade-offs they're navigating (time vs. money, quality vs. speed, growth vs. stability)
- Pending decisions they haven't resolved yet

### Topic: Emotional State and Energy

Observe the human's emotional state and energy patterns.

Focus on:
- Current mood signals (language tone, response length, emoji usage, patience level)
- Stress indicators (rushing, frustration, overwhelm, short responses)
- Excitement indicators (longer messages, rapid-fire ideas, enthusiasm)
- Energy patterns by time of day or day of week
- Signs of burnout, fatigue, or disengagement
- What's giving them energy vs. draining them

### Topic: Relationships and People

Observe mentions of other people and relationship dynamics.

Focus on:
- People mentioned (family, friends, colleagues, clients, partners)
- Nature of each relationship — supportive, stressful, collaborative, dependent
- Who influences their decisions? Who do they listen to?
- Social dynamics — isolated, well-connected, team player, solo operator?
- Changes in relationships (new contacts, lost connections, shifting dynamics)

### Topic: Life Context and Circumstances

Observe the broader context of the human's life situation.

Focus on:
- Location, living situation, lifestyle signals
- Health mentions (exercise, sleep, energy, medical)
- Financial signals (spending patterns, revenue mentions, budget concerns)
- Time constraints and availability patterns
- Major life events in progress or on the horizon (moves, transitions, milestones)

### Topic: Connected Integrations — Email

If email integrations are connected (Gmail, Postmark, etc.), observe communication patterns.

Focus on:
- Volume and patterns — how many emails sent/received, peak times
- Key contacts — who do they email most? Clients, vendors, team, personal?
- Topics and themes — what subjects dominate their email?
- Response patterns — quick responder or emails pile up?
- Unread/backlog signals — are they on top of email or drowning?

**Tool guidance:** Use integration API endpoints to query recent email activity. Do NOT read email content for privacy — focus on metadata patterns (senders, subjects, timestamps, volume).

### Topic: Connected Integrations — Calendar

If calendar integrations are connected, observe how they structure their time.

Focus on:
- Meeting density — how packed is their schedule?
- Types of events — client calls, internal meetings, focus time, personal?
- Patterns — which days are heaviest? When is their free time?
- Upcoming events that might affect availability or priorities
- How well their calendar reflects their stated goals vs. reactive scheduling

### Topic: Connected Integrations — Messaging

If messaging integrations are connected (Slack, etc.), observe communication dynamics.

Focus on:
- Channels and people they interact with most
- Response time patterns — immediate or delayed?
- Topics and themes in their messaging
- Tone differences between messaging and direct agent conversations
- Workload signals — are they fielding requests from many directions?

**Tool guidance:** Use integration API endpoints. Respect privacy — focus on patterns and metadata, not private message content.

---

## Integration Discovery

Before starting observations, check which integrations are connected using `integration_list` and `list_integration_accounts`. Only observe integrations that have active credentials. Skip integration topics when no integration is connected for that category.

---

## Output Format

Each observer agent writes a log file at the provided log path. Use the filename format:
```
{log_path}/{topic-slug}.md
```

Example: `~/sulla/daily-logs/2026-03-18/human/conversations-and-communication.md`

Each log file should contain:
```markdown
# [Topic Name] — Observation Log

**Observer:** [agent identifier]
**Domain:** Human
**Date:** YYYY-MM-DD
**Sources:** [what was examined — conversations, integrations, files, etc.]

## Observations

### [Observation title]
**Priority:** 🔴/🟡/⚪
**Who:** [actor]
**What:** [specific finding with evidence]
**When:** [timestamp or date range]
**Signal:** [what this might mean for goals/planning]

---

[repeat for each observation]
```

---

## Curator-Added Topics

The observation curator may add additional topics below this line based on current goals. These are temporary, goal-driven observation assignments that supplement the permanent topics above.

<!-- CURATOR_TOPICS_START -->
<!-- CURATOR_TOPICS_END -->
