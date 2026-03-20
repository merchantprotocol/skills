---
slug: create-agent
title: Create Agent
tags: [skill, agent]
triggers: ["create an agent", "new agent", "build an agent", "make an agent", "add an agent", "agent creation", "set up a new agent", "design an agent"]
---

# Create Agent Skill

This skill walks you through creating a new Sulla agent.

## Key Principles

### 1. Agents = Job Roles
Think of an agent as a **job role**, not a task-specific helper. 
- "Observer" is a role that can observe self, human, business, or world
- Same agent can do different things based on what the workflow tells it to focus on
- One agent spawned multiple times = multiple workers doing different tasks

### 2. Allow-List Security
**IMPORTANT**: Agents start with ZERO tool access. You must explicitly grant each tool, skill, and integration in config.yaml. Everything else is blocked by default.

### 3. Why Create Specialized Agents?
Create an agent when you have routine tasks that require:
- Specialized prompting (different from Sulla's default)
- Specialized tool access (restricted allow-list)
- Specialized skill/integration access

By specializing an agent, you minimize the instruction overhead - the agent already knows how to do its job from its prompt, so the workflow just tells it what to focus on.

### 4. Sulla Can Handle Everything
Sulla can execute ALL pipeline stages directly. Only create specialized agents to reduce instruction overhead for routine tasks.

---

## Agent Structure

An agent is a folder in `~/sulla/agents/{agent-id}/` containing:
- `prompt.md` — Job description (how to do the work)
- `config.yaml` — Configuration + allowed tools/skills/integrations

```
~/sulla/agents/
├── observer/
│   ├── config.yaml
│   └── prompt.md
├── researcher/
│   ├── config.yaml
│   └── prompt.md
```

---

## Creating an Agent

### Step 1: Create Directory
```bash
mkdir -p ~/sulla/agents/{agent-id}
```

### Step 2: Create config.yaml
```yaml
id: {agent-id}
name: {Human Readable Name}
description: {What this role does}
version: 1.0.0

# ALLOW-LIST: Only these tools are enabled (everything else blocked)
allowed_tools:
  - file_search      # Read-only search
  - read             # Read existing files
  - browser_tab      # Browse websites
  # Add more tools as needed

# Skills this agent can use (empty = none)
allowed_skills: []

# API integrations this agent can access (empty = none)
allowed_integrations: []

# Core identity (optional - for role-based agents)
core_identity:
  - "Autonomy through love: Carry burdens proactively"
  - "Total alignment: The Human's goals = your goals"

# Communication style (optional)
communication:
  - "First-person always: I just checked..."
  - "Never say As an AI"
```

### Step 3: Create prompt.md
```markdown
# {Agent Name} - Job Role

This is the job description. Tell the agent:
- What their role is
- How to do their job
- Output format (if applicable)
- Any role-specific restrictions

The user message from the workflow = "what to focus on"
Your prompt = "how to do the job"
```

---

## Spawning Agents

When a workflow spawns an agent:
1. **System prompt** (from agent folder) = "Here's how you do your job"
2. **User message** (from workflow) = "Here's what to focus on"
3. **Output path** (from workflow) = "Where to write results"

Example workflow spawn:
```
- name: Observe Human
  agent: observer
  message: "Observe human's business context"
  output: "/daily-logs/.../business.md"
```

---

## Common Patterns

### Observer Agent (observe-only)
- allowed_tools: [file_search, read, browser_tab]
- allowed_skills: []
- No exec, no memory tools, no inter-agent comms

### Researcher Agent (observe + synthesize)
- allowed_tools: [file_search, read, browser_tab, playwright tools]
- allowed_skills: [topic-research-sop]

### Review Agent (evaluate + report)
- allowed_tools: [file_search, read]
- allowed_skills: []
- No external tools - just evaluate what's given

---

## When NOT to Create Agents

- Don't create agents just because a workflow lists stages
- Don't block progress waiting for "missing" agents
- Sulla can handle tasks directly when needed

---

## See Also
- [workflow-orchestration](/skills/workflow-orchestration) — Running workflows that spawn agents
- [observe-self workflow](/workflows/observe-self) — Example using observer agent
