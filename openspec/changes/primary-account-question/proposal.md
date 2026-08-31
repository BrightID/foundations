## Why

The BrightID domain asks *"is this identifier this person's only account?"*

**It demands a disclosure BrightID has always refused to demand.** Showing up as an
individual in one place has never obliged anyone to account for every other place they
exist. "Only" makes that obligation the condition of being verified at all.

It is also unanswerable. Whether someone holds other BrightIDs is unobservable, and it can
change the day after you answer. A careful evaluator can never honestly give a 4.

And it punishes the people most worth having. Aura wants red-teaming — people who build
Sybils to probe the system and say so. Under "only", declaring an alternate account makes
the honest answer *no* for every account that person holds, including their real one.

**Now, because it is currently free.** The question text lives nowhere in the code or the
database — an evaluation stores a domain and a category, and the scorer never reads the
question. Production is not in use; all real data is on the test node. Today this is a
wording change with no migration and no history to rewrite. Once the node reaches
production, "only" becomes the meaning of every rating in the system, and changing it costs
a data migration and an argument.

## What Changes

- **§2** — the BrightID domain's question becomes *"is this the account to verify for this
  person?"*
- **§2** — one sentence stating that the evaluator is not being asked whether other
  accounts exist.
- **§2**, calibration — one clause noting that the two answer directions are not
  symmetric.
- **§4**, tier table, Subject row — matches §2.
- **§10**, decay — the illustration assumes the old question's near-static answer.
- **§12** — four words, so the privacy claim stays true when strangers may answer.

No change to scoring, thresholds, level requirements, storage, or the API. Not breaking.

## Capabilities

### New Capabilities

None. This change sets `skip_specs: true`.

This repo holds one evolving narrative document, not a collection of discrete capability
specs — as its README states, and the reason it is referenced as a compass rather than as
an OpenSpec store. Creating a capability spec tree here to satisfy validation would invent
structure the repo does not have.

### Modified Capabilities

None.

## Impact

- `how-aura-works.md` §2, §4, §10, §12. Roughly two sentences of net new prose.
- **No code.** The question text is absent from the node and from the evaluation record;
  the scorer filters by category and never reads the question.
- Client copy in the Player and front-end apps carries this wording where it surfaces the
  question, and will need the same edit. Out of scope for this change.
- **Unblocks** the canonical-identifier work — how unclaimed subjects are created, claimed,
  and deduplicated depends on what this question means.
