## Why

The document is silent on rows created with an empty registered-key list, even though the node creates and scores them. §3 says a subject needs "a stable identifier, so two evaluators can be sure they mean the same restaurant. Nothing more" and stops there. Nothing says a row can be created and evaluated before it holds any registered key, and nothing says what a later key holder could do about such a row — because no operation does it.

A reader therefore cannot tell what is possible today from what is a design still to be made. This change makes the current behaviour and the gap explicit, so the design conversation starts from a fixed point.

## What Changes

**§3** gains one **▸ In the code** marker — a row is created by the first evaluation of an identifier, with an empty `signingKeys` list (not proof no one holds a key — the identifier-derived signing fallback doesn't reach a row that already exists), and is scored like any other — and one **▸ Not yet built** marker: claiming, scoped to a row with no registered keys and no recovery connections. **§14** gains two open questions, as questions, and question 13 is generalised in place to cover reconciling two rows for one subject.

§13 is unchanged, and its *Non-person subjects — Not built* row still holds: what exists is the `users/` row, not the domain around it. Nothing in the new text claims non-person subjects are supported today.

About 188 words of net new document text (the §3 markers, the six-word growth in question 13, and questions 15–16) — over the original 150-word estimate; the added line references, the verified commit, and the fallback qualification account for the difference. *Canonical*, *primary*, a claim mechanism, and a two-axis framing are deliberately not introduced — see `design.md`.

## Capabilities

### New Capabilities

None. `skip_specs: true`, for the reason the previous change gave: this repo holds one evolving narrative document, not discrete capability specs.

### Modified Capabilities

None.

## Impact

- `how-aura-works.md` §3 and §14.
- **The node.** No change. Row creation and scoring as described already ship; claiming would need a new operation, which this change does not specify.
- **Scoring.** No change — the scorer never opens the users collection.
- **Front-end apps.** No change. `aura-player` has no node client and issues no *Evaluate* operation, so it does not create these rows today; any client that issues one already does.
- **Any claim mechanism designed later** must answer the new open questions before it can be built: what authorizes control and what establishes the claimant's relationship to the subject, the standing of pre-claim evaluations, and — via the revised question 13 — reconciling two rows for one subject.
