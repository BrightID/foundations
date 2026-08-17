# foundations

The authoritative description of how BrightID works and what we mean to build.

BrightID is the overarching project.
Aura — evaluations, tiers of evaluators, energy-weighted scoring, teams, the league — is a core, inseparable facet of BrightID, extending its goals.

This repo holds two different kinds of content, governed differently:

- **Big-picture docs** — currently [`how-aura-works.md`](how-aura-works.md).
  The mechanism and intent for how the whole system works, marked throughout with **▸ In the code**, **▸ Not yet built**, and **▸ Open**.
  Every change requires an [OpenSpec](https://github.com/Fission-AI/OpenSpec) change proposal through this repo's `openspec/` workflow — no direct edits.
  Note: as of this writing `how-aura-works.md` still reads Aura-first (BrightID mentioned only as one domain instantiation of the general Aura protocol) — reframing it BrightID-first is expected to be one of the first OpenSpec changes here, not something already done by moving the file.
- **History docs** — currently [`aura-historical-context.md`](aura-historical-context.md).
  Curated indexes of external design docs and links, evolving as the project does — written more like a historian's record than a governed decision.
  Loosely governed: small additions can land directly; larger revisions may go through OpenSpec at the maintainer's judgment, but it isn't required the way it is for big-picture docs.
  More history docs may be added later (e.g. a BrightID-history equivalent of this index) — none exist yet beyond the Aura one.

This repo is an evolving narrative.
Other BrightID repos should point at this repo as a compass in their own `openspec/config.yaml` `context:` field (a plain pointer, shown to the agent as background).

For specs and ADRs shared across multiple BrightID repos, see [BrightID/shared-specs](https://github.com/BrightID/shared-specs) instead.
