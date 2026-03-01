# Machine Policy — Roadmap

## Done

- [x] Define standalone `connect.txt` format (added to index.html 2026-02-28)
- [x] Write full Connect protocol spec — [CONNECT-SPEC.md](CONNECT-SPEC.md)
- [x] Draft IANA registration requests for `connect.txt`, `connect.json`, `connect-ledger.json` — copy-paste ready in [FILINGS.md](FILINGS.md)

## Pending review (filed, waiting on expert)

- [ ] IANA `agents.txt` — [issue #72](https://github.com/protocol-registries/well-known-uris/issues/72) (filed 2026-02-20)
- [ ] IANA `agents.json` — [issue #73](https://github.com/protocol-registries/well-known-uris/issues/73) (filed 2026-02-20)
- [ ] IANA `agent.txt` — [issue #74](https://github.com/protocol-registries/well-known-uris/issues/74) (filed 2026-02-20)
- [ ] IANA `agent.json` — [issue #75](https://github.com/protocol-registries/well-known-uris/issues/75) (filed 2026-02-20)
- [ ] IANA `ai.txt` — [issue #76](https://github.com/protocol-registries/well-known-uris/issues/76) (filed 2026-02-22)
- [ ] IANA `ai.json` — [issue #77](https://github.com/protocol-registries/well-known-uris/issues/77) (filed 2026-02-22)

Reviewer: [@mnot](https://github.com/mnot). 14-day window. None in the IANA registry yet.

## Next

- [ ] Get CONNECT-SPEC.md at a public URL (merge to main or publish to GitHub Pages)
- [ ] Submit IANA registrations for `connect.txt`, `connect.json`, `connect-ledger.json` — fill in `[SPEC URL]` and open 3 issues
- [ ] Add Connect spec to the ai-txt repo README and SPEC.md
- [ ] Submit IETF Internet-Draft for Connect protocol (defensive publication / prior art)

## Build

- [ ] Build the `@ai-txt/connect` npm package
- [ ] Build the aggregation/dashboard layer (stewardship — collects ledger data across all participating sites)
- [ ] First real deployment with a crisis services organization

## Reference

- [FILINGS.md](FILINGS.md) — IP strategy, registration templates, filing order
- [CONNECT-SPEC.md](CONNECT-SPEC.md) — full Connect protocol specification
