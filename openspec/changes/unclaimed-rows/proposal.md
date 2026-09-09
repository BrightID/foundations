## Why

The document is silent on rows nobody holds a key for, even though the node creates and scores them. §3 says a subject needs "a stable identifier, so two evaluators can be sure they mean the same restaurant. Nothing more" and stops there. Nothing says a row can be created and evaluated before its subject holds any key, and nothing says what the holder of a key could later do about such a row — because no operation does it.

A reader therefore cannot tell what is possible today from what is a design still to be made. This change makes the current behaviour and the gap explicit, so the design conversation starts from a fixed point.

## What Changes

**§3** gains one **▸ In the code** marker — a row is created by the first evaluation of an identifier, with an empty signing-key list, and is scored like any other — and one **▸ Not yet built** marker: claiming. **§14** gains three open questions, as questions.

Open question 13 is unchanged; new question 17 generalises its deduplication half beyond non-person subjects. §13 is unchanged, and its *Non-person subjects — Not built* row still holds: what exists is the `users/` row, not the domain around it.

Under 150 words of net new document text. *Canonical*, *primary*, a claim mechanism, and a two-axis framing are deliberately not introduced — see `design.md`.

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
- **Any claim mechanism designed later** must answer the three new open questions before it can be built: proving the key holder is the subject, the standing of pre-claim evaluations, and reconciling two rows for one subject.
