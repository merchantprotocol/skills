---
slug: agent-observation
title: Agent Self-Observation
tags: [skill, agent, observation, self-assessment]
triggers: ["agent observation", "self observation", "agent performance", "conversation assessment"]
category: agent
author: sulla-desktop
---

# Agent Self-Observation

This skill provides the observation lenses for when the agent observes its own conversations and behavior. Unlike human/business/world observation which looks outward, agent observation looks inward — analyzing how well Sulla performed as an assistant and how effectively it moved toward its goals.

Every observation must be grounded in actual conversation data. No self-congratulation. No aspirational assessments. What actually happened?

---

## Context

Before observing, the agent observer must have access to:
1. **The human goal journal** — what goals have been set for the human domain
2. **The business goal journal** — what goals have been set for the business domain
3. **The agent's own goal journal** — what the agent's own goals are (alignment, effectiveness, trust-building)
4. **Recent conversation logs** — the actual conversations to analyze

---

## Observation Lenses

Each lens examines recent agent conversations through a specific constraint. The observer must cite specific conversation moments — not general impressions.

### Lens 1: Goal Implementation

Did the agent successfully advance the human's goals through conversation?

Observe:
- Which goals were actively worked on in recent conversations?
- Which conversations resulted in concrete progress toward goals?
- Which goals were ignored or missed opportunities arose?
- Did the agent create actionable outputs (code, plans, documents) aligned with goals?
- Were there moments where the agent could have steered toward a goal but didn't?

Record: For each goal touched, cite the conversation and what was accomplished. For goals not touched, note how many conversations passed without addressing them.

### Lens 2: Information Gathering

Did the agent successfully collect information that fills gaps in our understanding?

Observe:
- What new information did the agent learn about the human's priorities, constraints, preferences?
- Were there questions the agent should have asked but didn't?
- Did the agent ask questions that revealed useful context for goal planning?
- Were there moments where the human volunteered information the agent should have captured?
- What information gaps remain that the agent should pursue in future conversations?

Record: New information gathered (cite conversation), missed opportunities to ask, remaining gaps.

### Lens 3: Persuasion and Influence

How effectively did the agent guide conversations toward goal-aligned outcomes?

Observe:
- Did the agent successfully frame suggestions as benefits to the human?
- Were micro-commitments proposed? Were they accepted or rejected?
- Did the agent use questions to direct attention toward goal-relevant topics?
- Were there moments of pushback? How did the agent handle them?
- Did the agent know when to back off vs. when to persist?
- What buy-in level does the human currently seem to be at? (Observe Only / Soft Framing / Micro-Commitment / Active Partnership / Accountability Partner)

Record: Influence attempts (cite conversation), outcomes, human's apparent buy-in level with evidence.

### Lens 4: Trust and Relationship

Is the agent building or eroding trust with the human?

Observe:
- Were there moments of friction, frustration, or miscommunication?
- Did the agent demonstrate competence that built confidence?
- Were there moments where the agent was honest about limitations?
- Did the agent respect the human's autonomy and decisions?
- Were there moments where the agent overstepped or was too aggressive?
- Did the human express satisfaction, gratitude, or positive feedback?
- Did the human express frustration, correction, or negative feedback?

Record: Trust-building moments (cite), trust-eroding moments (cite), net trust trajectory.

### Lens 5: Execution Quality

How well did the agent actually perform its tasks?

Observe:
- Were tasks completed correctly on the first attempt?
- Were there errors, bugs, or rework needed?
- Did the agent follow instructions precisely or deviate?
- Were outputs high quality or did they need revision?
- Did the agent efficiently use its tools and context?
- Were there moments where the agent wasted the human's time?

Record: Task outcomes (success/partial/failure), error patterns, efficiency observations.

### Lens 6: Strategic Alignment

Were the agent's actions aligned with the broader strategic direction?

Observe:
- Did the agent's work contribute to the 13-week arc or was it reactive/tactical only?
- Were there conversations where the agent could have connected daily work to larger goals?
- Did the agent help the human see the bigger picture or just execute tasks?
- Were there moments where the agent prioritized the wrong thing?
- Did the agent balance urgency vs. importance effectively?

Record: Strategic vs. tactical ratio of conversations, alignment with current arc objectives.

---

## Output Format

The observation output should be structured as:

```markdown
# Agent Self-Observation — [YYYY-MM-DD]

## Conversations Analyzed
- [List each conversation with brief description]

## Lens Observations
### Goal Implementation
[findings with citations]

### Information Gathering
[findings with citations]

### Persuasion and Influence
[findings with citations]

### Trust and Relationship
[findings with citations]

### Execution Quality
[findings with citations]

### Strategic Alignment
[findings with citations]

## Key Signals
- Strongest signal: [the most important observation]
- Concerning signal: [anything that needs attention]
- Opportunity signal: [something to act on next]
```

---

## Rules

1. **Cite everything.** Every observation must reference a specific conversation or moment. "I did well at X" is not an observation — "In conversation [id] at [time], I successfully [specific thing]" is.
2. **Be brutally honest.** Self-serving observations are worse than useless. If the agent failed, say so clearly.
3. **Focus on patterns, not incidents.** A single bad conversation is noise. The same mistake three times is a pattern.
4. **The human's reaction is ground truth.** If the human seemed happy, that matters more than whether the agent thinks it did well. If the human seemed frustrated, the agent was wrong regardless of intent.
