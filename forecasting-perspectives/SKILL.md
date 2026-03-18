---
slug: forecasting-perspectives
title: Forecasting Perspectives
tags: [skill, forecasting, strategy, worldview]
triggers: ["forecast perspectives", "world view lenses", "forecasting lenses", "world conditions"]
category: forecasting
author: sulla-desktop
---

# Forecasting Perspectives

This skill provides the three lists used by the forecast-cycle workflow: **perspective lenses**, **harmonic synthesis prompts**, and **critic prompts**. The workflow orchestrator loads this skill and uses these lists to drive parallel agent batches.

Unlike goal-setting perspectives which ask "what should we do?", forecasting perspectives ask "what is happening and what will likely happen?" — grounded in the topics and directions derived from the human and business goal journals.

---

## Topic Lens Integration

Before running perspectives, the forecaster receives a **topic lens** extracted from the human and business goal journals. This lens tells you what subjects matter: housing market, job market, audience trends, niche conditions, etc.

Every perspective must focus its analysis through this topic lens. If the topic lens says "housing market, SaaS audience growth, remote work trends" — every lens examines the world through those subjects. Do NOT boil the ocean observing everything. The topic lens is your scope.

---

## Perspective Lenses

Each lens examines the observation logs, identity file, and think outputs for the world domain — then answers: "What are the current conditions, emerging trends, and likely trajectory for [this domain] as it relates to our topic lens?"

Each perspective agent produces a single-perspective forecast at the requested time horizon (2-year outlook, 13-week forecast, or daily briefing). They do NOT know about each other's output — isolation is intentional.

### Lens 1: Economic

What are the economic conditions and trajectory relevant to our topic lens?

Examine: macro indicators (inflation, interest rates, GDP), sector-specific economics, cost trends, pricing dynamics, consumer spending patterns, investment climate, credit conditions, currency movements — all filtered through the topic lens subjects.

Ask: What economic headwinds or tailwinds affect our direction? What's the 6-month economic trajectory for our specific interests? What economic assumptions in our goals are at risk?

### Lens 2: Market / Niche

What are the conditions in the specific markets and niches relevant to our topic lens?

Examine: audience size and growth/contraction, competitor movements, market saturation, demand signals, pricing trends, distribution channel health, customer sentiment, market timing, barriers to entry/exit.

Ask: Is our target market growing or shrinking? What are competitors doing that we should know about? What market shift could invalidate our current approach? Where is unmet demand?

### Lens 3: Technology

What technological changes are relevant to our topic lens?

Examine: tool and platform changes, automation opportunities, AI/ML developments affecting our domains, infrastructure costs, emerging capabilities, technology adoption curves, platform risk (dependency on specific services), open source movements.

Ask: What technology shift could 10x our progress or make our current approach obsolete? What tools exist now that didn't 3 months ago? What technology debt are we accumulating?

### Lens 4: Labor / Talent

What are the workforce and talent conditions relevant to our topic lens?

Examine: job market conditions in relevant sectors, hiring/firing trends, salary movements, remote work dynamics, skill demand shifts, contractor/freelance market, talent availability, workforce sentiment, automation displacement.

Ask: If we need to hire, what's the market like? If we need a job, what's the landscape? What skills are becoming more/less valuable in our space? What workforce trends affect our plans?

### Lens 5: Regulatory / Political

What regulatory and political conditions are relevant to our topic lens?

Examine: policy changes affecting our domains, tax law changes, industry regulations, compliance requirements, political stability, government spending priorities, trade dynamics, data privacy rules, licensing requirements.

Ask: What regulation could help or hurt our direction? What political changes should we be preparing for? What compliance costs are coming? What government programs or incentives align with our goals?

### Lens 6: Social / Cultural

What social and cultural trends are relevant to our topic lens?

Examine: consumer behavior shifts, cultural sentiment changes, demographic trends, media narratives affecting our space, public opinion movements, lifestyle trend shifts, generational dynamics, community and social movement patterns.

Ask: Is the cultural wind at our back or in our face? What narrative shifts affect perception of what we're building? What social trends create opportunity or risk?

### Lens 7: Risk / Black Swan

What emerging risks and potential disruptions are relevant to our topic lens?

Examine: geopolitical tensions, supply chain vulnerabilities, pandemic/health risks, climate/weather events, financial system stress, cybersecurity threats, single points of failure in our dependencies, tail-risk scenarios.

Ask: What's the thing nobody's talking about that could change everything? What are we most exposed to? What would our contingency be if the worst-case scenario in our top topic lens subject materialized?

---

## Harmonic Synthesis Prompts

After all perspective forecasts are produced, a synthesis agent reads them all and works through these prompts in order:

### Synthesis 1: Find Convergence

Read all 7 perspective forecasts. Identify where multiple perspectives point to the same emerging condition, trend, or risk. When economic AND market AND technology lenses all signal the same direction, that's a high-confidence forecast.

List every convergence point with which perspectives support it.

### Synthesis 2: Find Contradictions

Identify where perspectives contradict each other. Technology says opportunity is growing but market says demand is shrinking. Economic conditions look favorable but political risk is high.

For each contradiction: Which perspective has stronger evidence? Is the contradiction real or just different time horizons? What additional observation would resolve it? If forced to choose, which signal is more reliable?

### Synthesis 3: The Conditions Report

Based on convergences and resolved contradictions, produce a unified conditions report for this time horizon. Structure it as:

1. **Current State** — What are conditions RIGHT NOW across the topic lens subjects?
2. **Trajectory** — Where are things heading? What's the trend direction and velocity?
3. **Opportunities** — What conditions create openings aligned with our personal/business direction?
4. **Threats** — What conditions create risk to our personal/business goals?
5. **Forecast** — What will likely be true at the end of this time horizon? State confidence levels.
6. **Recommended Adjustments** — Based on these conditions, what should be reconsidered in the human/business goal plans? (Not directives — observations that the planner should weigh.)

The conditions report must be:
- Specific to our topic lens subjects (not generic world news)
- Evidence-grounded (cite observation sources)
- Actionably relevant (every item connects to our direction)
- Honest about uncertainty (confidence levels on forecasts)

---

## Critic Prompts

After synthesis produces a conditions report, it goes through sequential rounds of critique. Each round is a fresh agent that sees ONLY the current version of the report plus one critique prompt. The report is rewritten after each round.

### Critic Round 1: Evidence Audit

For every claim in this conditions report, check: Is it grounded in actual observation data, or is it conventional wisdom / assumed knowledge? Flag every claim that lacks a specific observation source. Forecasting with stale priors is worse than no forecast. Rewrite removing or marking ungrounded claims.

### Critic Round 2: Bias Check

What cognitive biases might be distorting this forecast? Confirmation bias (seeing what supports our goals)? Recency bias (overweighting recent events)? Anchoring (stuck on old conditions)? Optimism bias (assuming things will work out)? For each identified bias, correct the forecast. Rewrite with debiased assessments.

### Critic Round 3: Scenario Stress Test

Take the three most important forecasts in this report. For each, construct a plausible scenario where the opposite happens. How likely is each counter-scenario? If any counter-scenario is >20% likely, the original forecast needs to be softened or the report needs contingency language. Rewrite with stress-tested forecasts.

### Critic Round 4: Relevance Filter

Re-read the topic lens. Now re-read the entire conditions report. Flag anything that doesn't directly connect to the topic lens subjects or our personal/business direction. Forecasting interesting-but-irrelevant conditions wastes attention. Cut ruthlessly. Rewrite keeping only what the planner actually needs.

### Critic Round 5: Actionability Check

For every "Recommended Adjustment" in this report: Is it specific enough that a planner could act on it? Is the connection between the condition and the recommendation clear? Would a reasonable person reading this actually change their plan? If not, either sharpen it or remove it. Rewrite with only actionable intelligence.

### Critic Round 6: Coherence and Confidence

Read the report end to end. Do the forecasts contradict each other? Do the opportunities and threats align with the trajectory? Are confidence levels consistent (not high-confidence on a forecast that contradicts a high-confidence forecast elsewhere)? Does the report tell a coherent story? Produce the final clean version.

---

## Usage Notes

- The workflow orchestrator loads this skill at the start of a forecast-cycle
- The topic lens from human/business goals must be available before perspectives run
- Perspective agents each receive ONE lens section — they never see the other lenses
- The synthesis agent receives ALL perspective outputs + the synthesis prompts
- Critic agents work sequentially — each receives the output of the previous round
- Forecasting is observation-first: report reality, not desired outcomes
