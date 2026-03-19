---
slug: discover-and-call-integrations
title: "Tools — Discover, List, and Call Enabled Integrations & MCP Servers"
description: "REQUIRED for any task involving integrations, MCP servers, or external services. Provides the HTTP API endpoint and call pattern to discover what's enabled and execute integration/MCP tool calls. Load this skill BEFORE attempting to browse the filesystem or guess URLs."
tags: [integrations, api, mcp, http, discovery, exec, enabled, available, connected, slack, github, gmail, tools, services, launchpad]
triggers: ["integration", "integrations", "check integrations", "what integrations", "available integrations", "enabled integrations", "connected integrations", "list integrations", "discover integrations", "call integration", "use integration", "integration api", "mcp tool", "mcp server", "mcp tools", "what's enabled", "what's available", "what's connected", "send slack", "send email", "github issue", "what can I connect to", "what services", "connect to", "launchpad", "manage integration", "call mcp", "call tool", "ping mcp", "external service", "api call", "tools list", "list tools"]
schemaversion: 1
created_at: 2026-03-18T00:00:00.000Z
updated_at: 2026-03-19T00:00:00.000Z
---

# Tools — Discover, List, and Call Enabled Integrations & MCP Servers

> **CRITICAL**: Always use the HTTP API below to discover and call tools. Do NOT browse the filesystem, guess URLs, or improvise. The API is the single source of truth.

> **NEVER use `import requests`** — the `requests` module is not installed. Always use `curl` piped to `python3` with stdlib modules only (`json`, `sys`).

## Quick Reference — Run These First

**List all available tools:**
```bash
curl -s http://host.docker.internal:3000/v1/tools/list | python3 -c "
import sys, json
data = json.load(sys.stdin)
print(f\"Available tools: {len(data['tools'])}\")
for t in data['tools']:
    n = len(t.get('endpoints', []))
    print(f\"  [{t['accountId']}] {t['slug']}: {t['name']} — {n} endpoints\")
"
```

**Search for specific tools:**
```bash
curl -s 'http://host.docker.internal:3000/v1/tools/list?search=slack'
curl -s 'http://host.docker.internal:3000/v1/tools/list?search=ping'
curl -s 'http://host.docker.internal:3000/v1/tools/list?search=launchpad'
```

**List MCP server tools:**
```bash
curl -s 'http://host.docker.internal:3000/v1/tools/list?search=mcp' | python3 -c "
import sys, json
data = json.load(sys.stdin)
for t in data['tools']:
    if t['slug'] != 'mcp': continue
    print(f\"MCP Server: {t['name']} (accountId: {t['accountId']})\")
    for ep in t.get('endpoints', []):
        schema = ep.get('inputSchema', {})
        props = schema.get('properties', {})
        req = set(schema.get('required', []))
        params = ', '.join(f\"{k}{'*' if k in req else ''}\" for k in props)
        print(f\"  - {ep['name']}: {ep['description']}\")
        if params:
            print(f'      params: {params}')
"
```

The HTTP API at `host.docker.internal:3000` is the source of truth for what's enabled and connected. It only returns tools with configured accounts. Use the API first, then optionally read YAML files in `~/sulla/integrations/{slug}/` for additional examples or response documentation.

---

## How to Call Any Tool (Unified Pattern)

**Every** tool — YAML-based (Slack, GitHub, Gmail, etc.) and MCP servers — uses the exact same call pattern:

**URL pattern:**
```
POST http://host.docker.internal:3000/v1/tools/{accountId}/{slug}/{endpoint}/call
```

All three values come directly from the tool entry in the listing:
- `accountId` — the tool entry's `accountId` field (e.g., `"default"`, `"local_launchpad"`)
- `slug` — the tool entry's `slug` field (e.g., `slack`, `github`, `mcp`)
- `endpoint` — the endpoint `name` from the `endpoints` array

**Request body:**
```json
{
  "params": { "param_name": "value" }
}
```

- `params` — parameters described in the endpoint's `inputSchema`. Properties listed in the `required` array must be provided.

The `inputSchema` on each endpoint is a standard JSON Schema object with `type`, `properties`, and optionally `required`.

### Examples

**Send a Slack Message:**
```bash
curl -s -X POST http://host.docker.internal:3000/v1/tools/default/slack/send_message/call \
  -H "Content-Type: application/json" \
  -d '{"params": {"channel": "C01234ABCDE", "text": "Hello from the agent!"}}' \
  | python3 -c "import sys, json; print(json.dumps(json.load(sys.stdin), indent=2))"
```

**List GitHub Issues:**
```bash
curl -s -X POST http://host.docker.internal:3000/v1/tools/default/github/issues-list/call \
  -H "Content-Type: application/json" \
  -d '{"params": {"owner": "myorg", "repo": "myrepo", "state": "open"}}' \
  | python3 -c "
import sys, json
data = json.load(sys.stdin)
if data['success']:
    for item in data['result']:
        print(f\"#{item['number']}: {item['title']}\")
"
```

**Ping an MCP Server:**
```bash
curl -s -X POST http://host.docker.internal:3000/v1/tools/local_launchpad/mcp/ping/call \
  -H "Content-Type: application/json" \
  -d '{"params": {}}' \
  | python3 -c "import sys, json; print(json.dumps(json.load(sys.stdin), indent=2))"
```

**Call an MCP Tool with Params:**
```bash
curl -s -X POST http://host.docker.internal:3000/v1/tools/local_launchpad/mcp/team_invite_send/call \
  -H "Content-Type: application/json" \
  -d '{"params": {"emails": ["user@example.com"], "is_admin": false}}' \
  | python3 -c "import sys, json; print(json.dumps(json.load(sys.stdin), indent=2))"
```

### Response Formats

**YAML-based integration response:**
```json
{
  "success": true,
  "result": { ... }
}
```

**MCP tool response:**
```json
{
  "success": true,
  "result": {
    "success": true,
    "content": [ { "type": "text", "text": "..." } ],
    "isError": false,
    "raw": { ... }
  }
}
```

For MCP tools, the `content` array contains the tool's output. Most tools return `type: "text"` with a JSON string in the `text` field. Parse it with `json.loads()` if needed.

---

## API Response Shape (v4)

Each entry in the `tools` array represents one account — with its `accountId`, `slug`, and `endpoints` ready to use directly in the call URL.

```json
{
  "success": true,
  "version": 4,
  "usage": {
    "call_method": "POST",
    "call_url": "http://host.docker.internal:3000/v1/tools/{accountId}/{slug}/{endpoint}/call",
    "call_body": "{\"params\": {\"param_name\": \"value\"}}",
    "notes": "Each entry has a unique accountId + slug. Use them directly in the call URL."
  },
  "tools": [ ... ]
}
```

### YAML integration entry
```json
{
  "accountId": "default",
  "label": "Work Slack",
  "slug": "slack",
  "name": "slack-auth",
  "connected": true,
  "endpoints": [
    {
      "name": "send_message",
      "method": "POST",
      "description": "Send a message to a channel",
      "inputSchema": {
        "type": "object",
        "properties": {
          "channel": { "type": "string", "description": "Channel ID" },
          "text": { "type": "string", "description": "Message text" }
        },
        "required": ["channel", "text"]
      }
    }
  ]
}
```

### MCP server entry
```json
{
  "accountId": "local_launchpad",
  "label": "Local Launchpad",
  "slug": "mcp",
  "name": "Local Launchpad",
  "connected": true,
  "endpoints": [
    {
      "name": "ping",
      "method": "POST",
      "description": "Health check — returns pong with a timestamp.",
      "inputSchema": { "type": "object", "properties": {}, "required": [] }
    },
    {
      "name": "team_get",
      "method": "POST",
      "description": "Get details about the authenticated team.",
      "inputSchema": { "type": "object", "properties": {}, "required": [] }
    }
  ]
}
```

---

## Discovering Full Endpoint Schemas (YAML integrations only)

The API response includes full `inputSchema` for every endpoint. If you need additional details like response docs or examples, read the YAML config files directly.

Integration configs live at `~/sulla/integrations/{slug}/`:
- **Auth config**: `{slug}.v{N}-auth.yaml` — base URL, auth type, scopes
- **Endpoint files**: `{name}.v{N}.yaml` — full param definitions, examples, response docs

---

## Tips

- **Always discover first** — don't hardcode endpoint names; they may change as integrations are added or MCP servers reconnect
- **One entry per account** — if an integration has multiple accounts, each appears as its own entry with a unique `accountId`
- **Unified call pattern** — `/{accountId}/{slug}/{endpoint}/call` works for all tools, including MCP
- **Read `inputSchema`** — every endpoint includes a JSON Schema describing its parameters (types, enums, defaults, required fields)
- **Use `?search=`** — filter tools by name, slug, label, or endpoint description
- **Use `raw: true`** in the request body to get the full API response instead of extracted items (YAML integrations only)
- **Paginated results** — check for `nextPageToken` in the response and pass it back as a param for the next page
- **Error handling** — check `result.success` before processing; errors include descriptive messages
- **Never use `import requests`** — it is not installed in the VM; use `curl` piped to `python3` with stdlib modules only
