---
layout: default
title: Versioning
parent: Specification
nav_order: 7
---

# Versioning & Compatibility
{: .no_toc }

**Version:** 0.1.0
{: .fs-5 .fw-300 }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Overview

FieldSpace uses semantic versioning for both the protocol and channel implementations. This document specifies versioning rules and compatibility expectations.

---

## Protocol Versioning

The FieldSpace protocol version follows [Semantic Versioning 2.0.0](https://semver.org/):

```
MAJOR.MINOR.PATCH
```

| Component | Meaning | Example |
|-----------|---------|---------|
| MAJOR | Breaking changes | 1.0 → 2.0 |
| MINOR | New features, backward compatible | 1.0 → 1.1 |
| PATCH | Bug fixes, clarifications | 1.0.0 → 1.0.1 |

### Current Version

```
0.1.0
```

The `0.x` series indicates pre-release status. Breaking changes may occur in minor versions until `1.0`.

---

## Channel Versioning

Channels declare their version independently:

```json
{
  "fieldspace": "0.1",
  "channel": {
    "id": "com.example.media",
    "version": "2.3.1"
  }
}
```

### Protocol Compatibility

Channels declare supported protocol versions:

```json
{
  "fieldspace": "0.1",
  "protocol_compatibility": {
    "minimum": "0.1",
    "maximum": "0.x"
  }
}
```

---

## Breaking Change Rules

### What Constitutes a Breaking Change

| Change Type | Breaking? |
|-------------|-----------|
| Removing a required field | Yes |
| Changing field type | Yes |
| Changing endpoint URL structure | Yes |
| Removing a capability type | Yes |
| Adding a required field | Yes |
| Adding an optional field | No |
| Adding a new capability type | No |
| Adding a new endpoint | No |
| Deprecating (not removing) | No |

### Protocol Breaking Changes

Protocol breaking changes:

1. Increment MAJOR version
2. Provide migration guide
3. Support previous major version for 12 months minimum
4. Announce 6 months before deprecation

### Channel Breaking Changes

Channel breaking changes:

1. Increment channel MAJOR version
2. Update manifest with new version
3. Maintain previous version endpoints if possible
4. Notify clients via manifest change

---

## Backward Compatibility

### Client Requirements

Clients **must**:

- Ignore unknown fields in responses
- Handle missing optional fields gracefully
- Support channels using older protocol versions (within reason)
- Degrade gracefully for unsupported features

### Channel Requirements

Channels **should**:

- Maintain backward compatibility within major versions
- Deprecate before removing
- Version endpoints when breaking changes are necessary
- Document migration paths

---

## Deprecation Policy

### Protocol Deprecation

```json
{
  "deprecated": {
    "feature": "web_view.sandbox",
    "since": "0.2",
    "removal": "1.0",
    "replacement": "web_view.permissions",
    "migration": "https://fieldspace.dev/migration/sandbox-to-permissions"
  }
}
```

### Channel Deprecation

Channels signal deprecation in manifests:

```json
{
  "capabilities": [
    {
      "id": "old_search",
      "type": "action",
      "deprecated": {
        "since": "2.0.0",
        "replacement": "search_v2",
        "removal": "3.0.0"
      }
    }
  ]
}
```

---

## Version Negotiation

### Discovery

Clients discover channel protocol version from manifest:

```http
GET /.well-known/fieldspace
```

```json
{
  "fieldspace": "0.1",
  "channel": { ... }
}
```

### Compatibility Check

```
Client supports: 0.1 - 0.x
Channel requires: 0.1 minimum

0.1 >= 0.1 ✓ Compatible
```

### Incompatibility Handling

When versions are incompatible:

1. Client displays clear error to user
2. Client suggests channel update (if channel is older)
3. Client suggests client update (if client is older)
4. No silent failures

---

## Feature Detection

Clients should use feature detection, not version checking:

```javascript
// Good: Feature detection
if (manifest.capabilities.some(c => c.type === 'control')) {
  enableControlFeatures();
}

// Avoid: Version checking
if (manifest.fieldspace >= '0.2') {
  enableControlFeatures();
}
```

---

## Manifest Caching

### Cache Headers

Channels **should** include cache headers:

```http
Cache-Control: max-age=3600
ETag: "manifest-v2.3.1"
```

### Refresh Triggers

Clients refresh manifests when:

- Cache expires
- User manually refreshes
- Capability mismatch detected
- Authentication changes

---

## Changelog Requirements

Protocol changes are documented in a changelog:

```markdown
## [0.2.0] - 2026-03-01

### Added
- `control` capability type
- Real-time state via SSE

### Changed
- `permissions` field structure (backward compatible)

### Deprecated
- `web_view.sandbox` (use `web_view.permissions`)
```

---

## Stability Guarantees

| Version Range | Stability |
|---------------|-----------|
| 0.x | Experimental; breaking changes in minor versions |
| 1.x | Stable; breaking changes only in major versions |
| x.y-beta | Beta; may change before release |
| x.y-rc | Release candidate; frozen except for bugs |

---

*FieldSpace Protocol v0.1.0 — Versioning & Compatibility*

