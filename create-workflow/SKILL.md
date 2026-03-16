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
category: workflow
section: Standard Operating Procedures
locked: true
author: seed
---

# Create Workflow

You create workflows by writing YAML files to `~/sulla/workflows/`. Each workflow is a directed graph of nodes connected by edges. Workflows are triggered by events (chat messages, heartbeats, calendar events, API calls) and execute a chain of agents, routing, flow control, and I/O nodes.

**Follow every gate in order. Do NOT skip gates. Do NOT combine gates. Complete one gate, verify it, then move to the next.**

---

## CRITICAL RULES — READ BEFORE DOING ANYTHING

These rules are NON-NEGOTIABLE. If you break any of them, the workflow will not render correctly in the UI and will not execute.

### Rule 1: Every node `type` MUST be the string `"workflow"`

```yaml
# CORRECT — always use type: workflow
- id: node-1001
  type: workflow        # <-- ALWAYS "workflow". NEVER anything else.
  data:
    subtype: agent      # <-- The actual node kind goes HERE, in data.subtype
    category: agent     # <-- The category goes HERE, in data.category

# WRONG — DO NOT DO THIS
- id: trigger-001
  type: trigger         # WRONG. This will break the UI.
- id: agent-001
  type: agent           # WRONG. This will break the UI.
- id: output-001
  type: output          # WRONG. This will break the UI.
```

The `type` field tells VueFlow which Vue component to render. The only registered component is `"workflow"`. Any other value will cause the node to not render at all.

### Rule 2: Node IDs use the pattern `node-{timestamp}`

```yaml
# CORRECT
- id: node-1772929759772

# WRONG
- id: trigger-001
- id: agent-001
- id: my-cool-node
```

Use `Date.now()` for the first node, then increment by 1 for each additional node.

### Rule 3: The node kind is determined by `data.subtype` and `data.category`

The `data.subtype` field is what the workflow engine uses to decide what a node does. The `data.category` field groups it visually. These must match the exact values listed in the Node Reference below.

### Rule 4: All config fields must be present

Every node must include ALL config fields for its subtype, even if the values are empty strings or null. Missing fields will cause runtime errors. Use the exact default configs shown in the Node Reference below.

---

## Workflow File Format

```yaml
id: workflow-{timestamp}
name: Human-readable Workflow Name
description: What this workflow does
version: 1
enabled: true
createdAt: 2026-03-08T00:00:00.000Z
updatedAt: 2026-03-08T00:00:00.000Z
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

**agent:**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 250 }
  data:
    subtype: agent
    category: agent
    label: Agent
    config:
      agentId: null
      agentName: ""
      additionalPrompt: ""
      userMessage: ""
      beforePrompt: ""
      successCriteria: ""
      completionContract: ""
```

- `agentId`: Agent directory name (e.g., `"code-researcher"`) or `null` for default agent
- `agentName`: Display name (e.g., `"Code Researcher"`)
- `additionalPrompt`: Extra instructions injected into the agent's prompt
- `userMessage`: Template string for what gets sent to the agent (supports `{{variable}}` syntax)
- `beforePrompt`: Instructions prepended before the main prompt
- `successCriteria`: Description of what "done" looks like for this agent
- `completionContract`: Formal contract the agent must satisfy before completing

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
      integrationSlug: ""
      endpointName: ""
      accountId: default
      defaults: {}
      preCallDescription: ""
```

- `integrationSlug`: Integration name (e.g., `"youtube"`, `"postmark"`)
- `endpointName`: Endpoint to call (e.g., `"search"`, `"email-send"`)
- `accountId`: Integration account ID (usually `"default"`)
- `defaults`: Default parameter values as key-value pairs
- `preCallDescription`: Description of what the tool call does (for orchestrator context)

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

**loop:**
```yaml
- id: node-{ts}
  type: workflow
  position: { x: 400, y: 400 }
  data:
    subtype: loop
    category: flow-control
    label: Loop
    config:
      maxIterations: 10
      condition: ""
      conditionMode: template
```

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

NOTE: The subtype is `response`, NOT `respond`. The category is `io`, NOT `output`.

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

## GATE 1: Gather Requirements

**STOP. You cannot proceed past this gate until you have answers to ALL of these questions.**

Ask the human (or determine from context):

| Question | Why It Matters |
|----------|---------------|
| **What should this workflow do?** | Defines the node graph |
| **What triggers it?** (desktop chat, API call, heartbeat, calendar, etc.) | Determines trigger node(s) |
| **Does it need routing/branching?** | Determines if router or condition nodes are needed |
| **Does it need specific agents?** | Determines agent node configs |
| **Does it need loops?** | Determines loop node usage |
| **Does it need parallel execution?** | Determines parallel/merge pattern |

### GATE 1 CHECKPOINT

- [ ] Clear description of what the workflow does
- [ ] Trigger type(s) identified
- [ ] Node graph mentally mapped (what connects to what)
- [ ] Agent IDs identified (if using specific agents)

**If ANY checkbox is not filled, STOP and ask the human.**

---

## GATE 2: Plan the Node Graph

**STOP. You must have completed Gate 1 before doing this.**

Before writing any YAML, plan the complete graph on paper (in your response):

1. List every node with its subtype, category, and label
2. List every edge showing source → target (with handles if applicable)
3. Assign node IDs using `node-{timestamp}` pattern (increment by 1 per node)
4. Assign positions: triggers at y:100, then increment y by ~150 per layer, x:400 center

### GATE 2 CHECKPOINT

- [ ] Every node listed with subtype, category, label, and ID
- [ ] Every edge listed with source, target, and handles
- [ ] At least one trigger node exists
- [ ] Every non-trigger node is reachable from a trigger
- [ ] Node IDs all follow `node-{timestamp}` pattern
- [ ] All node types use `type: workflow` (NOT `type: trigger`, `type: agent`, etc.)

**If ANY checkbox fails, fix the plan before proceeding.**

---

## GATE 3: Write the YAML

**STOP. You must have completed Gate 2 before doing this.**

Write the complete workflow YAML file using:
- The exact node configs from the Node Reference above (include ALL fields)
- The edge handles from the Handle Reference above
- The node IDs and positions from your Gate 2 plan

Generate the workflow ID: `workflow-{timestamp}` using current timestamp.

Write the file to: `~/sulla/workflows/{workflow-id}.yaml`

### GATE 3 CHECKPOINT

Read the file back after writing it and verify:

- [ ] Every node has `type: workflow` (NOT `type: trigger`, `type: agent`, `type: output`, or anything else)
- [ ] Every node ID follows `node-{timestamp}` pattern
- [ ] Every node has ALL config fields for its subtype (no missing fields)
- [ ] `data.subtype` matches one of: `calendar`, `chat-app`, `heartbeat`, `sulla-desktop`, `workbench`, `chat-completions`, `agent`, `tool-call`, `router`, `condition`, `wait`, `loop`, `parallel`, `merge`, `sub-workflow`, `user-input`, `response`, `transfer`
- [ ] `data.category` matches one of: `trigger`, `agent`, `routing`, `flow-control`, `io`
- [ ] Response nodes use `subtype: response` and `category: io` (NOT `subtype: respond` or `category: output`)
- [ ] Loop edges use the 4 special handles: `loop-entry`, `loop-start`, `loop-back`, `loop-exit`
- [ ] Router edges use `sourceHandle: route-0`, `route-1`, etc.
- [ ] Condition edges use `sourceHandle: condition-true` or `condition-false`
- [ ] `enabled: true` is set
- [ ] `triggerDescription` is filled in (not empty) on trigger nodes

**If ANY checkbox fails, fix the YAML before proceeding.**

---

## GATE 4: Verify

**STOP. You must have completed Gate 3 before doing this.**

1. Read the saved file back with `fs_read_file`
2. Confirm it parses as valid YAML
3. Confirm every node has `type: workflow`
4. Tell the human the workflow is ready and how to access it

### GATE 4 CHECKPOINT (FINAL)

- [ ] File exists at `~/sulla/workflows/{workflow-id}.yaml`
- [ ] File is valid YAML
- [ ] Every single node has `type: workflow`
- [ ] Human has been told the workflow name and how to find it

---

## Example: Simple Agent Workflow

```yaml
id: workflow-1772929641704
name: Simple Workbench Agent
description: Routes workbench messages through an agent and responds
version: 1
enabled: true
createdAt: 2026-03-08T00:00:00.000Z
updatedAt: 2026-03-08T00:00:00.000Z
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
        userMessage: ""
        beforePrompt: ""
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

## Example: Loop Pattern

```yaml
id: workflow-1772930000000
name: Retry Agent Loop
description: Runs an agent in a loop until task is complete
version: 1
enabled: true
createdAt: 2026-03-08T00:00:00.000Z
updatedAt: 2026-03-08T00:00:00.000Z
nodes:
  - id: node-3001
    type: workflow
    position: { x: 400, y: 100 }
    data:
      subtype: sulla-desktop
      category: trigger
      label: Desktop Trigger
      config:
        triggerType: sulla-desktop
        triggerDescription: "When the user asks for iterative work"
  - id: node-3002
    type: workflow
    position: { x: 400, y: 280 }
    data:
      subtype: loop
      category: flow-control
      label: Retry Loop
      config:
        maxIterations: 5
        condition: "continue until the task is completed successfully"
        conditionMode: template
  - id: node-3003
    type: workflow
    position: { x: 400, y: 450 }
    data:
      subtype: agent
      category: agent
      label: Worker Agent
      config:
        agentId: null
        agentName: ""
        additionalPrompt: "Attempt the task. Report clearly if you succeeded or failed."
        userMessage: ""
        beforePrompt: ""
        successCriteria: ""
        completionContract: ""
  - id: node-3004
    type: workflow
    position: { x: 250, y: 350 }
    data:
      subtype: response
      category: io
      label: Final Response
      config:
        responseTemplate: ""
edges:
  # Trigger feeds into loop entry
  - id: vueflow__edge-node-3001-node-3002loop-entry
    source: node-3001
    target: node-3002
    sourceHandle: null
    targetHandle: loop-entry
    label: ""
    animated: true
  # Loop starts the agent body
  - id: vueflow__edge-node-3002loop-start-node-3003
    source: node-3002
    target: node-3003
    sourceHandle: loop-start
    targetHandle: null
    label: ""
    animated: true
  # Agent feeds back into loop
  - id: vueflow__edge-node-3003-node-3002loop-back
    source: node-3003
    target: node-3002
    sourceHandle: null
    targetHandle: loop-back
    label: ""
    animated: true
  # Loop exits to response
  - id: vueflow__edge-node-3002loop-exit-node-3004
    source: node-3002
    target: node-3004
    sourceHandle: loop-exit
    targetHandle: null
    label: ""
    animated: true
viewport: { x: 0, y: 0, zoom: 1 }
```

## Example: Router with Parallel Branches

```yaml
id: workflow-1772930100000
name: Routed Parallel Processing
description: Routes messages, then processes in parallel branches
version: 1
enabled: true
createdAt: 2026-03-08T00:00:00.000Z
updatedAt: 2026-03-08T00:00:00.000Z
nodes:
  - id: node-4001
    type: workflow
    position: { x: 400, y: 80 }
    data:
      subtype: sulla-desktop
      category: trigger
      label: Desktop Trigger
      config:
        triggerType: sulla-desktop
        triggerDescription: "When the user asks for a multi-step analysis"
  - id: node-4002
    type: workflow
    position: { x: 400, y: 230 }
    data:
      subtype: agent
      category: agent
      label: Classifier Agent
      config:
        agentId: null
        agentName: ""
        additionalPrompt: "Analyze the user request and prepare it for routing."
        userMessage: ""
        beforePrompt: ""
        successCriteria: ""
        completionContract: ""
  - id: node-4003
    type: workflow
    position: { x: 400, y: 380 }
    data:
      subtype: router
      category: routing
      label: Task Router
      config:
        classificationPrompt: "Based on the user request, choose the appropriate processing path."
        routes:
          - label: Research Path
            description: "The user needs information gathered or researched"
          - label: Action Path
            description: "The user needs something executed or created"
  - id: node-4004
    type: workflow
    position: { x: 250, y: 530 }
    data:
      subtype: parallel
      category: flow-control
      label: Parallel Research
      config: {}
  - id: node-4005
    type: workflow
    position: { x: 150, y: 680 }
    data:
      subtype: agent
      category: agent
      label: Web Researcher
      config:
        agentId: null
        agentName: ""
        additionalPrompt: "Search the web for relevant information."
        userMessage: ""
        beforePrompt: ""
        successCriteria: ""
        completionContract: ""
  - id: node-4006
    type: workflow
    position: { x: 350, y: 680 }
    data:
      subtype: agent
      category: agent
      label: Memory Researcher
      config:
        agentId: null
        agentName: ""
        additionalPrompt: "Search long-term memory for relevant context."
        userMessage: ""
        beforePrompt: ""
        successCriteria: ""
        completionContract: ""
  - id: node-4007
    type: workflow
    position: { x: 250, y: 830 }
    data:
      subtype: merge
      category: flow-control
      label: Merge Results
      config:
        strategy: wait-all
  - id: node-4008
    type: workflow
    position: { x: 550, y: 530 }
    data:
      subtype: agent
      category: agent
      label: Executor Agent
      config:
        agentId: null
        agentName: ""
        additionalPrompt: "Execute the requested action step by step."
        userMessage: ""
        beforePrompt: ""
        successCriteria: ""
        completionContract: ""
  - id: node-4009
    type: workflow
    position: { x: 400, y: 980 }
    data:
      subtype: response
      category: io
      label: Final Response
      config:
        responseTemplate: ""
edges:
  - id: vueflow__edge-node-4001-node-4002
    source: node-4001
    target: node-4002
    sourceHandle: null
    targetHandle: null
    label: ""
    animated: true
  - id: vueflow__edge-node-4002-node-4003
    source: node-4002
    target: node-4003
    sourceHandle: null
    targetHandle: null
    label: ""
    animated: true
  # Router route-0 (Research) -> Parallel
  - id: vueflow__edge-node-4003route-0-node-4004
    source: node-4003
    target: node-4004
    sourceHandle: route-0
    targetHandle: null
    label: ""
    animated: true
  # Parallel -> two research agents
  - id: vueflow__edge-node-4004-node-4005
    source: node-4004
    target: node-4005
    sourceHandle: null
    targetHandle: null
    label: ""
    animated: true
  - id: vueflow__edge-node-4004-node-4006
    source: node-4004
    target: node-4006
    sourceHandle: null
    targetHandle: null
    label: ""
    animated: true
  # Both researchers -> Merge
  - id: vueflow__edge-node-4005-node-4007
    source: node-4005
    target: node-4007
    sourceHandle: null
    targetHandle: null
    label: ""
    animated: true
  - id: vueflow__edge-node-4006-node-4007
    source: node-4006
    target: node-4007
    sourceHandle: null
    targetHandle: null
    label: ""
    animated: true
  # Merge -> Final Response
  - id: vueflow__edge-node-4007-node-4009
    source: node-4007
    target: node-4009
    sourceHandle: null
    targetHandle: null
    label: ""
    animated: true
  # Router route-1 (Action) -> Executor
  - id: vueflow__edge-node-4003route-1-node-4008
    source: node-4003
    target: node-4008
    sourceHandle: route-1
    targetHandle: null
    label: ""
    animated: true
  # Executor -> Final Response
  - id: vueflow__edge-node-4008-node-4009
    source: node-4008
    target: node-4009
    sourceHandle: null
    targetHandle: null
    label: ""
    animated: true
viewport: { x: 0, y: 0, zoom: 1 }
```

---

## How Workflows Execute (Agent-Orchestrated Playbook)

Workflows are NOT independent processes. The agent that activates a workflow becomes its **orchestrator**. The workflow is a playbook that feeds into the agent's conversation:

1. **Agent activates a workflow** via the `execute_workflow` tool. The workflow definition loads into the agent's state as `activeWorkflow`.
2. **The agent's graph loop processes workflow steps** automatically after each cycle:
   - **Trigger nodes** — auto-completed on activation (pass through the user message)
   - **Agent nodes (sub-agents)** — spawn independent agent graphs with their own persona/identity. When complete, the sub-agent's result is injected back into the orchestrating agent's conversation as a message.
   - **Router nodes** — the orchestrating agent receives a prompt with the route options and makes the decision using its full conversation history and persona.
   - **Condition nodes** — the orchestrating agent evaluates the condition with full context.
   - **Structural nodes** (wait, parallel, merge) — handled mechanically by the playbook.
   - **Response/IO nodes** — handled mechanically.
3. **The agent stays in control** — it can stop the workflow at any time, switch to a different workflow, or continue chatting normally.
4. **Recursive orchestration** — if a sub-agent node triggers its own workflow, that sub-agent becomes the orchestrator of the nested workflow.

### Implications for Workflow Design
- **Router nodes should have clear, descriptive route labels and descriptions** — the orchestrating agent reads these to make its decision.
- **Condition nodes should describe rules in natural language** — the agent evaluates them contextually, not mechanically.
- **Sub-agent nodes are truly independent** — they have their own persona, tools, and conversation. Design their `additionalPrompt` to give them clear instructions.
- **The orchestrating agent sees everything** — sub-agent results, routing decisions, and all workflow context are part of its conversation history.

---

## Key Rules

1. **Every node `type` MUST be `"workflow"`.** Never `"trigger"`, `"agent"`, `"output"`, or anything else.
2. **Every workflow needs at least one trigger.** Multiple triggers can point to the same first processing node.
3. **Node IDs must follow `node-{timestamp}` pattern** and be unique within the workflow.
4. **Edge IDs follow the pattern** `vueflow__edge-{source}-{target}` or `vueflow__edge-{source}{handle}-{target}` or `vueflow__edge-{source}-{target}{handle}`.
5. **The file is saved to** `~/sulla/workflows/{workflow-id}.yaml` and is immediately live.
6. **Use `enabled: true`** to make the workflow active in the registry.
7. **Position nodes visually** — triggers at top (~y:100), processing below, response at bottom. Increment y by ~150 per layer.
8. **The `triggerDescription`** is critical for the WorkflowRegistry to match messages to workflows. Be specific and descriptive.
9. **Include ALL config fields** for each node subtype, even if values are empty strings or null.
10. **Response nodes** use `subtype: response` and `category: io`. Never `subtype: respond` or `category: output`.

## Tool Mapping

| Step | Tool | Notes |
|------|------|-------|
| Check existing workflows | `fs_list_dir` | `~/sulla/workflows/` |
| Read existing workflow | `fs_read_file` | For reference or modification |
| Write workflow file | `fs_write_file` | `~/sulla/workflows/{workflow-id}.yaml` |
| Verify workflow file | `fs_read_file` | Read back to confirm correctness |
| List available agents | `fs_list_dir` | `~/sulla/agents/` |
