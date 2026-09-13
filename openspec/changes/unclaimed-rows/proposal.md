## Why

The document is silent on rows created with an empty registered-key list, even though the node creates and scores them. §3 says a subject needs "a stable identifier, so two evaluators can be sure they mean the same restaurant. Nothing more" — and stops. Nothing says a row can be evaluated before it holds any registered key, and nothing says what a later key holder could do about one, because no operation does. A reader cannot tell what ships today from what is still to be designed. This change states the behaviour and the gap, and leaves the identity questions open.

## Decisions

**D1. Mark ▸ In the code that a subject can be evaluated before its row holds any registered key.** The first evaluation of an identifier with no row creates one with an empty `signingKeys` list, scored like any other row. Empty is not proof no one holds a key — `verifyUserSig`'s identifier-derived fallback applies only before a row exists. *Rejected:* describe a claim mechanism now — that decides protocol questions inside a document edit.

**D2. Mark claiming ▸ Not yet built, scoped to this row.** For a row with no registered keys and no recovery connections, no operation registers a key: *Add Signing Key* needs the row's own signature, which an empty key list cannot give; *Social Recovery* needs recovery connections designated in advance. *Rejected:* the broader "no operation lets a key holder take over an existing row" — Social Recovery does exactly that for rows that have connections.

**D3. Add two ▸ Open questions and extend question 13 in place.** Authorization and pre-claim evaluations become questions 15 and 16; 13 keeps its non-person identifier scope and gains reconciling two rows for one subject. *Rejected:* a separate reconciliation question — neither half is answerable without the same missing mechanism.

## Proposed document text

Old → new. Nothing else in the document changes. Claims verified against `Meta-Node/BrightID-Aura-Node` `dev` at commit `469d0b0`.

**§3 — insert after the closing paragraph of *Subjects need an identifier, not an identity*, before the `---` that ends the section.** OLD: nothing (insertion). NEW:

> > **▸ In the code.** A subject can be evaluated before its row holds any registered key: the first evaluation of an identifier with no row creates one with an empty `signingKeys` list (`web_services/foxx/v6/db.js:198–203`, in `evaluate`), scored like any other row — the scorer never opens the users collection (`scorer/verifications/aura.py`). Empty is not proof no one holds a key: `verifyUserSig`'s identifier-derived fallback (`web_services/foxx/v6/operations.js:20–38`) applies only before a row exists, not to one an evaluation already created.
>
> > **▸ Not yet built.** Claiming, for such a row — no registered keys, no recovery connections: no operation registers a key for it. *Add Signing Key* needs the row's own signature, which an empty key list can never give; *Social Recovery* needs recovery connections designated in advance, and none exist.

**§14 — revise question 13 in place; append questions 15 and 16 under *Design*.**

OLD (13):

> 13. How identifiers for non-person subjects are created and deduplicated (Section 3).

NEW (13):

> 13. How identifiers for non-person subjects are created and deduplicated, and how two rows for one subject are reconciled (Section 3).

NEW (appended after question 14):

> 15. What authorizes control of a row like this, and what establishes the relationship between a claimant and the subject it was evaluated as (Section 3).
> 16. What becomes of evaluations made before a row is claimed (Section 3).

## Impact

- `how-aura-works.md` §3 and §14. §13's *Non-person subjects — Not built* row still holds: what the new text describes is the `users/` row, not the domain around it.
- The node, the scorer, and the apps: no change. Row creation and scoring already ship; claiming would need a new operation this change does not specify.
- Any claim mechanism designed later must answer questions 13, 15 and 16 before it can be built.
