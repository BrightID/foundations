# How Aura Works

*And what we mean to build.*

---

## About this document

Aura's design currently lives in seven places that disagree with each other. This puts it in one place. This repo is the source of truth: the user-facing guides — getting verified, the player's guide, the integration guide — are generated from what's here rather than being sources of truth themselves.

Three markers appear throughout:

> **▸ In the code.** Verified against the running implementation, with a line reference.

> **▸ Not yet built.** Intended. Not implemented.

> **▸ Open.** Undecided.

Previous attempts failed by writing intentions as descriptions. The markers are the fix.

The scorer referenced throughout is [`scorer/verifications/aura.py`](https://github.com/Meta-Node/BrightID-Aura-Node/blob/master/scorer/verifications/aura.py) on `master` of the Aura node fork — 477 lines, the only scoring implementation we found in either org. `master` stops at October 2024; the `dev` branch is active (scorer last touched July 2025, repo through May 2026, differing from `master` only by a `modified` timestamp on impacts). All current development and testing runs on `dev`, at aura-test.brightid.org; production is not in use yet, and the `modified` change is still to be merged into it. Every mechanism cited below is identical on both branches.

---

## 1. What Aura is

Aura provides expert evaluations to applications — answers to questions no algorithm can settle. Is this account a real, distinct person? Is this restaurant good? The answers come from participants who have proven they answer accurately.

Participants answer questions honestly. Every layer above decides who to listen to, and how much. A participant has one job: be accurate and honest. Get it right and the people above you listen more; get it wrong and they listen less.

**Weighting belongs to the listener, never the speaker.** An answer doesn't change because someone decided to trust its author less. Any number of parties can weigh the same body of answers differently.

**New information can always change the answer.** Recomputation and revocation keep the present answer accurate rather than preserving past ones, and every incentive points at being accurate and honest — including updating when you learn you were wrong. Attacks will sometimes succeed; each one that breaks teaches the network what it looks like, and that attack doesn't work twice.

---

## 2. Evaluations

An evaluation is the only kind of fact in Aura. Scores, levels, and published answers are derived from evaluations by arithmetic.

An evaluation is one participant's answer to a domain's question about a subject, with a confidence in that answer.

In the BrightID domain, the question is "is this identifier this person's only account?", the answer is positive or negative, and confidence is one of four values.

> **▸ In the code.** Confidence is an integer 1–4, shown as Low / Medium / High / Very High (`aura:packages/domain/src/labels.ts`). One answer and one confidence per evaluator, per subject, per tier. Neither field is validated server-side — `confidence` is an unconstrained number in `web_services/foxx/brightid/schemas.js`.

> **▸ Not yet built.** A domain may ask several questions, with a confidence per answer. BrightID asks one.

### Confidence does two jobs

Confidence scales an evaluation's weight: a very-high-confidence answer counts four times a low-confidence one.

Confidence is also a gate. A team can require that a level be supported by evaluations at or above a given confidence, from evaluators at or above a given level. Without the gate, one powerful evaluator's offhand opinion could confer a level, and many weak opinions could add up to a confidence nobody holds.

### Teams set what a rating means

A team tells its participants what its ratings mean. If a three means you have direct knowledge, then giving threes casually makes you unreliable, and the people evaluating you will score you accordingly. Another team may draw the line elsewhere.

The conditions for giving a four are often subjective and unconfirmable, so they're a shared expectation rather than a rule the protocol enforces. Confidence is only comparable between evaluators who share a calibration; the team supplies it.

### Negative answers count for more

A negative evaluation carries four times the weight of an equivalent positive one. Negative information is how a compromised participant is stopped quickly, so it should propagate harder than praise.

> **▸ In the code.** `FLAGGING_MULTIPLIER = 4` (`aura.py:17`), applied in all four tiers (lines 117, 202, 287, 377). The 4× appears in no document — the Levels doc's formula gives sign as ±1.

> **▸ Not yet built.** The multiplier should be configurable. Four has felt roughly right for years, but there will be domains where it isn't.

The 4× belongs to score accumulation, and never touches energy. Energy moves only along positive manager evaluations (Section 5), so a negative evaluation of a manager is indistinguishable there from no evaluation at all: it changes that manager's score, not the energy reaching them.

### An evaluation is a standing claim

An evaluation states a position you currently hold, not something you did once. You remain responsible for it while it stands.

An evaluation carries the weight its author has now, not the weight they had when they gave it. Someone with no standing can evaluate freely; their answers affect nothing. The day someone above them vouches for them, everything they've said goes live at once, including answers given months earlier.

---

## 3. Who evaluates, and what gets evaluated

### Evaluators must be accountable

An evaluation is only worth something if manufacturing evaluators is hard. An evaluator is either:

- **A verified-unique human.** The existing BrightID guarantee.
- **An agent backed by a human or an organization.** Agents are copyable, so an agent can't be verified unique. Backing supplies what a counterparty needs: continuity, and an identified party who answers for it. A thousand agents achieve nothing when each must anchor to a scarce identity with standing to lose.

An agent is not a free-floating participant. It registers to a person or an organization, and ten agents from one person are not ten independent answers. Every payout goes to a person: you need an identity to be paid.

> **▸ Not yet built.** Agent registration, and the payout path to a backing identity.

### Subjects need an identifier, not an identity

A subject is whatever the domain asks about. In the BrightID domain that's a person, because the question is about people. In a restaurant domain it's a restaurant.

Joe's Pizza isn't a verified-unique human. It needs a stable identifier, so two evaluators can be sure they mean the same restaurant. Nothing more.

> **▸ In the code.** Today all subjects are identities. Evaluations are edges in the `connections` collection between `users/` documents, so a subject is necessarily a user. That is the BrightID domain's shape, not a design commitment.

Identifiers for non-person subjects are a task for the domain's own experts — check whether the thing is already listed before adding it — with incentives arranged accordingly.

---

## 4. Domains and tiers

A **domain** is a question space: what subjects can be evaluated, and what is asked about them.

Questions are append-only. A domain can add questions and turn them on and off, but never reset them, so an answer always refers to a question that still exists.

### A domain is a stack of tiers

Each **tier** says who is evaluated, what is asked about them, and who evaluates.

| Tier | Subjects | The question | Evaluated by |
|---|---|---|---|
| Subject | BrightIDs | is this a person's only account? | Players |
| Player | Players | does this player answer accurately? | Trainers |
| Trainer | Trainers | does this trainer back accurate players? | Managers |
| Manager | Managers | does this manager evaluate well? | Managers |

Each tier judges how well the tier below judges. A trainer looks for accurate players and takes responsibility for the ones they vouch for; if those players prove accurate, the trainer accrues standing, and if not, the trainer loses it.

The bottom tier's subjects are the domain's real subjects. Every tier above takes the tier below's evaluators as its subjects — a trainer is a subject in the trainer tier and an evaluator in the player tier.

The top tier evaluates itself. That's what Section 5 is about.

### Why three

Three evaluator tiers are fixed architecture, not the domain author's to choose. Three is the partition that lets someone contribute knowing only their own layer: players know how to answer the questions asked about subjects, trainers know who qualifies as a player on a team, managers understand how energy flows. Collapsing any two tiers forces one participant to hold two of those jobs at once. Keeping the amount you must understand small is what keeps the system open to new people.

A domain may simplify what its participants *see* — a narrow use case can present as flat — but the three tiers run underneath it.

Trainers carry a power the table doesn't show: an evaluation names the team or teams it counts for. Trainers therefore decide which players land on which teams, and can hold new players on a starter team until they have built a record.

### The manager row asks two things at once

Every other row in the table names what is being judged. The manager row can't, because a manager's evaluation of another manager does two jobs. It sets that manager's score — the force they carry when they evaluate trainers — and it directs energy, which is their influence over other managers (Section 5). One evaluation, two consequences, and the question it asks has never been written down.

Those are different competencies. Backing accurate trainers is hands-on assessment of people doing the work. Backing accurate managers is judgment about judgment. A manager can be good at one and poor at the other, and nothing in the system today tells them apart.

> **▸ Open.** Whether the manager tier asks one question or two, and whether the two should be evaluated separately — with energy directed by the peer judgment rather than the trainer-facing one. This would not add a tier; managers would remain the top tier, asked two questions instead of one. It sits upstream of how negative manager evaluations should work (Sections 2 and 5) and of what energy keys off, so it wants answering before either.

### Anyone can participate immediately

There is no gate on doing the work. Anybody can answer questions as a player, or start evaluating players or trainers.

What you can't do is matter before someone above you says you should. Until a participant in the tier above evaluates you, your standing is zero and your answers affect nothing. They're still recorded, and the moment you receive standing they all become live.

The Levels doc names this **level 0** — a provisional role. Anyone can hold one.

> **▸ In the code.** The scorer computes a score in a tier only for participants evaluated in that tier. One concrete unlock rule is shipped: you become eligible for Trainer evaluations after making your third evaluation.

Unlock rules in general will differ by domain. The constraint is firm — gate influence, not participation.

---

## 5. How scores are computed

Two calculations run in Aura. **Energy** handles the top tier, which evaluates itself. **Accumulation** handles every tier below, where evaluation flows strictly downward.

The top tier needs different treatment for two reasons. Managers evaluate managers, so trust can loop — I vouch for you, you vouch for someone who vouches for me — and left alone that inflates without bound. And someone has to be the origin: with no tier above to confer weight, the top tier must be seeded.

### Manager energy

Energy exists only among managers. It's the input to manager scores, and it never leaves the manager tier — everything below is computed from scores by arithmetic.

Energy starts at the team's owners and passes along positive manager-to-manager evaluations in proportion to confidence. A manager passes on everything they hold at each hop and retains nothing; what they have after a hop is only what flowed to them.

Team owners are no exception, and this catches people out. They hold energy at step zero and pass on all of it at the first hop like anyone else, so unless they vouch for each other they hold nothing from the first hop onward. Seeding is an origin, not a privilege — and setting up a team has to make that hard to get wrong.

When the hops finish, the settled energy is spent as weight. A manager's **score** is the sum, over the manager evaluations they received, of confidence × the evaluator's settled energy × sign. Holding energy gives you influence over other managers' scores; your own score comes from who evaluated you, weighted by *their* energy.

```
scale  = Σ confidence over the holder's positive manager evaluations
energy = holder.energy × confidence / scale          # per outgoing edge, each hop

manager score = Σ  confidence × evaluator.energy × (positive ? 1 : −4)
```

The number of hops is log(N) — the SybilRank result, corroborated by years of running it. The code's fixed 4 and the Levels doc's "2 or 4 depending on the size of the team" are that rule frozen at one scale.

> **▸ In the code.** `STARTING_ENERGY = 1000000` split evenly across two hardcoded `TEAM_OWNERS`; `HOPS = 4` (`aura.py:11–17`). The destination collection is emptied each hop — no retention, so a manager's final energy is the last hop's inflow alone. Manager scores at `aura.py:97–133`: the weight is the evaluator's energy, looked up from the settled `energy` collection.

> **▸ Not yet built.** Hops computed from the size of the population rather than fixed at 4.

### Energy is conserved

Energy is a closed system: a fixed quantity redistributed, never created. Whenever leakage has crept in before, we've regretted it — so the design goal is to make leaking impossible, with guardrails that are themselves elegant.

Today it leaks. A manager who holds energy but evaluates no other managers has no outgoing edge, so their share vanishes at the next hop. The cost isn't that manager's standing — their score comes from their evaluators, not their own energy. The cost is systemic: the total pool shrinks every hop, so every manager's score shrinks against level thresholds that are fixed absolute numbers. Levels get harder to reach for reasons unrelated to anyone's behaviour.

The fix is at the routing step. Energy is not sent to a manager with no outgoing edge; it redistributes among the holder's other edges as though that edge weren't there. A result we know we don't want is made impossible rather than discouraged.

> **▸ Not yet built.** The scorer still routes energy to dead ends.

### Accumulation below

A manager's score weights their evaluations of trainers; trainer scores weight player evaluations; player scores weight subject evaluations. Energy stays at the top. Scores are what travel down.

Each evaluation contributes:

```
impact = confidence × max(evaluator score, 0) × (positive ? 1 : −4)
score  = Σ impacts
```

An evaluator with a negative score contributes nothing — not a reduced amount, nothing — across their entire body of work at once. A negative score doesn't say a particular answer was wrong. It says that in totality this person should not be listened to, so nothing they've said is counted.

This is what lets a large attack be stopped by a single evaluation change. The protection against collateral damage isn't a cap on vouching — it's having more than one person above you. If your only backer is discredited, that's a fact about how you built your standing.

> **▸ In the code.** The `max(…, 0)` guard at `aura.py:201, 286, 376` (trainer, player, subject). The manager tier needs no guard — energy can't go negative. Added October 2024; commit message: *"fix bug where an evaluator with a negative score can have an impact."*

### Scores are derived, never stored as facts

A score is always recomputed from the evaluations currently standing and the configuration currently in force. Nothing is grandfathered. Copy a domain's configuration and participants exactly and its scores come out identical; change anything and they change with it.

This settles what to do when a parameter changes: recompute everything under the current parameters. A score is a present claim, not a historical record.

### The pipeline today

An evaluation enters through the node's API and lands within seconds as an edge in the `connections` collection — one per (evaluator, subject, tier), newest replacing oldest. The scorer runs in batches, roughly every twenty minutes: it reads the graph, computes energy, then scores, then levels, and writes results to the `verifications` collection. Applications consume from there — per user, `domains → categories → { score, level, impacts }`, one category per tier.

---

## 6. Levels

A score is a number whose magnitude means nothing outside its own team. A **level** is a claim that means something.

A level requires a minimum score **and** minimum evaluation requirements — how many evaluations, at what confidence, from evaluators at what level. Requirements combine with AND and OR.

> **▸ In the code.** Subject level 3 (`aura.py:387–390`):
> ```
> score >= 100000000 and (
>     count(impacts[* filter CURRENT.level >= 2 and CURRENT.confidence >= 3]) >= 1 or
>     count(impacts[* filter CURRENT.level >= 2 and CURRENT.confidence >= 2]) >= 2
> )
> ```
> The full shipped ladders: manager 1000 / 200000 and trainer 500000 / 1M — score only (`aura.py:122–126, 207–211`); player 1M / 2M / 3M and subject 10M / 50M / 100M / 150M, with requirements on the upper rungs. Subject reaches level 4, which demands level-3 evaluators.

The shipped ladders belong to the implicit single team (Section 13) — which is why absolute thresholds can coexist with "score magnitude means nothing outside its own team." When real teams exist, each defines its own. Current thresholds and requirements are starting points, not the destination; level definitions stay flexible enough to experiment with gates nobody has thought of yet.

The requirements are why levels are worth having. A score can be reached by accumulation; a level can't be reached without a specified quality of evidence from evaluators who have themselves earned standing.

**Level −1** exists for a negative score. Requirements don't apply to it; the score alone decides. **Level 0** is the provisional state — a role taken but not yet vouched for.

Manager and trainer levels carry no evaluation requirements, and that is a design choice. Every role is open to everyone, and evaluations from others are what graduate someone out of provisional.

One bootstrap rule: team owners — the Levels doc calls them captains; same role — are exempt from minimum evaluation requirements and reach their manager levels on score alone. Without the exemption a new team couldn't start — its founders can't satisfy requirements that need evaluators who don't exist yet.

---

## 7. Teams

A team is an independent scoring body. It has its own owners and its own standards, and computes its own scores from the evaluations it receives.

Multiple teams give the system resilience. If a team is captured or becomes unreliable, consumers stop weighting it and the rest keep working.

Teams differ by their standards, not their rosters. Two teams can share every trainer and still answer differently, because their level definitions differ: a low-confidence evaluation clears one team's bar and not the other's, with no decision by the evaluator.

Standards run in both directions. A team says what it takes to **receive** a level — score and evaluation requirements (Section 6) — and what it means to **give** a rating: if a three means direct knowledge, giving threes casually makes you unreliable there.

### Membership

Players never choose or join. A trainer on a team evaluates you and your evaluations start counting there.

The choice and the cap sit at trainer and above, on the **outbound** side: standing flows in uncapped from any number of teams, and you pick a handful to pass down through. Teams you don't pick get nothing from you. The point of the limit is scarcity — good evaluators become something teams compete for, which pushes teams to send real resources downstream and keeps them from converging on the same people. Five is the working number, and nobody has a derivation for it. It applies per trainer, so a player with several trainers can reach more than five teams that way.

The quantity of evaluations anyone gives is not the concern; confidence is the gate. A high-confidence evaluator working at scale is exactly what the system wants. Low confidence at scale is the problem, and Section 6's requirements are what catch it.

### Team owners

A team starts with one or more owners, who become its first managers and the origin of its energy. Owners add and remove owners by a two-thirds majority. Owners define the team's levels. Creating a team costs a fee.

How the team's starting energy is divided between the owners is the owners' own decision, taken by quorum. A quorum is likewise required to add or remove a member.

> **▸ Not yet built.** All of Section 7. The backend has two hardcoded owner keys acting as a single implicit team — no teams collection, no membership, no per-team scoring.

---

## 8. The league

An application asking Aura a question needs one answer, not a table of team scores. Two separate things produce that answer, and neither of them is a body that ingests what the teams put out.

Teams publish their own scores. The weights over those teams come from **crowd wisdom** — the people who rely on Aura's answers. A consumer takes the weights and the scores and combines them, and every consumer doing so arrives at the same result independently. There is no privileged copy of the answer. This is the same rule as everywhere else in Aura: the output is derived, so anyone holding the inputs can recompute it (Section 5). Crowd wisdom is the one input the system collects from outside itself; the fullest written account of it is [Decentralizing BrightID with Collective Intelligence](https://paragraph.com/@adamstallard/decentralizing-brightid-with-collective-intelligence).

The league is the organization, not the calculator. It is the metanode — where the final decision gets made, and what makes this one cohesive platform rather than a protocol with participants scattered around it. Somebody convenes the teams, offers one product — Aura answers a customer's questions, the way the NFL offers one product with many teams inside it — and answers for that product when it is wrong. The league contract in Section 9 is its concrete form: the money and membership rail.

How the league governs itself is deliberately out of scope. Today it's a few people making decisions; later it may have many participants, or defer to a market — possibly a prediction-market layer where timestamped answers are graded as reality reveals itself and payouts follow the results. Nothing below the league changes when that changes.

There is one league today, and network effects favor there staying one. The fork exists as an outlet, not a rival: if the league is ever corrupted, a new league is a new set of weights over an unchanged body of evaluations, and participants decide where to spend their time. That property requires the league to stay thin — teams and participants are independently addressable and never owned by the league, or forking stops being cheap. A league that is only an organization is already thin in that way.

> **▸ Open.** What crowd wisdom is, mechanically — the largest gap in this document. It sets every weight in the system, it is the only input gathered from outside Aura, and per Section 9 it is also what moves the money. Nothing anywhere describes how it is collected, who counts as the crowd, or why it resists capture. A weighting scheme can be provisional; a payout rule can't.

> **▸ Open.** Whether teams publish levels or scores. A level is a score **plus** the evaluation requirements of Section 6, so a level has already cleared the team's evidence bar where a raw score has not — which is the same reason Section 6 gives for levels being worth having at all. This decides the format every team must publish.

> **▸ Not yet built.** None of this exists in the code. No weights are collected, no combination is computed, and there is one implicit team to mix (Section 13).

---

## 9. Money

Applications that want answers fund the domain that produces them. The more resources in a domain, the more participants direct their time and attention to its questions, because payouts follow. Show me the incentive and I'll tell you the outcome: people put funds in the top because they want answers, and participants answer accurately and honestly because proving accuracy is what gets rewarded.

Rewards reach participants through their teams — scores provide a natural scale for dividing a team's share — and applications can direct attention where they need it, like paying teams to cover a region.

### The league contract

Money moves through a contract, one per domain. It accepts registration from teams, including a fee to enter a domain; it accepts funds from applications into that domain; and it applies crowd wisdom results to distribute those funds to teams. Team owners withdraw their team's funds and allocate them to participants by each participant's score within their tier.

Note what this is and isn't. The contract holds money and gates membership; it computes nothing about trust. Scores stay with teams and the mix stays with consumers (Section 8) — but the crowd wisdom that weights the mix is also what moves the money, which is the whole reason that mechanism has to be pinned down.

The detailed economics — fee structure, payout paths, the fee that creates a team (Section 7) — is a separate track on its own timeline. It doesn't gate this document.

> **▸ Not yet built.** The contract does not exist.

---

## 10. Time

An evaluation is a standing claim, so it keeps counting until its author changes it. That's usually right and sometimes wrong.

A restaurant review from five years ago wasn't incorrect; the restaurant has changed hands twice since. The reviewer has no new information and no reason to revisit their answer, so nothing about the evaluation itself will ever mark it as stale.

Decay is the answer, as an option a domain turns on rather than a default. The rate tracks how fast the *subject* changes: near zero for "is this a unique human", fast for a restaurant.

> **▸ Not yet built.** Decay appears nowhere in the code.

---

## 11. Starting a new domain

A new domain shouldn't start empty. We have the data, the connections, and the structure of who listens to whom; a new domain should be able to start from them.

What transfers cleanly: **level definitions** (editable), and **subjects** when the new domain asks about the same things. What never transfers: **scores** — they're recomputed from what you brought and how you configured it (Section 5).

A fork is a snapshot. It has no live link to its source and must be able to diverge fully. Domain isolation exists so that failure stays contained, and a live link would make a fork's integrity depend on its parent staying healthy — reintroducing exactly what isolation removes.

What the snapshot carries splits at the trainer line. **Managers and trainers copy wholesale**, along with manager evaluations: "this manager evaluates well" is a claim about judgment itself, and management skill transfers across domains. **Players don't.** An evaluation answers a specific question — a trainer said a player was good *at evaluating BrightIDs*, and carrying that into an insurance domain converts it into an opinion the trainer never gave. So the trainer decides which of their players come and at what starting level: everyone at level 1, or hand-picked mappings.

There's room for automation around that: standing offers, tranches, a team joining a new domain as a unit and bringing its structure with it.

> **▸ Not yet built.** Nothing about forking exists in the code.

---

## 12. Privacy

The rule Aura already lives by: **no information should be shared with anyone that doesn't already know it.** Verification comes from people who already know the person being verified — that's what makes it privacy-preserving rather than privacy-invading.

For domains, at least four things separate: the **trust structure** (who evaluates whom, and their standing), the **question set**, the **answers**, and the **interpretation** that turns answers into levels. A business may want the structure open and the rest closed — asking questions and getting answers without publishing either.

An opaque thing is trusted through whoever stands behind it. That's one mechanism serving two cases: an agent is trusted because accountable identities back it, and a private domain is trusted the same way. A domain that's completely dark — no visibility, no credible backers — isn't trustable.

> **▸ Open.** Nearly all of this. Which layers can be private, what a private domain must publish, and whether its outputs can be relied on by third parties.

---

## 13. What is not built

The design above is largely not implemented. The honest state:

| | Status |
|---|---|
| Evaluations, confidence, signed scores, 4× negatives | Built |
| Level requirements (AND/OR over count, confidence, evaluator level) | Built, for subject and player levels only |
| Manager energy, seeded and bounded | Built — even split, leaks at dead ends; hops hardcoded to 4 where the rule is log(N) |
| Teams | Not built. Two hardcoded owner keys act as one implicit team |
| Multiple domains | Not built. Evaluations carry a `domain` field; the scorer ignores it and pools everything under `"BrightID"` |
| Crowd-wisdom weights, combined consumer-side | Not built. No weights are collected and no combination exists |
| The league contract — registrations, fees, fund distribution | Not built |
| Forking a domain | Not built |
| Decay | Not built |
| Agent registration and payout to a backing identity | Not built |
| Non-person subjects | Not built. Subjects are `users/` documents |
| Per-evaluator cap | Not built, though the Levels document's formula has one |
| Configurable negative multiplier | Not built |
| Input validation on confidence and evaluation | Not built |

`master` stops at October 2024; `dev` carries dashboard-integration and signed-verification work through May 2026. The scoring algorithm itself is unchanged since October 2024 apart from a timestamp field. Most recent work has gone into the app and SDK.

---

## 14. Open questions

**The scoring math**

1. `log(N)` hops: which base, and N over which population — every participant in the domain, or the managers the iteration actually runs over.
2. Dead-end routing is settled (Section 5); the mechanics aren't. How the redistribution is computed, and what happens on the last hop, where by construction every node is a dead end.
3. The per-evaluator cap in the Levels doc's formula: dropped once level requirements existed, or never built?
4. Whether a negative manager evaluation should have any effect in the energy layer (Sections 2 and 5). Today it has none — the difference between withholding support and actively withdrawing it does not exist above the trainer tier.

**Design**

5. Whether the manager tier asks one question or two (Section 4): backing accurate trainers and backing accurate managers are different competencies carried by a single evaluation. Upstream of question 4 and of what energy keys off.
6. What crowd wisdom is, mechanically (Sections 8 and 9). It sets every weight, it's the only input from outside Aura, and it moves the money. The largest undefined thing in this document by a distance.
7. Levels or scores as the published unit (Section 8) — decides the format every team must publish.
8. Whether allocation by score inside a team is enforced by the league contract or is a convention team owners follow, and what stops an owner allocating otherwise.
9. The outbound number (Section 7). Five is the working convention, not a derivation.
10. What a decay rate attaches to — the domain or the individual question, since a domain could hold both a slow question and a fast one (Section 10).
11. What stops someone spinning up many identities to increase payouts (Sections 3 and 9).
12. Role unlock rules per domain, beyond the shipped third-evaluation rule (Section 4).
13. How identifiers for non-person subjects are created and deduplicated (Section 3).
14. The privacy model, essentially all of it (Section 12).

This list is not complete and isn't meant to be. Found a new one? Add it.

---

## How to engage

This document lives in BrightID/foundations. Changes go through an OpenSpec change proposal — see the repo's `openspec/` workflow.

- Something marked **▸ In the code** is wrong? Cite the line that proves it — those claims are meant to be falsifiable in minutes.
- Think something stated here is wrong? Say so on the PR. Don't write intentions as descriptions — that's how the previous attempts died.
- Can answer an open question? Answer it.

A decision is real only when a human states it — here, in an issue, or in the workroom. Nothing in this document is a decision until then.
