---
layout: default
title: Views
parent: Specification
nav_order: 5
---

# Views (Optional Layer)
{: .no_toc }

**Version:** 0.1.0
{: .fs-5 .fw-300 }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Overview

Views are an **optional, advisory** layer in FieldSpace. Providers may suggest how their content should be rendered, but clients are free to ignore these suggestions.

**Critical principle**: Views are non-authoritative. Clients own the rendering surface.

---

## Why Views Are Advisory

1. **Platform constraints**: tvOS cannot render web views; clients must adapt
2. **User preferences**: Users may prefer different layouts or themes
3. **Accessibility**: Clients may need to override views for accessibility
4. **Consistency**: Clients maintain visual consistency across providers
5. **Agent operation**: Agents operate on data/state, not views

---

## View Declaration

Providers declare view hints in their manifest:

```json
{
  "views": {
    "default": {
      "type": "grid",
      "columns": { "compact": 2, "medium": 4, "expanded": 6 },
      "item_template": "media_card"
    },
    "detail": {
      "type": "hero_detail",
      "sections": ["media", "metadata", "actions", "related"]
    },
    "playback": {
      "type": "fullscreen_player",
      "controls": ["play_pause", "seek", "next_previous"]
    }
  }
}
```

---

## View Types

### Template Views

Providers suggest templates; clients render using their own implementations:

```json
{
  "type": "grid",
  "columns": { "compact": 2, "medium": 4, "expanded": 6 },
  "item_template": "media_card",
  "spacing": "comfortable"
}
```

Standard template types:

| Type | Description |
|------|-------------|
| `grid` | Grid of items |
| `list` | Vertical list |
| `carousel` | Horizontal scrolling row |
| `hero_detail` | Large hero image with details |
| `fullscreen_player` | Media playback view |
| `dashboard` | Multi-section layout |

### Web Views (Non-Guaranteed)

Providers may offer web-based rendering:

```json
{
  "web_view": {
    "url": "https://provider.example.com/embed",
    "sandbox": ["allow-scripts", "allow-same-origin"],
    "communication": "postMessage",
    "fallback": "default"
  }
}
```

**Important**: Web views are **not guaranteed** to be available:

- tvOS does not support WKWebView for third-party content
- Some clients may disable web views for security
- Users may prefer native rendering

Providers **must** declare a fallback for when web views are unavailable.

### Native View Hints

Providers may include platform-specific hints:

```json
{
  "native_hints": {
    "tvos": {
      "focus_style": "scale",
      "parallax": true,
      "media_passthrough": "AVPlayer"
    },
    "android_tv": {
      "leanback_compatible": true,
      "media_passthrough": "ExoPlayer"
    },
    "ios": {
      "supports_pip": true,
      "supports_airplay": true
    }
  }
}
```

---

## Client Behavior

### Clients MAY

- Use provider view hints as suggestions
- Render content using provider-suggested templates
- Load web views when available and appropriate
- Apply native hints for platform optimization

### Clients MAY NOT

- Be required to render provider views exactly as specified
- Be required to support web views
- Allow providers to break out of their pane boundaries

### Clients MUST

- Render provider content even without view hints
- Fall back gracefully when preferred views are unavailable
- Maintain control of system chrome and navigation
- Expose provider state regardless of view rendering

---

## View State Synchronization

When using web views, providers must synchronize state via the protocol:

```
┌─────────────────────────────────────────┐
│              Client                      │
│  ┌─────────────────────────────────┐    │
│  │         Web View (iframe)        │    │
│  │  ┌─────────────────────────┐    │    │
│  │  │   Provider UI           │    │    │
│  │  └───────────┬─────────────┘    │    │
│  └──────────────┼──────────────────┘    │
│                 │ postMessage            │
│                 ▼                        │
│  ┌─────────────────────────────────┐    │
│  │      State Bridge               │    │
│  └─────────────────────────────────┘    │
│                 │                        │
│                 ▼                        │
│  ┌─────────────────────────────────┐    │
│  │   Protocol State Endpoint       │◄───┼── Agent reads state here
│  └─────────────────────────────────┘    │
└─────────────────────────────────────────┘
```

The web view **must not** be the only source of state. State must be accessible via the protocol endpoint.

---

## Focus Hints

For remote-controlled environments, providers may declare focus behavior:

```json
{
  "focus_hints": {
    "initial_focus": "first_item",
    "focus_groups": ["header", "content", "actions"],
    "wrap_navigation": false,
    "focus_sound": true
  }
}
```

Clients use these hints to manage focus but may override them.

---

## Rendering Tiers

Views map to rendering tiers (defined by clients like ScreenSpace):

| Tier | View Type | Client Control | Provider Control |
|------|-----------|----------------|------------------|
| 0 | Data only | Full | None |
| 1 | Template hints | High | Low (hints only) |
| 2 | Web view | Medium | Medium (bounded) |
| 3 | Native passthrough | Low | High (media only) |

Providers declare preferred tier; clients select based on platform capabilities.

---

## View Versioning

View schemas may evolve. Providers declare supported versions:

```json
{
  "views": {
    "schema_version": "0.1",
    "templates": { ... }
  }
}
```

Clients ignore unrecognized view properties and fall back gracefully.

---

*FieldSpace Protocol v0.1.0 — Views (Optional Layer)*

