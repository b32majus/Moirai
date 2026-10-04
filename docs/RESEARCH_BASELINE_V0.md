# Moirai — Research Baseline V0

This document records the **initial solution landscape identified before implementation**. It is a starting point for audit, not a final technical selection.

## 1. Primary candidate: Wardrowbe

Repository: https://github.com/Anyesh/wardrowbe

Why it is the first candidate to audit:

- open-source wardrobe management;
- visual garment inventory;
- AI-assisted garment tagging;
- outfit generation;
- weather / occasion / preference context;
- ability to build around a selected garment;
- history / feedback-oriented behaviour;
- self-hosting;
- support for external or OpenAI-compatible model endpoints;
- architecture that appears compatible with separating the wardrobe system from an external reasoning agent.

### What we need to verify before adopting it

- exact data model and extensibility;
- how well it handles shoes, jewellery, bags and accessories;
- API surface and whether all required data/actions are exposed;
- exportability / portability of user data and photos;
- how feedback and preference learning actually work;
- ability to validate recommendations against real item IDs;
- whether travel/capsule planning can be added cleanly;
- whether shopping-gap analysis can be added cleanly;
- quality of mobile/PWA capture workflow;
- operational requirements and ease of self-hosting;
- licence details and implications for modification/distribution;
- current project health and maintenance quality.

**Current status:** candidate, not adopted.

---

## 2. Architectural reference: AI Closet

Repository: https://github.com/jonnykate/ai-closet

Why it matters:

- demonstrates a personal wardrobe + LLM workflow;
- uses a finite real wardrobe rather than open-ended fashion suggestions;
- provides a useful pattern for stable item IDs;
- emphasises structured LLM output;
- validates generated looks against actual inventory;
- uses history/feedback to avoid treating every recommendation as a blank slate;
- provides inspiration for rendering complete outfit compositions/collages.

### Patterns worth carrying into Moirai

1. Model outputs should reference stable inventory IDs.
2. A deterministic validator should sit between generation and user-facing recommendation.
3. Invalid/nonexistent item references should fail validation.
4. Outfit history and feedback should be first-class data.
5. Repetition should be intentional, not accidental.

**Current status:** reference implementation / pattern source, not preferred platform base.

---

## 3. UX reference: Libre Closet

Repository: https://github.com/Lazztech/Libre-Closet

Why it matters:

- visual wardrobe management;
- mobile/PWA-style interaction;
- camera-oriented capture;
- outfit construction;
- background-removal-oriented workflow;
- useful reference for keeping ingestion lightweight and visual.

**Current status:** UX/reference candidate. We do not currently assume it provides the reasoning layer Moirai needs.

---

## 4. Possible future agent runtime: Hermes Agent

Repository: https://github.com/NousResearch/hermes-agent

Hermes Agent may be relevant later if Moirai needs an independent persistent agent runtime with tools/memory/skills.

However, Hermes does **not** solve the first product problem: building and operating the wardrobe inventory and visual UI.

Therefore:

- do not start with Hermes;
- first determine the wardrobe-system base;
- introduce an agent runtime only if the stylist layer genuinely benefits from it.

**Current status:** deferred architecture option.

---

## 5. Supporting components identified

### rembg

Repository: https://github.com/danielgatis/rembg

Potential use:

- automatic image background removal during wardrobe ingestion;
- cleaner garment thumbnails and outfit compositions.

Use only if the selected wardrobe base does not already solve this sufficiently.

### FashionCLIP

Repository: https://github.com/patrickjohncyh/fashion-clip

Potential use:

- fashion-specific image/text embeddings;
- semantic retrieval or classification of garments.

This is **not required for V0**. A capable multimodal model may be enough for the initial wardrobe size and tagging problem. Do not introduce FashionCLIP unless the pilot demonstrates a concrete retrieval/classification gap.

### Local / self-hosted multimodal models

A local or self-hosted multimodal model may be useful for garment tagging and visual analysis if quality is sufficient. The project should prefer already-available model capacity before adding paid dependencies.

The user currently has access to Snapbuilder model capacity with mixed usage tiers, including an unlimited Gemma 4 option. Exact model/provider details should be verified when selecting the ingestion or reasoning model.

---

## 6. Current reuse thesis

The strongest current hypothesis is:

```text
User
 ├─ visual wardrobe UI
 │   └─ wardrobe platform / service
 │       ├─ items + photos
 │       ├─ outfits
 │       ├─ history
 │       └─ feedback
 │
 └─ conversational stylist
     └─ reasoning / agent layer
         ├─ context
         ├─ style profile
         ├─ travel
         ├─ wardrobe audit
         └─ shopping-gap reasoning
```

The wardrobe platform should remain the **system of record** for clothing data. The reasoning layer should query and act on that data rather than becoming an improvised database.

---

## 7. Research decision rule

For each candidate capability, classify it as:

- **FIT** — existing solution already meets the need adequately;
- **REUSE** — component/pattern can be adopted with little change;
- **MODIFY** — useful base but needs contained extension;
- **BUILD** — genuinely missing and worth implementing ourselves;
- **DROP** — unnecessary complexity or feature for our actual problem.

The first formal audit should apply this matrix to Wardrowbe before any bespoke implementation begins.
