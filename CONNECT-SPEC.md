# Connect Protocol Specification

**Version:** 1.0
**Status:** Draft
**Date:** 2026-03-05
**Authors:** Tech Enrichment / Machine Policy

---

## Abstract

Connect is a protocol for publishing how agents should connect to and use a website or service. It uses well-known URIs so any agent or system can discover the base URL, capability document, auth modes, session rules, rate limits, transport requirements, and request conventions before acting.

There is no standard way to say "here is how an agent should connect to this site." Connect adds one.

---

## 1. Overview

Connect has two layers:

| Layer | File | Purpose |
|-------|------|---------|
| Declaration | `connect.txt` or `ai.txt` Connect block | States that a site publishes agent connection metadata and where to find it |
| Data | `connect.json` | Live connection semantics: auth, session, rate limits, methods, transport |

### Protocol flow

```
discover -> connect -> authenticate -> establish session -> call capabilities
```

### Two entry points

Organizations can declare Connect participation two ways:

1. **Standalone `connect.txt`** at `/.well-known/connect.txt`
2. **Connect block in `ai.txt`** at `/.well-known/ai.txt`

Both are semantically equivalent.

---

## 2. connect.txt

### Location

```
/.well-known/connect.txt
```

### Format

Plain text. One field per line. Field name and value separated by `: `.

### Fields

| Field | Required | Values | Description |
|-------|----------|--------|-------------|
| `Spec-Version` | Yes | `1.0` | Protocol version |
| `Site-Name` | Yes | Free text | Human-readable site name |
| `Site-URL` | Yes | URL | Canonical site URL |
| `Connect-Version` | Yes | `1.0` | Connect schema version |
| `Base-URL` | Yes | URL | Base URL for the agent-facing surface |
| `Agents-URL` | No | URL | Location of `agents.txt` or `agents.json` capability declarations |
| `Endpoint` | Yes | URL | Location of `connect.json` |
| `Auth` | No | Comma-separated list | Supported auth modes such as `none`, `bearer`, `api-key`, `oauth2` |
| `Session` | No | `none`, `optional`, `required` | Session requirement |
| `Rate-Limit` | No | Free text | Human-readable rate-limit declaration |

### Example

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

### Parsing rules

1. Fields are case-insensitive.
2. Values are trimmed.
3. Unknown fields MUST be ignored.
4. Duplicate fields: last value wins.
5. File MUST be served as `text/plain` with UTF-8 encoding.

---

## 3. Connect block in ai.txt

Organizations that already publish `ai.txt` can add a Connect block instead of a separate file.

### Fields

| Field | Required | Values | Description |
|-------|----------|--------|-------------|
| `Connect` | Yes | `true` | Enables Connect for this site |
| `Connect-Version` | Yes | `1.0` | Same as in `connect.txt` |
| `Connect-Base-URL` | Yes | URL | Same as `Base-URL` |
| `Connect-Agents-URL` | No | URL | Same as `Agents-URL` |
| `Connect-Endpoint` | Yes | URL | Same as `Endpoint` |
| `Connect-Auth` | No | Comma-separated list | Same as `Auth` |
| `Connect-Session` | No | `none`, `optional`, `required` | Same as `Session` |
| `Connect-Rate-Limit` | No | Free text | Same as `Rate-Limit` |

---

## 4. connect.json

### Location

Specified by the `Endpoint` field. Convention:

```
/.well-known/connect.json
```

### Content-Type

`application/json`

### Schema

```json
{
  "base_url": "URL",
  "capabilities_url": "URL",
  "auth": ["none", "bearer"],
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

### Fields

| Field | Required | Type | Description |
|-------|----------|------|-------------|
| `base_url` | Yes | URL | Base URL for the agent-facing surface |
| `capabilities_url` | No | URL | Location of capability declarations |
| `auth` | No | Array | Supported auth modes |
| `session` | No | Object | Session behavior and token/header conventions |
| `session.mode` | No | String | `none`, `optional`, `required` |
| `session.header` | No | String | Header name for session tokens |
| `rate_limit` | No | Object | Request ceiling guidance |
| `rate_limit.requests` | No | Number | Allowed requests per window |
| `rate_limit.window` | No | String | Time window such as `minute` or `hour` |
| `methods` | No | Array | Allowed HTTP methods |
| `content_types` | No | Array | Accepted request content types |
| `transport` | No | Array | Allowed transports such as `https` |

---

## 5. Auth modes

Suggested auth mode values:

- `none`
- `bearer`
- `api-key`
- `oauth2`
- `mTLS`

Sites MAY define extensions, but SHOULD prefer interoperable names.

---

## 6. Session model

If session state exists, the site SHOULD declare whether it is required and how agents should send the session token.

Examples:

- `none`: no session token needed
- `optional`: session supported but not required
- `required`: session token required before privileged calls

---

## 7. Rate limits and transport

Connect does not enforce rate limits. It publishes them so agents can comply before a site has to reject them.

Sites SHOULD declare:

- request ceiling
- time window
- transport requirements
- accepted methods
- accepted content types

---

## 8. Security considerations

- Sites SHOULD declare auth rules explicitly rather than forcing agents to guess.
- Connect metadata SHOULD be served over HTTPS.
- Sites SHOULD avoid publishing secrets or dynamic credentials in `connect.json`.
- Connect does not replace authentication or authorization; it describes them.
- Agents SHOULD treat declared rate limits as normative guidance.

---

## 9. Position in the protocol family

Connect is not the same as permissions, policy, matching, or verification.

- `agents.txt` declares what agents can do
- `connect.txt` declares how agents connect and use a site
- `ai.txt` declares AI policy
- `match.txt` declares needs, capacity, and matching outcomes across organizations
- `verify.txt` declares how claims or outcomes can be independently verified

This separation keeps each primitive legible.
