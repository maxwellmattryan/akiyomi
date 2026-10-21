# Risks and trust

Status: Draft · Last updated: 2026-10-21

Risks from the original brief and extended spec, plus the new ones the Scout agent introduces. Each has an owner doc where the mitigation is designed.

## Agent risks (new with Scout)

| Risk | Where it comes from | Mitigation | Design |
| --- | --- | --- | --- |
| The agent sends something the user didn't approve | Bugs, prompt injection, model error | Agent can only create proposals; a separate executor sends; approval bound to a payload hash; red-team tests; kill switch | [Guardrails](../agent/actions-and-guardrails.md) |
| Prompt injection via listing text or town replies | Untrusted Japanese HTML, PDFs and emails reach the model | Untrusted content is data, never instructions; it can't add recipients or trigger actions; injection cases in the eval set | [Guardrails](../agent/actions-and-guardrails.md#prompt-injection) |
| Spamming small town halls | Many users, or one user, inquiring at scale | One inquiry per town per user per week; global per-town daily cap; only send for houses the user has looked at; sandbox by default | [Guardrails](../agent/actions-and-guardrails.md#rate-limits-and-being-a-good-guest) |
| Looking like brokerage | Agent writing to towns on someone's behalf | Inquiries only ask to view and ask questions; never negotiate price or terms; sent as the user (reply-to) with clear disclosure; legal review before 1.0 bookings | [Guardrails](../agent/actions-and-guardrails.md) |
| Money moved without real consent (1.0) | Booking handoff | User completes payment on the provider's own checkout; Akiyomi never holds funds or cards; spend caps; second confirmation | [1.0](v1.md#scout-agent-long-running) |
| Creepy or wrong memory | Learned preferences from weak signals | Low-confidence facts confirmed before use; every fact shows its source; forget is instant; no inference of protected traits | [Memory](../agent/memory.md#what-we-never-learn) |
| Runaway cost | Planner loops, retries, large contexts | Per-Scout budgets for tool calls, tokens and money; cost logged per step | [Planning](../agent/planning.md#budgets) |
| Third-party API terms | Flight and hotel APIs may forbid some uses or caching | Check terms in the API spike; cache only what's allowed; link out where required | [External APIs](../agent/external-apis.md) |

## Product and data risks (from the brief)

| Risk | Mitigation |
| --- | --- |
| Scraping terms or blocking by listing sites | Check terms per source; respect `robots.txt`; link out instead of republishing; ask municipalities directly, since they want buyers |
| Unlicensed brokerage or legal advice | Information, not brokerage; "ask a professional" in every relevant output; legal advice before 1.0 |
| Wrong hazard or subsidy info leads to a bad purchase | Cite every claim with its "as of" date; show confidence; a clear "verify before buying" step |
| Inaccurate geocoding of rural addresses | Precision levels and confidence; buffered hazard checks; users can fix the pin |
| Stale data | Show the date checked on every claim; scheduled re-crawls; users can report errors |
| Sensitive personal data (profiles, emails) | Minimal collection; encryption at rest; deletion on request; APPI and GDPR basics before launch |
| Harming communities | Show community obligations up front; respect towns' buyer screening; no rental-conversion promotion where restricted |
| Competition from listing sites | Compete on assessment and Scout, not listings |

## Open trust questions

Tracked in [open questions](../research/open-questions.md):

- Do we need a licensed partner before the agent sends inquiries, or only before bookings?
- Will municipalities welcome agent-drafted inquiries, or should we tell them up front and offer a direct channel?
