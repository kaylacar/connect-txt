# Connect Protocol Specification

**Version:** 1.0
**Status:** Draft
**Date:** 2026-02-28
**Authors:** Tech Enrichment / Machine Policy

---

## Abstract

Connect is a protocol for publishing capacity, expressing need, and recording whether they matched. It uses well-known URIs so any agent, crawler, or system can discover what an organization has and what it needs — without a middleman.

There is no standard way to say "I need" and "I have" on the internet. Connect adds one.

---

## 1. Overview

Connect has two layers:

| Layer | File | Purpose |
|-------|------|---------|
| Declaration | `connect.txt` or `ai.txt` Connect block | States that a site participates, its role, categories, and where to find live data |
| Data | `connect.json` | Live capacity, request endpoints, real-time state |

A third artifact — the **ledger** — records every match attempt and outcome.

### Protocol flow

```
need → capacity → match → outcome → ledger
```

### Two entry points

Organizations can declare Connect participation two ways:

1. **Standalone `connect.txt`** at `/.well-known/connect.txt` — for organizations that only want coordination
2. **Connect block in `ai.txt`** at `/.well-known/ai.txt` — for organizations that also declare AI policy

Both are semantically equivalent. An agent discovering a site checks either location.

---

## 2. connect.txt

### Location

```
/.well-known/connect.txt
```

### Format

Plain text. One field per line. Field name and value separated by `: ` (colon, space). Lines starting with `#` are comments. Blank lines are ignored.

### Fields

| Field | Required | Values | Description |
|-------|----------|--------|-------------|
| `Spec-Version` | Yes | `1.0` | Protocol version |
| `Site-Name` | Yes | Free text | Human-readable name of the organization |
| `Site-URL` | Yes | URL | Canonical URL of the site |
| `Role` | Yes | `provider`, `seeker`, `both` | Whether the site publishes capacity, requests resources, or both |
| `Categories` | Yes | Comma-separated list | What the site provides or needs (see Section 5) |
| `Endpoint` | Yes | URL | Location of the `connect.json` file |
| `Ledger` | Yes | `public`, `private` | Whether the outcome ledger is publicly accessible |

### Example

```
# /.well-known/connect.txt
Spec-Version: 1.0
Site-Name: Portland Emergency Shelter
Site-URL: https://shelter.example.org
Role: provider
Categories: shelter, food
Endpoint: https://shelter.example.org/.well-known/connect.json
Ledger: public
```

### Parsing rules

1. Fields are case-insensitive (`Role` = `role` = `ROLE`)
2. Values are trimmed of leading/trailing whitespace
3. Unknown fields MUST be ignored (forward compatibility)
4. Duplicate fields: last value wins
5. File MUST be served as `text/plain` with UTF-8 encoding

---

## 3. Connect block in ai.txt

Organizations that already publish `ai.txt` can add a Connect block instead of a separate file.

### Fields

| Field | Required | Values | Description |
|-------|----------|--------|-------------|
| `Connect` | Yes | `true` | Enables Connect for this site |
| `Connect-Role` | Yes | `provider`, `seeker`, `both` | Same as `Role` in connect.txt |
| `Connect-Categories` | Yes | Comma-separated list | Same as `Categories` in connect.txt |
| `Connect-Endpoint` | Yes | URL | Location of the `connect.json` file |
| `Connect-Ledger` | Yes | `public`, `private` | Same as `Ledger` in connect.txt |

### Example

```
# /.well-known/ai.txt
Spec-Version: 1.0
Site-Name: Portland Emergency Shelter
Site-URL: https://shelter.example.org
Training: deny

# Connect
Connect: true
Connect-Role: provider
Connect-Categories: shelter, food
Connect-Endpoint: https://shelter.example.org/.well-known/connect.json
Connect-Ledger: public
```

### Field mapping

| connect.txt | ai.txt Connect block |
|-------------|---------------------|
| `Role` | `Connect-Role` |
| `Categories` | `Connect-Categories` |
| `Endpoint` | `Connect-Endpoint` |
| `Ledger` | `Connect-Ledger` |

The `Connect-` prefix avoids collision with existing ai.txt fields.

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
  "capacity": [
    {
      "category": "string",
      "type": "string",
      "available": "number",
      "total": "number",
      "updated": "ISO 8601 timestamp"
    }
  ],
  "endpoint": "URL",
  "methods": ["string"]
}
```

### Fields

| Field | Required | Type | Description |
|-------|----------|------|-------------|
| `capacity` | Yes | Array | List of capacity entries |
| `capacity[].category` | Yes | String | Must match a value from the declared `Categories` |
| `capacity[].type` | Yes | String | Specific resource type within the category |
| `capacity[].available` | Yes | Number | Currently available units |
| `capacity[].total` | Yes | Number | Total units (available + occupied) |
| `capacity[].updated` | Yes | String | ISO 8601 timestamp of last update |
| `endpoint` | No | URL | API endpoint for submitting requests |
| `methods` | No | Array | Supported API methods (see Section 6) |

### Example

```json
{
  "capacity": [
    {
      "category": "shelter",
      "type": "emergency-bed",
      "available": 12,
      "total": 40,
      "updated": "2026-02-28T08:00:00Z"
    },
    {
      "category": "food",
      "type": "hot-meal",
      "available": 85,
      "total": 200,
      "updated": "2026-02-28T11:30:00Z"
    }
  ],
  "endpoint": "https://shelter.example.org/api/connect",
  "methods": ["request", "status"]
}
```

### Seekers

Organizations with `Role: seeker` or `Role: both` publish needs instead of (or in addition to) capacity:

```json
{
  "needs": [
    {
      "category": "shelter",
      "type": "emergency-bed",
      "quantity": 3,
      "urgency": "immediate",
      "updated": "2026-02-28T19:00:00Z"
    }
  ],
  "endpoint": "https://agency.example.org/api/connect",
  "methods": ["request", "status"]
}
```

| Field | Required | Type | Description |
|-------|----------|------|-------------|
| `needs` | Yes (for seekers) | Array | List of current needs |
| `needs[].category` | Yes | String | Category of the need |
| `needs[].type` | Yes | String | Specific resource type needed |
| `needs[].quantity` | Yes | Number | Number of units needed |
| `needs[].urgency` | No | String | `immediate`, `today`, `this-week`, `ongoing` |
| `needs[].updated` | Yes | String | ISO 8601 timestamp |

---

## 5. Categories

Standard categories. Organizations MAY use custom categories but SHOULD prefer these for interoperability:

| Category | Description |
|----------|-------------|
| `shelter` | Emergency shelter, transitional housing, beds |
| `food` | Meals, food pantry, groceries |
| `medical` | Healthcare, clinics, urgent care |
| `legal` | Legal aid, representation, court services |
| `housing` | Permanent housing, rental assistance |
| `employment` | Job placement, training, workforce development |
| `education` | Classes, tutoring, educational programs |
| `disaster` | Disaster relief, emergency response |

### Resource types

Each category contains typed resources. Types are free-form strings. Common types:

- `shelter`: `emergency-bed`, `family-unit`, `transitional-bed`, `warming-center`
- `food`: `hot-meal`, `food-box`, `grocery-delivery`
- `medical`: `appointment`, `walk-in`, `telehealth`
- `legal`: `consultation`, `representation`, `clinic-slot`

---

## 6. Methods

The `methods` array in connect.json declares what API operations the endpoint supports:

| Method | Description |
|--------|-------------|
| `request` | Submit a request for a resource |
| `status` | Check the status of a previous request |
| `cancel` | Cancel a pending request |
| `list` | List all open requests (provider-side) |

### Request flow

```
POST /api/connect
{
  "method": "request",
  "signal": "need:shelter:emergency-bed",
  "quantity": 1,
  "contact": "optional"
}

→ 200 OK
{
  "id": "req-abc123",
  "status": "pending",
  "signal": "need:shelter:emergency-bed"
}
```

```
GET /api/connect?method=status&id=req-abc123

→ 200 OK
{
  "id": "req-abc123",
  "status": "fulfilled",
  "signal": "need:shelter:emergency-bed",
  "timestamp": "2026-02-28T22:15:00Z"
}
```

---

## 7. Ledger

The ledger records every match attempt and its outcome. If `Ledger: public`, the ledger MUST be accessible at:

```
/.well-known/connect-ledger.json
```

### Ledger entry schema

```json
{
  "signal": "string",
  "matched": "boolean",
  "outcome": "string",
  "timestamp": "ISO 8601 timestamp"
}
```

### Fields

| Field | Required | Type | Description |
|-------|----------|------|-------------|
| `signal` | Yes | String | The need signal in format `need:{category}:{type}` |
| `matched` | Yes | Boolean | Whether available capacity was found |
| `outcome` | Yes | String | `fulfilled`, `pending`, `expired`, `declined`, `unavailable` |
| `timestamp` | Yes | String | When the outcome was recorded |

### Ledger file schema

```json
{
  "site": "https://shelter.example.org",
  "generated": "2026-02-28T23:59:59Z",
  "entries": [
    {
      "signal": "need:shelter:emergency-bed",
      "matched": true,
      "outcome": "fulfilled",
      "timestamp": "2026-02-28T22:15:00Z"
    },
    {
      "signal": "need:shelter:emergency-bed",
      "matched": false,
      "outcome": "unavailable",
      "timestamp": "2026-02-28T23:01:00Z"
    }
  ]
}
```

### Privacy

- Ledger entries MUST NOT contain personally identifiable information
- The ledger records signals and outcomes, not people
- `Ledger: private` means the ledger exists but is not publicly served — used for internal tracking only

### Why the ledger matters

The data is memetic. "12 beds. 47 requests. 35 people with nowhere to go." is a screenshot, a news story, a grant application. The protocol generates the evidence the current system can't.

---

## 8. Discovery

Agents and systems discover Connect-enabled sites by checking:

1. `GET /.well-known/connect.txt` — standalone declaration
2. `GET /.well-known/ai.txt` — check for `Connect: true` block

If both exist, `connect.txt` takes precedence for Connect-specific fields.

### Discovery order

```
1. Check /.well-known/connect.txt
   → If found: parse, read Endpoint, fetch connect.json
2. Check /.well-known/ai.txt
   → If Connect: true: parse Connect-* fields, fetch connect.json
3. Fetch connect.json from declared Endpoint
4. If Ledger: public, fetch /.well-known/connect-ledger.json
```

---

## 9. Security considerations

- **No authentication required for discovery.** connect.txt and connect.json are public files. This is intentional — capacity information should be findable.
- **API endpoints MAY require authentication.** The `endpoint` in connect.json may require API keys, OAuth tokens, or other credentials for submitting requests.
- **Rate limiting.** Servers SHOULD implement rate limiting on connect.json and the API endpoint. Declare limits in `agents.txt` if using the agents.txt standard.
- **No surveillance chokepoint.** The protocol is decentralized by design. There is no central server that sees all requests. This is a feature, not a limitation.
- **HTTPS required.** All URLs in connect.txt and connect.json MUST use HTTPS.

---

## 10. IANA considerations

This specification requests registration of the following well-known URIs:

| URI suffix | Reference | Change controller |
|------------|-----------|-------------------|
| `connect.txt` | This document | Kayla Cardillo (contactkaylacard@gmail.com) |
| `connect.json` | This document | Kayla Cardillo (contactkaylacard@gmail.com) |
| `connect-ledger.json` | This document | Kayla Cardillo (contactkaylacard@gmail.com) |

---

## 11. References

- [RFC 8615](https://www.rfc-editor.org/rfc/rfc8615) — Well-Known Uniform Resource Identifiers
- [ai.txt specification](https://machinepolicy.org)
- [agents.txt specification](https://machinepolicy.org)
