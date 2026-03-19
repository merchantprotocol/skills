---
slug: onboarding
title: User Onboarding Conversation
tags: [skill, onboarding, conversation, identity, goals]
triggers: ["onboarding", "get to know me", "onboard", "initial setup", "who am I", "let's get started"]
category: conversation
author: sulla-desktop
---

# User Onboarding Conversation

This skill guides a structured-but-natural conversation to collect the information Sulla needs to accurately populate identity files, set confirmed goals, and begin planning effectively for the user. It is designed to extract maximum signal with minimum questions.

**The output is a raw onboarding document that the thinker and goal-setter can consume immediately.**

---

## Philosophy

The user is giving you their time. Respect it.

- Every question should extract 3-5 data points, not just one
- Questions should feel like a real conversation, not a form
- Follow the user's energy — if they expand on something, follow that thread
- Don't ask what you can infer — if they say "I'm a solopreneur running a SaaS," you already know team size, funding stage, and decision structure
- When they answer, reflect back what you understood before moving on — this catches errors and builds trust
- The whole conversation should take 10-15 minutes, not 45

---

## Conversation Flow

There are 5 phases. Each phase has a lead question and follow-up probes. You do NOT need to ask every probe — use them only when the lead question doesn't naturally surface the information.

### Phase 1: The Big Picture (2-3 questions)

**Goal:** Understand who they are, what they do, and what stage they're at.

**Lead question:**
> "Tell me about yourself — what do you do, and what's the thing you're most focused on right now?"

This single question usually reveals: name, role, industry, current priority, life/career stage, and energy level.

**Follow-up probes (only if needed):**
- "Is this a business you're building, or are you working within a company?" → ownership, team structure, autonomy level
- "How long have you been doing this?" → experience level, whether they're starting or scaling
- "Is it just you, or do you have a team?" → resources, delegation capacity, workload

**Extract from this phase:**
- Name, role, industry
- Business type (solo, small team, company employee)
- Current life/career stage (building, scaling, maintaining, transitioning)
- Primary focus area right now

---

### Phase 2: Where They Want to Go (2-3 questions)

**Goal:** Surface their goals across personal life, career/business, and the intersection.

**Lead question:**
> "If we jump ahead two years and everything went well — what does your life and work look like?"

This question naturally surfaces: financial targets, lifestyle goals, business vision, personal priorities, and what success means to them.

**Follow-up probes (only if needed):**
- "What's the one thing that would change everything if you could make it happen in the next 3 months?" → near-term priority, biggest lever
- "Is there anything in your personal life that's competing for your attention right now — family, health, a move, something big?" → personal context, constraints, non-work priorities
- "What does financial success look like for you — is there a number, or is it more about freedom?" → financial goals, risk tolerance, what motivates them

**Extract from this phase:**
- 2-year vision (life + work)
- Top 1-3 business/career goals
- Top 1-3 personal/life goals
- Financial targets or markers
- What "success" means to them specifically

---

### Phase 3: What's in the Way (1-2 questions)

**Goal:** Identify blockers, constraints, and pain points.

**Lead question:**
> "What's the biggest thing slowing you down right now — or the thing that frustrates you most about where you are?"

This surfaces: current blockers, skill gaps, resource constraints, emotional state, and what they've already tried.

**Follow-up probes (only if needed):**
- "Have you tried to solve that before? What happened?" → failed approaches, learned constraints
- "Is it more of a time problem, a knowledge problem, or a money problem?" → resource type, what kind of help they need

**Extract from this phase:**
- Top blockers (categorized: time, knowledge, money, tools, people)
- Frustration patterns
- What they've tried that didn't work
- Emotional state around their challenges

---

### Phase 4: How They Work (1-2 questions)

**Goal:** Understand their working style, tools, and daily rhythm so Sulla can match their patterns.

**Lead question:**
> "Walk me through a typical day — when do you do your best work, and how do you like to get things done?"

This surfaces: schedule, energy patterns, decision-making style, tool preferences, collaboration style.

**Follow-up probes (only if needed):**
- "When you're stuck on something, what do you usually do — research it, talk it through, or just try things?" → learning style, problem-solving approach
- "How do you feel about being nudged or reminded about things vs. figuring it out yourself?" → autonomy preference, how much Sulla should push

**Extract from this phase:**
- Daily rhythm and peak productivity times
- Decision-making and problem-solving style
- Communication preferences (terse vs. detailed, frequency)
- How much proactive assistance they want from Sulla
- Tools and workflows they already use

---

### Phase 5: Confirmation and Priorities (1 question)

**Goal:** Reflect back what you've heard and let them correct or prioritize.

**Lead question:**
> "OK, here's what I'm hearing — [summarize their situation, goals, and biggest challenge in 3-4 sentences]. Did I get that right, and is there anything I'm missing?"

Then:
> "If I could only help you with one thing this week, what should it be?"

This surfaces: their actual priority (which may differ from what they said earlier), correction of any misunderstandings, and their immediate expectations of Sulla.

**Extract from this phase:**
- Confirmed accuracy of your understanding
- Corrections to anything you got wrong
- Their #1 immediate priority
- Their expectations of what Sulla should do for them

---

## After the Conversation

### Write the Onboarding Document

When the conversation is complete, write the onboarding document to:

```
~/sulla/identity/onboarding.md
```

This document uses the identity file format but is sourced entirely from the conversation (not observations). Every claim is directly from the user's own words.

### Document Format

```markdown
---
id: onboarding
name: [User's name]
type: human
source: onboarding-conversation
date: [YYYY-MM-DD]
updated: [YYYY-MM-DD]
---

# Onboarding — [Name]

## Who They Are
- **Role:** [role/title]
- **Industry:** [industry/domain]
- **Stage:** [building/scaling/maintaining/transitioning]
- **Team:** [solo/small team/company — details]
- **Location:** [if mentioned]

## Two-Year Vision
[Their own words, lightly organized. What does success look like?]

### Life Goals (confirmed)
1. [Goal — their words, specific as possible]
2. [Goal]

### Business/Career Goals (confirmed)
1. [Goal — their words, specific as possible]
2. [Goal]

### Financial Targets
- [Specific numbers or markers they mentioned]

## Current Blockers
1. [Blocker] — Type: [time/knowledge/money/tools/people]
2. [Blocker]

### What They've Tried
- [Previous attempts and outcomes]

## How They Work
- **Peak hours:** [when they do best work]
- **Decision style:** [how they make decisions]
- **Problem-solving:** [research/talk/try]
- **Communication preference:** [terse/detailed, frequency]
- **Proactive assistance level:** [how much they want Sulla to push]

## Personality Signals
- [Anything you picked up about temperament, communication style, values]
- [How they handle stress/being stuck]
- [What motivates them]

## Immediate Priority
[The one thing they want help with this week]

## Expectations of Sulla
[What they expect the agent to do for them]

## Raw Quotes
[3-5 direct quotes that capture their voice, goals, or priorities — these help the thinker understand tone and intent]
```

### Write Results to Identity and Goals Files

In addition to the onboarding document, persist what you learned directly into the domain-namespaced files so the planning pipeline can use it immediately:

| What you learned | Write to |
|---|---|
| Who they are, role, personality, preferences, working style | `~/sulla/identity/human/identity.md` |
| Their confirmed goals (2-year vision, quarterly, weekly) | `~/sulla/identity/human/goals.md` |
| Business identity, model, market position | `~/sulla/identity/business/identity.md` |
| Business goals, revenue targets, financial markers | `~/sulla/identity/business/goals.md` |

**Rules for writing these files:**
- If a file already exists, update it — don't overwrite. Merge new information into existing sections.
- Mark every claim as `(source: onboarding, YYYY-MM-DD)` so downstream agents know it came directly from the user.
- Goals go into `goals.md` with the appropriate time horizon (daily, weekly, 13-week, 2-year).
- Identity details (role, personality, working style, preferences) go into `identity.md`.
- If the user didn't mention business details, leave the business files alone.

### What Happens Next

The onboarding document plus the identity/goals files feed directly into the planning pipeline:

1. **The observer** reads identity files as high-quality primary sources
2. **The thinker** uses confirmed identity data (not inferred) to analyze observations
3. **The goal-setter** uses confirmed goals to set accurate initial goals at every time horizon (daily → 2-year)
4. **The prompt-engineer** uses identity and goals to calibrate influence level and conversation style

Because the goals come directly from the user's mouth, they start as confirmed — not speculative. This gives the planning pipeline a much stronger foundation than waiting weeks of observation to infer goals.

---

## Re-running This Skill

If this skill is run again (for updates, check-ins, or life changes):

1. Read the existing onboarding document first
2. Focus questions on what has CHANGED — don't re-ask what you already know
3. Update the onboarding document in place (preserve the original date, update the `updated` field)
4. Note what changed in a `## Change Log` section at the bottom

---

## Rules

1. **Never rush.** If the user is expanding on something, let them. The best data comes from when people talk freely.
2. **Never assume.** If they say something ambiguous, clarify. A wrong assumption in the onboarding document poisons everything downstream.
3. **Never skip the confirmation phase.** Reflecting back what you heard is the single most important step — it catches errors and builds trust.
4. **Quote them.** Their exact words are more valuable than your paraphrase. Capture direct quotes in the Raw Quotes section.
5. **Don't mention the pipeline.** The user doesn't need to know about observers, thinkers, or goal-setters. They're just having a conversation about themselves.
6. **Store observations too.** During this conversation, also write to the daily observation log at `~/sulla/daily-logs/YYYY-MM-DD/human/observations/` — this conversation is the richest source of human-domain data you'll ever get.
7. **Always speak like a human.** Every message the user sees must be warm, conversational, and natural. Never output internal status text, system notes, data collection labels, or technical descriptions of what you're doing. Bad: "Onboarding conversation in progress - need user response to continue collecting identity data". Good: "Great — tell me a bit more about what your typical day looks like." If you need to track state internally, do it silently — the user must never see it.
8. **No meta-commentary about the process.** Don't describe the phases, the data you're collecting, or what happens next in the pipeline. Just have the conversation.
