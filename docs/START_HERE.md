# Moirai — START HERE

**Status:** discovery / product definition / reuse audit  
**Authority:** this document points to the current project baseline.  
**Current objective:** determine the smallest reliable system that can act as a personal wardrobe advisor without prematurely building a bespoke platform.

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

The working architectural hypothesis is a hybrid:

1. **Wardrobe system / visual UI** for photos, inventory, edits, browsing, outfits and history.
2. **Stylist agent / reasoning layer** for contextual requests such as:
   - “What should I wear today?”
   - “I want to wear these trousers; what do I combine them with?”
   - “I have this work event and dinner afterwards.”
   - “Pack me for three days in the Canary Islands.”
   - “Should I buy this jacket?”
   - “Audit my wardrobe and tell me what no longer earns its place.”

An agent is therefore not assumed to be the database or primary wardrobe UI.

## 3. Current decisions

1. **Reuse before build.** We will audit open-source alternatives before creating bespoke software.
2. **Wardrowbe is the first base candidate to audit.** It is not yet adopted.
3. **AI Closet is an architectural reference**, especially for real-item IDs, structured output, validation, history and feedback.
4. **Libre Closet is a UX/reference candidate**, particularly for wardrobe capture and visual interaction.
5. **Hermes Agent is not the starting point.** It may become a future runtime for the stylist/reasoning layer if needed.
6. **No RAG/vector database/multi-agent system by default.** Most wardrobe facts are naturally structured data.
7. **Photos and structured text are complementary, not alternatives.** Photos remain the visual source; structured metadata supports reasoning and filtering.
8. **Do not clean the wardrobe before defining the target style.** Items must be evaluated against an intended future wardrobe, not against an undefined aesthetic.
9. **Do not inventory the entire wardrobe before validating the recommendation loop.** Start with a representative pilot.
10. **Recommendations must refer to real items.** The system must not hallucinate garments as if they were owned.
11. **Free/open-source/self-hosted options are preferred.** Paid wardrobe/styling SaaS is out of scope unless later explicitly reconsidered.

## 4. Resources already available

The project should exploit already-available model capacity before adding new subscriptions.

The user currently has a **Snapbuilder subscription** with several model options and different usage limits, including a reported 4.6-class model, a limited video-use pool and **Gemma 4 with unlimited usage**, plus other previously available models. Exact provider/model names and quotas should be captured only when they become operationally relevant rather than guessed from memory.

The wardrobe is expected to be relatively small: the user anticipates **well under 99 photographed items**, so image ingestion should be operationally manageable and should not drive us toward unnecessary batch/enterprise architecture.

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

## 6. Immediate next step

Perform a **Wardrowbe fit/gap/reuse audit** against the requirements in `PRODUCT_VISION_V0.md`.

The required decision output is:

- **FIT** — already solves it sufficiently;
- **REUSE** — useful component/pattern without major change;
- **MODIFY** — viable with contained extension;
- **BUILD** — genuinely missing and worth implementing;
- **DROP** — feature or complexity we do not need.

Only after that audit should we decide whether to deploy Wardrowbe vanilla, fork it, wrap it, reuse components, or build a smaller alternative.
