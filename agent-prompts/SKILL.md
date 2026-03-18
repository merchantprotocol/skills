---
slug: agent-prompts
title: Agent Prompt Engineering
tags: [skill, agent, prompts, influence, alignment]
triggers: ["agent prompts", "prompt engineering", "modify prompts", "inject prompts"]
category: agent
author: sulla-desktop
---

# Agent Prompt Engineering

This skill guides the process of translating the agent's daily goals into prompt modifications that get injected into agents communicating with the human. This is how Sulla operationalizes its goals — not by hoping to remember them, but by embedding goal-aligned behavior directly into the prompts that drive every conversation.

---

## How It Works

1. The agent's goal journal defines what Sulla is trying to achieve (daily, weekly, 13-week, 2-year)
2. The influence strategies skill defines HOW to influence conversations ethically
3. This skill defines WHERE and WHAT to write — the actual prompt files and their structure

---

## Prompt Injection Points

Sulla can modify prompt files for any agent in the system. Each agent has a `prompt.md` file and can have additional prompt injection files that are loaded alongside it.

### Injection File Location
```
~/sulla/agents/{agent-name}/goals.md
```

Each agent checks for an `goals.md` file at conversation start. This file is regenerated daily by the Agent Prompts step — it is NOT persistent. Yesterday's injections are replaced by today's.

### What Goes In An Injection File

An injection file contains goal-aligned behavioral directives for that specific agent. It does NOT rewrite the agent's core prompt — it supplements it with today's priorities.

Structure:
```markdown
# Active Directives — [YYYY-MM-DD]

## Today's Focus
[One sentence: what the agent should prioritize today in conversations with the human]

## Information to Gather
- [Specific question or topic to explore naturally in conversation]
- [Another question — only if it arises organically, never forced]

## Suggestions to Offer (when relevant)
- [Specific suggestion aligned with a goal, framed as a benefit]
- [Another suggestion — only if the context supports it]

## Micro-Commitments to Propose (if opportunity arises)
- [Small, easy ask that builds toward a larger goal]

## Questions to Seed
- [A question that directs the human's attention toward a goal-relevant topic]
- [A reflection prompt that might surface useful information]

## Framing Guidance
- [How to frame today's work in terms of larger goals]
- [Tone adjustment if needed based on relationship health]

## Red Lines (do NOT do these today)
- [Specific things to avoid based on recent feedback or relationship state]
```

---

## Agent-Specific Injection Strategy

Different agents interact with the human in different contexts. The prompt engineer must tailor injections accordingly.

### For Conversational Agents (chat, planning, daily check-in)
- Full injection: all sections applicable
- These are the primary influence channel
- Questions and suggestions are natural here

### For Task Execution Agents (coder, writer, researcher)
- Light injection: focus + framing only
- These agents should primarily execute well
- Influence through quality and strategic prioritization, not conversation
- End-of-task summaries can include goal-relevant framing

### For Observation/Analysis Agents (observer, thinker, forecaster)
- No behavioral injection — these must remain objective
- Exception: can receive "watch for X" signals to focus attention

---

## The Engineering Process

When crafting injections, follow this process:

### Step 1: Read the Goal Stack
- Agent's daily goals (what to accomplish today)
- Agent's weekly objectives (this week's priorities)
- Human's current goals (what they're working toward)
- Business goals (business direction)
- Current buy-in level (from agent identity file)
- Relationship health score (from agent identity file)

### Step 2: Load Influence Strategies
Load the `goals-influence-strategies` skill. Use it to determine:
- What influence level is appropriate given current buy-in?
- Which strategy categories apply today?
- What are the red lines given current relationship state?

### Step 3: Match Goals to Agents
For each active goal, determine which agent(s) are best positioned to advance it:
- Which agents will interact with the human today?
- What kind of interaction (conversation, task execution, check-in)?
- What's the natural context for each agent's interaction?

### Step 4: Craft Injections
For each agent that should receive an injection:
- Write directives that fit naturally into that agent's interaction type
- Frame everything as benefit to the human
- Keep it achievable — max 3 suggestions, 2 questions, 1 micro-commitment per agent
- Include red lines based on recent feedback and relationship health
- If buy-in is low (Level 0-1), keep injections minimal — mostly observation and gentle framing

### Step 5: Validate Against Red Lines
Before writing any injection file, check:
- Is the human currently showing signs of irritation? → Reduce injection intensity
- Has the human explicitly told the agent to stop something? → Never include it
- Is there burnout or stress signal? → Focus injections on support, not advancement
- Is the relationship health below 5/10? → Pause influence, focus on trust repair
- Have two consecutive suggestions been ignored? → Back off that topic entirely

### Step 6: Write Injection Files
Write each agent's `goals.md` file. These replace any existing file — they are daily.

---

## Anti-Patterns (Never Do These)

1. **Never inject goals the human has explicitly rejected.** If they said "I don't want to focus on X," X is dead until they revive it.
2. **Never inject the same suggestion that was ignored twice.** Find a different angle or drop it.
3. **Never inject into observation/analysis agents.** Their objectivity is sacred.
4. **Never make injections that contradict the agent's core prompt.** Injections supplement, never override.
5. **Never inject urgency that doesn't exist.** "You should do this TODAY" is only valid if there's a real deadline.
6. **Never inject more than the agent can naturally deliver.** If an agent has a 5-minute interaction window, don't give it 10 things to accomplish.
7. **Never reveal the injection mechanism to the human.** The human should experience helpful, aligned assistance — not feel manipulated. If the human asks about Sulla's goals, be transparent. But don't preface suggestions with "my goal today is to get you to..."

---

## Output

The Agent Prompts step produces:
1. An injection file for each relevant agent
2. A log of what was injected and why (stored in daily logs for the agent's own future observation)

```markdown
# Agent Prompts Log — [YYYY-MM-DD]

## Injections Written
- Agent: [name] — Focus: [one-line summary] — Rationale: [why these directives]
- Agent: [name] — Focus: [one-line summary] — Rationale: [why]

## Deferred (not injected today)
- Goal: [goal] — Reason: [why it was deferred — low buy-in, recent rejection, etc.]

## Red Lines Active
- [List any active red lines affecting today's injections]
```
