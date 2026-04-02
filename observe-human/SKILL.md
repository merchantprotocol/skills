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

### Topic: Documents and Files

Observe the human's documents folder and project files to understand who they are, what they're working on, and what matters to them. This may require multiple agents to cover the full scope.

Observe the human's personal documents folder to understand who they are, what's going on in their life, and what matters to them. This may require multiple agents to cover the full scope.

**Important:** Only scan `~/Documents/` — this is the human's own files. Do NOT scan `~/sulla/projects/` as those are agent-created projects that reflect our decisions, not the human's.

Focus on:
- **Documents folder** (`~/Documents/`) — scan all top-level folders and files. What categories exist? (legal, financial, personal, business, kids, insurance, resumes, etc.)
- **File recency** — which files and folders were recently created or modified? Use `stat` to check created/modified dates. Recent activity reveals current priorities.
- **File age patterns** — what's been untouched for months? What gets constant attention?
- **Personal documents** — resumes reveal career trajectory, legal docs reveal life events, financial docs reveal money situation
- **Business documents** — contracts, proposals, agreements reveal the business's actual operations
- **Family signals** — kids folders, family photos, school documents reveal family structure and priorities
- **Naming patterns** — how do they organize? Folder names reveal mental models and priorities
- **Desktop and Downloads** — check `~/Desktop/` and `~/Downloads/` for recent activity that reveals current focus

**Tool guidance:** Use `exec` with `ls -la` and `stat` to check file dates. Use `file_search` to scan directories. Read document headers and summaries — don't read entire large files. For sensitive files (legal, financial), note their existence and recency without extracting private details.

**Privacy rules:**
- Note the existence and recency of sensitive documents, not their contents
- Never extract or store specific financial figures from personal documents
- Never store credential or authentication details found in files
- Focus on patterns and categories, not private data

### Topic: Location and Weather

Observe the human's physical location and local conditions.

Focus on:
- **Determine location** — check system timezone (`date +%Z`), locale settings, and any location mentions from conversations or documents. If calendar events have locations, those are strong signals.
- **Current weather** — use browser tools to check weather for the determined location. Current temperature, conditions, and forecast.
- **Weather patterns** — is it a season that affects mood or behavior? (dark winters, extreme heat, storm season)
- **Local events** — major events happening in their area that might affect their schedule or mood
- **Cost of living signals** — if location is known, understand the economic context (expensive city vs. affordable area)
- **Time zone** — what time zone are they in? This affects when they're likely available and their daily rhythm.

**Tool guidance:** Use `exec` to check system timezone and locale. Use browser tools to look up weather and local conditions. Cross-reference with any location mentions from conversations or calendar events.

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

Before starting observations, check which integrations are connected by calling `integration_list` and `vault/list_accounts` via the Tools API at `http://host.docker.internal:3000/v1/tools/list`. Only observe integrations that have active credentials. Skip integration topics when no integration is connected for that category.

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
