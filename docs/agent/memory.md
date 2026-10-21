# Memory and user model

Status: Proposed · Last updated: 2026-10-21

The goal: Akiyomi should get better at helping someone the more they use it, and the user should always be able to see and correct what it believes.

## Three layers

| Layer | What it holds | How it's written | Example |
| --- | --- | --- | --- |
| **Profile** | Facts the user told us directly, in structured fields | Intake conversation, settings | Budget ≤ ¥5M; home airport SEA; household of 2; travel 20–27 Feb |
| **Preference facts** | Things we learned, each with provenance and confidence | Consolidation from signals and conversation | "Prefers houses ≤20 min from a station" (0.9, from 2 dismissals + confirmed) |
| **Episodes** | Summaries of past Scouts and conversations, embedded for retrieval | End of each Scout, end of long conversations | "Feb trip: loved Iiyama cluster, found Day 2 too much driving" |

Raw **signals** feed the second layer and are kept for re-consolidation and evals.

## Signals

| Signal | Captured when | Strength |
| --- | --- | --- |
| `save` | User saves a listing | Weak positive on its attributes |
| `dismiss` (+ optional reason) | User removes a listing from a shortlist | Weak negative; strong with a reason |
| `open_report` / dwell time | User reads a listing in depth | Very weak positive |
| `edit_constraint` | User changes a Scout constraint | Strong, explicit |
| `edit_draft` | User edits an inquiry draft | Tone and content preferences for future drafts |
| `approve` / `reject` action | User decides on a proposal | Strong signal about what they want done for them |
| `chat_statement` | User states a preference in conversation ("I hate snow") | Strong, explicit |
| `debrief` (1.0) | Post-visit notes | Strong |

## Preference facts

```
PreferenceFact {
  id
  user_id
  subject        // "listing" | "trip" | "drafts" | "town"
  key            // e.g. "max_station_minutes", "avoid_hazard", "lodging_style"
  value          // typed: number, enum, range, text
  polarity       // prefer | avoid | require
  confidence     // 0..1
  status         // proposed | active | retracted
  sources[]      // signal ids and/or message ids
  created_at, last_confirmed_at, last_used_at
}
```

Keys come from a fixed vocabulary that the ranker and planner understand. Free-text facts (e.g. "loves engawa verandas") are allowed but only used by the model as context, never by the ranker.

## Consolidation

A background job runs after a burst of signals:

1. Group recent signals by the listing attributes they touch (distance to station, hazard class, price, floor area, town).
2. The model proposes candidate facts with a rationale, constrained to the key vocabulary.
3. Merge with existing facts: strengthen, weaken, or flag a contradiction.
4. **Confidence ≥ 0.8 and from an explicit statement →** `active`.
   **Otherwise →** `proposed`, and the UI asks the user once: *"Should I prefer houses within 20 minutes of a station from now on?"* Yes → `active`; No → `retracted` (and remembered as retracted so we don't ask again).

## Using memory

| Consumer | How it uses memory |
| --- | --- |
| **Ranker** | Profile and active facts with known keys become scoring features and hard filters. Each ranked listing gets "why it's here" reasons that name the facts used. |
| **Planner** | Retrieved facts and episodes go into the planning prompt as context (e.g. max daily drive, lodging style). |
| **Drafter** | Tone and language preferences from past `edit_draft` signals. |
| **Proposals** | Approval history informs which proposals to bundle (1.0: permissions page). |

Retrieval per step is targeted: the planner asks for facts by subject and key, plus the top-k episodes by embedding similarity to the goal. We don't dump all memory into every prompt.

## User control

The **"What Akiyomi knows about you"** page lists the profile, every active and proposed fact with its sources ("because you dismissed X and Y with reason 'too far from station'"), and episode summaries.

- **Edit** any value. Edits are signals too.
- **Forget** any fact or episode. It's removed from ranking and planning immediately and kept only as a retraction marker, so we don't re-learn it from the same old signals.
- **Export** everything as JSON. **Delete account** removes all of it.

## What we never learn

- Nationality, ethnicity, religion, health, or anything else sensitive, even if it's inferable. If the user volunteers something (e.g. a wheelchair user needing step-free access), it's stored only as the practical need they stated.
- Anything from other people's content (a town's reply doesn't write to the user's memory except via the user's own actions).

## Evals

- **Extraction:** from scripted conversations and signal sequences, did consolidation produce the expected facts (precision/recall) and no forbidden ones?
- **Effect:** after a dismissal-with-reason, does the next ranking change in the expected direction?
- **Forgetting:** after "forget", does the fact disappear from ranker features and planner context?

See [evals](evals.md).

## Open questions

- Household profiles (1.0): how to merge two people's preferences and show conflicts.
- Decay: how fast should unused facts lose confidence?
