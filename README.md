# FieldSpace Protocol

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![Version](https://img.shields.io/badge/version-0.1.0-blue.svg)](https://github.com/FieldSpaceProtocol/fieldspace-protocol/releases)

An open, language-agnostic protocol for exposing content and interactivity across screen environments—from televisions and tablets to phones, desktops, and digital signage.

**📖 [Read the Documentation](https://fieldspaceprotocol.github.io/fieldspace-protocol)**

---

## Overview

FieldSpace enables **channels** (external services) to expose capabilities, data, state, and actions to **clients** (such as ScreenSpace) that render and mediate interaction across diverse surfaces.

### Key Features

- **Surface-first** — Designed for screens, not tools
- **Stateful** — Channels maintain and expose current state
- **Agent-native** — AI agents can discover, reason about, and act on capabilities
- **Interaction-adaptive** — Works with remote controls, touch, voice, or full keyboard
- **Consumer-safe** — Designed for home, family, and public environments

## Quick Example

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

## Documentation

| Document | Description |
|:---------|:------------|
| [Getting Started](https://fieldspaceprotocol.github.io/fieldspace-protocol/getting-started) | Build your first channel |
| [Concepts](spec/concepts.md) | Core terminology and conceptual model |
| [Capabilities](spec/capabilities.md) | Capability declaration and discovery |
| [Data & State](spec/data-state.md) | Data access and state inspection |
| [Actions](spec/actions.md) | Action model and invocation |
| [Views](spec/views.md) | Optional view layer (advisory) |
| [Permissions](spec/permissions.md) | Permission and trust model |
| [Versioning](spec/versioning.md) | Versioning and compatibility |

## Schema

Validate your manifests against the JSON Schema:

- [`manifest.schema.json`](manifest.schema.json)

## Examples

See complete example manifests in [`examples/`](examples/):

- [Media Catalogue](examples/media-catalogue.json) — Video library with playback
- [Smart Lights](examples/smart-lights.json) — IoT device control
- [Video Feed](examples/video-feed.json) — Live video streaming

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## License

This specification is released under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

---

**FieldSpace Protocol v0.1.0** — Draft

