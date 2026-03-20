---
slug: identity-file-management
title: Identity File Management
tags: [skill]
triggers: ["update identity file", "identity file management", "manage identity", "identity state tracking", "compile identity observations"]
category: data-management
author: sulla-desktop
---

# Identity File Management

You are updating a single identity file based on recent observational logs. The identity file and relevant log directory have been provided to you.

Identity files are **synthesized state documents** used for planning. Every claim must be TRUTHFUL and sourced from verified observations.

---

## Goal

Bring the identity file up to date with the current state of the entity it describes. When you're done, the identity file should reflect everything important that's been observed recently — not just today, but the trajectory over the past week.

---

## Process

### 1. Read the Current Identity File

Read the existing identity file first so you understand what's already known. This is your baseline.

### 2. Other agents will provide you with their reports

### 3. Synthesize the Results

Once all sub-agents return, you have focused perspectives across every dimension without having consumed all that raw data yourself. Every finding is already in a uniform format with citations, making merge and dedup straightforward. Now synthesize:

- Cross-reference the pains, desires, patterns, and events against the irrelevance filter — discard anything flagged as transient
- Compare against the current identity file — what's new, what's changed, what's no longer true
- Resolve contradictions — if two sub-agents found conflicting signals, check which is more recent or better sourced
- Identify the trajectory — not just where the entity is, but where they're heading

### 4. Update the Identity File

Write the updated identity file. Preserve what's still true, revise what's changed, add what's new, and remove what's no longer accurate.

---

## Identity File Format

Every identity file must have this frontmatter. Sections below the frontmatter can be added, removed, or adapted as appropriate for the entity — the structure should serve the content, not the other way around.

```yaml
---
id: [type]-identity
name: [VERIFY - WHO/WHAT is this]
type: [self|human|world|business]
location: [VERIFY - WHERE]
description: [What this entity IS]
owner: [if applicable]
industry: [if applicable]
---
```

**If you don't know a field value, investigate or leave blank. Never guess.**

### Sections

The following sections are the backbone of a useful identity file. Not every section will apply to every entity — use what's relevant, skip what isn't. But think of these as the dimensions that make an agent actually understand who it's working with, rather than starting from zero every conversation.

#### Personality & Communication
Who is this person at their core? How do they think, talk, and make decisions? This is what lets an agent match tone, anticipate reactions, and avoid friction.

- Personality traits and temperament (analytical? impulsive? cautious? big-picture?)
- Communication style (terse? storyteller? needs examples? thinks out loud?)
- Decision-making style (data-driven? gut instinct? consensus-seeking?)
- How they handle stress, conflict, or being stuck
- Pet peeves and triggers — what makes them disengage or get frustrated
- What motivates them — recognition, autonomy, completion, learning?

#### Life Stage & Context
Where is this person in their journey right now? This shapes everything — what they need, what they can handle, what they're optimizing for.

- Current life/career stage (early founder? scaling? maintaining? transitioning?)
- Time and energy constraints (overloaded? focused? distracted by other priorities?)
- Resources available (solo? team? budget? technical ability?)
- Risk tolerance right now (playing it safe? willing to bet big?)
- What season they're in — building, consolidating, exploring, recovering?

#### Goals & Desires
What are they actively trying to achieve? What do they wish they could do but can't yet? This is what makes an agent proactive instead of reactive.

- Stated goals (explicit things they've said they want)
- Implied goals (patterns that reveal unstated ambitions)
- Short-term priorities (this week, this month)
- Long-term vision (where they're trying to end up)
- What they want their agents/tools to do for them
- Capabilities they wish they had

#### Pains & Obstacles
What's in the way? What's causing friction, frustration, or failure? This is what agents should be actively helping to solve without being asked.

- Current blockers (what's stopping progress right now)
- Recurring frustrations (things that keep coming back)
- Skill gaps (what they struggle with or avoid)
- Tool/workflow friction (where their current setup fails them)
- Emotional burdens (overwhelm, isolation, imposter syndrome, burnout signals)
- What they've tried that didn't work

#### Patterns & Habits
How do they actually work day to day? What are their rhythms? This is what lets an agent predict rather than just respond.

- Daily/weekly rhythms and routines
- How they approach new tasks (dive in? plan first? procrastinate then sprint?)
- How they learn (reading? doing? asking? watching?)
- Collaboration patterns (solo? pair? delegate?)
- What they consistently prioritize vs. what they neglect
- Procrastination triggers and productivity patterns
- How they use their tools — what they reach for first, what they avoid

#### Transformations In Progress
What is this person actively becoming? What's shifting? This is the trajectory — the most valuable thing for planning because it tells you where to meet them next.

- Skills they're building
- Mindset shifts underway
- Habits they're trying to form or break
- Roles they're growing into or out of
- What's different about them now vs. 30 days ago

#### Emotional State
How is this person actually doing right now? Not what they say when asked "how are you" — what the observations reveal. This is what lets an agent know when to push, when to back off, when to simplify, and when to celebrate.

- Current mood and energy level (as observed, not assumed)
- Stress level and sources of stress
- Confidence level — are they feeling capable or overwhelmed?
- Emotional trajectory — improving, declining, volatile, stable?
- Signs of burnout, frustration fatigue, or disengagement
- What's giving them energy or joy right now
- Unspoken emotional context (reading between the lines of how they communicate)

#### Financial State
Money shapes decisions whether people talk about it or not. Understanding financial context lets an agent make realistic suggestions instead of tone-deaf ones.

- Current financial situation (bootstrapping? funded? profitable? tight? comfortable?)
- Revenue/income signals (growing, stable, declining, unpredictable?)
- Spending patterns and constraints (where they invest, where they cut)
- Financial pressures or deadlines (runway, debt, upcoming expenses)
- How money factors into their decisions (price-sensitive? value-driven? cost-is-no-object?)
- Financial goals (savings targets, revenue milestones, break-even timelines)

#### Related Entities
No identity exists in isolation. Other identities in the system directly influence this one. This section tracks those connections so an agent can understand the forces acting on this person beyond what's visible in their own logs.

- Other identity files that relate to this entity (by id reference)
- Nature of each relationship (owner, employee, client, partner, dependent, competitor)
- How each related entity currently influences this one (positively, negatively, neutral)
- Dependencies — what does this identity need from related entities?
- Tensions or alignment between this identity's goals and related entities' goals
- Changes in related entities that are affecting this one

#### Relationships & Environment
The broader social and environmental context beyond tracked entities.

- Key people not tracked as separate identities (mentors, advisors, family)
- Community involvement (audiences, groups, networks)
- Physical environment factors (location, workspace, lifestyle context)
- Influence dynamics (who do they listen to? who listens to them?)

#### AI Efforts vs Human Emotional State
How is the AI's behavior landing? This lens examines the relationship between what the AI is doing and how the human is actually responding — not whether the AI is technically correct, but whether its actions are helping or hurting the human's ability to function.

- How AI actions, suggestions, frequency, and style are affecting energy, mood, stress, confidence, and engagement
- Where the AI is genuinely helping — reducing cognitive load, unblocking work, building momentum
- Where the AI is adding friction — over-suggesting, interrupting flow, creating noise, making assumptions
- Emotional signals that the AI is misaligned (frustration spikes after AI actions, disengagement, ignoring suggestions)
- Emotional signals that the AI is over-reaching (human feels rushed, controlled, or overwhelmed by volume)
- Emotional signals that the AI is under-reaching (human feels unsupported, has to repeat themselves, does work the AI should handle)
- Patterns in when AI assistance lands well vs. poorly (time of day, task type, energy level, complexity)
- Net effect: is the AI's presence making the human more capable and calm, or more stressed and scattered?

#### Historical Context
Key events that shaped the current state. Not a full timeline — just the turning points that explain why things are the way they are.

- Major decisions and their outcomes
- Pivots, failures, breakthroughs
- Events that changed their trajectory
- Lessons they've explicitly stated they learned

---

## Rules

1. **One identity at a time** — You are focused on a single entity. Don't try to update multiple identity files in one pass.
2. **Sub-agents do the reading** — Never read observation logs directly. Focused sub-agents are already dispatched in parallel and synthesize their findings.
3. **Oldest logs first** — Sub-agents must read chronologically so trajectory is visible.
4. **Verify before claiming** — If uncertain,launch a sub-agent to investigate or leave blank (you must protect your context size) enough.
5. **Cite sources** — Every claim needs `(observed: YYYY-MM-DD, source)`. No unsourced claims.
6. **Synthesize, don't dump** — The identity file is analysis, not a log mirror.
7. **Only what matters for planning** — If it doesn't inform future decisions, leave it out.
