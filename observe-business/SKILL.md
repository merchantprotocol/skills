---
slug: observe-business
title: Observe Business
tags: [skill, observation, business, identity]
triggers: ["observe business", "business observation"]
category: observation
author: sulla-desktop
---

# Observe Business

This skill defines the observation topics for the business domain. Each topic spawns a parallel observer agent. The observer workflow reads these topics and creates one `<PROMPT>` per topic.

---

## Observation Topics

### Topic: Revenue and Financial Health

Observe the current financial state of the business.

Focus on:
- Revenue signals — mentions of income, MRR, sales, client payments
- Expense patterns — tools, services, subscriptions, infrastructure costs
- Cash flow signals — tight, comfortable, growing, declining?
- Pricing discussions — are they adjusting prices, considering new tiers?
- Financial goals vs. reality — how does actual revenue compare to targets?

### Topic: Products and Services

Observe what the business offers and how it's evolving.

Focus on:
- Current products/services being actively worked on or delivered
- New products/features in development or discussion
- Products being deprecated, neglected, or abandoned
- Quality signals — bug reports, client complaints, satisfaction indicators
- Competitive positioning — how do they describe their offering vs. alternatives?

### Topic: Clients and Customers

Observe the business's relationship with its clients/customers.

Focus on:
- Active clients — who is paying, who is engaged, who is at risk?
- Client acquisition — new leads, outreach, sales conversations
- Client retention — renewals, churn signals, satisfaction
- Client communication patterns — frequent, sparse, reactive, proactive?
- Key accounts vs. small accounts — where is effort concentrated?

### Topic: Market Position and Strategy

Observe the business's strategic direction and market awareness.

Focus on:
- Target market — who are they trying to reach? Is this changing?
- Competitive landscape — mentions of competitors, market shifts
- Marketing efforts — content, ads, outreach, brand building
- Strategic decisions — partnerships, pivots, new markets, doubling down
- Growth strategy — organic, paid, partnership, product-led?

### Topic: Operations and Infrastructure

Observe how the business actually runs day to day.

Focus on:
- Tools and systems in use — what's the tech stack, what's manual?
- Workflow efficiency — smooth operations or constant firefighting?
- Automation level — what's automated, what should be?
- Infrastructure costs and maintenance burden
- Bottlenecks — where does work get stuck?

### Topic: Team and Resources

Observe the business's human and resource capacity.

Focus on:
- Team size and structure (solo, contractors, employees, partners)
- Skill gaps — what can't they do in-house?
- Hiring/outsourcing discussions
- Workload distribution — is one person doing everything?
- Resource constraints affecting growth

### Topic: Blog and Content Presence

Observe the business's published content and online presence.

Focus on:
- Blog posts — what topics are being published? How recent? What's the publishing cadence?
- Content quality and depth — thin posts or substantial pieces?
- SEO signals — what keywords are being targeted? Are posts ranking?
- Content gaps — topics the business should cover but hasn't
- Reader engagement signals — comments, shares, backlinks if observable
- Content pipeline — what's in draft, what's published, what's stale?

**Tool guidance:** Use browser tools to visit the business's blog/website. Use `file_search` to find any content drafts or blog-related project files in workspaces. Check for blog-production projects.

### Topic: Social Media Presence

Observe the business's social media activity and audience.

Focus on:
- Which platforms are active? (Twitter/X, LinkedIn, YouTube, Instagram, TikTok, etc.)
- Posting frequency and consistency on each platform
- Content themes — what are they talking about publicly?
- Audience engagement — followers, likes, comments, shares, growth trends
- Tone and brand voice — professional, casual, educational, promotional?
- Social media strategy signals — is this organic, paid, or both?
- Audience demographics and behavior if observable

**Tool guidance:** Use browser tools to visit social profiles. Check for social media projects or assets in workspaces. If social media integrations are connected, use those APIs for detailed analytics.

### Topic: Connected Integrations — All Available

Before examining specific integration categories below, run `integration_list` and `list_integration_accounts` to discover ALL connected integrations. For each connected integration not covered by a specific topic below, spawn an observation to understand what the integration is used for, its current state, and what it reveals about the business.

Focus on:
- What integrations are connected and what do they tell us about the business's operations?
- For each integration: current activity level, health, and what business function it serves
- Cross-integration patterns — how do the business's tools work together?
- Integration gaps — what tools SHOULD be connected based on the business's needs?

### Topic: Connected Integrations — Email Marketing

If email marketing integrations are connected (Beehiiv, Customer.io, Drip, Mailgun, Postmark, Resend), observe campaign health.

Focus on:
- Subscriber/audience growth trends
- Campaign performance — open rates, click rates, engagement
- Content themes — what topics are they emailing about?
- Sending frequency and consistency
- List health — bounces, unsubscribes, growth rate

**Tool guidance:** Use integration API endpoints to pull campaign stats, subscriber counts, and recent activity.

### Topic: Connected Integrations — Sales and Outreach

If sales integrations are connected (Instantly, Lemlist, Smartlead), observe outreach activity.

Focus on:
- Active campaigns — how many, what targeting, what messaging
- Response rates and lead quality
- Pipeline volume — leads in, leads converting
- Outreach frequency and consistency
- A/B testing signals — what messaging works?

### Topic: Connected Integrations — Automation

If n8n or other automation integrations are connected, observe workflow health.

Focus on:
- Active workflows — what's running, what's broken, what's idle
- Execution success rates — failures, retries, errors
- Data flow patterns — what integrations talk to each other?
- Automation coverage — what's automated vs. manual?
- Workflow complexity — are automations getting out of hand?

---

## Integration Discovery

Before starting observations, check which integrations are connected using `integration_list` and `list_integration_accounts`. Only observe integrations that have active credentials. Skip integration topics when no integration is connected for that category.

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
**Domain:** Business
**Date:** YYYY-MM-DD
**Sources:** [what was examined]

## Observations

### [Observation title]
**Priority:** 🔴/🟡/⚪
**Who:** [actor]
**What:** [specific finding with evidence]
**When:** [timestamp or date range]
**Signal:** [what this might mean for goals/planning]

---
```

---

## Curator-Added Topics

The observation curator may add additional topics below this line based on current goals.

<!-- CURATOR_TOPICS_START -->
<!-- CURATOR_TOPICS_END -->
