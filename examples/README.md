---
layout: default
title: Examples
nav_order: 4
---

# Example Channels
{: .no_toc }

Fully worked example channel manifests demonstrating different FieldSpace use cases.
{: .fs-6 .fw-300 }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Overview

This directory contains complete, valid FieldSpace manifests that can be used as templates for new channels.

## Examples

| Example | Description | Capabilities |
|---------|-------------|--------------|
| [video-feed.json](./video-feed.json) | YouTube-like video feed | Data, state, actions, controls |
| [media-catalogue.json](./media-catalogue.json) | Read-only media library | Data only |
| [smart-lights.json](./smart-lights.json) | Controllable smart lighting | State, actions, controls |

## Video Feed Provider

A full-featured media provider demonstrating:

- Multiple data feeds (subscriptions, recommended, trending)
- Playback state management
- Media control actions (play, pause, seek)
- Agent-friendly capability declarations
- Platform-specific view hints

## Media Catalogue Provider

A minimal read-only provider demonstrating:

- Simple data exposure
- No actions or controls
- Agent observation without action
- Graceful minimal implementation

## Smart Lights Provider

A stateful control provider demonstrating:

- Device state exposure
- Control capabilities (brightness, color)
- Action permissions (agent confirmation required)
- Side effect declarations

---

These examples are valid FieldSpace manifests that can be used as templates for new providers.

