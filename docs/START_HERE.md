# Moirai — START HERE

**Status:** style authority closed for V0 / MCP bridge decision closed / deployment next  
**Authority:** this document points to the current project baseline.  
**Current objective:** deploy the pinned Wardrowbe pilot, connect the selected MCP bridge with correct external-authoring semantics, and validate the recommendation loop on 15–25 real garments before expanding the wardrobe or shopping.

## 1. Problem to solve

The user does not want a fashion hobby or a generic outfit generator. She wants to externalize a recurring decision problem:

- difficulty combining clothes already owned;
- tendency to use the same small subset repeatedly;
- accumulation of garments with little clarity about what should stay, leave or be replaced;
- difficulty buying garments that integrate with the existing wardrobe;
- uncertainty about what level/type of clothing fits a given context, event or trip;
- difficulty coordinating shoes, bags, jewellery and other accessories with the rest of the outfit.

The target outcome is a system that knows the real wardrobe, learns the user's style and constraints, and can answer concrete questions with recommendations using actual owned items.

## 2. Accepted architecture

Moirai is a **personal wardrobe decision system**, not merely a digital closet.

Useful core:

**visual inventory + structured garment data + personal style authority + contextual reasoning + feedback/history.**

Boundary:

1. **Wardrowbe** = wardrobe system / visual UI / operational system of record for photos, inventory, identifiers, edits, outfits, wear history and feedback.
2. **Moirai stylist layer** = semantic reasoning such as:
   - “What should I wear today?”
   - “I want to wear these trousers; what do I combine them with?”
   - “I have this work event and dinner afterwards.”
   - “Pack me for three days.”
   - “Should I buy this jacket?”
   - “Audit my wardrobe and tell me what no longer earns its place.”

An agent is not the database or primary wardrobe UI.

## 3. Closed decisions

### Wardrowbe substrate

Audit authority:

`docs/audits/WARDROWBE_FIT_GAP_AUDIT_V1_20261004.md`

Verdict:

> **GO — deploy + configure Wardrowbe upstream; no fork.**

Pilot pin:

`Anyesh/wardrowbe v1.10.3`  
`f9664a693eaaf65daa2a09ddedc57956c206eaa2`

### Personal style authority

Issue #2 is closed.

Authorities:

- `docs/style/SIL_STYLE_PROFILE_V0.md`
- `docs/style/STYLE_EVALUATION_SCENARIOS_V0.md`

The profile is sufficient for a real-wardrobe pilot. It remains intentionally learnable rather than pretending every preference is known.

Important V0 product rules include:

- distinguish **dislike** from **“I would not think of combining this”**;
- distinguish **dislike** from **“I forget to use this while dressing”**;
- treat work/casual as a formality/polish continuum unless real-use evidence disproves it;
- calibrate whether ordinary workwear should be one step more polished through real A/B outfit choices, not assumptions;
- recommendations must use actual owned-item IDs.

### MCP bridge

Audit authority:

`docs/audits/WARDROWBE_MCP_BRIDGE_AUDIT_V1_20261004.md`

Verdict:

> **REUSE WITH THIN EXTERNAL-AUTHORING ADAPTER.**

Preferred pilot bridge:

`jansitarski/wardrowbe-mcp@f2f172d6ae309c9ec9478ee75444642ffe611baa`

Use it for:

- garment browsing/search;
- multimodal image access;
- representative-item ingest;
- external tagging queue;
- tag/description write-back;
- wear/wash/archive;
- outfit/history/analytics reads;
- feedback.

Do **not** persist Moirai recommendations using its current `wardrowbe_create_outfit` tool, because that maps to Wardrowbe Studio/manual authoring and creates synthetic accepted feedback.

Moirai recommendations must use Wardrowbe's dedicated external-authoring surfaces:

- `POST /api/v1/outfits/suggestions`;
- `POST /api/v1/pairings/item/{source_item_id}`.

Add only this missing surface through two thin MCP tools, a tiny sidecar/upstream contribution or direct REST. Do not build a broad custom MCP or fork Wardrowbe.

## 4. Privacy / storage boundary

`b32majus/Moirai` is currently **public**.

Therefore never commit to this repository:

- personal wardrobe photos;
- body/reference photographs;
- Wardrowbe database dumps containing personal data;
- tokens, secrets or `.env` values;
- other private wardrobe data.

The repo may contain product docs, architecture and non-secret configuration templates.

Wardrowbe PostgreSQL + image storage must be persistent and private, under the user's control, with backup defined before full wardrobe ingestion.

## 5. What “good” looks like

Moirai should eventually be able to:

- understand and index the actual wardrobe from photographs;
- distinguish objective garment attributes from subjective preference;
- propose complete outfits from owned items;
- start from a chosen garment and build alternatives around it;
- account for occasion, weather, formality, comfort and personal preferences;
- incorporate shoes, bags, jewellery and accessories;
- avoid unnecessary repetition while recognising genuine favourites;
- learn from worn/not-worn decisions and ratings;
- prepare compact travel capsules across multiple days/events;
- audit items as keep / restyle / repair / replace / exit / uncertain;
- identify functional wardrobe gaps;
- evaluate a purchase against the existing wardrobe before buying;
- explain recommendations without fashion jargon.

## 6. Current frontier — Issue #3

Open issue:

**#3 — Wardrowbe pilot — MCP bridge audit + pinned deployment**

The MCP audit portion is closed. Remaining execution frontier:

1. deploy pinned Wardrowbe;
2. create private persistent PostgreSQL + image storage;
3. define backup/restore path;
4. deploy/pin the selected MCP bridge;
5. add the minimal external-suggestion / external-pairing adapter;
6. ingest only 15–25 representative real garments;
7. tag/correct those garments;
8. run the V0 style evaluation scenarios;
9. capture acceptance/rejection, actual wear and formality calibration;
10. document measured gaps before any custom product expansion.

## 7. Pilot garment sample

Do not ingest the entire wardrobe first.

The initial 15–25 items should deliberately include a useful spread of:

- slim/skinny, tailored/no-pleat, chino and jeans bottoms;
- colourful tops, blouses and knits;
- relaxed blazer;
- short jacket and/or cardigan;
- ankle boots;
- sandals;
- modern smart sneakers;
- a few belts/accessories if available.

The sample should include both heavily-used garments and items that might be good but are currently underused because combination confidence is low.

## 8. Evaluation sequence

Run at least these scenarios against real inventory:

1. ordinary professional day;
2. casual-professional crossover;
3. professional without blazer;
4. guided colour expansion;
5. accessory-memory assist;
6. casual/weekend;
7. work-to-dinner;
8. conference/speaking;
9. habitual work baseline vs one-step-more-polished alternative.

Do not infer a wardrobe gap from one failed outfit. A purchase candidate should emerge only after repeated evidence that a useful role cannot be filled from existing items.

## 9. Explicit non-goals for this frontier

Do not add yet:

- full wardrobe purge;
- shopping automation;
- vector DB / RAG;
- multi-agent orchestration;
- bespoke wardrobe frontend;
- Wardrowbe fork;
- travel optimiser;
- production-scale ingestion.

Those are later only if pilot evidence justifies them.

## 10. Source of truth

Detailed decisions: `docs/DECISIONS_V0.md`  
Wardrowbe audit: `docs/audits/WARDROWBE_FIT_GAP_AUDIT_V1_20261004.md`  
MCP audit: `docs/audits/WARDROWBE_MCP_BRIDGE_AUDIT_V1_20261004.md`  
Style authority: `docs/style/SIL_STYLE_PROFILE_V0.md`  
Evaluation scenarios: `docs/style/STYLE_EVALUATION_SCENARIOS_V0.md`
