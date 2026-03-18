# How to Create a Map Pack (Step-by-Step Guide)

A **map pack** is a small folder of JSON files that Graph Explorer turns into an interactive graph you can click, filter, and explore. Think of it like giving the app a spreadsheet — but instead of rows and columns, you're describing **nodes** (the circles) and **edges** (the lines between them).

---

## What You'll End Up With

```
maps/
  my-map/
    dataset.json          ← Your graph data (required)
    presentation.json     ← Visual styling (optional)
  manifest.json           ← Register your map here
```

That's it — three files max, two of which are just JSON.

---

## Step 1: Create Your Map Folder

Inside the `maps/` directory, create a new folder with a short, lowercase name:

```
maps/my-map/
```

> Use hyphens instead of spaces (e.g., `sales-pipeline`, not `Sales Pipeline`).

---

## Step 2: Define Your Nodes (the circles on the graph)

Create a file called `dataset.json` inside your folder. Start with this template and replace the example data with your own:

```json
{
  "version": 1,
  "meta": {
    "id": "my-map",
    "name": "My First Map",
    "version": "1.0",
    "description": "What this map is about",
    "author": "Your Name"
  },
  "nodes": [],
  "edges": []
}
```

Now add nodes to the `"nodes"` array. Each node is one item on your graph — a service, a person, an issue, a team, anything.

### What a node looks like

```json
{
  "id": "api-gateway",
  "label": "API Gateway",
  "type": "Service",
  "layer": "Infrastructure",
  "status": "Active",
  "meta": {
    "desc": "Routes all incoming traffic",
    "tags": ["core", "networking"]
  }
}
```

### Node fields explained

| Field | Required? | What it does |
|-------|-----------|-------------|
| `id` | **Yes** | A unique identifier (no spaces). Used to wire up edges. |
| `label` | **Yes** | The display name people see on the graph. |
| `type` | Recommended | What kind of thing it is (e.g., "Service", "Database", "Person"). Shows in the inspector panel. |
| `layer` | Recommended | Which visual group it belongs to. Controls the node's color. |
| `status` | Optional | Current state (e.g., "Active", "At Risk"). Can change the node's border color. |
| `meta` | Optional | Extra details — description, tags, risks, links, or anything you want. |

### Common meta fields you can use

| Key | Type | What it does |
|-----|------|-------------|
| `desc` | text | A description shown in the detail panel. |
| `tags` | list | Rendered as small chips/badges. Good for filtering. |
| `risks` | list | Shown as a bullet list under "Risks". |
| `nextMoves` | list | Shown as a bullet list under "Next Moves". |
| `url` | text | A clickable link (needs presentation config to activate). |
| `owner` | text | Who owns this item. |

> You can add **any** custom keys to `meta`. They'll show up in the inspector as long as you tell the presentation file about them (covered in Step 4).

---

## Step 3: Connect Your Nodes with Edges (the lines on the graph)

Add edges to the `"edges"` array. Each edge draws a line from one node to another.

### What an edge looks like

```json
{ "from": "api-gateway", "to": "user-service", "type": "calls" }
```

### Edge fields explained

| Field | Required? | What it does |
|-------|-----------|-------------|
| `from` | **Yes** | The `id` of the source node. |
| `to` | **Yes** | The `id` of the target node. |
| `type` | Recommended | What kind of relationship it is (e.g., "calls", "data", "reports-to"). Controls line style. |

### Common edge types

| Type | Good for |
|------|----------|
| `calls` | Service A talks to Service B |
| `data` | Data flows from A to B |
| `reference` | A references or depends on B |
| `reports-to` | Org chart reporting lines |
| `has-issue` | A project has this issue |
| `matches-pattern` | This issue matches a pattern |

> You can invent any edge type you want — just style it in the presentation file.

---

## Step 4 (Optional): Style Your Map with presentation.json

If you skip this step, Graph Explorer uses sensible defaults from `maps/default-presentation.json`. But if you want to control colors, the inspector layout, or add filter buttons, create `maps/my-map/presentation.json`.

### Starter template

```json
{
  "title": "My First Map",
  "eyebrow": "Example v1.0",
  "description": "A short description shown below the title.",

  "layers": {
    "Infrastructure": "#818cf8",
    "Backend": "#38bdf8",
    "Data": "#22c55e"
  },

  "edgeStyles": {
    "calls": { "stroke": "rgba(56,189,248,0.42)", "dasharray": null },
    "data":  { "stroke": "rgba(52,211,153,0.45)", "dasharray": "5,3" }
  },

  "inspector": {
    "fields": [
      { "key": "type",   "label": "Type" },
      { "key": "layer",  "label": "Layer" },
      { "key": "status", "label": "Status" }
    ],
    "metaFields": [
      { "key": "desc", "label": "Description" }
    ],
    "metaArrays": [
      { "key": "risks",     "label": "Risks",      "emptyText": "None" },
      { "key": "nextMoves", "label": "Next Moves",  "emptyText": "None" }
    ],
    "metaChips": ["tags"],
    "metaLink": null
  },

  "focusModes": [
    { "id": "All", "label": "All" },
    { "id": "infra", "label": "Infrastructure Only", "filter": { "layers": ["Infrastructure"] } }
  ],

  "stats": [
    { "label": "Visible", "count": "visible" },
    { "label": "Services", "filter": { "layers": ["Infrastructure", "Backend"] } }
  ]
}
```

### What each section controls

| Section | What it does |
|---------|-------------|
| `title` | The big heading in the UI. |
| `eyebrow` | Small label above the title (e.g., "Audit v2.1"). |
| `description` | Paragraph below the title. |
| `layers` | **Map each layer name to a hex color.** Every `layer` value in your nodes needs a color here. |
| `edgeStyles` | **Map each edge type to a line style.** `dasharray: null` = solid line. `"5,3"` = dashed. `"2,4"` = dotted. |
| `statusStyles` | Map status values to colors (e.g., `"At Risk": "#ef4444"`). Changes the node border color. |
| `inspector.fields` | Which top-level node fields appear in the detail panel. |
| `inspector.metaFields` | Which `meta.*` keys show as label/value rows. |
| `inspector.metaArrays` | Which `meta.*` keys show as bullet lists. |
| `inspector.metaChips` | Which `meta.*` keys render as tag badges (usually `["tags"]`). |
| `inspector.metaLink` | Which `meta.*` key becomes a clickable URL. |
| `focusModes` | Filter buttons in the toolbar. Use `filter.layers` to filter by layer, or `filter.ids` to filter by specific node IDs. |
| `stats` | Number chips in the header. `"count": "visible"` shows the current count. `filter` counts matching nodes. |

---

## Step 5: Register Your Map in the Manifest

Open `maps/manifest.json` and add an entry for your map:

```json
{
  "id": "my-map",
  "title": "My First Map",
  "description": "Three services and a database"
}
```

Graph Explorer will automatically look for `maps/my-map/dataset.json` and `maps/my-map/presentation.json`.

---

## Step 6: Load and Test

1. Open Graph Explorer in your browser.
2. Your map should appear in the dropdown menu.
3. Select it — your graph renders.
4. **Check the browser console** (F12) for any validation errors or warnings.

### What the validator checks

**Errors (your map won't load):**
- Missing `nodes` or `edges` arrays
- Duplicate node IDs
- A node missing `id` or `label`
- An edge pointing to a node that doesn't exist

**Warnings (your map loads, but something's off):**
- Missing `version` field
- Nodes without `type` or `layer`
- Orphan nodes (not connected to anything)
- Self-loops (a node pointing to itself)
- Duplicate edges

---

## Pre-Flight Checklist

Before you call it done, verify:

- [ ] `dataset.json` has `"version": 1` at the top
- [ ] Every node has a unique `id` and a `label`
- [ ] Every edge's `from` and `to` match a real node `id`
- [ ] Every `layer` in your nodes has a color in `presentation.layers`
- [ ] Every edge `type` has a style in `presentation.edgeStyles`
- [ ] You added an entry to `maps/manifest.json`
- [ ] No console errors when you load the map
- [ ] Focus modes and stats reference layers/IDs that actually exist

---

## Tips for Success

- **Start small.** Get 5-10 nodes working first, then expand. It's much easier to debug a small graph.
- **Copy the starter template.** If `maps/sample-starter-template/` exists, duplicate it and rename — everything is pre-wired.
- **Keep layers to 3-6.** More than that gets visually noisy.
- **Use layers for grouping, types for meaning.** Layers control color. Types show in the inspector but don't change visuals.
- **Tags are free-form.** Use them for cross-cutting concerns that don't fit neatly into layers.

---

## Example Map Packs to Study

| Map | What it shows | Folder |
|-----|--------------|--------|
| Software Architecture | Services, databases, APIs | `maps/software-architecture/` |
| Ecosystem Issues | Projects + issues + patterns | `maps/ecosystem-issues/` |
| Org Chart | People, teams, departments | `maps/sample-org-chart/` |
