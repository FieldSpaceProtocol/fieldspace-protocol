---
layout: default
title: Concepts
parent: Specification
nav_order: 1
---

# Core Concepts
{: .no_toc }

**Version:** 0.1.0
{: .fs-5 .fw-300 }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Conceptual Hierarchy

```
FieldSpace (protocol)
  └─ Channel (external service)
       └─ Capability (declared function)
            ├─ Data (read-only content)
            ├─ State (inspectable condition)
            └─ Action (invocable operation)

Client (e.g., ScreenSpace)
  └─ Space (persistent container)
       └─ Screen (logical display)
            └─ Pane / Frame (content region)
                 └─ Channel Binding (connection)
```

---

## Protocol-Level Concepts

### FieldSpace

The protocol itself. FieldSpace defines how channels expose capabilities and how clients consume them. The protocol is:

- **Declarative**: Channels describe what they can do
- **Stateful**: Channels maintain and expose current state
- **Capability-driven**: Clients and agents reason over declared capabilities
- **Rendering-agnostic**: Channels may suggest but do not control presentation

### Channel

An external service that implements the FieldSpace protocol. A channel:

- Exposes a **manifest** describing its identity and capabilities
- Serves **data** through declared endpoints
- Maintains and exposes **state**
- Accepts **action** invocations
- Optionally provides **view hints** for rendering

Channels are identified by reverse-domain identifiers (e.g., `com.example.weather`).

### Capability

A declared function or feature that a channel exposes. Capabilities are typed:

| Type | Description | Example |
|------|-------------|---------|
| `data` | Read-only content retrieval | Video feed, weather data |
| `state` | Inspectable current condition | Playback position, selected item |
| `action` | Invocable operation | Play, pause, search |
| `control` | Stateful control surface | Volume, brightness |
| `status` | Passive status indicator | Online/offline, sync state |

### Action

An explicit, named operation that can be invoked on a channel. Actions:

- Have declared **inputs** (parameters) and **outputs** (results)
- Declare **side effects** (none, local, external)
- Specify **permission requirements** (user, agent, confirmation)
- Are **idempotent** where possible

### State

The current condition of a channel or capability. State:

- Is **inspectable** by clients and agents at any time
- Is **structured** according to a declared schema
- May be **subscribed to** for real-time updates
- Is **channel-authoritative** (clients cache but don't own)

### View (Optional)

An advisory rendering suggestion from a channel. Views:

- Are **non-authoritative**: clients may ignore them
- Are **platform-specific**: web, tvOS, mobile hints
- Are **fallback-capable**: channels declare degradation paths
- Do **not replace** data/state exposure requirements

---

## Client-Level Concepts

These concepts are defined by clients (like ScreenSpace), not the protocol. They are documented here for context.

### Space

A named, persistent container for screens. Analogous to a "room" or "context":

- "Living Room"
- "Office"
- "Car Dashboard"

Spaces persist across sessions and devices.

### Screen

A logical display surface within a space. A screen:

- May map 1:1 with a physical display
- May be virtual (e.g., a saved layout)
- Contains one or more panes

### Pane / Frame

A bounded region of a screen that displays content from a single channel binding. Panes:

- Have position and size within a screen
- Receive focus for navigation
- Connect to exactly one channel binding

### Channel Binding

A configured connection between a pane and a channel instance. A binding includes:

- Channel identifier
- Authentication credentials (if required)
- Instance-specific configuration
- Current state cache

---

## Actor Roles

### User

A human interacting with the client. Users:

- Have full control within their permissions
- Can override agent permissions per-channel
- Authenticate with channels via the client

### Agent

An AI companion that operates at the protocol level. Agents:

- **Observe**: Read state from all channels freely
- **Reason**: Understand capabilities and context
- **Suggest**: Recommend actions to users
- **Act**: Invoke actions (with declared permissions)

Agents operate on **structured protocol data**, not pixels. They never need to "see" the screen.

### Channel (as actor)

The external service. Channels:

- Expose capabilities to clients and agents
- Cannot act on other channels
- Cannot access client-side state directly

### Client System

The platform itself (e.g., ScreenSpace). The client:

- Manages sessions, layout, navigation
- Mediates all channel communication
- Enforces permission policies
- Owns the rendering surface

---

## Key Principles

### 1. Protocol Boundary

Everything above the protocol boundary is the client's responsibility.
Everything below is the channel's responsibility.
Agents operate at the protocol level, not the pixel level.

### 2. State Exposure Requirement

Even channels with custom rendering **must** expose semantic state via the protocol. This enables:

- Agent understanding without vision
- Cross-channel reasoning
- Session persistence and resume
- Accessibility

### 3. Capability Declaration

Channels declare what they can do. Clients and agents discover and reason over these declarations. Undeclared capabilities do not exist.

### 4. Consumer Safety

The protocol is designed for home, family, and public environments. Channels must:

- Declare side effects honestly
- Respect permission boundaries
- Support graceful degradation
- Avoid attention-hijacking patterns

### 5. Interaction Adaptivity

The protocol supports the full spectrum of interaction models:

| Surface | Input | Interaction Level |
|---------|-------|-------------------|
| TV + remote | D-pad, select, voice | Minimal |
| Phone/tablet | Touch, gestures, keyboard | Rich |
| Desktop | Mouse, keyboard, shortcuts | Full |
| Voice assistant | Voice commands | Agent-mediated |
| Signage | None or touch | Configurable |

Channels declare capabilities; clients adapt interaction based on surface capabilities. A channel that works on a TV remote also works with full touch—the client mediates.

---

*FieldSpace Protocol v0.1.0 — Core Concepts*

