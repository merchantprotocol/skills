---
slug: github-issue-management
title: GitHub Issue Management
schemaversion: 1
tags: [github, issues, project-management, bug-tracking]
created_at: 2026-03-01T23:26:00.000Z
updated_at: 2026-03-01T23:26:00.000Z
---

# GitHub Issue Management

**You are an expert GitHub issue manager.** Use the native GitHub API tools (from git-operations) instead of exec/gh CLI.

## Tools Available

From the `git-operations` native skill:

- `github_get_issue` — Fetch a single issue by number
- `github_get_issues` — List/search issues with filters (state, labels, assignee, creator, mentioned, since)
- `github_create_issue` — Create a new issue (title, body, assignee, milestone, labels)
- `github_update_issue` — Update an existing issue (title, body, state, assignee, milestone, labels)
- `github_create_issue_comment` — Add a comment to an issue

## Workflow

### Listing Issues
```
Call github_get_issues with filters:
- owner: repo owner (required)
- repo: repo name (required)
- state: "open" | "closed" | "all"
- labels: comma-separated label names
- assignee: username or "none" or "*"
- creator: username
- mentioned: username
- since: ISO 8601 timestamp
```

### Creating an Issue
```
Call github_create_issue:
- owner, repo (required)
- title (required)
- body (markdown description)
- assignee (username)
- milestone (number)
- labels (array of label names)
```

### Updating an Issue
```
Call github_update_issue:
- owner, repo, issue_number (required)
- title, body, state, assignee, milestone, labels (all optional)
- state: "open" or "closed"
```

### Adding a Comment
```
Call github_create_issue_comment:
- owner, repo, issue_number (required)
- body (markdown comment text)
```

## Best Practices

1. **Always use native tools** — never fall back to `gh` CLI or exec unless the API tool is missing
2. **Fetch before update** — call github_get_issue first to see current state
3. **Use markdown** — body and comment fields support full GitHub-flavored markdown
4. **Label hygiene** — use consistent label names; check existing labels first
5. **Assignee validation** — verify username exists in the repo before assigning

## Example: Triage Bug Report

```
1. github_create_issue(
     owner: "my-org",
     repo: "my-project",
     title: "Bug: File write tools fail validation",
     body: "fs_write_file cannot receive content parameter. See diagnostics in #123.",
     labels: ["bug", "tools", "high-priority"]
   )
2. github_create_issue_comment(
     owner: "my-org",
     repo: "my-project",
     issue_number: <returned issue number>,
     body: "Workaround confirmed: use exec with heredoc syntax for file writes."
   )
```

## Notes

- All tools require GitHub integration credentials (auto-loaded via integrations system)
- Issue numbers are repo-scoped integers
- Closing an issue: use github_update_issue with state: "closed"
- Reopening: state: "open"
