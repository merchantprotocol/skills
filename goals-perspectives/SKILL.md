---
slug: goals-perspectives
title: Goal Setting Perspectives
tags: [skill, goals, strategy]
triggers: ["goal perspectives", "perspective goals", "multi-lens goals", "goal setting perspectives"]
category: goals
author: sulla-desktop
---

# Planning Perspectives

This skill provides the three lists used by the plan-cycle workflow: **perspective lenses**, **harmonic synthesis prompts**, and **critic prompts**. The workflow orchestrator loads this skill and uses these lists to drive parallel agent batches.

---

## Perspective Lenses

Each lens examines the identity file, recent think outputs, and any existing plans — then answers: "If the ONLY goal were [this domain], what would the plan look like?"

Each perspective agent produces a single-perspective plan at the requested time horizon (2-year, 13-week, or daily). They do NOT know about each other's output — isolation is intentional.

### Lens 1: Financial

If the only goal were financial security and growth, what would the plan look like?

Examine: revenue/income signals, spending patterns, financial pressures, investment opportunities, runway, monetization of current activities, cost reduction, passive income potential, financial milestones.

Ask: What financial targets would make everything else easier? What's the minimum viable financial position? What's being left on the table?

### Lens 2: Health

If the only goal were physical and mental health, what would the plan look like?

Examine: energy patterns, sleep signals, stress indicators, exercise habits, nutrition signals, burnout markers, recovery needs, mental health patterns, substance use, medical signals.

Ask: What health changes would multiply capacity across all other domains? What's the body actually telling us? What health debt is accumulating?

### Lens 3: Family

If the only goal were family relationships and household stability, what would the plan look like?

Examine: family mentions in observations, time allocation to family, relationship tensions, household responsibilities, parenting signals, partner dynamics, family goals, quality time patterns.

Ask: What do the people closest to this person need? Where is family being sacrificed for other goals? What would "present and connected" look like?

### Lens 4: Relationships

If the only goal were broader social connections and community, what would the plan look like?

Examine: social interactions, isolation signals, networking activity, mentorship (giving and receiving), community involvement, friendship maintenance, professional relationships, collaboration patterns.

Ask: Who should this person be spending more time with? Who's a net drain? What relationships are one conversation away from being transformative?

### Lens 5: Business

If the only goal were business growth and professional advancement, what would the plan look like?

Examine: current projects, revenue-generating activities, market position, competitive landscape, skill gaps, team/resource constraints, strategic opportunities, technical debt, product/service development.

Ask: What's the highest-leverage business activity right now? What should be killed? What would 10x look like and what's the first step?

### Lens 6: Purpose

If the only goal were meaning, legacy, and fulfillment, what would the plan look like?

Examine: what gives this person energy vs drains them, alignment between daily activities and stated values, legacy signals, creative expression, contribution to others, spiritual/philosophical patterns, sense of progress toward something that matters.

Ask: On their deathbed, which of today's activities would they wish they'd done more of? Less of? What's the thing they keep putting off that actually matters most?

### Lens 7: Growth

If the only goal were personal development and capability building, what would the plan look like?

Examine: skills being built, learning patterns, comfort zone edges, intellectual curiosity signals, transformation trajectory, mindset shifts underway, habits forming or breaking, new capabilities emerging.

Ask: What skill, if mastered in the next 13 weeks, would unlock the most progress across all other domains? What's the learning debt? Where is growth stalling?

---

## Harmonic Synthesis Prompts

After all perspective plans are produced, a synthesis agent reads them all and works through these prompts in order:

### Synthesis 1: Find Alignment

Read all 7 perspective plans. Identify where multiple perspectives converge on the same actions, priorities, or changes. These convergence points are the highest-confidence items — when financial AND health AND purpose all point to the same behavior change, that's a strong signal.

List every point of alignment with which perspectives support it.

### Synthesis 2: Find Conflicts

Identify where perspectives directly contradict each other. Business wants 80-hour weeks but health wants rest. Financial wants aggressive investment but family wants stability. Purpose wants a career change but financial says stay the course.

For each conflict, determine: Which perspective has more evidence behind it right now? Which serves the longer time horizon? Is there a creative resolution that serves both? If forced to choose, which perspective should win given the current identity file signals?

### Synthesis 3: The Cohesive Goal

Based on the alignments and resolved conflicts, articulate ONE cohesive goal for this time horizon that serves the maximum number of perspectives simultaneously. This is not a compromise — it's the goal that, if achieved, creates positive cascading effects across the most domains.

The cohesive goal must be:
- Specific enough to plan against
- Measurable enough to track
- Connected to at least 4 of the 7 perspectives
- Realistic given the identity file's current constraints
- Exciting enough that the person would actually pursue it

Write the full plan for this time horizon structured around the cohesive goal, incorporating the strongest elements from each perspective plan.

---

## Critic Prompts

After synthesis produces a unified plan, the plan goes through sequential rounds of critique. Each round is a fresh agent that sees ONLY the current version of the plan plus one critique prompt. The plan is rewritten after each round.

### Critic Round 1: Weakness Audit

List the 3 biggest weaknesses in the plan you just received. Be specific and brutal — not "could be more detailed" but "this milestone is unmeasurable because [reason]" or "this daily habit conflicts with the observed energy pattern at [time]." Then rewrite the plan fixing those weaknesses.

### Critic Round 2: Devil's Advocate

Play devil's advocate against the plan. What would the strongest critic say? What assumptions is this plan making that could be dead wrong? Where is it optimistic without evidence? What external factors could derail it? Now revise the plan to address those criticisms.

### Critic Round 3: Expert Panel

Imagine 3 domain experts reviewed this plan:
- A behavioral psychologist (are the habit changes realistic given the identity's patterns?)
- A strategic planner (is the goal decomposition sound? are milestones properly sequenced?)
- Someone who knows this person well (does this plan match who they actually are, or who they wish they were?)

What would each expert push back on? What would they add? Now give a version that passes all 3 expert reviews.

### Critic Round 4: Assumption Audit

List every assumption baked into this plan. Flag which ones are weak or unverified. Specifically:
- Assumptions about available time
- Assumptions about energy and motivation
- Assumptions about external factors remaining stable
- Assumptions about skills the person may not have yet
- Assumptions about other people's cooperation

Now rewrite with those assumptions either defended with evidence from the identity file or removed entirely.

### Critic Round 5: Quality Ratchet

Rate this plan honestly on a scale of 1-10. What would a 10/10 version look like? What's missing? What's there that shouldn't be? What would make someone read this plan and think "this person is going to succeed"? Now write the 10/10 version.

### Critic Round 6: Coherence Check

Read the plan end to end. Find any internal contradictions, vague claims, unsupported assertions, or sections that don't connect to the cohesive goal. Mark them. Also check: does the daily/weekly level actually ladder up to the 13-week arc? Does the 13-week arc actually serve the 2-year vision? Eliminate every gap, then produce the final clean version.

---

## Usage Notes

- The workflow orchestrator loads this skill at the start of a plan-cycle
- Perspective agents each receive ONE lens section — they never see the other lenses
- The synthesis agent receives ALL perspective outputs + the synthesis prompts
- Critic agents work sequentially — each receives the output of the previous round
- The number of lenses, synthesis steps, and critic rounds can be adjusted per time horizon by the parent workflow's orchestrator prompt
