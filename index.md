---
layout: default
title: Home
nav_order: 1
permalink: /
description: "FieldSpace is an open protocol for exposing content and interactivity across screen environments—TVs, tablets, phones, desktops, and signage."
image: /assets/images/social-card.png
---

# FieldSpace Protocol
{: .fs-9 }

An open, language-agnostic protocol for exposing content and interactivity across screen environments.
{: .fs-6 .fw-300 }

[Get Started](/getting-started){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 }
[View on GitHub](https://github.com/FieldSpaceProtocol/fieldspace-protocol){: .btn .fs-5 .mb-4 .mb-md-0 }

---

## What is FieldSpace?

FieldSpace enables **channels** (external services) to expose capabilities, data, state, and actions to **clients** (such as ScreenSpace) that render and mediate interaction across diverse surfaces—from televisions and tablets to phones, desktops, and digital signage.

### Design Philosophy

FieldSpace is a **surface-first protocol**, not a tool protocol.

| FieldSpace Is | FieldSpace Is Not |
|:--------------|:------------------|
| Stateful | Stateless RPC |
| Surface- and media-oriented | Tool- or function-oriented |
| Supports persistent views | Request-response only |
| Agent-readable and controllable | Human-only interfaces |
| Consumer-safe (TV, home, public) | Developer-only tooling |
| Interaction-adaptive | Single interaction model |

Channels expose **capabilities, data, state, and actions** — not apps.

## Quick Example

A minimal FieldSpace channel manifest:

```json
{
  "fieldspace": "0.1",
  "channel": {
    "id": "com.example.weather",
    "name": "Weather",
    "description": "Current weather conditions"
  },
  "capabilities": [
    {
      "id": "current_conditions",
      "type": "data",
      "endpoint": "/api/conditions"
    }
  ]
}
```

## Goals

1. **Interaction-adaptive** — Works with minimal interaction (remote, voice) but supports rich interaction (touch, keyboard) when available
2. **Agent-native** — AI agents can discover, reason about, and act on channel capabilities
3. **Platform-agnostic** — Works on tvOS, Android TV, web, mobile, desktop, signage
4. **Channel-agnostic** — Any backend technology can implement the protocol
5. **Consumer-safe** — Designed for home, family, and public environments

## Specification

| Document | Description |
|:---------|:------------|
| [Concepts](/spec/concepts) | Core terminology and conceptual model |
| [Capabilities](/spec/capabilities) | Capability declaration and discovery |
| [Data & State](/spec/data-state) | Data access and state inspection |
| [Actions](/spec/actions) | Action model and invocation |
| [Views](/spec/views) | Optional view layer (advisory) |
| [Permissions](/spec/permissions) | Permission and trust model |
| [Versioning](/spec/versioning) | Versioning and compatibility |

## License

This specification is released under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

---

**FieldSpace Protocol v0.1.0** — Draft

