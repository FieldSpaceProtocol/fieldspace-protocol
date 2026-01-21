---
layout: default
title: Actions
parent: Specification
nav_order: 4
---

# Action Model
{: .no_toc }

**Version:** 0.1.0
{: .fs-5 .fw-300 }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Overview

Actions are explicit, named operations that can be invoked on a provider. Unlike data and state endpoints (which are read-only), actions cause changes.

---

## Action Declaration

Actions are declared as capabilities in the provider manifest:

```json
{
  "id": "play",
  "type": "action",
  "name": "Play",
  "description": "Start playback of a media item",
  "endpoint": "/api/actions/play",
  "permissions": {
    "user": "allowed",
    "agent": "allowed"
  },
  "side_effects": "local",
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
        "error": { "type": "string" }
      }
    }
  }
}
```

---

## Action Invocation

### Request Format

```http
POST /api/actions/play
Content-Type: application/json
Authorization: Bearer <token>

{
  "item_id": "video-123",
  "start_position": 0
}
```

### Response Format

**Success:**

```json
{
  "success": true,
  "result": {
    "new_state": {
      "playback": {
        "status": "playing",
        "current_item_id": "video-123"
      }
    }
  }
}
```

**Failure:**

```json
{
  "success": false,
  "error": {
    "code": "item_not_found",
    "message": "Video video-123 not found"
  }
}
```

---

## Side Effect Declarations

Providers **must** declare the side effect scope of each action:

| Value | Description | Example |
|-------|-------------|---------|
| `none` | No side effects (query-like action) | Search, calculate |
| `local` | Affects provider state only | Play, pause, seek |
| `external` | Affects external systems | Post to social media, send email |
| `destructive` | Irreversible changes | Delete, purchase |

```json
{
  "id": "delete_item",
  "type": "action",
  "side_effects": "destructive",
  "permissions": {
    "user": "confirmation_required",
    "agent": "forbidden"
  }
}
```

---

## Permission Model

Each action declares permissions for users and agents:

| Permission | Meaning |
|------------|---------|
| `allowed` | Can invoke without restriction |
| `confirmation_required` | Can invoke, but client must confirm with user |
| `forbidden` | Cannot invoke this action |

### Permission Examples

```json
{
  "actions": [
    {
      "id": "play",
      "permissions": { "user": "allowed", "agent": "allowed" }
    },
    {
      "id": "add_to_queue",
      "permissions": { "user": "allowed", "agent": "confirmation_required" }
    },
    {
      "id": "purchase",
      "permissions": { "user": "allowed", "agent": "forbidden" }
    },
    {
      "id": "delete",
      "permissions": { "user": "confirmation_required", "agent": "forbidden" }
    }
  ]
}
```

---

## Agent Visibility

Actions can be hidden from agents or made agent-only:

```json
{
  "id": "internal_sync",
  "type": "action",
  "metadata": {
    "agent_visible": false
  }
}
```

```json
{
  "id": "agent_summarize",
  "type": "action",
  "metadata": {
    "agent_only": true
  }
}
```

---

## Idempotency

Actions **should** be idempotent where semantically appropriate. Providers **may** require idempotency keys:

```http
POST /api/actions/add_to_queue
Idempotency-Key: 550e8400-e29b-41d4-a716-446655440000
Content-Type: application/json

{
  "item_id": "video-123"
}
```

Providers **must** return the same response for duplicate requests with the same idempotency key.

---

## Action Context

Clients **may** include context with action invocations:

```json
{
  "item_id": "video-123",
  "_context": {
    "invoked_by": "agent",
    "pane_id": "pane-abc-123",
    "space_id": "living-room",
    "timestamp": "2026-01-21T10:30:00Z"
  }
}
```

Providers **should** log context for debugging but **must not** require it.

---

## Batch Actions

Providers **may** support batch action invocation:

```http
POST /api/actions/batch
Content-Type: application/json

{
  "actions": [
    { "action": "add_to_queue", "params": { "item_id": "video-1" } },
    { "action": "add_to_queue", "params": { "item_id": "video-2" } }
  ]
}
```

Response:

```json
{
  "results": [
    { "success": true },
    { "success": true }
  ]
}
```

---

## Standard Actions

The following action identifiers are reserved for common operations:

| Action | Description |
|--------|-------------|
| `play` | Start playback |
| `pause` | Pause playback |
| `stop` | Stop playback |
| `seek` | Seek to position |
| `next` | Next item |
| `previous` | Previous item |
| `search` | Search content |
| `refresh` | Refresh data |

Providers **should** use these identifiers for matching functionality.

---

*FieldSpace Protocol v0.1.0 — Action Model*

