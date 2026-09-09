## Why

The document has no vocabulary for a subject nobody has claimed. §3 says a subject needs "a stable identifier, so two evaluators can be sure they mean the same restaurant. Nothing more" — then leaves creation and deduplication to "the domain's own experts" and files the rest as open question 13.

The gap is load-bearing, because what it fails to describe is already how the system works: evaluating an identifier that has no row creates the row, with no signing keys. Ten people pointing at a woman who has never heard of Aura is a supported operation today, and so is Joe's Pizza. The document cannot say that, cannot say what happens when she later takes control of the row, and cannot say which of two rows for the same restaurant everyone should mean.

So everyone building on it is guessing — whether a client creates a row or searches first, and whether evaluations made before a claim stand or are void. That last one is the difference between an inviting system and a hostile one.

## What Changes

**§3** gains two short subsections: the two questions asked of a row (*Can it act?* and *Is it the one?*, giving the words **unclaimed** and **canonical**, and separating *canonical* from *primary*), and how a row is created, becomes canonical, and re-points its duplicates. **§13**'s status table gains one row. **§14** question 13 narrows: creation and the re-point mechanism are settled here; what weight of standing makes a row canonical is not, and stays open alongside a possible lockout.

No change to scoring, thresholds, levels, storage, or the API. Not breaking.

## Capabilities

### New Capabilities

None. This change sets `skip_specs: true`, for the reason the previous change did: this repo holds one evolving narrative document, not discrete capability specs.

### Modified Capabilities

None.

## Impact

- `how-aura-works.md` §3, §13, §14. Roughly 280 words of net new prose.
- **The node.** No scorer change: the scorer never opens the users collection, so an unclaimed row already receives a subject score and a level on the next batch exactly like a live account. Claiming does need a new operation — *Add Signing Key* requires the row's own signature and *Social Recovery* requires a pre-designated recovery set, so neither reaches a row that arrived unclaimed. Additive either way; nothing existing changes meaning.
- **The Player and front-end apps.** Subject creation acquires a stated rule: search for a canonical row first, create only if none is found, never present an unclaimed row as an absent one. Re-pointing needs a one-tap affordance.
- **Anything consuming a subject level.** A level on an unclaimed row means what a level on a claimed one means, so consumers should not filter on key-holding — but they inherit a disclosed limit: unclaimed rows accumulate positives while shielded from the negatives a live account attracts, so their scores read high.
- **Unblocks** backing — agent registration, company attribution, declared alternates — which all need a canonical identifier before there is anything to back.
