---
schemaversion: 1
slug: create-integration-yaml
title: "Create Integration YAML Files"
section: "Standard Operating Procedures"
category: "Developer Tools"
tags:
  - skill
  - integration
  - yaml
  - api
  - configapi
  - rest
  - endpoints
  - authentication
order: 5
locked: true
author: seed
---

# Create Integration YAML Files

**Triggers**: Human says "create integration", "add API yaml", "new integration yaml", "build integration files", "add endpoints for", "create yaml for API", "integration config", "configapi".

Create YAML-defined API integration files that the ConfigApiClient system automatically discovers and makes available as callable endpoints — no code changes required.

---

## Why Use This System (Instead of Freehanding API Calls)

### The Problem with Freehand API Calls
When an AI model needs to call an external API, the naive approach is to construct `fetch`/`curl` calls from memory. This fails in predictable ways:

1. **Hallucinated endpoints** — Models fabricate URLs, params, and response shapes that don't exist
2. **Auth mistakes** — Wrong header names, missing tokens, incorrect OAuth flows
3. **No credential management** — API keys hardcoded in prompts or lost between sessions
4. **No discoverability** — Other agents/tools can't find or reuse API knowledge
5. **No pagination** — Manual offset tracking, missed cursor fields, incomplete data
6. **Drift** — API versions change, but freehand calls use stale knowledge

### What ConfigAPI Solves
The YAML integration system provides a **declarative, versionable, discoverable** API layer:

- **Auto-discovery**: Drop YAML files into `~/sulla/integrations/{slug}/` and they're instantly available — no code changes, no deploys, no restarts needed
- **Credential resolution**: Tokens resolve automatically: DB-stored credentials > environment variables > raw YAML values
- **Type-safe calls**: `configapi-call(slug, endpointName, params)` validates required params, interpolates paths, and builds URLs correctly
- **Pagination built-in**: Declare pagination config once, use `client.paginate()` to auto-walk all pages
- **Testable**: The API Test Panel (Postman-style UI) lets users test any endpoint interactively
- **Reusable**: Any agent, tool, or skill can call any integration — the YAML is the single source of truth
- **Version-tracked**: YAML files live in the user's `~/sulla/integrations/` directory, can be git-tracked, shared, and customized

### When NOT to Use This System
This system is designed for **standard REST/HTTP APIs**. It does NOT support:

- **WebSocket / real-time streams** — No persistent connections (use dedicated clients)
- **GraphQL with complex fragments** — Basic GraphQL POST works, but deep fragment/subscription patterns don't fit well (create a single `graphql-query` endpoint and document queries in notes)
- **Binary uploads** — `multipart/form-data` file uploads aren't supported (the client sends JSON bodies)
- **OAuth interactive flows** — The YAML declares OAuth config, but the actual browser redirect flow is handled by the OAuth system, not ConfigAPI
- **APIs requiring request signing** — AWS Signature V4, OAuth 1.0a HMAC signing, etc. require custom logic
- **SOAP/XML APIs** — JSON-only; no XML serialization
- **gRPC / Protocol Buffers** — HTTP/JSON only

For these cases, build a dedicated tool/client in TypeScript instead.

---

## Architecture Overview

```
~/sulla/integrations/
  {slug}/                          # Directory name = integration slug
    {slug}.v{N}-auth.yaml          # Auth config (exactly 1 per integration)
    {endpoint-name}.v{N}.yaml      # Endpoint files (1+ per integration)
    {endpoint-name}.v{N}.yaml
```

The system works like this:

1. **IntegrationConfigLoader** scans `~/sulla/integrations/` on startup
2. Each subdirectory becomes an integration (directory name = slug)
3. Files matching `*.v{N}-auth.yaml` are parsed as auth configs
4. Files matching `*.v{N}.yaml` (not auth) are parsed as endpoint configs
5. A **ConfigApiClient** is instantiated per integration
6. Agents call endpoints via IPC: `configapi-call(slug, endpointName, params)`

---

## Tool Mapping

| Step | Tool | Notes |
|------|------|-------|
| Check if integration exists | `fs_read_dir` | Look in `~/sulla/integrations/{slug}/` |
| Read API documentation | `web_fetch` | Fetch the provider's API docs to get accurate endpoints |
| Create integration directory | `fs_mkdir` | `~/sulla/integrations/{slug}/` |
| Create auth file | `fs_write` | `{slug}.v{N}-auth.yaml` |
| Create endpoint files | `fs_write` | `{endpoint-name}.v{N}.yaml` per endpoint |
| Test the endpoint | `configapi-call` | `configapi-call(slug, endpointName, params)` |
| Reload integrations | `configapi-reload` | Refresh after adding new files |

---

## File Naming Conventions

### Auth File
```
{slug}.v{version}-auth.yaml
```
- `{slug}` = integration directory name (e.g., `stripe`, `discord`, `youtube`)
- `{version}` = API version number (e.g., `1`, `2`, `3`, `10`, `2024`)
- Examples: `stripe.v1-auth.yaml`, `discord.v10-auth.yaml`, `klaviyo.v2024-auth.yaml`

### Endpoint Files
```
{endpoint-name}.v{version}.yaml
```
- `{endpoint-name}` = descriptive kebab-case name (e.g., `contacts-list`, `send-message`, `orders-create`)
- `{version}` = same version as auth file
- Examples: `contacts-list.v3.yaml`, `send-message.v10.yaml`, `chat-completions.v1.yaml`

### Naming Best Practices
- Use `{resource}-{action}` pattern: `contacts-list`, `orders-create`, `invoices-get`
- Common actions: `list`, `get`, `create`, `update`, `delete`, `search`
- Keep names short but descriptive
- Match the version number to the actual API version you're targeting

---

## Auth File Schema

The auth file declares the API provider, base URL, and authentication method.

### Bearer Token Auth (most common)
```yaml
# {slug}.v{N}-auth.yaml
api:
  name: {provider}-v{N}       # Unique identifier
  version: v{N}               # API version string
  provider: {slug}            # Provider name
  base_url: https://api.example.com/v{N}

auth:
  type: bearer
  client_secret: "${ENV_VAR_NAME}"
  # Resolves: DB credential > env var > raw value

  token_storage: local
  refresh_automatically: false
```

### API Key Auth (custom header)
```yaml
auth:
  type: apiKey
  header: X-Api-Key              # The header name to send the key in
  client_secret: "${ENV_VAR}"    # The key value (or env var reference)
```
Use this when the API uses a non-standard header like `X-Api-Key`, `Api-Token`, `xi-api-key`, `DD-API-KEY`, etc.

For APIs needing multiple custom headers, use `default_headers`:
```yaml
auth:
  type: apiKey
  header: DD-API-KEY
  client_secret: "${DATADOG_API_KEY}"

  default_headers:
    DD-APPLICATION-KEY: "${DATADOG_APP_KEY}"
```

### OAuth 2.0 Auth
```yaml
auth:
  type: oauth2
  flow: authorization_code
  client_id: "${CLIENT_ID_ENV}"
  client_secret: "${CLIENT_SECRET_ENV}"
  redirect_uris:
    - "http://localhost:3000/auth/callback"

  authorization_url: https://provider.com/oauth/authorize
  token_url: https://provider.com/oauth/token

  scopes:
    - read
    - write

  token_storage: local
  refresh_automatically: true
  token_expiry_buffer_seconds: 300
```

### Basic Auth
```yaml
auth:
  type: basic
  username: "${ACCOUNT_SID}"
  password: "${AUTH_TOKEN}"
```

### No Auth (local services, token-in-URL)
```yaml
api:
  base_url: https://api.telegram.org/bot${BOT_TOKEN}

auth:
  type: none
```

### API Key Fallback (query parameter)
Some APIs accept auth via query param as a fallback (e.g., YouTube `?key=`):
```yaml
api_key_fallback:
  enabled: true
  param_name: key
  value: "${YOUTUBE_API_KEY}"
```

---

## Endpoint File Schema

Each endpoint file defines one callable API endpoint.

### Complete Endpoint Template
```yaml
# {endpoint-name}.v{N}.yaml
# {Provider} API - {Description}

endpoint:
  name: {endpoint-name}           # Must be unique within the integration
  description: {What this endpoint does}
  path: /resource/{id}/subresource  # URL path (appended to base_url)
  method: GET                      # GET, POST, PUT, DELETE, PATCH
  auth: required                   # required, optional, none
  quota_cost: 1                    # API quota units consumed (informational)

path_params:                       # URL path parameters (for {placeholders})
  id:
    type: string
    required: true
    description: Resource ID

query_params:                      # URL query parameters
  limit:
    type: integer
    required: false
    default: 20
    max: 100
    description: Number of results per page

  status:
    type: string
    required: false
    enum: [active, archived, deleted]
    description: Filter by status

  q:
    type: string
    required: false
    description: Search query

pagination:                        # Optional: enables auto-pagination
  type: nextPageToken              # nextPageToken, offsetLimit, linkHeader
  next_token_path: nextPageToken   # JSON path to next page token in response
  items_path: items                # JSON path to results array

response:
  items_path: items                # JSON path to results array (for list endpoints)
  notes: |
    Each item:
    - id, name, email, created_at
    - status: active, archived
    - metadata: { key: value }
    Pagination: nextPageToken in response

examples:
  example_name:
    params:
      limit: 50
      status: active
```

### GET List Endpoint (most common pattern)
```yaml
endpoint:
  name: contacts-list
  description: List all contacts with filtering and pagination
  path: /contacts
  method: GET
  auth: required
  quota_cost: 1

query_params:
  limit:
    type: integer
    required: false
    default: 20
    max: 100

  offset:
    type: integer
    required: false
    default: 0

  email:
    type: string
    required: false
    description: Filter by email address

response:
  items_path: contacts
  notes: |
    Each contact:
    - id, firstName, lastName, email, phone
    - createdAt, updatedAt
    Pagination: offset + limit

examples:
  all_contacts:
    params:
      limit: 100
```

### GET Single Resource
```yaml
endpoint:
  name: contact-get
  description: Get a single contact by ID
  path: /contacts/{contact_id}
  method: GET
  auth: required
  quota_cost: 1

path_params:
  contact_id:
    type: string
    required: true
    description: Contact ID

response:
  notes: |
    Returns:
    - id, firstName, lastName, email, phone
    - company, title, address
    - tags[], customFields[]
    - createdAt, updatedAt

examples:
  get_contact:
    params:
      contact_id: "abc123"
```

### POST Create/Action Endpoint
```yaml
endpoint:
  name: messages-create
  description: Send a message
  path: /messages
  method: POST
  auth: required
  quota_cost: 1

response:
  notes: |
    POST body (sent via options.body):
    - to (string, required): Recipient identifier
    - body (string, required): Message content
    - subject (string): Optional subject line
    - attachments (array): [{ url, filename }]

    Returns:
    - id, status, createdAt
    - to, from, body

examples:
  send_message:
    params: {}
```

### POST Search Endpoint
```yaml
endpoint:
  name: search
  description: Search across resources with filters
  path: /search
  method: POST
  auth: required
  quota_cost: 1

response:
  notes: |
    POST body (sent via options.body):
    - query (string): Full-text search
    - filters (object): { field: value }
    - sort (array): [{ field, direction }]
    - limit (integer): Max results
    - cursor (string): Pagination cursor

    Returns:
    - results[]: { id, type, fields }
    - total, cursor

examples:
  search_all:
    params: {}
```

---

## Pagination Patterns

### Token-based (cursor) Pagination
```yaml
pagination:
  type: nextPageToken
  next_token_path: meta.next_cursor    # Where to find the next page token
  items_path: data                      # Where to find the results array
```

### Offset/Limit Pagination
```yaml
pagination:
  type: offsetLimit
  offset_key: offset
  limit_key: limit
  items_path: results
```

### When pagination config is absent
If you don't include a `pagination` block, the response is returned as-is. For most list endpoints, include at least `response.items_path` so the system knows where the array of results lives.

---

## Credential Resolution Order

When ConfigApiClient needs a credential, it checks in this order:

1. **Explicit option** — `options.token` or `options.apiKey` passed to the call
2. **IntegrationService DB** — Credentials the user saved via the Sulla UI (keyed by slug + property name)
3. **Environment variable** — `${ENV_VAR}` references in the YAML resolve to `process.env`
4. **Raw value** — Literal string in the YAML (avoid for secrets)

### Environment Variable Convention
Use `${PROVIDER_PROPERTY}` format:
- `${STRIPE_SECRET_KEY}`
- `${DISCORD_BOT_TOKEN}`
- `${OPENAI_API_KEY}`
- `${DATADOG_API_KEY}`

---

## Step-by-Step Workflow

### 1. Research the API
Before writing YAML, understand the API:
- Read the official API documentation
- Identify the base URL and API version
- Determine the auth method (Bearer, API key, OAuth, Basic)
- List the most useful endpoints (prioritize list/get/search/create)
- Note pagination patterns and response shapes

### 2. Create the Integration Directory
```
~/sulla/integrations/{slug}/
```
The slug should match the integration ID in the native catalog if one exists. Use lowercase kebab-case: `meta-ads`, `google-meet`, `zoho-crm`.

### 3. Write the Auth File First
Start with `{slug}.v{N}-auth.yaml`. This defines the base URL and auth — everything else builds on it.

### 4. Write Endpoint Files
Create one file per endpoint. Start with the most commonly used endpoints:
- **List/search** endpoints (GET) — usually the most useful
- **Get by ID** endpoints (GET) — for fetching details
- **Create** endpoints (POST) — for actions
- **Update/Delete** — if commonly needed

### 5. Test via API Test Panel
Use the Postman-style API Test Panel in Sulla to verify each endpoint works:
1. Select the integration from the dropdown
2. Choose an endpoint
3. Fill in required params
4. Click Send
5. Verify the response shape matches your `response.notes`

### 6. Iterate
If the response doesn't match, update the YAML. The system hot-reloads via `configapi-reload`.

---

## Common Patterns Reference

### Pagination Styles by Provider
| Pattern | Providers | Query Param | Response Field |
|---------|-----------|-------------|----------------|
| Cursor token | Stripe, Slack, Meta, Klaviyo | `starting_after`, `cursor`, `after` | `has_more`, `paging.cursors.after` |
| Page number | WooCommerce, Freshdesk | `page`, `page_size` | `total_pages`, `X-WP-TotalPages` header |
| Offset/limit | Pipedrive, ActiveCampaign, Mailchimp | `start`/`limit`, `offset`/`count` | `additional_data.pagination` |
| Next URL | HubSpot, Intercom | N/A (use returned URL) | `paging.next.after` |
| Link header | GitHub, Netlify | N/A | `Link` response header |

### Auth Patterns by Provider Type
| Auth Type | Common Providers | YAML `auth.type` |
|-----------|-----------------|-------------------|
| Bearer token (API key) | Stripe, OpenAI, Vercel, HubSpot | `bearer` |
| Custom header key | ElevenLabs (`xi-api-key`), Datadog (`DD-API-KEY`), Apollo (`X-Api-Key`) | `apiKey` |
| OAuth 2.0 | Google, Zoom, Xero, QuickBooks | `oauth2` |
| Basic auth | Twilio, Freshdesk, Mux | `basic` |
| Token in URL | Telegram Bot API | `none` (embed in `base_url`) |

---

## Quality Checklist

Before considering an integration complete, verify:

- [ ] Auth file has correct `base_url` (no trailing slash, correct version prefix)
- [ ] Auth type matches the provider's actual auth method
- [ ] Environment variable names follow `${PROVIDER_PROPERTY}` convention
- [ ] Each endpoint has a unique `name` within the integration
- [ ] Required `path_params` are marked `required: true`
- [ ] Required `query_params` are marked `required: true`
- [ ] `response.items_path` is set for list endpoints (e.g., `data`, `results`, `items`)
- [ ] `response.notes` documents the response shape (field names, types, nesting)
- [ ] POST endpoints document the request body structure in `response.notes`
- [ ] `examples` section has at least one example with realistic param values
- [ ] File naming follows `{name}.v{N}.yaml` pattern
- [ ] Version number in filename matches the actual API version

---

## Example: Complete Integration (Stripe)

### Auth: `stripe.v1-auth.yaml`
```yaml
api:
  name: stripe-v1
  version: v1
  provider: stripe
  base_url: https://api.stripe.com/v1

auth:
  type: bearer
  client_secret: "${STRIPE_SECRET_KEY}"

  token_storage: local
  refresh_automatically: false
```

### Endpoint: `customers-list.v1.yaml`
```yaml
endpoint:
  name: customers-list
  description: List customers with filtering and pagination
  path: /customers
  method: GET
  auth: required
  quota_cost: 1

query_params:
  limit:
    type: integer
    required: false
    default: 10
    max: 100

  starting_after:
    type: string
    required: false
    description: Cursor for next page (customer ID)

  email:
    type: string
    required: false
    description: Filter by exact email

  created:
    type: string
    required: false
    description: "Filter by creation date (e.g., created[gte]=1609459200)"

response:
  items_path: data
  notes: |
    Each customer:
    - id, object, name, email, phone
    - description, metadata
    - created (Unix timestamp)
    - default_source, invoice_settings
    - subscriptions: { data[], has_more }
    Pagination: has_more + last item ID as starting_after

examples:
  recent_customers:
    params:
      limit: 50
```

### Endpoint: `charges-create.v1.yaml`
```yaml
endpoint:
  name: charges-create
  description: Create a new charge
  path: /charges
  method: POST
  auth: required
  quota_cost: 1

response:
  notes: |
    POST body (sent via options.body):
    - amount (integer, required): Amount in cents
    - currency (string, required): ISO 4217 (e.g., "usd")
    - source (string): Payment source token
    - customer (string): Customer ID
    - description (string): Charge description
    - metadata (object): Key-value pairs

    Returns:
    - id, amount, currency, status (succeeded, pending, failed)
    - paid, refunded, captured
    - source, customer, receipt_url

examples:
  create_charge:
    params: {}
```

---

## Tips

- **Start with 3-5 endpoints** per integration — cover the most common use cases first, add more later
- **Prefer GET endpoints** — they're easier to test and more broadly useful
- **Document POST bodies in `response.notes`** — since the YAML schema doesn't have a `body_params` field, describe the expected JSON body in the notes section
- **Use comments liberally** — YAML supports `#` comments; explain non-obvious auth setups, rate limits, or quirks
- **Check the native catalog** — if the integration already exists in the native integration catalog, the slug should match the catalog `id`
- **Version pinning** — use the specific API version you're targeting, not "latest" — APIs change and your YAML should be stable
