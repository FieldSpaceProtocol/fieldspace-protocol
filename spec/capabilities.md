---
layout: default
title: Capabilities
parent: Specification
nav_order: 2
---

# Capability Model
{: .no_toc }

**Version:** 0.1.0
{: .fs-5 .fw-300 }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Overview

Capabilities are the fundamental unit of channel functionality in FieldSpace. A capability is a declared feature that a channel exposes to clients and agents.

Channels **must** declare all capabilities in their manifest. Undeclared capabilities do not exist from the protocol's perspective.

---

## Capability Types

### `data`

Read-only content retrieval. Data capabilities expose collections or items that clients can fetch and display.

```json
{
  "id": "videos",
  "type": "data",
  "name": "Video Library",
  "endpoint": "/api/videos",
  "metadata": {
    "searchable": true,
    "sortable": true,
    "paginated": true
  },
  "schema": {
    "type": "array",
    "items": { "$ref": "#/schemas/video" }
  }
}
```

### `state`

Inspectable current condition. State capabilities expose the channel's current state that clients and agents can observe.

```json
{
  "id": "playback_state",
  "type": "state",
  "name": "Playback State",
  "endpoint": "/api/state/playback",
  "metadata": {
    "refresh_interval_seconds": 1
  },
  "schema": {
    "type": "object",
    "properties": {
      "status": { "enum": ["idle", "playing", "paused", "buffering"] },
      "position_seconds": { "type": "number" },
      "duration_seconds": { "type": "number" },
      "current_item_id": { "type": "string" }
    }
  }
}
```

### `action`

Invocable operation. Action capabilities define operations that can be triggered by users or agents.

```json
{
  "id": "play",
  "type": "action",
  "name": "Play",
  "endpoint": "/api/actions/play",
  "permissions": {
    "user": "allowed",
    "agent": "allowed"
  },
  "schema": {
    "input": {
      "type": "object",
      "properties": {
        "item_id": { "type": "string" },
        "start_position": { "type": "number", "default": 0 }
      },
      "required": ["item_id"]
    },
    "output": {
      "type": "object",
      "properties": {
        "success": { "type": "boolean" },
        "new_state": { "$ref": "#/schemas/playback_state" }
      }
    }
  }
}
```

### `control`

Stateful control surface. Control capabilities expose adjustable parameters with current values.

```json
{
  "id": "volume",
  "type": "control",
  "name": "Volume",
  "endpoint": "/api/controls/volume",
  "permissions": {
    "user": "allowed",
    "agent": "confirmation_required"
  },
  "schema": {
    "type": "object",
    "properties": {
      "value": { "type": "number", "minimum": 0, "maximum": 100 },
      "muted": { "type": "boolean" }
    }
  }
}
```

### `status`

Passive status indicator. Status capabilities expose read-only status information that may change over time.

```json
{
  "id": "connection_status",
  "type": "status",
  "name": "Connection Status",
  "endpoint": "/api/status/connection",
  "metadata": {
    "passive": true,
    "refresh_interval_seconds": 30
  },
  "schema": {
    "type": "object",
    "properties": {
      "connected": { "type": "boolean" },
      "latency_ms": { "type": "integer" },
      "last_sync": { "type": "string", "format": "date-time" }
    }
  }
}
```

---

## Capability Metadata

Metadata provides hints about capability behavior and characteristics.

| Field | Type | Description |
|-------|------|-------------|
| `searchable` | boolean | Capability supports search queries |
| `sortable` | boolean | Results can be sorted |
| `filterable` | boolean | Results can be filtered |
| `paginated` | boolean | Results are paginated |
| `passive` | boolean | Read-only, no user interaction expected |
| `agent_visible` | boolean | Visible to agents (default: true) |
| `agent_only` | boolean | Only accessible by agents, not shown in UI |
| `refresh_interval_seconds` | integer | Recommended polling interval |

---

## Agent Discovery and Reasoning

Agents discover capabilities by reading the channel manifest. The manifest provides sufficient information for agents to:

### 1. Understand What's Available

Agents enumerate capabilities by type to understand channel functionality:

```
Channel "com.example.media" offers:
- data: videos, playlists
- state: playback_state, queue
- action: play, pause, skip, search
- control: volume
```

### 2. Reason About Permissions

Agents check permissions before suggesting or invoking actions:

```
Action "play": agent=allowed → can invoke directly
Action "delete": agent=confirmation_required → must ask user
Action "purchase": agent=forbidden → cannot invoke
```

### 3. Understand Data Shapes

Agents use schemas to understand data structure:

```
videos.schema.items has: id, title, duration, thumbnail_url
→ Agent can describe videos meaningfully
```

### 4. Plan Multi-Step Operations

Agents combine capabilities to achieve goals:

```
User: "Play something relaxing"
Agent plan:
  1. GET /api/videos?mood=relaxing (data capability)
  2. Select appropriate item
  3. POST /api/actions/play (action capability)
  4. Observe /api/state/playback (state capability)
```

---

## Capability Namespacing

Standard capabilities use simple identifiers. Channel-specific capabilities should use namespaced identifiers:

```json
{
  "capabilities": [
    { "id": "play", "type": "action" },
    { "id": "pause", "type": "action" },
    { "id": "com.example.custom_feature", "type": "action" }
  ]
}
```

---

## Required vs Optional Capabilities

No specific capabilities are required by the protocol. However, channels **should** expose:

- At least one `data` or `state` capability (otherwise there's nothing to display)
- Appropriate `action` capabilities for any interactive features

Clients **must** gracefully handle channels with minimal capabilities.

---

*FieldSpace Protocol v0.1.0 — Capability Model*

