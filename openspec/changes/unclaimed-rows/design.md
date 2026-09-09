## Context

The document defines neither *canonical* nor *primary*, and has no framing that separates controlling a row from being the subject it was evaluated as. A change that described claiming would have to introduce that vocabulary and use it in the same breath — and would settle, in a document edit, questions that belong on a call: what proves a key holder is the subject, and what weight of standing settles a contested row. So this change records only what the node does today and what is missing.

## Goals / Non-Goals

**Goals** — state the behaviour that already ships; name claiming as absent; put the identity questions on the open list, unanswered.

**Non-Goals** — a claim mechanism; a standing threshold; deduplication or re-pointing; withdrawal of an evaluation; any new vocabulary.

## Decisions

**D1. Mark as ▸ In the code that a subject can be evaluated before anyone holds a key for it.** The row is created by the first evaluation with an empty signing-key list, and scoring treats it as it treats any other row. *Rejected:* introduce the canonical/claim design now — it decides open protocol questions inside a document change.

**D2. Mark claiming ▸ Not yet built, in two sentences.** No operation lets the holder of a key take over an existing row; the two operations that set keys cannot reach one. *Rejected:* describe how claiming should work — there is nothing built to describe, and the shape is exactly what is undecided.

**D3. Add three ▸ Open questions: subject proof, pre-claim evaluations, reconciliation of two rows.** They are the questions any claim mechanism must answer first. *Rejected:* answer them here with a standing threshold — authorization and weight are protocol decisions, not editorial ones.

## Risks / Trade-offs

- Naming behaviour without a mechanism can read as licence to build one ad hoc. The **▸ Not yet built** and **▸ Open** markers are the guard; the tasks require they survive review.
- `verifyUserSig` derives a signing key from the BrightID itself *only when no row exists*, so creating a keyless row removes that fallback. Whether that locks out a BrightID that has never transacted is a claim about the operation path, not the row primitive, and is deliberately not stated in the document.
- The three open questions overlap open question 13 for non-person subjects. 13 is left as written; 17 states the general case.

## Proposed document text

Plain text, old → new. Nothing else in the document changes.

**§3 — insert after the closing paragraph of *Subjects need an identifier, not an identity* ("Identifiers for non-person subjects are a task for the domain's own experts…"), before the `---` that ends the section.**

NEW:

> > **▸ In the code.** A subject can be evaluated before anyone holds a key: the first evaluation of an identifier with no row creates one with an empty `signingKeys` list (`web_services/foxx/v6/db.js`, in `evaluate`), and scoring treats it like any other row; the scorer never opens the users collection. A restaurant, a book, or an agent with no key carries evaluations today, as a `users/` document — all that exists for non-person subjects (Section 13).
>
> > **▸ Not yet built.** Claiming. No operation lets a key holder take over an existing row: *Add Signing Key* needs a signature from the row itself, *Social Recovery* connections designated in advance.

**§14 — append three questions after question 14, under *Design*.**

NEW:

> 15. How a key holder proves they are the subject a row was evaluated as (Section 3). The key alone does not.
> 16. What becomes of evaluations made before a row is claimed (Section 3).
> 17. How two rows for one subject are reconciled, and by whom (Section 3).

## Migration Plan

None. No stored data changes, and the rows this change describes already exist.
