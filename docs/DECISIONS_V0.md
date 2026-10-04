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

---

## D-016 — Wardrowbe is the initial wardrobe substrate

The formal Wardrowbe audit closed with:

> **GO — deploy + configure upstream; no fork.**

Wardrowbe is accepted as the initial system of record for:

- wardrobe items and photos;
- stable item identity;
- outfits;
- history and feedback;
- baseline preferences and learning signals;
- weather/basic recommendation context.

Evidence: `docs/audits/WARDROWBE_FIT_GAP_AUDIT_V1_20261004.md`.

**Status:** accepted.

---

## D-017 — Do not fork Wardrowbe before measured need

Moirai-specific intelligence should live outside Wardrowbe wherever practical and use supported API/external-agent write surfaces.

A fork requires evidence that a required capability cannot reasonably be achieved through:

1. configuration;
2. external-agent logic;
3. existing extension fields;
4. a small Moirai sidecar contract;
5. an upstream contribution.

This preserves upgradeability and avoids owning a generic wardrobe platform unnecessarily.

**Status:** accepted.

---

## D-018 — Moirai owns semantic intelligence; Wardrowbe owns wardrobe operations

The architectural boundary is now accepted rather than provisional:

**Wardrowbe owns**

- photos;
- item CRUD;
- identifiers;
- outfits;
- wear/wash/history;
- feedback;
- baseline learning;
- generic visual wardrobe UX.

**Moirai owns**

- explicit personal style authority;
- rich conversational context;
- wardrobe audit decisions;
- travel capsule optimisation;
- shopping-gap reasoning;
- candidate purchase evaluation;
- any durable Moirai-specific semantics that do not belong in a generic wardrobe product.

**Status:** accepted.

---

## D-019 — Existing MCP bridge is a candidate, not yet a dependency

`mcp-wardrowbe` and related Wardrowbe MCP patterns show that the agent bridge can be reused rather than invented from scratch.

However, the bridge must pass a focused audit/smoke test before becoming infrastructure authority. Direct Wardrowbe REST access remains the fallback and prevents adapter lock-in.

**Status:** accepted.

---

## D-020 — Pin upstream during the pilot

Do not deploy unpinned `latest` images for the Moirai validation loop.

Initial audited candidate is Wardrowbe `v1.10.3` / commit `f9664a693eaaf65daa2a09ddedc57956c206eaa2`. If a newer version is selected before deployment, explicitly review its delta first.

Back up PostgreSQL and image storage independently of the application.

**Status:** accepted.
