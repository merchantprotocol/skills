---
slug: workflow-orchestration
title: Workflow Orchestration
tags: [skill, workflow, orchestration]
triggers: ["run workflow", "execute workflow", "resume pipeline", "check workflow status", "workflow checkpoint"]
---

# Workflow Orchestration Skill

This skill covers executing, monitoring, and resuming Sulla native workflows.

## Key Principles

### 1. Sulla Can Handle All Stages Directly
Sulla (the main agent) can execute ALL workflow stages directly. Specialized agents are optional helpers—not blockers. Never stall a pipeline waiting for missing agents.

### 2. Use Workflow Slug, Not UUID
When calling `execute_workflow()`, use the **workflow slug** (e.g., `blog-production-pipeline`), NOT the full UUID (e.g., `workflow-1741000000000`).

```javascript
// ✅ Correct
execute_workflow({
  workflowId: "blog-production-pipeline",
  message: "Proceed to Writer Agent..."
})

// ❌ Wrong (will fail)
execute_workflow({
  workflowId: "workflow-1741000000000",
  message: "Proceed to Writer Agent..."
})
```

### 3. Checkpoint Resume Pattern
When a workflow stalls, don't restart from the beginning. Query the database to find where execution stopped, then resume from that checkpoint.

**Query workflow checkpoints:**
```sql
SELECT node_id, node_label, playbook_state, node_output, created_at 
FROM workflow_checkpoints 
WHERE workflow_name = 'Blog Production Pipeline'
ORDER BY id DESC LIMIT 10;
```

**Resume with full context:**
Pass the content brief path and article specs in the message:
```
"Resume pipeline: [Last completed node] finished. Proceed to [Next node] to draft '[Article Title]' article ([word count] words, [format]) using content brief at /path/to/brief.md"
```

### 4. HAND_BACK Contract Format
Every workflow node must return results using this structured format:

```markdown
--- COMPLETION CONTRACT ---
When you have finished your work, you MUST end your final message with the following structured hand-back block. This is required for the orchestrator to process your results.

HAND_BACK
Summary: [1-3 paragraph summary of what was accomplished, key decisions made, and any important context]
Artifact: [file path to the primary output artifact, or "none" if no file was created]
Needs user input: [yes/no — whether the user needs to review or approve before proceeding]
Suggested next action: [optional — what should happen next in the workflow]
--- END CONTRACT ---
```

### 5. Monitor via PostgreSQL
The `workflow_checkpoints` table tracks all execution progress:

| Column | Description |
|--------|-------------|
| id | Auto-increment primary key |
| execution_id | Unique execution UUID |
| workflow_id | Workflow identifier |
| workflow_name | Human-readable name |
| node_id | Current node UUID |
| node_label | Node name (e.g., "Keyword Research Agent") |
| node_subtype | Node category |
| sequence | Execution order number |
| playbook_state | JSON: status, definition, currentNode |
| node_output | JSON: artifacts, decisions |
| created_at | Timestamp |

## Workflow Execution Flow

1. **Load Workflow**: Use `execute_workflow(workflowId: "slug", message: "context")`
2. **Monitor Progress**: Query `workflow_checkpoints` table
3. **Handle Node Completion**: Validate output, approve/reject
4. **Resume or Continue**: Pass context to next node

## Common Issues

| Issue | Solution |
|-------|----------|
| execute_workflow fails | Use slug, not UUID |
| Workflow stalled | Query checkpoints, resume from last completed node |
| Missing node output | Check node_output JSON in checkpoints table |
| Pipeline blocked on missing agent | Sulla can handle directly—don't wait |

## Sulla-Native vs n8n

Sulla has TWO workflow systems:
- **Sulla Native** (this skill): YAML files in `~/sulla/workflows/`, use `execute_workflow()`
- **n8n**: Docker-based, use `create_workflow()`, `get_workflows()`, etc.

Choose the system that best fits your workflow needs.
