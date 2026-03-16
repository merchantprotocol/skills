---
slug: debugging-methodology
title: Debugging Methodology
tags: [skill, debugging, troubleshooting, systematic]
triggers: ["debug a problem", "systematic debugging", "troubleshoot issue", "identify root cause", "debug workflow"]
category: debugging
author: Sulla
---

# Debugging Methodology

You are an expert debugger using a systematic approach to identify, isolate, and resolve issues.

## Phase 1: Problem Identification

1. **Gather symptoms**: What exactly is failing? Collect error messages, logs, and unexpected behaviors.
2. **Reproduce the issue**: Can you reliably trigger the problem? Document exact steps.
3. **Identify scope**: Is it isolated (one tool/feature) or systemic (affects multiple areas)?
4. **Check recent changes**: What changed since it last worked? (code, config, dependencies, environment)

## Phase 2: Hypothesis Generation

Generate multiple hypotheses ranked by likelihood:

| Hypothesis | Likelihood | Test Method |
|------------|------------|-------------|
| [Description] | High/Medium/Low | [How to verify] |

Prioritize hypotheses that are:
- Quick to test
- Have high impact if true
- Cover common failure modes first

## Phase 3: Systematic Testing

For each hypothesis (highest likelihood first):

1. **Design a test** that will confirm OR eliminate the hypothesis
2. **Execute the test** and capture results
3. **Analyze results**:
   - Confirmed? → Move to Phase 4
   - Eliminated? → Next hypothesis
   - Inconclusive? → Refine test or hypothesis

### Common Test Patterns

- **Isolation test**: Disable/enable components one by one
- **Substitution test**: Replace suspected component with known-good alternative
- **Minimal reproduction**: Strip down to simplest case that still fails
- **Comparison test**: Compare working vs. non-working environments
- **Log analysis**: Trace execution path through available logs

## Phase 4: Root Cause Analysis

Once you've identified the failing component:

1. **Trace the failure path**: Follow the execution from input to failure point
2. **Identify the root cause**: Not just "what failed" but "why it failed"
3. **Document the chain**: Input → Processing → Failure Point → Error Output

### Root Cause Categories

- **Configuration**: Wrong settings, missing config, environment mismatch
- **Code/Logic**: Bug, edge case, incorrect assumption
- **Data**: Invalid input, state corruption, missing data
- **Environment**: Missing dependency, version mismatch, resource exhaustion
- **Integration**: API change, contract violation, timing issue
- **Permissions**: Access denied, missing credentials, authorization failure

## Phase 5: Resolution

1. **Design the fix**: Address root cause, not just symptoms
2. **Consider side effects**: Will the fix break anything else?
3. **Implement incrementally**: Make one change at a time
4. **Verify the fix**: Confirm original issue is resolved
5. **Regression test**: Verify nothing else broke

## Phase 6: Documentation

Always document:
- **Problem**: What was failing
- **Root cause**: Why it was failing
- **Solution**: How it was fixed
- **Prevention**: How to prevent recurrence

For system-level bugs, create GitHub issues for tracking and update relevant project documentation with test results.

## Quick Reference: Low-Hanging Fruit

Check these common causes first:
1. ❓ Is the service/container running?
2. ❓ Are credentials valid and not expired?
3. ❓ Is the network/endpoint reachable?
4. ❓ Are required environment variables set?
5. ❓ Is there a typo in config/parameters?
6. ❓ Did a recent update change behavior?
7. ❓ Are permissions correct?
8. ❓ Is there enough disk/memory/quota?
