---
slug: discover-and-call-integrations
title: "Integrations — Discover, List, and Call Enabled Services & MCP Servers"
tags: [integrations, api, mcp, http, discovery, exec, enabled, available, connected, slack, github, gmail, tools, services]
triggers: ["integration", "integrations", "check integrations", "what integrations", "available integrations", "enabled integrations", "connected integrations", "list integrations", "discover integrations", "call integration", "use integration", "integration api", "mcp tool", "mcp server", "mcp tools", "what's enabled", "what's available", "what's connected", "send slack", "send email", "github issue", "what can I connect to", "what services"]
schemaversion: 1
created_at: 2026-03-18T00:00:00.000Z
updated_at: 2026-03-19T00:00:00.000Z
---

# Integrations — Discover, List, and Call Enabled Services & MCP Servers

> **NEVER use `import requests`** — the `requests` module is not installed. Always use `curl` piped to `python3` with stdlib modules only (`json`, `sys`).

## Quick Reference — Run These First

**What integrations are enabled?**
```bash
curl -s http://host.docker.internal:3000/v1/integrations | python3 -c "
import sys, json
data = json.load(sys.stdin)
print(f\"Enabled integrations: {len(data['integrations'])}\")
for i in data['integrations']:
    slug = i['slug']
    name = i.get('name', '')
    accounts = ', '.join(a['accountId'] for a in i.get('accounts', []))
    n = len(i.get('endpoints', []))
    extra = ''
    if slug == 'mcp':
        extra = f\" (serverUrl: {i.get('serverUrl', 'n/a')})\"
    print(f\"  {slug}: {name} (accounts: {accounts or 'default'}, {n} endpoints){extra}\")
"
```

**What MCP tools are available?**
```bash
curl -s http://host.docker.internal:3000/v1/integrations | python3 -c "
import sys, json
data = json.load(sys.stdin)
mcps = [i for i in data['integrations'] if i['slug'] == 'mcp']
if not mcps:
    print('No MCP servers connected')
else:
    for server in mcps:
        aid = server['accounts'][0]['accountId'] if server.get('accounts') else 'unknown'
        print(f\"MCP Server: {server['name']} (accountId: {aid})\")
        print(f\"  serverUrl: {server.get('serverUrl', 'n/a')}\")
        for ep in server.get('endpoints', []):
            schema = ep.get('inputSchema', {})
            props = schema.get('properties', {})
            req = set(schema.get('required', []))
            params = ', '.join(
                f\"{k}{'*' if k in req else ''}\"
                for k in props
            )
            print(f\"  - {ep['name']}: {ep['description']}\")
            if params:
                print(f'      params: {params}')
"
```

The HTTP API at `host.docker.internal:3000` is the source of truth for what's enabled and connected. It only returns integrations with configured accounts. Use the API first to see what's available, then optionally read the YAML files in `~/sulla/integrations/{slug}/` if you need additional examples or response documentation.

---

## How to Call Any Integration (Unified Pattern)

**Every** integration — YAML-based (Slack, GitHub, Gmail, etc.) and MCP servers — uses the exact same call pattern:

**URL pattern:**
```
POST http://host.docker.internal:3000/v1/integrations/{accountId}/{slug}/{endpoint}/call
```

- `accountId` — from the `accounts` array in the discovery response (e.g., `"default"`, `"local_launchpad"`)
- `slug` — the integration slug (e.g., `slack`, `github`, `mcp`)
- `endpoint` — the endpoint name from the `endpoints` array

**Request body:**
```json
{
  "params": { "param_name": "value" }
}
```

- `params` — parameters described in the endpoint's `inputSchema`. Properties marked in the `required` array must be provided.

The `inputSchema` on each endpoint is a standard JSON Schema object with `type`, `properties`, and optionally `required`. Use it to understand what parameters are expected, their types, enums, defaults, and descriptions.

### Examples

**Send a Slack Message:**
```bash
curl -s -X POST http://host.docker.internal:3000/v1/integrations/default/slack/send_message/call \
  -H "Content-Type: application/json" \
  -d '{"params": {"channel": "C01234ABCDE", "text": "Hello from the agent!"}}' \
  | python3 -c "import sys, json; print(json.dumps(json.load(sys.stdin), indent=2))"
```

**List GitHub Issues:**
```bash
curl -s -X POST http://host.docker.internal:3000/v1/integrations/default/github/issues-list/call \
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
curl -s -X POST http://host.docker.internal:3000/v1/integrations/local_launchpad/mcp/ping/call \
  -H "Content-Type: application/json" \
  -d '{"params": {}}' \
  | python3 -c "import sys, json; print(json.dumps(json.load(sys.stdin), indent=2))"
```

**Call an MCP Tool with Params:**
```bash
curl -s -X POST http://host.docker.internal:3000/v1/integrations/local_launchpad/mcp/team_invite_send/call \
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

## Discovering Full Endpoint Schemas (YAML integrations only)

The API response includes full `inputSchema` for every endpoint. If you need additional details like response docs or examples, read the YAML config files directly.

Integration configs live at `~/sulla/integrations/{slug}/`:
- **Auth config**: `{slug}.v{N}-auth.yaml` — base URL, auth type, scopes
- **Endpoint files**: `{name}.v{N}.yaml` — full param definitions, examples, response docs

```python
import yaml, os, glob

slug = "slack"
config_dir = os.path.expanduser(f"~/sulla/integrations/{slug}")

for f in sorted(glob.glob(f"{config_dir}/*.yaml")):
    if "-auth.yaml" not in f:
        with open(f) as fp:
            ep = yaml.safe_load(fp)
        name = ep.get("endpoint", {}).get("name", os.path.basename(f))
        desc = ep.get("endpoint", {}).get("description", "")
        print(f"{name}: {desc}")
```

---

## API Response Shape (v3)

All integrations — YAML and MCP — share the same top-level shape:

```json
{
  "success": true,
  "version": 3,
  "usage": {
    "call_method": "POST",
    "call_url": "http://host.docker.internal:3000/v1/integrations/{accountId}/{slug}/{endpoint}/call",
    "call_body": "{\"params\": {\"param_name\": \"value\"}}",
    "notes": "Use the first accountId from the accounts array for any integration, including MCP."
  },
  "integrations": [ ... ]
}
```

### Integration entry (all types)
```json
{
  "slug": "slack",
  "name": "slack-auth",
  "accounts": [
    { "accountId": "default", "label": "Work Slack", "active": true, "connected": true }
  ],
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

### MCP entry (same shape, with `serverUrl`)
```json
{
  "slug": "mcp",
  "name": "Local Launchpad",
  "accounts": [
    { "accountId": "local_launchpad", "label": "local_launchpad", "active": true, "connected": true }
  ],
  "serverUrl": "https://host.docker.internal/mcp",
  "endpoints": [
    {
      "name": "ping",
      "method": "POST",
      "description": "Health check — returns pong with a timestamp.",
      "inputSchema": {
        "type": "object",
        "properties": {},
        "required": []
      }
    },
    {
      "name": "team_get",
      "method": "POST",
      "description": "Get details about the authenticated team.",
      "inputSchema": {
        "type": "object",
        "properties": {},
        "required": []
      }
    }
  ]
}
```

Each MCP server appears as its own entry with `slug: "mcp"` and its own `accounts` array containing one account.

---

## Tips

- **Always discover first** — don't hardcode endpoint names; they may change as integrations are added or MCP servers reconnect
- **Unified call pattern** — the URL pattern `/{accountId}/{slug}/{endpoint}/call` works for all integrations, including MCP
- **Read `inputSchema`** — every endpoint includes a JSON Schema describing its parameters (types, enums, defaults, required fields)
- **MCP servers are listed individually** — each MCP server appears as its own entry with `slug: "mcp"` and its own `accounts` array
- **Use `raw: true`** in the request body to get the full API response instead of extracted items (YAML integrations only)
- **Paginated results** — check for `nextPageToken` in the response and pass it back as a param for the next page
- **Error handling** — check `result.success` before processing; errors include descriptive messages
- **Never use `import requests`** — it is not installed in the VM; use `curl` piped to `python3` with stdlib modules only
