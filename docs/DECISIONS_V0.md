# Moirai — Decisions V0

This document captures the initial decisions already agreed so future work does not silently reopen them without evidence.

## D-001 — The problem is decision support, not fashion discovery

Moirai is intended to reduce day-to-day clothing decision friction using the user’s actual wardrobe and context.

It is not primarily:

- a fashion inspiration app;
- a trend tracker;
- an ecommerce surface;
- a social wardrobe network.

**Status:** accepted.

---

## D-002 — Reuse before bespoke build

Before creating a custom web app, agent platform or wardrobe database, audit existing open-source options.

The first candidate is Wardrowbe.

**Status:** accepted.

---

## D-003 — Hybrid architecture is preferred over “agent does everything”

Current working model:

- visual application / wardrobe service = inventory system of record;
- reasoning layer / stylist agent = contextual interpretation and recommendations.

An agent should not be forced to become the photo gallery, CRUD UI and primary persistence layer.

**Status:** accepted as working hypothesis; subject to audit evidence.

---

## D-004 — Photos and structured metadata are both required

Images are retained as the visual source of truth. Structured fields are used for filtering, reasoning, validation and analytics.

Do not choose “photo-only” or “text-only”.

**Status:** accepted.

---

## D-005 — Define target style before wardrobe cleanup

Do not classify garments as obsolete or removable before establishing the style and functional wardrobe being aimed for.

Otherwise the audit has no valid reference frame.

**Status:** accepted.

---

## D-006 — Pilot before full inventory

Do not photograph and classify the entire wardrobe before validating the recommendation loop.

Start with a representative sample of roughly 15–25 items covering:

- bottoms;
- tops;
- jackets/layers;
- shoes;
- a small accessory sample.

If the user finds recommendations genuinely wearable, then expand to the full wardrobe.

**Status:** accepted.

---

## D-007 — Real-item validation is mandatory

Any recommendation claiming “wear X” must reference a garment that actually exists in the inventory.

Suggested future purchases must be explicitly separated from owned items.

A deterministic validator is preferred between model output and user-facing recommendation.

**Status:** accepted.

---

## D-008 — Learning should come from real usage, not only declared preferences

The system should learn from:

- worn vs not worn;
- liked/disliked;
- comfort;
- repeatability;
- rejected combinations;
- actual usage frequency.

Declared style preferences are important but not sufficient.

**Status:** accepted.

---

## D-009 — Shopping follows wardrobe gaps

The preferred flow is:

**existing wardrobe → functional gap → desired garment role → candidate evaluation → purchase decision**

not:

**see garment → buy → attempt integration later**.

**Status:** accepted.

---

## D-010 — Travel is an optimisation problem, not a packing checklist

Travel mode should optimise across days/events and reuse garments rather than generating independent outfits that produce an oversized suitcase.

**Status:** accepted.

---

## D-011 — Accessories belong in the actual wardrobe model

Shoes, bags, belts, jewellery and relevant accessories are not an afterthought. They should be representable and available to the recommendation engine.

**Status:** accepted.

---

## D-012 — Free/open-source/self-hosted first

The initial solution should avoid paid wardrobe/styling SaaS.

Already-available model subscriptions/capacity may be used where useful, but do not add a new paid dependency without a demonstrated gap.

**Status:** accepted.

---

## D-013 — No premature AI infrastructure

Do not introduce by default:

- vector databases;
- RAG pipelines;
- multi-agent orchestration;
- fashion-specific embedding services;
- large-scale ingestion architecture.

Use structured data and a capable multimodal/reasoning model first. Add infrastructure only when a measured limitation requires it.

**Status:** accepted.

---

## D-014 — Hermes Agent is optional and deferred

Hermes Agent may later host the stylist/reasoning layer, but it is not the starting architecture and is not needed to validate the product.

**Status:** accepted.

---

## D-015 — Name and semantic model

Project name: **Moirai**.

Optional semantic names:

- **Clotho** — ingest / wardrobe creation;
- **Lachesis** — styling / planning / allocation;
- **Atropos** — declutter / replace / exit.

These are naming semantics only. Do not create artificial service boundaries merely to match the mythology.

**Status:** accepted.
