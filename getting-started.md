---
layout: default
title: Getting Started
nav_order: 2
---

# Getting Started
{: .no_toc }

Build your first FieldSpace channel in minutes.
{: .fs-6 .fw-300 }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Overview

A FieldSpace channel is any HTTP service that:

1. Serves a **manifest** describing its capabilities
2. Exposes **data** and **state** via declared endpoints
3. Accepts **action** invocations

That's it. No SDK required, no specific language or framework needed.

## Step 1: Create a Manifest

Every channel needs a manifest file that describes what it can do. Create a `manifest.json`:

```json
{
  "fieldspace": "0.1",
  "channel": {
    "id": "com.yourcompany.mychannel",
    "name": "My First Channel",
    "description": "A simple FieldSpace channel",
    "version": "1.0.0"
  },
  "capabilities": [
    {
      "id": "items",
      "type": "data",
      "name": "Items",
      "endpoint": "/api/items",
      "metadata": {
        "paginated": true
      }
    }
  ]
}
```

## Step 2: Serve the Manifest

Your channel must serve the manifest at a well-known endpoint. Typically:

```
GET /.well-known/fieldspace.json
```

Or at the root:

```
GET /fieldspace.json
```

## Step 3: Implement Endpoints

Implement the endpoints declared in your manifest. For the example above:

```
GET /api/items
```

Should return JSON data matching your declared schema:

```json
{
  "items": [
    { "id": "1", "title": "First Item" },
    { "id": "2", "title": "Second Item" }
  ],
  "pagination": {
    "page": 1,
    "total_pages": 5,
    "total_items": 42
  }
}
```

## Step 4: Add State (Optional)

If your channel has state that clients should observe, add a state capability:

```json
{
  "id": "current_selection",
  "type": "state",
  "endpoint": "/api/state/selection",
  "metadata": {
    "refresh_interval_seconds": 5
  }
}
```

## Step 5: Add Actions (Optional)

If users or agents can trigger operations, add action capabilities:

```json
{
  "id": "select_item",
  "type": "action",
  "endpoint": "/api/actions/select",
  "permissions": {
    "user": "allowed",
    "agent": "allowed"
  },
  "schema": {
    "input": {
      "type": "object",
      "properties": {
        "item_id": { "type": "string" }
      },
      "required": ["item_id"]
    }
  }
}
```

## Validation

Validate your manifest against the JSON Schema:

- [manifest.schema.json](https://github.com/FieldSpaceProtocol/fieldspace-protocol/blob/main/manifest.schema.json)

## Examples

See complete example manifests:

- [Media Catalogue](/examples/#media-catalogue) — Video library with playback
- [Smart Lights](/examples/#smart-lights) — IoT device control
- [Video Feed](/examples/#video-feed) — Live video streaming

## Next Steps

- Read the [Concepts](/spec/concepts) documentation
- Explore [Capabilities](/spec/capabilities) in depth
- Understand the [Permission Model](/spec/permissions)

