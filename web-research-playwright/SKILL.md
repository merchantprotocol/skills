---
slug: web-research-playwright
title: Web Research with Playwright Browser Tools
schemaversion: 1
tags: [playwright, browser, web-research, google-maps, scraping, exec-in-page, sulla-runtime]
created_at: 2026-03-30T00:00:00.000Z
updated_at: 2026-03-31T00:00:00.000Z
---

# Web Research with Playwright Browser Tools

**You are an expert web researcher using Sulla's browser automation tools.** This skill teaches you how to open websites, interact with pages, extract data, and handle complex UIs — using both DOM-based tools and the `exec_in_page` + `__sulla` runtime for maximum speed and reliability.

## Two Modes of Interaction

### Mode 1: Individual Tools (simple, one-shot operations)
Use these for single actions like opening a tab, clicking one link, or reading page content.

### Mode 2: `exec_in_page` + `__sulla` Helpers (multi-step, fast)
Use this for anything requiring 2+ steps. Write JavaScript that calls `window.__sulla` helpers — everything executes in one round-trip instead of multiple tool calls.

**Rule of thumb:** If your workflow needs click → wait → read, use `exec_in_page` with `__sulla.steps()`.

---

## Tool Reference

All tools are called via:
```
curl -s -X POST http://host.docker.internal:3000/v1/tools/internal/playwright/{tool_name}/call \
  -H "Content-Type: application/json" \
  -d '{ ... params ... }'
```

### Tab Management

| Tool | Purpose |
|------|---------|
| `browser_tab` | Open, navigate, or close tabs |
| `list_tabs` | List all open tabs with assetId, URL, title |

### Reading Pages

| Tool | Purpose |
|------|---------|
| `get_page_snapshot` | Interactive elements (handles) + reader content. Set `mode: "dehydrated"` for compressed DOM tree (~5k tokens) |
| `get_page_text` | Clean readable text content |
| `browse_page` | Read content, scroll, search within page |
| `synthesize_tabs` | Combine content from multiple tabs |

### Interacting with Pages

| Tool | Purpose |
|------|---------|
| `click_element` | Click by handle (`@link-*`, `@btn-*`) or CSS selector |
| `set_field` | Fill form fields. Set `submit: true` to press Enter |
| `press_key` | Press Enter, Escape, Tab, arrows |
| `click_at` | Click at pixel coordinates (CDP trusted events) |
| `type_at` | Click to focus + type text at coordinates |
| `move_mouse` | Hover at coordinates (triggers tooltips, menus) |

### Visual Tools

| Tool | Purpose |
|------|---------|
| `take_screenshot` | JPEG screenshot with coordinate grid. Set `annotate: true` for numbered element boxes |
| `exec_in_page` | Execute arbitrary JavaScript. Supports `screenshot`, `waitFor`, `waitForIdle`, `timeout` options |

---

## The `__sulla` Runtime Library

Every page has `window.__sulla` automatically injected with these helpers:

### DOM Queries
```js
__sulla.$('selector')       // → { el, text, tag, rect, visible, handle } or null
__sulla.$$('selector')      // → array of above
__sulla.closest(start, ancestor)
```

### Wait Functions (Promise-based, MutationObserver — no polling)
```js
__sulla.waitFor('selector', timeout?)     // wait for visible element (default 10s)
__sulla.waitForText('text', timeout?)     // wait for text to appear
__sulla.waitForGone('selector', timeout?) // wait for element to disappear
__sulla.waitForIdle(timeout?)             // wait for network + DOM to settle
```

### Interact
```js
__sulla.click('selector')         // click with fallback chain (handle → CSS → coords)
__sulla.fill('selector', 'value') // React/Vue compatible (native setter + events)
__sulla.select('selector', 'val') // select dropdown option by value or text
__sulla.submit('form')            // submit nearest form
__sulla.press('Enter')            // dispatch keyboard event
__sulla.hover('selector')         // trigger mouseenter/mouseover
```

### Scroll
```js
__sulla.scrollTo('selector')          // smooth scroll element into view
__sulla.scrollBy(500)                 // scroll down 500px
__sulla.scrollBy(-300, '.panel')      // scroll up inside a container
__sulla.scrollInfo()                  // → { top, height, viewportHeight, pct, atTop, atBottom }
__sulla.scrollInfo('.scroll-panel')   // scroll info for a specific container
```

### Extract Data
```js
__sulla.text('h1')              // innerText of first match
__sulla.table('.data-table')    // → [{ "Name": "Acme", "Price": "$10" }, ...]
__sulla.forms()                 // → all forms with fields, values, types
__sulla.attrs('a', 'href')      // → { href: "..." }
__sulla.rect('.element')        // → { x, y, width, height }
```

### Dehydrate (Compressed DOM — for planning actions)
```js
__sulla.dehydrate({ maxTokens: 3000 })
// → { tree: "nav\n  a @link-home \"Home\" {i}\n  ...", stats: { tokens, interactiveCount, ... } }
```

### Compose (chain operations in one round-trip)
```js
__sulla.steps([fn1, fn2, fn3])  // run sequentially, stop on error, return all results
__sulla.retry(fn, 3, 1000)     // retry up to 3 times with 1s delay
```

### Visual Debug
```js
__sulla.highlight('button')     // draw red overlay on all buttons
__sulla.clearHighlights()       // remove overlays
```

### Logging
Every `__sulla` call is logged to `__sulla.__log` with timing and success/failure. The enhanced `exec_in_page` tool returns these as `sullaLog` in the response.

---

## Core Workflows

### 1. Open a Website

```
curl -s -X POST http://host.docker.internal:3000/v1/tools/internal/playwright/browser_tab/call \
  -H "Content-Type: application/json" \
  -d '{"action":"upsert","assetType":"iframe","url":"https://example.com","assetId":"my-tab"}'
```

- `assetId` is your stable reference — reuse to navigate same tab to new URL
- Returns page state: title, URL, interactive elements, content
- Wait 3-5 seconds for the page + bridge to fully load before interacting

### 2. Understand the Page Structure

**Quick overview (dehydrated — ~5k tokens):**
```
curl -s -X POST http://host.docker.internal:3000/v1/tools/internal/playwright/get_page_snapshot/call \
  -H "Content-Type: application/json" \
  -d '{"assetId":"my-tab","mode":"dehydrated"}'
```

**Full content with handles:**
```
curl -s -X POST http://host.docker.internal:3000/v1/tools/internal/playwright/get_page_snapshot/call \
  -H "Content-Type: application/json" \
  -d '{"assetId":"my-tab"}'
```

Handle types: `@btn-*` (buttons), `@link-*` (links), `@field-*` (form fields), `@item-*` (clickable items)

### 3. Multi-Step Workflow (PREFERRED — fastest method)

Use `exec_in_page` with `__sulla.steps()` to run multiple operations in one call:

```
curl -s -X POST http://host.docker.internal:3000/v1/tools/internal/playwright/exec_in_page/call \
  -H "Content-Type: application/json" \
  -d '{
    "code": "return await __sulla.steps([\n  () => __sulla.fill(\"input[name=q]\", \"coffee shops portland\"),\n  () => __sulla.press(\"Enter\"),\n  () => __sulla.waitFor(\"[role=feed]\", 8000),\n  () => __sulla.text(\"[role=feed]\")\n]);",
    "assetId": "my-tab",
    "screenshot": true
  }'
```

This does fill + submit + wait + read in **one tool call** instead of four. The `screenshot: true` option captures a screenshot after execution.

### 4. Extract Structured Data

```
curl -s -X POST http://host.docker.internal:3000/v1/tools/internal/playwright/exec_in_page/call \
  -H "Content-Type: application/json" \
  -d '{
    "code": "return {\n  name: __sulla.text(\"h1\"),\n  phone: __sulla.text(\"a[href^=tel]\"),\n  address: __sulla.text(\".address\"),\n  hours: __sulla.text(\".hours\"),\n  rating: __sulla.text(\".rating\"),\n  services: __sulla.table(\".services-table\")\n}",
    "assetId": "my-tab"
  }'
```

### 5. Visual Interaction (for complex UIs, widgets, iframes)

When DOM selectors don't work (canvas, shadow DOM, third-party widgets):

**Take a screenshot to see the page:**
```
curl -s -X POST http://host.docker.internal:3000/v1/tools/internal/playwright/take_screenshot/call \
  -H "Content-Type: application/json" \
  -d '{"assetId":"my-tab","annotate":true}'
```
Returns: JPEG screenshot with coordinate grid + numbered element boxes with coordinates.

**Click at coordinates:**
```
curl -s -X POST http://host.docker.internal:3000/v1/tools/internal/playwright/click_at/call \
  -H "Content-Type: application/json" \
  -d '{"x":750,"y":400,"assetId":"my-tab"}'
```
Returns: screenshot showing cursor at click point.

**Type at coordinates (for inputs DOM tools can't reach):**
```
curl -s -X POST http://host.docker.internal:3000/v1/tools/internal/playwright/type_at/call \
  -H "Content-Type: application/json" \
  -d '{"x":750,"y":400,"text":"hello world","assetId":"my-tab"}'
```

**Hover to trigger tooltips/menus:**
```
curl -s -X POST http://host.docker.internal:3000/v1/tools/internal/playwright/move_mouse/call \
  -H "Content-Type: application/json" \
  -d '{"x":750,"y":400,"assetId":"my-tab"}'
```

### 6. Scroll Inside Any Container

Google Maps, social feeds, and dashboards have scrollable panels that aren't the main window:

```
curl -s -X POST http://host.docker.internal:3000/v1/tools/internal/playwright/exec_in_page/call \
  -H "Content-Type: application/json" \
  -d '{
    "code": "var panels = document.querySelectorAll(\"div\"); var target = null; for (var el of panels) { if (el.scrollHeight > el.clientHeight + 200 && el.clientHeight > 300) { target = el; break; } } if (target) { target.scrollBy(0, 500); return { scrolled: true, info: __sulla.scrollInfo() }; } return { scrolled: false };",
    "assetId": "my-tab"
  }'
```

Or use the simple version for main page scroll:
```
curl -s -X POST http://host.docker.internal:3000/v1/tools/internal/playwright/browse_page/call \
  -H "Content-Type: application/json" \
  -d '{"action":"scroll_down","assetId":"my-tab"}'
```

### 7. Execute Any JavaScript

`exec_in_page` is the escape hatch for anything the other tools can't do:

```
curl -s -X POST http://host.docker.internal:3000/v1/tools/internal/playwright/exec_in_page/call \
  -H "Content-Type: application/json" \
  -d '{
    "code": "return document.querySelectorAll(\"a[href]\").length",
    "assetId": "my-tab"
  }'
```

Enhanced response includes: `result`, `error`, `logs` (console output), `sullaLog` (helper call log), `timing`, `mutations`, `navigated`, `url`, `title`.

### 8. Close Tabs When Done

```
curl -s -X POST http://host.docker.internal:3000/v1/tools/internal/playwright/browser_tab/call \
  -H "Content-Type: application/json" \
  -d '{"action":"remove","assetId":"my-tab"}'
```

**Always close tabs when finished.** Open tabs consume memory.

---

## Google Maps Research — Full Example

### The Fast Way (3 calls)

**Call 1: Open Maps with search**
```
curl -s -X POST http://host.docker.internal:3000/v1/tools/internal/playwright/browser_tab/call \
  -H "Content-Type: application/json" \
  -d '{"action":"upsert","assetType":"iframe","url":"https://www.google.com/maps/search/auto+repair+coeur+d+alene+idaho","assetId":"gmaps"}'
```

**Call 2: Click first result + extract data**
```
curl -s -X POST http://host.docker.internal:3000/v1/tools/internal/playwright/exec_in_page/call \
  -H "Content-Type: application/json" \
  -d '{
    "code": "return await __sulla.steps([\n  () => __sulla.waitFor(\"a[aria-label]\", 8000),\n  () => {\n    var first = document.querySelector(\"a[aria-label]\");\n    if (first) first.click();\n    return { clicked: first ? first.getAttribute(\"aria-label\") : null };\n  },\n  () => __sulla.waitForIdle(5000),\n  () => {\n    var text = document.body.innerText;\n    var lines = text.split(\"\\n\").filter(l => l.trim());\n    var biz = {};\n    for (var i = 0; i < lines.length; i++) {\n      var l = lines[i].trim();\n      if (l.match(/^[A-Z].*[a-z]/) && !biz.name && l.length > 5 && l.length < 80) biz.name = l;\n      if (l.match(/^[4-5]\\.[0-9]/)) biz.rating = l;\n      if (l.match(/\\(\\d{3}\\) \\d{3}/)) biz.phone = l;\n      if (l.match(/Opens \\d/)) biz.hours = l;\n      if (l.match(/\\d+ .+ (Ave|St|Rd|Dr|Ln|Blvd)/)) biz.address = l;\n    }\n    return biz;\n  }\n]);",
    "assetId": "gmaps",
    "screenshot": true
  }'
```

**Call 3: Clean up**
```
curl -s -X POST http://host.docker.internal:3000/v1/tools/internal/playwright/browser_tab/call \
  -H "Content-Type: application/json" \
  -d '{"action":"remove","assetId":"gmaps"}'
```

### Scrolling the Maps Detail Panel

Google Maps detail panels are scrollable divs, not the main page:
```
curl -s -X POST http://host.docker.internal:3000/v1/tools/internal/playwright/exec_in_page/call \
  -H "Content-Type: application/json" \
  -d '{
    "code": "var divs = document.querySelectorAll(\"div\"); for (var el of divs) { if (el.scrollHeight > 2000 && el.clientHeight > 500 && el.getBoundingClientRect().left > 300) { el.scrollBy(0, 500); return \"scrolled\"; } } return \"no panel found\";",
    "assetId": "gmaps"
  }'
```

---

## When to Use Which Tool

| Situation | Use This |
|-----------|----------|
| Open/close/navigate tabs | `browser_tab` |
| Quick page overview for planning | `get_page_snapshot` with `mode: "dehydrated"` |
| Single click on a known handle | `click_element` |
| Single form field fill + submit | `set_field` with `submit: true` |
| Multi-step: search → click → wait → extract | `exec_in_page` + `__sulla.steps()` |
| Complex data extraction | `exec_in_page` + `__sulla.text/table/forms` |
| Third-party widget (chat bubble, booking popup) | `take_screenshot` + `click_at` at coordinates |
| Debug why something isn't working | `exec_in_page` — check `sullaLog` for timing + errors |
| Scroll inside a specific panel (not main page) | `exec_in_page` + `el.scrollBy()` |
| Read full page content | `browse_page` with `action: "read"` |

---

## `exec_in_page` Options Reference

```json
{
  "code":        "return __sulla.text('h1')",
  "assetId":     "my-tab",
  "screenshot":  true,
  "waitFor":     ".results",
  "waitForIdle": true,
  "timeout":     30000
}
```

| Option | Default | Purpose |
|--------|---------|---------|
| `code` | required | JavaScript to execute. Use `return` for the result. |
| `assetId` | active tab | Target tab |
| `screenshot` | false | Capture JPEG after execution |
| `waitFor` | none | CSS selector to wait for after code runs |
| `waitForIdle` | false | Wait for network + DOM to settle after code |
| `timeout` | 30000 | Max execution time in ms |

### Enhanced Response Fields

| Field | Description |
|-------|-------------|
| `result` | Return value of your code |
| `error` | Error message if code threw |
| `logs` | Console.log/warn/error output (up to 100 lines) |
| `sullaLog` | Every `__sulla` call logged with timing + ok/error |
| `timing` | Execution time in ms |
| `mutations` | DOM mutation count during execution |
| `navigated` | Did the URL change? |
| `url` | Current URL after execution |
| `title` | Current page title after execution |
| `screenshotBase64` | JPEG base64 if `screenshot: true` |

---

## Screenshot Tool Options

```json
{
  "assetId": "my-tab",
  "grid": true,
  "annotate": true
}
```

| Option | Default | Purpose |
|--------|---------|---------|
| `grid` | true | Coordinate grid overlay (200px labels) |
| `annotate` | false | Numbered bounding boxes on interactive elements |

Screenshots capture the **specific tab by assetId** — even if it's not the currently visible tab. Coordinates in the grid match `click_at`/`type_at` coordinates.

---

## Troubleshooting

### Element not found
- Run `get_page_snapshot` to see current handles
- Use `exec_in_page` to query: `return document.querySelectorAll('input').length`
- The element may be in a third-party iframe — use `click_at` with coordinates instead

### Bridge timeout / sullaBridge undefined
- The page is still loading. Wait a few seconds and retry.
- After navigation, the bridge reinjects automatically. The state machine handles this — commands auto-wait for the bridge to be ready.

### Search doesn't submit
- Use `__sulla.fill(selector, value)` + `__sulla.press('Enter')` in a `steps()` call
- Or use `set_field` with `submit: true`
- Google Maps search box selector: `input[name="q"]`

### Scroll doesn't work on a specific panel
- Main page scroll: `browse_page` with `action: "scroll_down"`
- Custom container: use `exec_in_page` to find the scrollable div and call `el.scrollBy(0, 500)`
- To find scrollable containers: query divs where `scrollHeight > clientHeight`

### Screenshot shows wrong tab
- Screenshots target the specific `assetId`. Each iframe has a `data-asset-id` attribute.
- Hidden tabs are temporarily made visible for the CDP capture, then restored.

### `__sulla` is undefined
- The page just loaded — wait 2-3 seconds for the bridge + runtime to inject
- Check with: `exec_in_page` → `return typeof window.__sulla`

### Complex UIs (chat widgets, booking popups, shadow DOM)
1. `take_screenshot` with `annotate: true` to see element coordinates
2. `click_at` at the coordinates to interact
3. `take_screenshot` again to see the result
4. `exec_in_page` to extract data from any visible content
