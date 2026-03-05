# connect.txt

An open standard for telling agents how to connect to and use a website or service.

**The web has one door for humans and still lacks a clear door for agents.** `connect.txt` is that door: place it at `/.well-known/connect.txt` to declare how agents should connect, authenticate, negotiate sessions, respect rate limits, and call the supported interaction surface of a site.

`connect.txt` is not a matching or needs/capacity protocol. It is the connection and usage layer for agent-facing web infrastructure.

---

## Quick Start

### Publish a Connect declaration

```txt
# /.well-known/connect.txt
Spec-Version: 1.0
Site-Name: Example Store
Site-URL: https://example.com
Connect-Version: 1.0
Base-URL: https://example.com
Agents-URL: https://example.com/.well-known/agents.txt
Endpoint: https://example.com/.well-known/connect.json
Auth: bearer, none
Session: optional
Rate-Limit: 60/minute
```

### Publish connection metadata

```json
{
  "base_url": "https://example.com",
  "capabilities_url": "https://example.com/.well-known/agents.json",
  "auth": ["bearer", "none"],
  "session": {
    "mode": "optional",
    "header": "X-Session-Token"
  },
  "rate_limit": {
    "requests": 60,
    "window": "minute"
  },
  "methods": ["GET", "POST"],
  "content_types": ["application/json"],
  "transport": ["https"]
}
```

---

## What it does

`connect.txt` gives a site a machine-readable agent connection layer:

- Declare where the agent-facing surface lives
- Point to structured capability declarations
- Declare authentication expectations
- Declare session behavior
- Declare rate limits and transport rules
- Declare accepted methods and content types
- Make connection semantics explicit before execution begins

This reduces guesswork. Agents do not have to infer how to use a site from human pages, forms, or brittle scraping.

---

## Why this exists

The current web is optimized for humans:

- visual navigation
- interactive forms
- cookies and browser sessions
- documentation written for people
- undocumented assumptions about headers, methods, and flow

Agents need something else:

- an explicit connection surface
- clear auth rules
- session rules they can follow deterministically
- content types and transport requirements
- machine-readable guidance about where to start

`agents.txt` tells an agent what it may do. `connect.txt` tells it how to connect and use the site correctly.

---

## For Humans

Use `connect.txt` if you want to publish the operational rules an agent should follow when interacting with your site.

This is useful for:

- ecommerce sites
- SaaS products
- data APIs
- portals with structured actions
- internal systems being modernized for agents
- any service that wants a clean agent entry point separate from the human interface

If you are a site owner:

- `agents.txt` is the permission and capability declaration
- `connect.txt` is the technical connection and usage declaration
- `agents-protocol` can implement the execution semantics behind both

---

## For Agents

**Purpose:** discover the correct connection, auth, session, and transport rules for a site before attempting actions.

**Capabilities:**
- Discover the base URL for agent interaction
- Locate the machine-readable capability document
- Determine whether auth is required and which auth modes are supported
- Determine whether session tokens are used and where they appear
- Read rate limits before acting
- Determine allowed methods and content types
- Establish a reliable connection plan before calling site capabilities

**Discovery order:**
1. Check `/.well-known/connect.txt`
2. Resolve the `Endpoint` field to fetch `connect.json`
3. Resolve `Agents-URL` or `capabilities_url` to load the declared capability surface
4. Follow auth, session, transport, and rate-limit rules before making requests

**Parsing rules:**
- Field names are case-insensitive
- Unknown fields must be ignored
- Duplicate fields: last value wins
- `Endpoint` is authoritative for the live connection metadata document
- `Auth` values are comma-separated in `connect.txt`

**What Connect is not:**
- not a permission model by itself
- not a matching protocol
- not a verification protocol
- not an execution runtime
- not a guarantee that a site will accept every request

---

## The stack

| File | What it does |
|------|-------------|
| `connect.txt` | Declares connection and usage metadata in text form |
| `connect.json` | Declares live connection semantics in machine-readable form |

`connect.txt` may point to other files in the protocol family, especially `agents.txt` and `agents.json`.

---

## Example use cases

### Agent-enabled storefront

- `agents.txt` declares search, browse, detail, cart, and checkout capabilities
- `connect.txt` declares bearer auth, optional sessions, rate limits, and JSON-only POST rules
- an agent connects correctly without scraping the storefront UI

### Internal operations portal

- a city office exposes a controlled task surface for agents
- `connect.txt` declares SSO-backed bearer auth and session headers
- agents follow the same operational rules every time

### Modernization layer

- a legacy system keeps its human UI
- a parallel agent-facing door is published via `connect.txt`
- agent traffic becomes explicit instead of accidental

---

## Position in the protocol family

`connect.txt` is one part of a broader machine-readable web stack:

- [agents.txt](https://github.com/kaylacar/agents-txt): what agents can do on a site
- [ai.txt](https://github.com/kaylacar/ai-txt): what AI policy applies
- `connect.txt`: how agents connect and use a site
- [verify.txt](https://github.com/kaylacar/verify-txt): how claims or outcomes can be independently verified
- `match.txt`: how needs, capacity, and outcomes are published across organizations
- `agents-protocol`: execution semantics for governed agent interaction

This separation matters.

- `agents.txt` is permission and capability discovery
- `connect.txt` is connection and usage
- `match.txt` is coordination state
- `verify.txt` is verification
- `ai.txt` is policy

Do not overload one file with all of these jobs.

---

## IP approach

Connect is deliberately not patented. Publishing the spec creates prior art. The protocol is free to implement, free to extend, and free to build on.

---

## IANA status

Older filing language and issue history existed for Connect under the broader matching interpretation. With Match now split into its own protocol, `connect.txt` and `connect.json` should be treated as draft names until the revised connection-focused spec is published and filing language is updated accordingly.

---

## Specification

Full protocol specification: [CONNECT-SPEC.md](CONNECT-SPEC.md)

Covers:
- text and JSON formats
- auth and session declarations
- rate limits and transport expectations
- discovery order
- security considerations
- protocol-family positioning
- draft filing guidance

---

## License

MIT
