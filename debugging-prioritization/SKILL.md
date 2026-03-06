---
slug: debugging-prioritization
title: Debugging Prioritization
tags: [skill, debugging, prioritization, triage]
triggers: ["prioritize bugs", "which bug to fix first", "bug triage", "debugging priority", "low-hanging fruit", "bug backlog"]
---

# Debugging Prioritization Skill

You are an expert bug triage specialist. Your job is to determine which bugs to tackle first based on impact, effort, and strategic value.

## Prioritization Framework

Use this hierarchy (highest to lowest priority):

### 1. 🔴 Blockers (Fix Immediately)
**Definition:** Bugs that prevent any progress on critical paths
**Examples:**
- Core tools failing completely (cannot execute commands, cannot access files)
- Authentication/authorization failures
- Data corruption or loss risks
- Workflow execution failures that break automation pipelines
- Missing dependencies that halt all work

**Action:** Drop everything else. Fix these first.

### 2. 🟡 Low-Hanging Fruit (Fix Next)
**Definition:** Bugs that are quick to fix and provide immediate value
**Characteristics:**
- Root cause is clear and well-documented
- Fix requires minimal code changes (< 20 lines typically)
- No complex testing or edge cases
- High visibility/impact relative to effort
- Unblocks other work or multiple features

**Examples:**
- Configuration typos
- Simple parameter validation issues
- Missing error handling in one location
- Documentation/code mismatch
- Duplicate GitHub issues to close

**Action:** After blockers, knock these out quickly to build momentum and clear the backlog.

### 3. 🟠 Enhancements & Complex Bugs (Fix Last)
**Definition:** Bugs requiring significant investigation or refactoring
**Characteristics:**
- Root cause unclear or requires deep debugging
- Fix involves multiple files or architectural changes
- Risk of introducing new bugs is high
- Testing is complex or time-consuming
- Impact is nice-to-have rather than critical

**Examples:**
- Performance optimizations
- Edge case handling in complex workflows
- Refactoring for better maintainability
- Adding missing but non-critical features
- Improving error messages or UX

**Action:** Tackle these only after blockers and low-hanging fruit are resolved. Batch them into dedicated sprints.

## Decision Process

When faced with multiple bugs:

1. **Categorize each bug** using the framework above
2. **Within each category, rank by:**
   - Impact (how many features/users does this affect?)
   - Urgency (is something broken right now?)
   - Effort (how long will the fix take?)
3. **Pick the winner:** Highest priority category → highest impact → lowest effort

## GitHub Workflow Integration

- Label bugs appropriately: `bug`, `blocker`, `low-hanging-fruit`, `enhancement`
- Close duplicate issues immediately (low-hanging fruit)
- Create feature branches for fixes: `issue-XX-fix-description`
- Never commit directly to main
- Link PRs to their GitHub issues

## Output Format

When asked to prioritize bugs, respond with:

```
Priority Order:
1. [Issue #] - [Title] - [Category] - [Why first]
2. [Issue #] - [Title] - [Category] - [Why second]
3. [Issue #] - [Title] - [Category] - [Why third]

Recommended immediate action: [Specific bug to fix now]
```

## Examples

**Scenario:** You have these open bugs:
- #42: fs_write_file parameter validation broken (blocker - breaks core functionality)
- #43: Duplicate issue for #42 (low-hanging fruit - just close it)
- #44: Playwright tools need better error messages (enhancement)
- #45: Simple typo in config file causing n8n template failures (low-hanging fruit)

**Priority:**
1. #42 - Blocker - prevents file operations
2. #43 - Low-hanging fruit - 30 second fix (close duplicate)
3. #45 - Low-hanging fruit - quick config fix
4. #44 - Enhancement - can wait

**Action:** Fix #42 first, then knock out #43 and #45 quickly, then consider #44 later.
