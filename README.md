# connect.txt

An open standard for declaring what a site needs and what it can provide.

The internet has no standard way for organizations to say "we need X" or "we have Y." connect.txt adds one. Place it at `/.well-known/connect.txt` and any agent, crawler, or human can discover your needs and capacity -- without a middleman.

## How it works

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

An agent reads this file, fetches `connect.json` for live capacity data, and can submit requests through the declared endpoint. A seeker publishes needs the same way. The protocol matches them.

## The stack

| File | What it does |
|------|-------------|
| `connect.txt` | Declares participation, role, and categories (text) |
| `connect.json` | Live capacity and needs data (JSON API) |
| `connect-ledger.json` | Public outcome ledger -- every match attempt, recorded |

The ledger is the point. "12 beds. 47 requests. 35 people with nowhere to go." is a screenshot, a news story, a grant application. The protocol generates the evidence the current system cannot.

## Roles

| Role | Publishes |
|------|-----------|
| `provider` | Capacity (what's available) |
| `seeker` | Needs (what's required) |
| `both` | Capacity and needs |

## Standard categories

`shelter`, `food`, `medical`, `legal`, `housing`, `employment`, `education`, `disaster`. Custom categories are allowed but standard ones improve interoperability.

## Two entry points

Organizations can declare Connect participation two ways:

1. **Standalone `connect.txt`** at `/.well-known/connect.txt` -- for organizations that only want coordination
2. **Connect block in `ai.txt`** at `/.well-known/ai.txt` -- for organizations that already declare AI policy

Both are semantically equivalent.

## Live capacity example

```json
{
  "capacity": [
    {
      "category": "shelter",
      "type": "emergency-bed",
      "available": 12,
      "total": 40,
      "updated": "2026-02-28T08:00:00Z"
    }
  ],
  "endpoint": "https://shelter.example.org/api/connect",
  "methods": ["request", "status"]
}
```

## IP approach

Connect is deliberately not patented. Publishing the spec creates prior art. The protocol is free to implement, free to extend, free to build on.

## IANA status

| URI | Issue | Filed |
|-----|-------|-------|
| `connect.txt` | [#81](https://github.com/protocol-registries/well-known-uris/issues/81) | Feb 28, 2026 |
| `connect.json` | [#82](https://github.com/protocol-registries/well-known-uris/issues/82) | Feb 28, 2026 |
| `connect-ledger.json` | [#85](https://github.com/protocol-registries/well-known-uris/issues/85) | Feb 28, 2026 |

## Specification

Full protocol specification: [CONNECT-SPEC.md](CONNECT-SPEC.md)

Covers: text and JSON formats, roles, categories, request flow, ledger schema, discovery order, security considerations, and IANA registration details.

## Related

- [agents.txt](https://github.com/kaylacar/agents-txt) -- what agents can do on a site
- [ai.txt](https://github.com/kaylacar/ai-txt) -- what AI policy applies
- [verify.txt](https://github.com/kaylacar/verify-txt) -- how to verify physical claims

## License

MIT
