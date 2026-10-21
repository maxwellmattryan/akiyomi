# 0003. The agent acts only through approved proposals run by a separate executor

Status: Proposed · Date: 2026-10-21

## Context

Scout sends emails to real municipal offices on users' behalf (and in 1.0 prepares bookings). The model reads untrusted Japanese text from listings, PDFs and replies, so prompt injection is a real risk. A wrong send can't be undone and reflects on the user and on Akiyomi.

## Options

1. **Agent has send tools, guarded by prompts and a confirmation message.** Simple, but the model is the only barrier between injected text and an email.
2. **Agent creates proposals; a separate executor with its own credentials runs only proposals with a matching approval hash.** More code, but the model can't send anything by itself, whatever it's told.
3. **No sending; drafts only.** Safest, but doesn't demonstrate acting on the user's behalf.

## Decision

Option 2, as specified in [actions and guardrails](../../agent/actions-and-guardrails.md): action tiers, payload-hash-bound approvals, recipient allowlist from official source pages, undo window, rate limits, sandbox by default, append-only audit log and a kill switch.

## Consequences

- Two deployables (or processes) with different credentials from the start.
- Every new action type needs a tier, a payload schema, an approval view and red-team eval cases before it ships.
- Guardrail evals are a hard CI gate with no override.
