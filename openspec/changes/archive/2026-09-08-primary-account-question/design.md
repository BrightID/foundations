## Context

See `proposal.md` — Why.

Two constraints shape this. The question text exists only in prose: an evaluation record
stores a domain and a category, and the scorer filters by category and sums weighted
answers without ever reading what was asked. And production is not in use, so there is no
body of answers to preserve. Together these make the change a documentation edit rather
than a migration — but only until the node ships.

## Goals / Non-Goals

**Goals**

- A question an evaluator can answer from what they can observe.
- A question an honest red-teamer can answer *yes* to about their own real account.
- The smallest possible amount of new prose. §2 must still read straight through.

**Non-Goals**

- Defining how alternate accounts get declared. That is its own change.
- Changing scoring, thresholds, level requirements, or decay behaviour.
- Client copy in the Player and front-end apps.

## Decisions

**The question becomes: *"is this the account to verify for this person?"***

The question is settled; the sentence is not. Better wording is welcome from any reviewer, provided it keeps both properties: answerable from what an evaluator can observe, and answerable *yes* by a red-teamer about their own real account.

*Verify* is the document's own word — "a verified-unique human", "the existing BrightID
guarantee", "getting verified". *The account* carries the one-per-person scarcity; *for
this person* carries the human link. It also names what the answer is for: apps consume
verifications.

Alternatives considered:

- ***"...to approve for this person?"*** — rejected late. "Approve" appears nowhere in the
  document. It would import a concept into a document whose discipline is staying readable
  straight through.
- ***"is this their primary account?"*** — rejected. "Primary" reads as something the
  holder designates, and the holder is not answering. It also collides with vocabulary
  needed elsewhere: *canonical* is the one identifier everyone points at, and applies to
  restaurants and agents as much as to people.
- **Keep "only" and fix it with calibration** — rejected. No shared expectation makes an
  unobservable fact observable.
- **Version the question, keep both** — rejected. Versioning protects answers already
  given, and there are none worth protecting.

**Anyone may answer; the protocol does not gate eligibility.** A recognition-style
wording — *"do you know this person, and is this the account they use?"* — was considered
and rejected. It would make personal knowledge a condition of answering. Deciding whose
answers are worth listening to is what Aura already does, on every question, dynamically.
Building that judgment into the wording would hard-code an answer the system exists to
compute. *(This principle is broader than this change. It may belong in §3 later; it is not
proposed here.)*

**The two directions are not symmetric**, and one clause in §2's existing calibration
paragraph says so: a strong positive rests on knowing the person, a strong negative often
does not — an account can be plainly wrong without your knowing whose it is. This is
coherent with the 4× already applied to negatives, and it is what lets someone who does not
know the subject still contribute usefully.

**Declared alternates are not mentioned.** The earlier draft added a paragraph saying a
negative answer is not an accusation. It is cut: the new wording no longer accuses anyone,
so the paragraph fixed a problem the change had already fixed.

## Risks / Trade-offs

- **§12 currently claims verification comes from people who already know the person.**
  Under this wording strangers may answer, so the sentence gains four words —
  *"verification that carries weight"* — leaving the privacy claim true and the eligibility
  question open. → Task 1.7.
- **§10's decay illustration assumes a near-static answer.** Under the old question that
  held. Under the new one the answer moves whenever someone changes which account should be
  verified. → Task 1.6.
- **Client copy drifts from the document** until the apps are updated → tracked as
  follow-on work, not silently assumed done.
- **The window closes on someone else's schedule.** The node is being driven toward
  production now. Landing after that stops being free.
- **A person can still hold two accounts that are both honestly theirs.** The new question
  detects this no better than the old one. It belongs to the canonical-identifier work.

## Migration Plan

None. No stored data encodes the question.
