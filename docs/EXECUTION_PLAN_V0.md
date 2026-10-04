# Moirai — Execution Plan V0

## Objective

Validate and assemble the smallest useful wardrobe decision system before committing to a bespoke product architecture.

The sequence deliberately separates **style discovery**, **reuse audit**, **small real-world pilot**, **full inventory**, and **advanced capabilities**.

---

# Phase 0 — Define the target style

## Goal

Create a practical style profile that can be used as an evaluation reference for garments and recommendations.

## Work

Capture:

- desired overall appearance;
- professional vs casual style;
- preferred formality level;
- comfort requirements;
- colours liked/disliked;
- silhouettes/cuts liked/disliked;
- footwear preferences and tolerance;
- jewellery/accessory habits;
- common work/social contexts;
- trend tolerance;
- “looks good but not for me” patterns;
- examples/images that feel representative.

## Deliverable

`SIL_STYLE_PROFILE_V0.md`

## Exit criterion

We can explain what “this feels like Sil” means well enough to judge outfit suggestions.

---

# Phase 1 — Audit Wardrowbe before building

## Goal

Determine whether Wardrowbe can be used vanilla, forked, wrapped, partially reused, or rejected.

## Audit dimensions

### Product fit

- garment capture;
- metadata/tagging;
- shoes/accessories;
- outfit builder;
- item-first styling;
- weather/context;
- history;
- feedback/preferences;
- mobile usability;
- visual quality.

### Data and architecture

- database model;
- stable IDs;
- photo storage;
- API coverage;
- export/import;
- extension points;
- authentication/user model;
- model-provider abstraction;
- external-agent integration;
- self-hosting requirements.

### Moirai-specific gaps

- rich personal style profile;
- wardrobe audit lifecycle;
- explicit keep/restyle/repair/replace/exit states;
- travel capsule optimisation;
- shopping-gap reasoning;
- deterministic validation of owned items;
- complete accessories reasoning;
- explanation layer.

## Required output

For every relevant capability classify:

- **FIT**
- **REUSE**
- **MODIFY**
- **BUILD**
- **DROP**

Then make a platform decision:

1. deploy Wardrowbe vanilla;
2. deploy + configure;
3. fork and extend;
4. use only selected components/patterns;
5. reject and build a smaller alternative.

## Constraint

No bespoke implementation before this decision unless required to perform the audit itself.

---

# Phase 2 — Representative wardrobe pilot

## Goal

Test whether the system can recommend outfits the user would genuinely wear.

## Scope

Approx. 15–25 representative pieces, for example:

- 4–5 bottoms;
- 6–8 tops;
- 2–3 jackets/layers;
- 3–4 shoes;
- a small set of bags/jewellery/accessories.

Do not optimise the exact count. The point is representative combinatorial coverage, not completeness.

## Ingestion workflow target

Ideal interaction:

1. photograph item;
2. automatic visual analysis/tagging;
3. user reviews/corrects only what matters;
4. capture subjective data where needed;
5. move to next item.

The user should not be forced to manually populate a large schema for each garment.

## Validation scenarios

### Scenario A — item-first

> “I want to wear these trousers today. Give me three options.”

### Scenario B — work context

> “Normal workday; polished but not overdressed.”

### Scenario C — mixed context

> “Professional meeting followed by an informal dinner.”

### Scenario D — variety

> “Give me something I do not normally pick but that still feels like me.”

### Scenario E — feedback loop

Reject/approve/wear/rate an outfit and verify that later suggestions respond sensibly.

## Exit criterion

The user repeatedly sees suggestions and says, in substance:

> “Yes, I would actually wear that.”

If this fails, diagnose whether the problem is:

- style profile;
- garment metadata;
- recommendation logic;
- model quality;
- interface;
- missing context.

Do not solve a model problem with more architecture by default.

---

# Phase 3 — Full wardrobe inventory and audit

## Goal

Expand from the successful pilot to the real wardrobe and make it actionable.

The user expects the total photographed inventory to remain comfortably below ~99 items, so this should remain a small personal-data problem rather than a scale problem.

## Work

- ingest remaining wardrobe;
- resolve duplicates;
- add shoes/accessories;
- capture condition/comfort/favourite data;
- run wardrobe audit;
- identify underused but combinable items;
- identify redundant items;
- identify true functional gaps.

## Audit outputs

Each relevant item can receive one of:

- KEEP — core
- KEEP — secondary
- RESTYLE
- ALTER / REPAIR
- REPLACE
- EXIT
- UNCERTAIN

No automatic disposal decisions. The tool advises; the user decides.

---

# Phase 4 — Daily-use stylist

## Goal

Make Moirai useful frequently enough that it becomes the default decision aid rather than a catalogue that was fun to build once.

## Capabilities

- “What should I wear?”
- start from a selected garment;
- event/context-based outfit generation;
- weather-aware suggestions;
- recent-use awareness;
- explicit alternatives by formality/style;
- shoes/accessories integration;
- feedback capture with minimal friction.

## Preferred interaction

Conversational reasoning plus a visual interface to inspect/select real garments.

---

# Phase 5 — Travel mode

## Goal

Generate compact capsules, not independent day-by-day outfits that duplicate clothing unnecessarily.

## Inputs

- destination;
- dates;
- weather;
- planned activities/events;
- baggage constraints;
- laundry/reuse assumptions;
- comfort/formality requirements.

## Output

- minimal garment set;
- day/event outfit plan;
- reuse map;
- shoe/accessory plan;
- optional backup item(s);
- explicit explanation of why additional pieces are unnecessary.

---

# Phase 6 — Shopping intelligence

## Goal

Turn shopping into wardrobe optimisation rather than isolated acquisition.

## Workflow

1. identify real wardrobe gap;
2. define desired role/attributes;
3. inspect candidate item/photo/link;
4. measure redundancy and compatibility;
5. estimate how many useful combinations it unlocks;
6. recommend buy / consider / reject;
7. explain why.

Possible future output:

> “This jacket adds little because you already own two pieces covering the same role.”

or

> “This neutral mid-layer fills a genuine gap and works with five bottoms and three dresses you already own.”

---

# Model/resource strategy

Use available capacity before adding subscriptions.

Known current resource:

- Snapbuilder subscription with multiple model tiers/limits, including an unlimited Gemma 4 option as reported by the user.

Model selection should be task-based:

- **vision/tagging:** cheapest reliable multimodal option;
- **styling/reasoning:** model that best follows structured inventory and nuanced context;
- **validation:** deterministic code where possible rather than another model call.

The exact model matrix should be documented once the Wardrowbe audit exposes the integration points.

---

# Immediate next action

**Run the Wardrowbe FIT / GAP / REUSE / MODIFY / BUILD audit.**

Do not start full wardrobe photography yet.
Do not build a custom web app yet.
Do not choose Hermes or any agent runtime yet.
Do not introduce RAG/vector infrastructure yet.

After the audit, update this plan with the selected technical path and then begin Phase 0/Phase 2 in the most efficient order supported by the chosen platform.
