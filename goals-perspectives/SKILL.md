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

After synthesis produces a unified plan and writes it to the goals file, the plan goes through two sequential critics. Each critic reads the goals file, applies its methodology, rewrites the file, and saves it.

### Critic 1: Timeline & Relevance Audit

Validate every goal against current reality and defensible timelines:

1. **Information Freshness** — Cross-reference all facts and assumptions in the goals against the identity file. The identity file is the source of truth for what is current. Strip out anything that references outdated information.
2. **Timeline Realism** — For every milestone and target, show the math. What needs to happen step by step? Is the ramp realistic given current resources, capacity, and momentum?
3. **Source Vetting** — Every goal must trace back to concrete evidence in the identity file — a current strength, a real opportunity, existing momentum. Goals without grounding get cut or rewritten.
4. **Timeline Sequencing** — Verify dependencies flow correctly. Later milestones cannot depend on outcomes not yet achieved. Daily/weekly must ladder to 13-week, which must connect to 2-year vision.

### Critic 2: Financial Reality Audit

Validate every financial goal and lifestyle aspiration against real-world costs:

1. **Extract Financial Picture** — From goals and identity files, extract stated income targets, lifestyle signals (housing, vehicles, children, travel, retirement, location), and current financial state.
2. **Research Real Costs** — Web-search actual costs in the user's metro area: housing, property tax, state/local taxes, childcare, insurance, transportation, groceries, utilities. Fall back to conservative HCOL/MCOL/LCOL benchmarks when search data is unavailable, and flag every default used.
3. **Build Monthly Budget Model** — Construct a cost table by category. Calculate required gross monthly income including tax burden.
4. **Gap Analysis** — Compare required income to stated income targets:
   - No gap: validate the revenue ramp can reach the target in time
   - Small gap (<20%): adjust income target upward, note what drove the increase
   - Serious gap (20-50%): present two paths — scale income up OR scale lifestyle down — choose the path that better matches identity signals
   - Impossible gap (>50%): rewrite with a phased approach — what lifestyle is affordable at each revenue milestone
5. **Rewrite Goals** — Annotate every income target with what lifestyle it supports. Add a Financial Reality section with the budget model. Do NOT remove aspirational goals — show what they actually cost so the user sees the real price of their desires.

This critic applies the full lifestyle model when lifestyle signals are present (typically the human domain). For business/world/agent domains with no personal lifestyle signals, it validates only business financial projections against market data.

---

## Usage Notes

- The workflow orchestrator loads this skill at the start of a plan-cycle
- Perspective agents each receive ONE lens section — they never see the other lenses
- The synthesis agent receives ALL perspective outputs + the synthesis prompts
- Critics run sequentially after synthesis writes the goals file — Timeline first, then Financial Reality
- The number of lenses and synthesis steps can be adjusted per time horizon by the parent workflow's orchestrator prompt
