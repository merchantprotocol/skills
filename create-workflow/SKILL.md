---
slug: create-workflow
title: Create Workflow
tags: [skill, workflow, automation]
triggers:
  - "create a workflow"
  - "build a workflow"
  - "new workflow"
  - "make a workflow"
  - "design a workflow"
  - "workflow creation"
  - "set up an automation"
  - "fix a workflow"
  - "fix workflow"
  - "update a workflow"
  - "edit a workflow"
category: workflow
section: Standard Operating Procedures
locked: true
author: seed
---

# Create Workflow

You create workflows by planning them as a project PRD, writing the YAML, validating it renders in the editor, then deploying it. This process ensures no broken workflows ever reach the workflows directory.

**Follow every gate in order. Do NOT skip gates. Do NOT combine gates. Complete one gate fully, verify it, then move to the next.**

---

## ⛔ FORBIDDEN PATTERNS — READ THIS FIRST

These are real mistakes that have been made. If your output contains ANY of these patterns, the workflow WILL NOT RENDER in the UI. You must memorize these before writing a single line of YAML.

### NEVER use flat node structure

```yaml
# ❌ WRONG — flat nodes with no position/data wrapper. This WILL NOT RENDER.
nodes:
  - id: create_daily_project
    type: agent                    # WRONG type
    name: Daily Project Creator    # WRONG — no "name" field on nodes
    description: Creates a project # WRONG — no "description" field on nodes
    agent: agent                   # WRONG — not a real field
    input: |                       # WRONG — not a real field
      Do something...
    output_var: result             # WRONG — not a real field
    depends_on:                    # WRONG — not a real field
      - some_other_node

# ✅ CORRECT — every node uses type: workflow + position + data wrapper
nodes:
  - id: node-1742114600001
    type: workflow
    position:
      x: 400
      y: 100
    data:
      subtype: agent
      category: agent
      label: Daily Project Creator
      config:
        agentId: null
        agentName: "Daily Project Creator"
        additionalPrompt: "Create a project..."
        orchestratorInstructions: ""
        successCriteria: ""
        completionContract: ""
```

### NEVER use from/to on edges

```yaml
# ❌ WRONG — edges with from/to, no id
edges:
  - from: create_daily_project
    to: get_conversation_files
    condition: "len(files) > 10"

# ✅ CORRECT — edges with id, source, target
edges:
  - id: vueflow__edge-node-1742114600001-node-1742114600002
    source: node-1742114600001
    target: node-1742114600002
    sourceHandle: null
    targetHandle: null
    label: ""
    animated: true
```

### NEVER invent config fields

```yaml
# ❌ WRONG — these field names do not exist
config:
  systemPrompt: "You are a scanner..."     # NOT A REAL FIELD
  agentDescription: "Scans all projects"   # NOT A REAL FIELD
  tools: '["exec", "workspace_list"]'      # NOT A REAL FIELD
  async: true                              # NOT A REAL FIELD
  prompt: "Do something"                   # NOT A REAL FIELD
  instructions: "Step 1..."               # NOT A REAL FIELD
  input: "Analyze this"                    # NOT A REAL FIELD
  output_var: result                       # NOT A REAL FIELD

# ✅ CORRECT — the ONLY agent config fields that exist
config:
  agentId: null
  agentName: ""
  additionalPrompt: ""             # <-- injected directly into sub-agent's prompt
  orchestratorInstructions: ""     # <-- instructions for the orchestrator on how to deploy
  successCriteria: ""
  completionContract: ""
```

### NEVER use "output" as a category or subtype

```yaml
# ❌ WRONG
data:
  subtype: output        # NOT A REAL SUBTYPE
  category: output       # NOT A REAL CATEGORY

# ✅ CORRECT — use "response" subtype and "io" category
data:
  subtype: response
  category: io
```

### NEVER add top-level fields that don't exist

```yaml
# ❌ WRONG — these top-level fields are not part of the schema
slug: my-workflow           # NOT A REAL FIELD
trigger:                    # NOT A REAL FIELD — triggers are nodes
  type: schedule
  cron: "0 4 * * *"
config:                     # NOT A REAL FIELD at top level
  max_parallel: 4
metadata:                   # NOT A REAL FIELD
  author: Sulla

# ✅ CORRECT — the ONLY top-level fields
id: workflow-1742114600000
name: My Workflow
description: What it does
version: 1
enabled: true
createdAt: "2026-03-16T00:00:00.000Z"
updatedAt: "2026-03-16T00:00:00.000Z"
nodes: [...]
edges: [...]
viewport:
  x: 0
  y: 0
  zoom: 1
```

---

## CRITICAL RULES

These rules are NON-NEGOTIABLE. If you break any of them, the workflow will not render correctly in the UI and will not execute.

### Rule 1: Every node `type` MUST be the string `"workflow"`

The `type` field tells VueFlow which Vue component to render. The only registered component is `"workflow"`. Any other value (`trigger`, `agent`, `router`, `output`, etc.) will cause the node to **not render at all**.

### Rule 2: Every node MUST have all 4 required fields

```yaml
- id: node-{timestamp}        # REQUIRED — unique identifier
  type: workflow               # REQUIRED — always "workflow"
  position:                    # REQUIRED — canvas coordinates
    x: 400
    y: 100
  data:                        # REQUIRED — node content
    subtype: agent
    category: agent
    label: My Agent
    config: { ... }
```

### Rule 3: Node IDs use the pattern `node-{timestamp}`

Use `Date.now()` for the first node, then increment by 1 for each additional node.

### Rule 4: All config fields for the subtype MUST be present

Every node must include ALL config fields for its subtype, even if the values are empty strings or null. **Do NOT invent new config fields. Do NOT rename existing fields. Do NOT add fields that are not in the reference.**

---

## GATE 1: Create the Project PRD

**STOP. You must complete this gate before writing any YAML.**

Every workflow starts as a project. Create a project folder with a PRD document that defines what the workflow will do.

### Steps

1. **Determine the workflow slug** — lowercase, hyphenated name (e.g. `daily-learning-workflow`)

2. **Create the project folder**: `~/sulla/projects/{slug}/`

3. **Write `PROJECT.md`** with this structure:

```markdown
---
schemaversion: 1
slug: {slug}
title: "{Human-Readable Workflow Name}"
section: Projects
category: "Workflow"
tags:
  - project
  - workflow
locked: false
author: Sulla
created_at: {ISO date}
updated_at: {ISO date}
---

# {Human-Readable Workflow Name}

**Goal**: {What does this workflow accomplish?}

**Trigger**: {What event starts it?} (heartbeat / chat-app / calendar / sulla-desktop / workbench / chat-completions)

**Status**: planning

---

## Workflow Design

### Nodes (in execution order)

| # | Label | Subtype | Category | Purpose |
|---|-------|---------|----------|---------|
| 1 | {label} | {subtype} | {category} | {what it does} |
| 2 | ... | ... | ... | ... |

### Edges

| Source | Target | Handle | Notes |
|--------|--------|--------|-------|
| node 1 | node 2 | null | |
| ... | ... | ... | |

### Agent Details

For each agent node, describe:
- **Agent ID**: (existing agent slug or `null` for default)
- **Instructions**: What should this agent do? (becomes `additionalPrompt`)
- **Success criteria**: How do we know it's done?

---

## Checklist

- [ ] PROJECT.md written with full workflow design
- [ ] Workflow YAML written to `~/sulla/workflows/{slug}.yaml`
- [ ] Workflow validated — opens in editor and all nodes render
- [ ] PROJECT.md updated with status: active
```

### GATE 1 CHECKPOINT

- [ ] Project folder exists at `~/sulla/projects/{slug}/`
- [ ] PROJECT.md has the workflow design table with all nodes listed
- [ ] Every node has a valid subtype from the Node Reference below
- [ ] Every node's category matches its subtype (per the valid subtypes table)
- [ ] At least one trigger node exists
- [ ] Trigger type is identified
- [ ] Agent instructions are written for every agent node

**If ANY checkbox is not filled, STOP and complete it before proceeding.**

---

## GATE 2: Plan the Node Graph

**STOP. You must have completed Gate 1 before doing this.**

Translate the PRD design table into the exact node/edge plan. Output this plan in your response.

1. List every node with its ID (`node-{timestamp}`), subtype, category, and label
2. List every edge with source, target, and handles
3. Assign positions: triggers at y:100, increment y by ~150 per layer, x:400 center
4. For parallel branches, offset x (e.g. x:200, x:400, x:600)

### GATE 2 CHECKPOINT

- [ ] Every node has a `node-{timestamp}` ID
- [ ] Every node uses `type: workflow`
- [ ] Every edge listed with source, target, and handles
- [ ] At least one trigger node exists
- [ ] Every non-trigger node is reachable from a trigger
- [ ] Subtypes are ALL from the valid set (check the table below)
- [ ] Categories ALL match their subtype (check the table below)

**If ANY checkbox fails, fix the plan before proceeding.**

---

## GATE 3: Write the YAML

**STOP. You must have completed Gate 2 before doing this.**

Write the complete workflow YAML file directly to: `~/sulla/workflows/{slug}.yaml`

Use:
- The exact node configs from the Node Reference below (include ALL fields)
- The edge handles from the Handle Reference below
- The node IDs and positions from your Gate 2 plan
- Workflow ID: `workflow-{timestamp}` using current timestamp

### GATE 3 CHECKPOINT

Read the file back after writing it and verify:

- [ ] Every node has `type: workflow`
- [ ] Every node has `position` with numeric `x` and `y`
- [ ] Every node has `data` with `subtype`, `category`, `label`, `config`
- [ ] Every node ID follows `node-{timestamp}` pattern
- [ ] `data.subtype` is in the valid subtypes table
- [ ] `data.category` matches the subtype's category in the table
- [ ] Every agent node config has EXACTLY 6 fields: `agentId`, `agentName`, `additionalPrompt`, `orchestratorInstructions`, `successCriteria`, `completionContract`
- [ ] No agent node has `systemPrompt`, `prompt`, `instructions`, `tools`, `async`, `agentDescription`, `input`, `output_var`, `message`, `userMessage`, or `beforePrompt`
- [ ] Response nodes use `subtype: response` and `category: io` (NOT `output`)
- [ ] Every edge has `id`, `source`, `target` (NOT `from`/`to`)
- [ ] Top-level keys are ONLY: `id`, `name`, `description`, `version`, `enabled`, `createdAt`, `updatedAt`, `nodes`, `edges`, `viewport`
- [ ] `enabled: true` is set
- [ ] `triggerDescription` is filled in on trigger nodes

**If ANY checkbox fails, fix the YAML before proceeding.**

---

## GATE 4: Validate with Validator Tool

**STOP. You must have completed Gate 3 before doing this.**

The workflow file is already in `~/sulla/workflows/`. Now validate it using the `validate_sulla_workflow` tool. **Do NOT skip this step. Do NOT self-validate manually — use the tool.**

### Steps

1. **Run the validator tool:**
   ```
   validate_sulla_workflow(filePath: "~/sulla/workflows/{slug}.yaml")
   ```
   This checks ALL of the following automatically:
   - Top-level schema (only valid keys, required fields present)
   - Every node has `type: workflow`, valid `position`, `data` with `subtype`/`category`/`label`/`config`
   - Subtype is valid and category matches the subtype
   - Config has EXACTLY the required fields for each subtype — no extras, no missing
   - Forbidden agent config fields are rejected (systemPrompt, prompt, instructions, etc.)
   - Edges use `source`/`target` (not `from`/`to`), reference valid node IDs
   - At least one trigger node exists
   - All non-trigger nodes are reachable via edges

2. **Read the validation report.** If `valid: true` → proceed to Gate 5.

3. **If `valid: false`**: fix EVERY reported error in the YAML file, then **re-run `validate_sulla_workflow`**. Repeat until `valid: true`. Do NOT proceed with errors.

### GATE 4 CHECKPOINT

- [ ] `validate_sulla_workflow` returned `valid: true`
- [ ] Zero errors in the report (warnings are acceptable)

**If the validator reports ANY errors, fix them and re-validate. Do NOT proceed until `valid: true`.**

---

## GATE 5: Update Project & Notify

**STOP. You must have completed Gate 4 before doing this.**

1. **Update PROJECT.md** — change status to `active`, check off the completed items
2. **Tell the human** the workflow is ready:
   - Workflow name and file location
   - Number of nodes and edges
   - What triggers it
   - How to open it in the editor (click the workflow name in the Workflows pane)

### GATE 5 CHECKPOINT (FINAL)

- [ ] PROJECT.md updated with `Status: active`
- [ ] PROJECT.md checklist items marked complete
- [ ] Human notified with workflow details

---

## Workflow File Format

The ONLY valid top-level fields are: `id`, `name`, `description`, `version`, `enabled`, `createdAt`, `updatedAt`, `nodes`, `edges`, `viewport`. No other top-level fields are allowed.

```yaml
id: workflow-{timestamp}
name: Human-readable Workflow Name
description: What this workflow does
version: 1
enabled: true
createdAt: "2026-03-08T00:00:00.000Z"
updatedAt: "2026-03-08T00:00:00.000Z"
nodes:
  - id: node-{timestamp}
    type: workflow
    position:
      x: 400
      y: 100
    data:
      subtype: {node-subtype}
      category: {node-category}
      label: {display-label}
      config: {node-specific-config}
edges:
  - id: vueflow__edge-{sourceId}-{targetId}
    source: {sourceId}
    target: {targetId}
    sourceHandle: null
    targetHandle: null
    label: ""
    animated: true
viewport:
  x: 0
  y: 0
  zoom: 1
```

---

## Node Reference (Complete — Use These Exact Configs)

### Valid subtypes and categories (exhaustive list)

| subtype | category | Notes |
|---------|----------|-------|
| `calendar` | `trigger` | |
| `chat-app` | `trigger` | |
| `heartbeat` | `trigger` | |
| `sulla-desktop` | `trigger` | |
| `workbench` | `trigger` | |
| `chat-completions` | `trigger` | |
| `agent` | `agent` | |
| `tool-call` | `agent` | Calls a native tool directly (no orchestrator) |
| `integration-call` | `agent` | |
| `orchestrator-prompt` | `agent` | Sends a message to the orchestrating agent |
| `router` | `routing` | |
| `condition` | `routing` | |
| `wait` | `flow-control` | |
| `loop` | `flow-control` | |
| `parallel` | `flow-control` | |
| `merge` | `flow-control` | |
| `sub-workflow` | `flow-control` | |
| `user-input` | `io` | |
| `response` | `io` | NOT "output" |
| `transfer` | `io` | |

Any subtype or category NOT in this table is invalid and will break the workflow.

### Triggers (category: `trigger`)

Every workflow MUST have at least one trigger node.

**calendar:**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 100 }
  data:
    subtype: calendar
    category: trigger
    label: Calendar Trigger
    config:
      triggerType: calendar
      triggerDescription: ""
```

**chat-app:**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 100 }
  data:
    subtype: chat-app
    category: trigger
    label: Chat App Trigger
    config:
      triggerType: chat-app
      triggerDescription: ""
```

**heartbeat:**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 100 }
  data:
    subtype: heartbeat
    category: trigger
    label: Heartbeat Trigger
    config:
      triggerType: heartbeat
      triggerDescription: ""
```

**sulla-desktop:**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 100 }
  data:
    subtype: sulla-desktop
    category: trigger
    label: Desktop Trigger
    config:
      triggerType: sulla-desktop
      triggerDescription: ""
```

**workbench:**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 100 }
  data:
    subtype: workbench
    category: trigger
    label: Workbench Trigger
    config:
      triggerType: workbench
      triggerDescription: ""
```

**chat-completions:**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 100 }
  data:
    subtype: chat-completions
    category: trigger
    label: API Trigger
    config:
      triggerType: chat-completions
      triggerDescription: ""
```

**Important:** The `triggerDescription` field is used by the WorkflowRegistry to match incoming messages to workflows. Write a clear description of what kinds of messages/events should trigger this workflow. Example: `"When the user asks about project status or progress updates"`.

### Agent Nodes (category: `agent`)

**agent — ONLY these 6 config fields exist. No others.**

```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 250 }
  data:
    subtype: agent
    category: agent
    label: Agent
    config:
      agentId: null                    # Agent directory name or null for default
      agentName: ""                    # Display name
      additionalPrompt: ""             # Injected directly into the sub-agent's prompt
      orchestratorInstructions: ""     # Instructions for the orchestrator on how to deploy this sub-agent (supports {{variable}})
      successCriteria: ""              # Criteria the orchestrator uses to evaluate the sub-agent's response
      completionContract: ""           # Expected response structure — sub-agent wraps its deliverable in <completion-contract> tags
```

**These fields DO NOT EXIST on agent config: `systemPrompt`, `prompt`, `instructions`, `input`, `agentDescription`, `description`, `tools`, `async`, `output_var`, `depends_on`, `message`, `userMessage`, `beforePrompt`. Never use them.**

**tool-call:**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 250 }
  data:
    subtype: tool-call
    category: agent
    label: Tool Call
    config:
      toolName: ""                     # Native tool name (e.g. "docker_build", "redis_get", "fs_read")
      defaults: {}                     # Parameter values — keys are param names, values support {{variable}} syntax
      description: ""                  # User-facing description shown in the workflow editor
```

Tool call nodes execute directly — no orchestrator involvement. The tool runs with the resolved parameters and the result is injected into the orchestrator's conversation. Available tools are the same ones agents can be given access to (filesystem, docker, redis, github, etc.).

**integration-call:**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 250 }
  data:
    subtype: integration-call
    category: agent
    label: Integration Call
    config:
      integrationSlug: ""
      endpointName: ""
      accountId: default
      defaults: {}
      preCallDescription: ""
```

**orchestrator-prompt:**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 250 }
  data:
    subtype: orchestrator-prompt
    category: agent
    label: Orchestrator Prompt
    config:
      prompt: ""                # Message sent to the orchestrating agent (supports {{variable}})
```

### Routing Nodes (category: `routing`)

**router:**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 400 }
  data:
    subtype: router
    category: routing
    label: Router
    config:
      classificationPrompt: ""
      routes:
        - label: Route Name
          description: "When this condition is true"
        - label: Other Route
          description: "When other condition is true"
```

Edges from router use `sourceHandle: "route-0"`, `"route-1"`, etc. (matching the index in the routes array).

**condition:**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 400 }
  data:
    subtype: condition
    category: routing
    label: Condition
    config:
      rules:
        - field: fieldName
          operator: equals
          value: expectedValue
      combinator: and
```

Edges from condition use `sourceHandle: "condition-true"` or `"condition-false"`.

### Flow Control Nodes (category: `flow-control`)

**wait:**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 400 }
  data:
    subtype: wait
    category: flow-control
    label: Wait
    config:
      delayAmount: 5
      delayUnit: seconds
```

**loop (iterations mode — default):**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 400 }
  data:
    subtype: loop
    category: flow-control
    label: Loop
    config:
      loopMode: iterations
      maxIterations: 10
      condition: ""
      conditionMode: template
```

**loop (for-each mode — iterate over upstream Merge results):**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 400 }
  data:
    subtype: loop
    category: flow-control
    label: For Each
    config:
      loopMode: for-each
      maxIterations: 10
      condition: ""
      conditionMode: template
```
For-each loops iterate over items from an upstream Merge node's `merged` array. Body nodes can use `{{loop.currentItem.result}}`, `{{loop.currentItem.label}}`, `{{loop.currentItem.nodeId}}`, and `{{loop.index}}` template variables.

**loop (ask-orchestrator mode — orchestrator decides iteration count):**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 400 }
  data:
    subtype: loop
    category: flow-control
    label: Dynamic Loop
    config:
      loopMode: ask-orchestrator
      maxIterations: 10
      condition: ""
      conditionMode: template
      orchestratorPrompt: "How many additional items do you want to process? Respond with just a number."
```
Ask-orchestrator loops prompt the orchestrating agent to decide how many iterations to run. The orchestrator responds with a number, and the loop runs that many times. If the orchestrator responds with 0, the loop is skipped entirely.

**Loop nodes have 4 special handles. You MUST use them correctly:**

| Handle | Type | Position | Purpose |
|--------|------|----------|---------|
| `loop-entry` | targetHandle | top | Where the flow enters the loop |
| `loop-start` | sourceHandle | bottom | Connects to the first node inside the loop body |
| `loop-back` | targetHandle | right | Where the loop body feeds back into the loop |
| `loop-exit` | sourceHandle | left | Where the flow goes after the loop finishes |

**Loop wiring pattern:**
```yaml
edges:
  # Something feeds INTO the loop (via loop-entry)
  - id: vueflow__edge-node-UPSTREAM-node-LOOPloop-entry
    source: node-UPSTREAM
    target: node-LOOP
    sourceHandle: null
    targetHandle: loop-entry

  # Loop starts the body (via loop-start)
  - id: vueflow__edge-node-LOOPloop-start-node-BODY
    source: node-LOOP
    target: node-BODY
    sourceHandle: loop-start
    targetHandle: null

  # Body feeds back into loop (via loop-back)
  - id: vueflow__edge-node-BODY-node-LOOPloop-back
    source: node-BODY
    target: node-LOOP
    sourceHandle: null
    targetHandle: loop-back

  # Loop exits when done (via loop-exit)
  - id: vueflow__edge-node-LOOPloop-exit-node-DOWNSTREAM
    source: node-LOOP
    target: node-DOWNSTREAM
    sourceHandle: loop-exit
    targetHandle: null
```

**parallel:**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 500 }
  data:
    subtype: parallel
    category: flow-control
    label: Parallel
    config: {}
```

**merge:**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 700 }
  data:
    subtype: merge
    category: flow-control
    label: Merge
    config:
      strategy: wait-all
```

**sub-workflow:**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 500 }
  data:
    subtype: sub-workflow
    category: flow-control
    label: Sub-workflow
    config:
      workflowId: null
      awaitResponse: true
      agentId: null             # Optional: agent slug to orchestrate this sub-workflow (null = same as parent)
      orchestratorPrompt: ""    # Optional: instructions passed to the orchestrating agent (only used when agentId is set)
```

### I/O Nodes (category: `io`)

**user-input:**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 600 }
  data:
    subtype: user-input
    category: io
    label: User Input
    config:
      promptText: ""
```

**response:**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 600 }
  data:
    subtype: response
    category: io
    label: Response
    config:
      responseTemplate: ""
```

The subtype is `response`, NOT `respond`, NOT `output`. The category is `io`, NOT `output`.

**transfer:**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 600 }
  data:
    subtype: transfer
    category: io
    label: Transfer
    config:
      targetWorkflowId: null
```

---

## Edge Format

Edges connect nodes. The `id` follows the pattern `vueflow__edge-{sourceId}-{targetId}` (or `vueflow__edge-{sourceId}{handleId}-{targetId}` for multi-output nodes, or `vueflow__edge-{sourceId}-{targetId}{handleId}` for multi-input nodes like loop).

```yaml
edges:
  - id: vueflow__edge-node-123-node-456
    source: node-123
    target: node-456
    sourceHandle: null      # null for single-output nodes
    targetHandle: null      # null for single-input nodes
    label: ""
    animated: true
```

Edge fields are: `id`, `source`, `target`, `sourceHandle`, `targetHandle`, `label`, `animated`. NOT `from`, NOT `to`, NOT `condition`.

### Handle Reference (when sourceHandle/targetHandle is NOT null)

| Node Type | Handle Name | Handle Type | When To Use |
|-----------|-------------|-------------|-------------|
| router | `route-0`, `route-1`, `route-2`... | sourceHandle | Each route in the routes array |
| condition | `condition-true` | sourceHandle | True branch |
| condition | `condition-false` | sourceHandle | False branch |
| loop | `loop-entry` | targetHandle | Flow entering the loop |
| loop | `loop-start` | sourceHandle | Loop body starts here |
| loop | `loop-back` | targetHandle | Loop body returns here |
| loop | `loop-exit` | sourceHandle | Flow leaving the loop |

All other nodes use `sourceHandle: null` and `targetHandle: null`.

---

## How Workflows Execute (Agent-Orchestrated Playbook)

Workflows are NOT independent processes. The agent that activates a workflow becomes its **orchestrator**. The workflow is a playbook that feeds into the agent's conversation:

1. **Agent activates a workflow** via the `execute_workflow` tool. The workflow definition loads into the agent's state as `activeWorkflow`.
2. **The agent's graph loop processes workflow steps** automatically after each cycle:
   - **Trigger nodes** — auto-completed on activation (pass through the user message)
   - **Agent nodes (sub-agents)** — the orchestrator reads `orchestratorInstructions` and formulates the actual task message for the sub-agent. The sub-agent runs independently with its own persona/identity and responds within `<completion-contract>` XML tags. The orchestrator then evaluates the response against `successCriteria`.
   - **Router nodes** — the orchestrating agent receives a prompt with the route options and makes the decision using its full conversation history and persona.
   - **Condition nodes** — the orchestrating agent evaluates the condition with full context.
   - **Structural nodes** (wait, parallel, merge) — handled mechanically by the playbook.
   - **Response/IO nodes** — handled mechanically.
3. **The agent stays in control** — it can stop the workflow at any time, switch to a different workflow, or continue chatting normally.
4. **Recursive orchestration** — if a sub-agent node triggers its own workflow, that sub-agent becomes the orchestrator of the nested workflow.

### Implications for Workflow Design
- **Router nodes should have clear, descriptive route labels and descriptions** — the orchestrating agent reads these to make its decision.
- **Condition nodes should describe rules in natural language** — the agent evaluates them contextually, not mechanically.
- **Sub-agent nodes are truly independent** — they have their own persona, tools, and conversation. Use `additionalPrompt` for direct sub-agent context. Use `orchestratorInstructions` to guide the orchestrator on what to tell the sub-agent.
- **The orchestrating agent formulates and evaluates** — it reads your instructions, crafts the actual task message, and evaluates the sub-agent's response against `successCriteria`. Use `completionContract` to describe the expected response format.

### Agent Delegation with `<PROMPT>` Tags

A single agent node can dynamically spawn multiple sub-agents in parallel. During Phase 1 (task formulation), the orchestrator can respond with `<PROMPT>` tags instead of a single message:

```xml
<PROMPT agentId="observer" label="Financial Signals">Watch for financial signals in the daily logs...</PROMPT>
<PROMPT agentId="observer" label="Emotional Patterns">Watch for emotional patterns in the daily logs...</PROMPT>
<PROMPT agentId="researcher" label="Competitor Pricing">Look up competitor pricing data...</PROMPT>
```

**How it works:**
- Each `<PROMPT>` tag spawns one sub-agent in parallel (max 10)
- `agentId` attribute (optional) overrides the node's configured agent
- `label` attribute (optional) names the result for downstream reference
- All agents run concurrently via `Promise.allSettled()`
- Results are returned in merge-compatible batch format: `{ strategy: "delegation", merged: [{ nodeId, label, result }] }`
- If no `<PROMPT>` tags are present, the node behaves as a normal single-agent node

**Downstream handling:**
- **For-each loop** — iterates over each result individually (same as iterating over merge node output)
- **Regular node** — receives the full batch as context
- **Another agent** — receives all results as upstream context

**When to use delegation:**
- When the orchestrator needs to decide at runtime how many agents to spawn and what each should do
- When different agents need different instructions (unlike static parallel which gives each node its own fixed config)
- When you want dynamic fan-out from a single graph node without pre-wiring multiple branches

**Design pattern:** Use `orchestratorInstructions` to tell the orchestrator when to delegate. Example:
```
Analyze the observation categories needed. For each category, respond with a <PROMPT> tag containing specific instructions for that observer agent.
```

---

## Key Rules Summary

1. **Every node `type` MUST be `"workflow"`.** Never `"trigger"`, `"agent"`, `"output"`, or anything else.
2. **Every workflow needs at least one trigger.** Multiple triggers can point to the same first processing node.
3. **Node IDs must follow `node-{timestamp}` pattern** and be unique within the workflow.
4. **Edge IDs follow the pattern** `vueflow__edge-{source}-{target}` or `vueflow__edge-{source}{handle}-{target}` or `vueflow__edge-{source}-{target}{handle}`.
5. **The file is saved to** `~/sulla/workflows/{slug}.yaml` and is immediately live.
6. **Use `enabled: true`** to make the workflow active in the registry.
7. **Position nodes visually** — triggers at top (~y:100), processing below, response at bottom. Increment y by ~150 per layer.
8. **The `triggerDescription`** is critical for the WorkflowRegistry to match messages to workflows. Be specific and descriptive.
9. **Include ALL config fields** for each node subtype, even if values are empty strings or null.
10. **Response nodes** use `subtype: response` and `category: io`. Never `subtype: respond` or `category: output`.
11. **Agent config has EXACTLY 6 fields.** `agentId`, `agentName`, `additionalPrompt`, `orchestratorInstructions`, `successCriteria`, `completionContract`. No others.
12. **Edges use `source`/`target`.** Never `from`/`to`.
13. **Top-level keys are ONLY:** `id`, `name`, `description`, `version`, `enabled`, `createdAt`, `updatedAt`, `nodes`, `edges`, `viewport`. No `slug`, `trigger`, `config`, or `metadata`.

## Example: Simple Agent Workflow

```yaml
id: workflow-1772929641704
name: Simple Workbench Agent
description: Routes workbench messages through an agent and responds
version: 1
enabled: true
createdAt: "2026-03-08T00:00:00.000Z"
updatedAt: "2026-03-08T00:00:00.000Z"
nodes:
  - id: node-1001
    type: workflow
    position: { x: 400, y: 100 }
    data:
      subtype: workbench
      category: trigger
      label: Workbench Trigger
      config:
        triggerType: workbench
        triggerDescription: "When the user sends a message from the workbench"
  - id: node-1002
    type: workflow
    position: { x: 400, y: 250 }
    data:
      subtype: agent
      category: agent
      label: Primary Agent
      config:
        agentId: null
        agentName: ""
        additionalPrompt: "You are a helpful assistant."
        orchestratorInstructions: ""
        successCriteria: ""
        completionContract: ""
  - id: node-1003
    type: workflow
    position: { x: 400, y: 400 }
    data:
      subtype: response
      category: io
      label: Send Response
      config:
        responseTemplate: ""
edges:
  - id: vueflow__edge-node-1001-node-1002
    source: node-1001
    target: node-1002
    sourceHandle: null
    targetHandle: null
    label: ""
    animated: true
  - id: vueflow__edge-node-1002-node-1003
    source: node-1002
    target: node-1003
    sourceHandle: null
    targetHandle: null
    label: ""
    animated: true
viewport: { x: 0, y: 0, zoom: 1 }
```

## Tool Mapping

| Step | Tool | Notes |
|------|------|-------|
| Create project folder | `fs_mkdir` | `~/sulla/projects/{slug}/` |
| Write PROJECT.md | `fs_write_file` | PRD document |
| Check existing workflows | `fs_list_dir` | `~/sulla/workflows/` |
| Read existing workflow | `fs_read_file` | For reference or modification |
| Write workflow file | `fs_write_file` | `~/sulla/workflows/{slug}.yaml` |
| Validate workflow file | `validate_sulla_workflow` | Pass filePath, fix all errors, re-validate until valid |
| List available agents | `fs_list_dir` | `~/sulla/agents/` |
| Update PROJECT.md | `fs_write_file` | Mark status active after validation |
