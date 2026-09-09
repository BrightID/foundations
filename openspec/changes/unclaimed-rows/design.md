## Context

See `proposal.md` — Why. One constraint shapes everything below: the behaviour is already in the node, so this change describes a mechanism rather than inventing one, and the only new build is claiming and the re-point cascade.

## Goals / Non-Goals

**Goals** — words for a row nobody has claimed; one stated mechanism by which a row becomes the one everyone means; a stated answer for evaluations made before a claim.

**Non-Goals** — the standing threshold that makes a claim or a conferral stick (stays open); backing ("this is mine"); withdrawing an evaluation, which is a missing primitive of its own; client copy.

## Decisions

**D1. An unclaimed row is a first-class subject.** It is evaluated, scored, and levelled exactly like a claimed one. *Rejected:* hold evaluations in a staging area until someone claims — it loses seamless arrival and would need a scorer change, where today the scorer never opens the users collection.

**D2. Anyone creates a row by evaluating an identifier that has no row.** No permission, no fee, no cooperation from the subject. *Rejected:* a curated registry or subject consent to mint — a domain whose subjects cannot consent, like restaurants or books, could never start.

**D3. Two triggers, one mechanism.** Something that can hold a key becomes canonical by binding one. Something that never can becomes canonical when evaluators with standing converge on it. Both fire the same re-point cascade. *Rejected:* an owner-designation path for non-person subjects — the protocol has no way to know who owns Joe's Pizza, and appointing one imports an authority Aura does not have.

**D4. Evaluations made before a claim survive it unchanged.** They were answers about an identifier, not about a key holder, and the subject did not change. *Rejected:* void or re-confirm on claim — it makes claiming adversarial and destroys the standing the claim rests on.

**D5. A duplicate is never downvoted. It withers by not being chosen.** *Rejected:* a negative evaluation meaning "this is a duplicate" — it spends the 4× negative multiplier on a row that is not wrong, and folds a bookkeeping question into the domain's own question.

**D6. *Canonical* and *primary* are different words for different things.** Canonical is about identifiers: which row do we all mean; everything needs one. Primary is about people: which account is a given unique human; only humans, only in the BrightID domain. *Rejected:* one word for both — it collides exactly where it matters, since agents and companies get canonical identifiers and never primaries.

## Risks / Trade-offs

- **The threshold is the whole attack surface, and this change does not set it.** Too low, and a subset of a row's evaluators binds it to their own key and walks off with everyone else's standing; the same number governs conferral, so a restaurant's owner with a few well-placed friends designates his own row. It wants a majority of standing, not a headcount. Stated as **▸ Open**.
- **A keyless row may lock someone out.** `verifyUserSig` derives a signing key from the BrightID itself *when no row exists*; creating the row removes that fallback. That is exactly what makes an unclaimed row inert, and it may close the door on someone who generated a BrightID and never transacted. Stated as **▸ Open**, with claiming as the candidate way back.
- **Claiming needs a new operation, not a flag.** *Add Signing Key* requires the row's own signature and *Social Recovery* a pre-designated recovery set, so neither reaches a row that arrived unclaimed. Worth knowing before anyone scopes the build off "the primitive already exists": the row primitive does exist; the claim path does not.
- **Unclaimed rows carry inflated scores,** and re-pointing has no undo while withdrawing an evaluation does not exist. The first is disclosed in the document and not solved; the second is a named gap of its own.

## Proposed document text

Plain text, old → new. Nothing else in the document changes.

**§3 — replace the closing paragraph of *Subjects need an identifier, not an identity*.**

OLD:

> Identifiers for non-person subjects are a task for the domain's own experts — check whether the thing is already listed before adding it — with incentives arranged accordingly.

NEW:

> Identifiers for non-person subjects are a task for the domain's own experts, with incentives arranged accordingly.

**§3 — insert two new subsections immediately after that paragraph, before the `---` that ends the section.**

NEW:

> ### Two questions about a row, not one
>
> An identifier can be asked two independent questions.
>
> *Can it act?* The row holds a key, or it does not. A row holding no key is **unclaimed**: it can be evaluated and it earns a score and a level like any other subject, and nobody can operate as it.
>
> *Is it the one?* The row is **canonical** — the one identifier everyone points at for this subject — or it is a duplicate of the row that is. Everything evaluated needs a canonical identifier: people, agents, companies, restaurants.
>
> A restaurant only ever moves on the second question; it can never hold a key. A person moves on both at once, which is why the two look like one.
>
> Canonical is not primary. *Canonical* is about identifiers: which row we all mean. *Primary* is about people: which account is a given unique human — what the BrightID domain asks. Agents and companies get canonical identifiers and never primaries. That is a different predicate, not a lower standing.
>
> ### Rows arrive unclaimed
>
> Anyone makes a row by evaluating an identifier that does not have one. No permission, no fee, no cooperation from the subject: ten people can point at a woman who has never heard of Aura, or at Joe's Pizza.
>
> > **▸ In the code.** Evaluating a subject with no row inserts one with empty `signingKeys` (`web_services/foxx/v6/db.js`, in `evaluate`). `verifyUserSig` (`operations.js`) reads the row's keys, falling back to a key derived from the BrightID only when no row exists, so a keyless row can sign nothing and is inert by construction. The scorer never opens the users collection — its only mentions of `users` build edge ids from connections (`scorer/verifications/aura.py`) — so it scores and levels an unclaimed row on the next batch like any other.
>
> A row becomes canonical by one of two events. **The subject claims it** — something that can hold a key binds one and takes control. Or **evaluators with standing converge on it** — for something that can never hold a key, their standing is what makes it the one.
>
> Either event fires the same cascade: everyone sitting on another row for the same subject re-points with one tap, because they are already that row's evaluators. **A duplicate is never downvoted. It withers by not being chosen.** Nothing about it is false; it is simply not the one.
>
> Evaluations made before a claim survive it unchanged. They were answers about an identifier, not about a key holder, and the subject did not change.
>
> An unclaimed row accumulates positives while shielded from the negatives a live account attracts, so its score reads higher than the same subject's would once claimed. That is the price of letting evaluators work without the subject's cooperation.
>
> > **▸ Not yet built.** Claiming, and the re-point cascade. Neither existing way to set a row's keys reaches an unclaimed row: *Add Signing Key* requires a signature from the row itself, and *Social Recovery* requires connections the subject designated in advance. A row that arrived unclaimed has neither.
>
> > **▸ Open.** What weight of standing makes a row canonical, by claim or by conferral. Too low and a subset of a row's evaluators binds it to a key of their own and leaves with everyone else's standing; the same number governs conferral, so a restaurant's owner with a few well-placed friends designates his own row. It wants a majority of standing, not a headcount.
>
> > **▸ Open.** Whether creating an unclaimed row can lock out the holder of a BrightID that has never transacted, since the keyless row removes the fallback that derives a signing key when no row exists — and whether claiming is the way back.

**§13 — insert one row in the status table, after the *Non-person subjects* row.**

OLD:

> | Non-person subjects | Not built. Subjects are `users/` documents |

NEW:

> | Non-person subjects | Not built. Subjects are `users/` documents |
> | Claiming a row, and the re-point cascade | Not built. A row that arrived unclaimed can neither sign for itself nor be recovered |

**§14 — replace open question 13.**

OLD:

> 13. How identifiers for non-person subjects are created and deduplicated (Section 3).

NEW:

> 13. What weight of standing makes a row canonical (Section 3), by claim or by conferral. How rows are created and how duplicates re-point are settled; the threshold is the attack surface and is not.

## Migration Plan

None. No stored data changes, and the rows this change describes already exist.
