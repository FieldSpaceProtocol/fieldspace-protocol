---
layout: default
title: Permissions
parent: Specification
nav_order: 6
---

# Permissions & Trust
{: .no_toc }

**Version:** 0.1.0
{: .fs-5 .fw-300 }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Overview

FieldSpace defines a permission model that governs what users and agents can do with channel capabilities. The model prioritizes consumer safety and predictable behavior.

---

## Actors

| Actor | Description |
|-------|-------------|
| **User** | Human interacting with the client |
| **Agent** | AI companion operating at protocol level |
| **Channel** | External service exposing capabilities |
| **Client** | Platform mediating all interactions |

---

## Permission Levels

### For Actions

| Level | Meaning |
|-------|---------|
| `allowed` | Actor can invoke without restriction |
| `confirmation_required` | Actor can invoke, but user must confirm |
| `forbidden` | Actor cannot invoke this action |

### Declaration

```json
{
  "id": "play",
  "type": "action",
  "permissions": {
    "user": "allowed",
    "agent": "allowed"
  }
}
```

```json
{
  "id": "delete",
  "type": "action",
  "permissions": {
    "user": "confirmation_required",
    "agent": "forbidden"
  }
}
```

---

## User Permissions

Users have the highest permission level. They can:

- Invoke any action marked `user: allowed`
- Confirm and invoke actions marked `user: confirmation_required`
- Override agent permissions per-channel
- Grant or revoke channel access

Users **cannot**:

- Invoke actions marked `user: forbidden` (rare, typically system-reserved)
- Bypass client-enforced safety constraints

---

## Agent Permissions

Agents operate under the **principle of least surprise**:

### Agents CAN

1. **Observe freely**: Read all channel state at any time
2. **Suggest freely**: Recommend actions to users
3. **Act with permission**: Invoke actions marked `agent: allowed`
4. **Request confirmation**: Trigger confirmation for `agent: confirmation_required`

### Agents CANNOT

1. Invoke actions marked `agent: forbidden`
2. Bypass channel-declared restrictions
3. Act without user awareness (actions are logged)
4. Access capabilities marked `agent_visible: false`

### Agent Permission Hierarchy

```
User grants agent access to channel
  └─ Channel declares action permissions
       └─ User may further restrict agent
            └─ Agent operates within intersection
```

---

## User Overrides

Users can restrict agent permissions beyond channel declarations:

```json
{
  "user_preferences": {
    "channel_overrides": {
      "com.example.home": {
        "agent_can_observe": true,
        "agent_can_act": false
      },
      "com.example.media": {
        "agent_can_observe": true,
        "agent_can_act": true,
        "agent_requires_confirmation": ["add_to_queue", "skip"]
      }
    }
  }
}
```

Users **cannot** grant agents more permissions than channels allow.

---

## Read-Only vs Control Channels

### Read-Only Channels

Expose only `data` and `state` capabilities:

```json
{
  "capabilities": [
    { "id": "feed", "type": "data" },
    { "id": "status", "type": "status" }
  ]
}
```

Agents can observe but not act.

### Control Channels

Expose `action` and `control` capabilities:

```json
{
  "capabilities": [
    { "id": "devices", "type": "data" },
    { "id": "device_state", "type": "state" },
    { "id": "turn_on", "type": "action" },
    { "id": "brightness", "type": "control" }
  ]
}
```

Agents can act within declared permissions.

---

## Trust Levels

Channels declare their trust level:

| Level | Description | Verification |
|-------|-------------|--------------|
| `unverified` | No verification | None |
| `verified` | Publisher identity verified | Registry check |
| `signed` | Manifest cryptographically signed | Signature validation |

```json
{
  "trust": {
    "level": "verified",
    "publisher": "Example Corp",
    "verified_at": "2026-01-15T00:00:00Z"
  }
}
```

### Client Behavior by Trust Level

| Trust Level | Default Behavior |
|-------------|------------------|
| `unverified` | Warn user; may require explicit approval |
| `verified` | Normal operation |
| `signed` | Full trust; may enable additional features |

---

## Contract Stability

Channels make stability guarantees:

```json
{
  "stability": {
    "api_version": "1.0",
    "deprecation_policy": "6_months",
    "breaking_change_notice": "30_days"
  }
}
```

### Stability Levels

| Level | Meaning |
|-------|---------|
| `stable` | No breaking changes without major version bump |
| `beta` | May change with minor versions |
| `experimental` | May change at any time |

---

## Security Constraints

### Channel Sandboxing

Channels are sandboxed:

- Cannot access other channels' data
- Cannot access client-side storage
- Cannot execute arbitrary code on client
- Web views run in restricted iframes

### Data Minimization

Channels **should**:

- Request only necessary permissions
- Expose only relevant state
- Not collect unnecessary user data

### Audit Logging

Clients **should** log:

- All action invocations
- Permission changes
- Authentication events

---

## Authentication

### Methods

| Method | Use Case |
|--------|----------|
| `none` | Public channels |
| `api_key` | Simple authentication |
| `bearer` | Token-based auth |
| `oauth2` | User-authorized access |

### OAuth 2.0 Flow

```json
{
  "authentication": {
    "required": true,
    "methods": ["oauth2"],
    "oauth2": {
      "authorization_url": "https://channel.example.com/oauth/authorize",
      "token_url": "https://channel.example.com/oauth/token",
      "scopes": {
        "read": "Read content",
        "write": "Modify content",
        "control": "Control playback"
      }
    }
  }
}
```

---

*FieldSpace Protocol v0.1.0 — Permissions & Trust*

