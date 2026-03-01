# Filings & IP Strategy

## IP approach: defensive publication, not patents

The Connect protocol is patentable subject matter (system and method for decentralized resource coordination via well-known URIs). We are deliberately **not** patenting it. Reasons:

1. **Patents kill standard adoption.** Nobody builds on infrastructure they might get sued for using. robots.txt became universal because it was free. Connect needs the same path.
2. **The project philosophy is open.** Open source, open standards, no middlemen. A patent is a middleman made of paper.
3. **Defensive publication protects us.** Publishing the spec creates prior art. Once the IETF draft is submitted, no one else can patent the protocol either. The spec *is* the IP protection.

**Bottom line:** publish early, publish formally, keep it free.

---

## How IANA well-known URI registration works

Per [RFC 8615](https://www.rfc-editor.org/rfc/rfc8615.html), Section 3.1:

**Required fields:**
1. **URI suffix** — the well-known path (e.g., `connect.txt`)
2. **Change controller** — who owns the registration
3. **Specification document(s)** — URL to the spec
4. **Status** — `permanent` or `provisional`

**Where to submit:**
- GitHub: [protocol-registries/well-known-uris/issues](https://github.com/protocol-registries/well-known-uris/issues) — use the "Registration Request" issue template
- Email: `wellknown-uri-review@ietf.org`

**Expert reviewer:** [@mnot](https://github.com/mnot) (Mark Nottingham). 14-day review window, extendable to 21 days before IESG escalation.

**Naming precedent:** mnot pushed back on `agent.json` (A2A protocol) for being "an extremely generic name for a vendor proposal" — they renamed to `agent-card.json`. Our names (`connect.txt`, `connect.json`, `connect-ledger.json`) are protocol-specific and should be fine.

---

## Filed — pending review

| URI suffix | GitHub issue | Filed | Reviewer status |
|------------|-------------|-------|----------------|
| `agents.txt` | [#72](https://github.com/protocol-registries/well-known-uris/issues/72) | 2026-02-20 | Pending expert review |
| `agents.json` | [#73](https://github.com/protocol-registries/well-known-uris/issues/73) | 2026-02-20 | Pending expert review |
| `agent.txt` | [#74](https://github.com/protocol-registries/well-known-uris/issues/74) | 2026-02-20 | Pending expert review |
| `agent.json` | [#75](https://github.com/protocol-registries/well-known-uris/issues/75) | 2026-02-20 | Pending expert review |
| `ai.txt` | [#76](https://github.com/protocol-registries/well-known-uris/issues/76) | 2026-02-22 | Pending expert review |
| `ai.json` | [#77](https://github.com/protocol-registries/well-known-uris/issues/77) | 2026-02-22 | Pending expert review |

None of these are in the [IANA registry](https://www.iana.org/assignments/well-known-uris/well-known-uris.xhtml) yet. They are open GitHub issues awaiting expert approval.

---

## Ready to file

| URI suffix | Spec reference | Blocked by |
|------------|---------------|------------|
| `connect.txt` | [CONNECT-SPEC.md](CONNECT-SPEC.md) | Spec needs to be at a public URL |
| `connect.json` | [CONNECT-SPEC.md](CONNECT-SPEC.md) | Same |
| `connect-ledger.json` | [CONNECT-SPEC.md](CONNECT-SPEC.md) | Same |

---

## Registration requests — copy-paste ready

These use the exact [GitHub issue template](https://github.com/protocol-registries/well-known-uris/blob/main/.github/ISSUE_TEMPLATE/registration-request.md) format.

### connect.txt

```markdown
Please confirm that:

* [x] You have read and understood the [requirements for registration](https://tools.ietf.org/html/rfc8615#section-3.1).
* [x] You have checked [the registry](https://www.iana.org/assignments/well-known-uris/) and found no current value that meets your needs.
* [x] Your specification reference URL is stable; ideally, managed by a widely-recognised
      standards development organisation (e.g., published as an RFC). Otherwise, please give
      additional information.

If so, please enter the details of the well-known URI below:

* URI suffix: connect.txt
* Change controller: Kayla Cardillo (contactkaylacard@gmail.com)
* Specification document(s): [SPEC URL]

Any additional information (this will not be included in the registry)?

connect.txt is a standalone coordination protocol declaration file that
allows organizations to publish their participation in the Connect protocol
— declaring their role (provider, seeker, or both), supported categories
(shelter, food, legal, medical, etc.), and endpoints for live capacity data.
The same protocol is also accessible as a block within ai.txt (issue #76).
connect.txt provides a standalone entry point for organizations that only
need coordination infrastructure without the broader AI policy declarations
of ai.txt. Plain text format, one field per line.
See also: connect.json, connect-ledger.json (filed concurrently).
```

### connect.json

```markdown
Please confirm that:

* [x] You have read and understood the [requirements for registration](https://tools.ietf.org/html/rfc8615#section-3.1).
* [x] You have checked [the registry](https://www.iana.org/assignments/well-known-uris/) and found no current value that meets your needs.
* [x] Your specification reference URL is stable; ideally, managed by a widely-recognised
      standards development organisation (e.g., published as an RFC). Otherwise, please give
      additional information.

If so, please enter the details of the well-known URI below:

* URI suffix: connect.json
* Change controller: Kayla Cardillo (contactkaylacard@gmail.com)
* Specification document(s): [SPEC URL]

Any additional information (this will not be included in the registry)?

connect.json is the dynamic API layer of the Connect protocol. While
connect.txt (or the Connect block in ai.txt) declares participation,
connect.json provides real-time capacity data, request endpoints, and
supported methods. It is the machine-readable counterpart referenced by
the Endpoint field in connect.txt. JSON format.
See also: connect.txt, connect-ledger.json (filed concurrently).
```

### connect-ledger.json

```markdown
Please confirm that:

* [x] You have read and understood the [requirements for registration](https://tools.ietf.org/html/rfc8615#section-3.1).
* [x] You have checked [the registry](https://www.iana.org/assignments/well-known-uris/) and found no current value that meets your needs.
* [x] Your specification reference URL is stable; ideally, managed by a widely-recognised
      standards development organisation (e.g., published as an RFC). Otherwise, please give
      additional information.

If so, please enter the details of the well-known URI below:

* URI suffix: connect-ledger.json
* Change controller: Kayla Cardillo (contactkaylacard@gmail.com)
* Specification document(s): [SPEC URL]

Any additional information (this will not be included in the registry)?

connect-ledger.json is the public outcome ledger for the Connect protocol.
It records the results of coordination signals — whether needs were matched,
fulfilled, or unmet. The ledger is designed to be aggregatable across
participating sites, generating evidence of system-wide capacity gaps.
Contains no personally identifiable information. It is referenced by the
Ledger field in connect.txt. JSON format.
See also: connect.txt, connect.json (filed concurrently).
```

---

## IETF Internet-Draft

| Field | Value |
|-------|-------|
| Document name | `draft-techenrichment-connect-protocol-00` |
| Status | Spec written ([CONNECT-SPEC.md](CONNECT-SPEC.md)), draft not yet formatted/submitted |
| Covers | connect.txt format, connect.json schema, ledger format, matching semantics, role definitions, discovery, security |
| Why it matters | Formal defensive publication. Timestamped prior art in the IETF datatracker. Blocks anyone from patenting the protocol. |

### Submission process

1. Format the spec as an Internet-Draft using [xml2rfc](https://xml2rfc.tools.ietf.org/) or [kramdown-rfc](https://github.com/cabo/kramdown-rfc)
2. Submit via the [IETF Datatracker](https://datatracker.ietf.org/submit/)
3. Draft is published with a 6-month expiration window (renewable)

---

## Filing order

1. ~~Finalize the Connect spec~~ → Done ([CONNECT-SPEC.md](CONNECT-SPEC.md))
2. **Get spec at a public URL** → merge branch or publish to GitHub Pages so the registration can reference it
3. **Submit IANA registrations** for connect.txt, connect.json, connect-ledger.json → copy-paste the issues above, fill in `[SPEC URL]`
4. **Submit IETF Internet-Draft** → creates the formal prior art and a permanent spec URL

**Strategic note:** Consider waiting for agents.txt/ai.txt to be approved first. Approval establishes credibility with the reviewer for subsequent filings from the same change controller.

---

## What does NOT need filing

- **`connect.json` schema** — no separate registration beyond the well-known URI
- **Ledger format** — covered by the spec
- **`@ai-txt/connect` package name** — npm namespace, not a standards body concern
- **Trademark on "Connect"** — too generic, and we don't want to
