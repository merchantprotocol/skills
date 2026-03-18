---
slug: observe-world
title: Observe World
tags: [skill, observation, world, external]
triggers: ["observe world", "world observation"]
category: observation
author: sulla-desktop
---

# Observe World

This skill defines the observation topics for the world domain. Each topic spawns a parallel observer agent. The observer workflow reads these topics and creates one `<PROMPT>` per topic.

**Important:** World observation is focused through the topic lens extracted from human and business goals upstream. Each observer agent should filter its observations through those topic lens subjects — only observe conditions relevant to where we want to go.

---

## Observation Topics

### Topic: Economic Conditions

Observe the economic environment relevant to the topic lens subjects.

Focus on:
- Interest rates, inflation, and monetary policy signals
- Consumer spending trends in relevant markets
- Employment and hiring trends in relevant industries
- Economic indicators that could affect the human's goals (housing, tech, services)
- Recession/growth signals and timing

**Sources:** Financial news, economic data feeds, government reports via browser.

### Topic: Market and Niche Conditions

Observe the specific market(s) the business operates in.

Focus on:
- Market size and growth trajectory for relevant niches
- Customer demand signals — growing, stable, declining?
- Pricing trends in the market
- New entrants and competitive landscape shifts
- Market consolidation or fragmentation signals

**Sources:** Industry reports, competitor websites, product hunt, news via browser.

### Topic: Technology Landscape

Observe technology shifts relevant to the human's work and business.

Focus on:
- New tools, frameworks, or platforms gaining traction
- Technology deprecations or migrations affecting the stack
- AI/automation advances that could create opportunities or threats
- Open source developments in relevant domains
- Platform policy changes (cloud providers, app stores, APIs)

**Sources:** Tech news, GitHub trending, product launches, developer blogs via browser.

### Topic: Regulatory and Political

Observe regulatory and political conditions that could affect goals.

Focus on:
- New regulations or proposed legislation in relevant industries
- Tax policy changes affecting the business or personal finances
- Privacy/data regulation changes (GDPR, CCPA, etc.)
- Trade policy or international developments if relevant
- Government programs or incentives that could be leveraged

**Sources:** Government sites, regulatory news, legal analysis via browser.

### Topic: Social and Cultural Trends

Observe social/cultural shifts relevant to the human's goals and market.

Focus on:
- Audience behavior changes — how do target customers communicate, buy, decide?
- Cultural trends affecting demand for the business's offerings
- Workforce culture shifts (remote work, freelancing, AI adoption)
- Social media trends relevant to marketing or audience building
- Generational shifts in purchasing or technology adoption

**Sources:** Social media trends, cultural analysis, survey data via browser.

### Topic: Risk and Black Swan Signals

Observe potential disruptions, risks, or unexpected developments.

Focus on:
- Supply chain or infrastructure risks
- Cybersecurity threats relevant to the business's stack
- Natural disaster or climate risks for the human's location
- Industry-specific risks (platform dependency, regulatory crackdown)
- Weak signals of major disruptions (pandemic-scale, financial crisis-scale)

**Sources:** Risk analysis, security bulletins, news aggregators via browser.

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
**Domain:** World
**Date:** YYYY-MM-DD
**Topic Lens Subjects:** [list the subjects this was filtered through]
**Sources:** [URLs and sources consulted]

## Observations

### [Observation title]
**Priority:** 🔴/🟡/⚪
**What:** [specific finding with evidence and source]
**Relevance:** [which topic lens subject this relates to and why]
**Confidence:** [high/medium/low — how reliable is this data?]
**Signal:** [what this could mean for our goals]

---
```

---

## Curator-Added Topics

The observation curator may add additional topics below this line based on current goals.

<!-- CURATOR_TOPICS_START -->
<!-- CURATOR_TOPICS_END -->
