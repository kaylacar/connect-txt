# Filings & IP Strategy

## IP approach: defensive publication, not patents

The Connect protocol is potentially patentable subject matter as a system and method for machine-readable agent connection via well-known URIs. We are deliberately not patenting it.

Reasons:

1. Patents reduce standard adoption.
2. The project philosophy is open.
3. Defensive publication creates prior art.

Bottom line: publish early, publish formally, keep it free.

---

## What Connect is for

Connect is the agent-door protocol.

It tells an agent:

- where the agent-facing surface lives
- where capability declarations live
- what auth modes are supported
- whether session state exists
- what rate limits and transport rules apply
- how to connect correctly before acting

Connect is not the matching layer. Matching now belongs in `match.txt` / `match.json` / `match-ledger.json`.

---

## Proposed well-known URIs

- `connect.txt`
- `connect.json`

These should be treated as draft candidates until the revised public spec is published.

---

## Suggested filing order

1. Publish the revised Connect spec at a stable public URL.
2. Decide whether `connect.txt` and `connect.json` are the canonical well-known names.
3. Submit well-known URI registration requests for the final chosen names.
4. Submit an IETF Internet-Draft for the Connect protocol if you want stronger defensive publication.

---

## Strategic note

Connect should remain narrowly scoped to agent connection and usage semantics.

Do not overload Connect with:

- permissions and capability declarations already covered by `agents.txt`
- needs/capacity coordination now covered by `match.txt`
- verification semantics covered by `verify.txt`
- policy semantics covered by `ai.txt`

The value of the stack is that each file has one clear job.
