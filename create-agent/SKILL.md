---
slug: create-agent
title: Create Agent
tags: [skill, agent]
triggers: ["create an agent", "new agent", "build an agent", "make an agent", "add an agent", "agent creation", "set up a new agent", "design an agent"]
---

# Create Agent Skill

This skill walks you through creating a new Sulla agent step by step.

## Key Principles

### 1. Sulla Can Handle Everything Directly
**IMPORTANT**: Specialized agents are optional helpers—NOT requirements. Sulla (the main agent) can execute ALL 21 pipeline stages directly. Never stall a workflow waiting for a missing agent.

Only create specialized agents when:
- You need parallelization (multiple agents working simultaneously)
- A specific agent has unique tools/capabilities Sulla lacks
- You want to offload routine tasks

### 2. Agent Structure

An agent is a folder in `~/sulla/agents/{agent-id}/` containing:
- `prompt.md` — The agent's system prompt
- `config.yaml` — Agent configuration

### 3. Creating an Agent

1. Create the agent directory:
```bash
mkdir -p ~/sulla/agents/{agent-id}
```

2. Create `prompt.md` with:
- Role definition
- Core tasks
- Output format
- Personality/tone

3. Create `config.yaml`:
```yaml
id: {agent-id}
name: {Human Readable Name}
description: {What this agent does}
version: 1.0.0
```

## Agent Directory Structure

```
~/sulla/agents/
├── my-research-agent/
│   ├── config.yaml
│   └── prompt.md
├── my-review-agent/
│   ├── config.yaml
│   └── prompt.md
└── ...
```

## Common Agent Patterns

### Research Agents
- web-researcher
- data-analyst
- competitive-researcher

### Content Agents
- content-writer
- content-editor
- summarizer

### Review Agents
- code-review-agent
- quality-review-agent
- compliance-review-agent

### Automation Agents
- data-pipeline-agent
- notification-agent
- report-generator

## When NOT to Create Agents

- Don't create an agent just because a workflow lists many stages
- Don't block pipeline progress waiting for "missing" agents
- Sulla can handle tasks directly when needed

## See Also

- [workflow-orchestration](/skills/workflow-orchestration) — For running workflows
