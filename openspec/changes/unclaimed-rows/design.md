## Context

The document defines neither *canonical* nor *primary*, and has no framing that separates controlling a row from being the subject it was evaluated as. A change that described claiming would have to introduce that vocabulary and use it in the same breath — and would settle, in a document edit, questions that belong on a call: what proves a key holder is the subject, and what weight of standing settles a contested row. So this change records only what the node does today and what is missing.

## Goals / Non-Goals

**Goals** — state the behaviour that already ships; name claiming as absent; put the identity questions on the open list, unanswered.

**Non-Goals** — a claim mechanism; a standing threshold; deduplication or re-pointing; withdrawal of an evaluation; any new vocabulary.

## Decisions

**D1. Mark as ▸ In the code that a subject can be evaluated before its row holds any registered key.** The row is created by the first evaluation with an empty `signingKeys` list, and scoring treats it as it treats any other row. An empty list is not proof no one holds a key — `verifyUserSig`'s identifier-derived fallback only reaches a row that doesn't exist yet, so the document says both in one breath. *Rejected:* introduce the canonical/claim design now — it decides open protocol questions inside a document change.

**D2. Mark claiming ▸ Not yet built, scoped to the row this change describes.** For a row an evaluation created — no registered keys, no recovery connections — no operation registers a key for it: *Add Signing Key* needs a signature the row can't produce with an empty key list; *Social Recovery* needs recovery connections designated in advance, which none of these rows have. *Rejected:* the earlier "no operation lets a key holder take over an existing row" — too broad, since the Social Recovery exception it ignores concerns existing rows in general, not this keyless case.

**D3. Add two ▸ Open questions — authorization and the claimant's relationship to the subject, and pre-claim evaluations — and generalise question 13 in place to cover reconciling two rows for one subject, rather than add a fourth question that duplicates it.** These are the questions any claim mechanism must answer first. *Rejected:* answer them here with a standing threshold — authorization and weight are protocol decisions, not editorial ones. *Rejected:* a separate reconciliation question — 13 already asks it for non-person subjects; generalising is smaller than duplicating.

## Risks / Trade-offs

- Naming behaviour without a mechanism can read as licence to build one ad hoc. The **▸ Not yet built** and **▸ Open** markers are the guard; the tasks require they survive review.
- `verifyUserSig` derives a signing key from the identifier itself *only when no row exists*, so a row an evaluation already created (empty `signingKeys`, row present) doesn't get that fallback either — now stated in §3. Whether that locks out a BrightID that has never transacted before being evaluated is a claim about the operation path, not the row primitive, and remains deliberately not stated as a lockout.
- Question 13 now carries two questions instead of one — identifier dedup, and row reconciliation. Both concern the same missing claim mechanism; splitting them into separate numbered questions would suggest they're independently answerable when they aren't.

## Proposed document text

Plain text, old → new. Nothing else in the document changes. Verified against `Meta-Node/BrightID-Aura-Node` `dev` at commit `469d0b0` (`git -C aura-node rev-parse origin/dev`).

**§3 — insert after the closing paragraph of *Subjects need an identifier, not an identity* ("Identifiers for non-person subjects are a task for the domain's own experts…"), before the `---` that ends the section.**

OLD: (nothing — insertion)

NEW:

> > **▸ In the code.** A subject can be evaluated before its row holds any registered key: the first evaluation of an identifier with no row creates one with an empty `signingKeys` list (`web_services/foxx/v6/db.js:198–203`, in `evaluate`, `dev` @ `469d0b0`), scored like any other row — the scorer never opens the users collection (`scorer/verifications/aura.py`, `dev` @ `469d0b0`). Empty is not proof no one holds a key: `verifyUserSig`'s identifier-derived fallback (`web_services/foxx/v6/operations.js:20–38`, `dev` @ `469d0b0`) applies only before a row exists, not to one evaluation already created.
>
> > **▸ Not yet built.** Claiming, for such a row — no registered keys, no recovery connections: no operation registers a key for it. *Add Signing Key* needs the row's own signature, which an empty key list can never give; *Social Recovery* needs recovery connections designated in advance, and none exist.

**§14 — append two questions after question 14, under *Design*, and revise question 13 in place.**

OLD (question 13, currently under *Design*):

> 13. How identifiers for non-person subjects are created and deduplicated (Section 3).

NEW (question 13):

> 13. How identifiers are created and deduplicated, and how two rows for one subject are reconciled (Section 3).

NEW (appended after question 14):

> 15. What authorizes control of a row like this, and what establishes the relationship between a claimant and the subject it was evaluated as — the claimant need not be the subject; a restaurant acts through a representative (Section 3).
> 16. What becomes of evaluations made before a row is claimed (Section 3).

## Migration Plan

None. No stored data changes, and the rows this change describes already exist.
