---
layout: default
title: Data & State
parent: Specification
nav_order: 3
---

# Data & State Endpoints
{: .no_toc }

**Version:** 0.1.0
{: .fs-5 .fw-300 }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Overview

FieldSpace providers expose data and state through HTTP endpoints. This document specifies the requirements for data access, state inspection, and real-time updates.

---

## Data Endpoints

Data endpoints serve read-only content. They are the primary mechanism for providers to expose collections and items.

### Request Format

```http
GET /api/videos
Accept: application/json
Authorization: Bearer <token>
```

### Response Format

```json
{
  "data": [...],
  "meta": {
    "total": 100,
    "page": 1,
    "per_page": 20,
    "has_more": true
  }
}
```

### Pagination

Paginated endpoints **must** support:

| Parameter | Type | Description |
|-----------|------|-------------|
| `page` | integer | Page number (1-indexed) |
| `per_page` | integer | Items per page (max 100) |
| `cursor` | string | Opaque cursor for cursor-based pagination |

Providers **should** prefer cursor-based pagination for large datasets.

### Filtering

Filterable endpoints accept query parameters matching schema properties:

```http
GET /api/videos?category=music&duration_min=60
```

### Sorting

Sortable endpoints accept `sort` and `order` parameters:

```http
GET /api/videos?sort=published_at&order=desc
```

### Search

Searchable endpoints accept a `q` parameter:

```http
GET /api/videos?q=cooking+tutorial
```

---

## State Endpoints

State endpoints expose the provider's current condition. Unlike data endpoints, state represents mutable, real-time information.

### Request Format

```http
GET /api/state
Accept: application/json
Authorization: Bearer <token>
```

### Response Format

```json
{
  "provider_id": "com.example.media",
  "timestamp": "2026-01-21T10:30:00Z",
  "state": {
    "playback": {
      "status": "playing",
      "position_seconds": 142,
      "duration_seconds": 3600,
      "current_item_id": "video-123"
    },
    "queue": {
      "items": ["video-123", "video-456"],
      "position": 0
    }
  }
}
```

### Scoped State

Providers may expose state at different scopes:

```http
GET /api/state                    # Full provider state
GET /api/state/playback           # Playback state only
GET /api/state/queue              # Queue state only
```

---

## Real-Time Updates

Providers **may** support real-time state updates via Server-Sent Events (SSE) or WebSocket.

### Server-Sent Events (Recommended)

```http
GET /api/state/stream
Accept: text/event-stream
```

Response:

```
event: state_update
data: {"playback":{"status":"playing","position_seconds":143}}

event: state_update
data: {"playback":{"position_seconds":144}}
```

### WebSocket (Optional)

```
ws://provider.example.com/api/state/ws
```

Message format:

```json
{
  "type": "state_update",
  "path": "playback.position_seconds",
  "value": 145,
  "timestamp": "2026-01-21T10:30:05Z"
}
```

### Polling Fallback

When real-time updates are unavailable, clients **must** fall back to polling. Providers declare recommended intervals:

```json
{
  "metadata": {
    "refresh_interval_seconds": 5
  }
}
```

Clients **should** respect these intervals and **must not** poll more frequently than once per second.

---

## Idempotency and Safety

### Safe Methods

All data and state endpoints use `GET` requests, which are:

- **Safe**: No side effects
- **Idempotent**: Same request yields same result
- **Cacheable**: Responses may be cached per HTTP headers

### Cache Control

Providers **should** include appropriate cache headers:

```http
Cache-Control: max-age=60, stale-while-revalidate=300
ETag: "abc123"
Last-Modified: Tue, 21 Jan 2026 10:00:00 GMT
```

Clients **should** use conditional requests:

```http
GET /api/videos
If-None-Match: "abc123"
```

---

## Error Handling

### Error Response Format

```json
{
  "error": {
    "code": "not_found",
    "message": "Video not found",
    "details": {
      "video_id": "xyz-999"
    }
  }
}
```

### Standard Error Codes

| HTTP Status | Code | Description |
|-------------|------|-------------|
| 400 | `bad_request` | Invalid request parameters |
| 401 | `unauthorized` | Authentication required |
| 403 | `forbidden` | Insufficient permissions |
| 404 | `not_found` | Resource not found |
| 429 | `rate_limited` | Too many requests |
| 500 | `internal_error` | Provider error |
| 503 | `unavailable` | Provider temporarily unavailable |

---

## Content Types

### Request

Clients **must** send:

```http
Accept: application/json
```

### Response

Providers **must** respond with:

```http
Content-Type: application/json; charset=utf-8
```

---

*FieldSpace Protocol v0.1.0 — Data & State Endpoints*

