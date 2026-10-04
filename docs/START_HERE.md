# Moirai — START HERE

**Status:** reuse decision closed / style discovery next  
**Authority:** this document points to the current project baseline.  
**Current objective:** validate Wardrowbe as the operational wardrobe substrate while defining the explicit personal style authority that the Moirai reasoning layer will use.

## 1. Problem to solve

The user does not want a fashion hobby or a generic outfit generator. She wants to externalize a recurring decision problem:

- difficulty combining clothes already owned;
- tendency to use the same small subset repeatedly;
- accumulation of garments with little clarity about what should stay, leave or be replaced;
- difficulty buying garments that integrate with the existing wardrobe;
- uncertainty about what level/type of clothing fits a given context, event or trip;
- difficulty coordinating shoes, bags, jewellery and other accessories with the rest of the outfit.

The target outcome is a system that knows the real wardrobe, learns the user's style and constraints, and can answer concrete questions with recommendations using actual owned items.

## 2. Product thesis

Moirai should behave as a **personal wardrobe decision system**, not merely as a digital closet.

Its useful core is:

**visual inventory + structured garment data + personal style profile + contextual reasoning + feedback/history.**

The accepted architecture is a hybrid:

1. **Wardrowbe** as wardrobe system / visual UI / operational system of record for photos, inventory, edits, outfits, history and feedback.
2. **Moirai stylist layer** for rich contextual reasoning such as:
   - “What should I wear today?”
   - “I want to wear these trousers; what do I combine them with?”
   - “I have this work event and dinner afterwards.”
   - “Pack me for three days in the Canary Islands.”
   - “Should I buy this jacket?”
   - “Audit my wardrobe and tell me what no longer earns its place.”

An agent is not the database or primary wardrobe UI.

## 3. Current decisions

1. **Reuse before build.** Accepted and executed.
2. **Wardrowbe audit verdict: GO — deploy + configure upstream; no fork.** See `audits/WARDROWBE_FIT_GAP_AUDIT_V1_20261004.md`.
3. **Wardrowbe is the initial wardrobe substrate and system of record.**
4. **Do not fork Wardrowbe before a measured blocker.** Prefer configuration, external-agent logic, a small Moirai sidecar or an upstream contribution.
5. **AI Closet remains an architectural reference**, especially for real-item IDs, structured output, validation, history and feedback.
6. **Libre Closet remains a UX/reference candidate**, particularly for wardrobe capture and visual interaction.
7. **Hermes Agent is not required.** It remains a future runtime option only if it solves a measured need.
8. **A Wardrowbe MCP bridge is a reuse candidate, not yet infrastructure authority.** Audit/smoke-test it before adoption; direct REST remains available.
9. **No RAG/vector database/multi-agent system by default.** Most wardrobe facts are naturally structured data.
10. **Photos and structured text are complementary, not alternatives.** Photos remain the visual source; structured metadata supports reasoning and filtering.
11. **Do not clean the wardrobe before defining the target style.** Items must be evaluated against an intended future wardrobe, not against an undefined aesthetic.
12. **Do not inventory the entire wardrobe before validating the recommendation loop.** Start with a representative pilot of roughly 15–25 items.
13. **Recommendations must refer to real items.** Wardrowbe’s external authoring boundary validates real owned UUIDs.
14. **Free/open-source/self-hosted options are preferred.** Paid wardrobe/styling SaaS is out of scope unless later explicitly reconsidered.
15. **Pin upstream versions during validation.** Current audited candidate: Wardrowbe `v1.10.3` / `f9664a693eaaf65daa2a09ddedc57956c206eaa2`.

Full decision record: `DECISIONS_V0.md`.

## 4. Resources already available

The project should exploit already-available model capacity before adding new subscriptions.

The user currently has a **Snapbuilder subscription** with several model options and different usage limits, including a reported 4.6-class model, a limited video-use pool and **Gemma 4 with unlimited usage**, plus other previously available models. Exact provider/model names, quotas and API compatibility should be captured only when they become operationally relevant rather than guessed from memory.

The wardrobe is expected to be relatively small: the user anticipates **well under 99 photographed items**, so image ingestion should be operationally manageable and should not drive us toward unnecessary batch/enterprise architecture.

Wardrowbe can use an OpenAI-compatible AI endpoint internally, but Moirai is not required to couple its reasoning layer to that interface: internal vision/text capabilities can be disabled and delegated to an external agent.

## 5. What “good” looks like

Moirai should eventually be able to:

- understand and index the actual wardrobe from photographs;
- distinguish objective garment attributes from subjective user preferences;
- propose complete outfits from owned items;
- start from a chosen garment and build alternatives around it;
- account for occasion, weather, level of formality, comfort and personal preferences;
- incorporate shoes, bags, jewellery and accessories;
- avoid unnecessary repetition while still recognising true favourites;
- learn from worn/not-worn decisions and ratings;
- prepare compact travel capsules across multiple days and events;
- audit items as keep / restyle / repair / replace / exit / uncertain;
- identify functional wardrobe gaps;
- evaluate a potential purchase against the existing wardrobe before buying;
- explain the recommendation in understandable terms.

## 6. Closed frontier — Wardrowbe audit

The first reuse audit is complete.

Authority:

`docs/audits/WARDROWBE_FIT_GAP_AUDIT_V1_20261004.md`

Decision:

> **GO — deploy + configure Wardrowbe upstream; no fork.**

Key reasons:

- strong item/photo/outfit/history/feedback model;
- stable UUID item identity;
- built-in learning signals;
- OpenAI-compatible internal AI but optional AI capabilities;
- explicit external tagging and external outfit authoring;
- self-hosted PostgreSQL and image storage;
- MIT licence;
- active current maintenance;
- agent/MCP integration is an intended upstream use case rather than a hack.

## 7. Immediate next step

Create **`SIL_STYLE_PROFILE_V0`** before wardrobe cleanup and before judging recommendation quality.

The profile should establish, at minimum:

- desired everyday identity / impression;
- work style and levels of formality;
- speaking/event style;
- leisure/travel style;
- comfort constraints;
- preferred and rejected silhouettes;
- preferred/avoided colours and combinations;
- footwear reality;
- jewellery/accessory habits;
- “never wear” rules;
- tolerance for trends vs stable style;
- desired effort/time to get dressed;
- reference looks and anti-reference looks;
- uncertainty areas that should be learned from actual use rather than declared upfront.

Only after this authority exists should the pilot wardrobe be ingested and recommendations evaluated as “technically valid” vs “actually Silvia”.

## 8. Next validation sequence

1. `SIL_STYLE_PROFILE_V0`.
2. Focused audit/smoke test of candidate Wardrowbe MCP bridge.
3. Deploy pinned Wardrowbe candidate.
4. Ingest representative 15–25-item wardrobe.
5. Test native recommendations and item-first styling.
6. Capture acceptance/rejection and identify measured gaps.
7. Add the external Moirai stylist only where it materially improves results.
8. Expand to full wardrobe only after the recommendation loop passes.
