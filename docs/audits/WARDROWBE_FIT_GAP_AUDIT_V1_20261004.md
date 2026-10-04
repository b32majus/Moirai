# Moirai — Wardrowbe FIT / GAP / REUSE / MODIFY / BUILD audit

Date: 2026-10-04

Status: **CLOSED — GO WITH CONFIGURED UPSTREAM PILOT**

Audited candidate: `Anyesh/wardrowbe`

Audited current release / reference:

- latest release observed: `wardrowbe-v1.10.3`;
- release target commit: `f9664a693eaaf65daa2a09ddedc57956c206eaa2`;
- release published on 2026-10-03;
- licence: MIT.

## 1. Executive decision

Wardrowbe is a strong fit as the **wardrobe system of record and visual CRUD application** for Moirai.

The correct next move is **not** to fork it and **not** to build a custom wardrobe platform.

Decision for the Issue #1 choice set:

> **2. deploy + configure**

with one important architectural rule:

> Keep Moirai-specific reasoning outside Wardrowbe whenever possible and use Wardrowbe's supported external-agent/API write surfaces to read inventory and persist tags/outfits.

This preserves upstream upgradeability while giving Moirai freedom to implement richer personal-style reasoning, wardrobe audits, travel capsule optimisation and shopping-gap analysis.

A fork becomes justified only if a measured pilot gap cannot be solved by configuration, external-agent logic, existing JSONB extension fields, a small sidecar persistence layer, or an upstream contribution.

## 2. Why the answer changed from “possible fork” to “configured upstream”

The audit found that Wardrowbe is intentionally evolving toward the same hybrid architecture Moirai independently selected:

- wardrobe/photo system as structured source of truth;
- internal AI optional;
- external tagging supported;
- externally-authored outfit suggestions supported;
- externally-authored item pairings supported;
- item ownership validated server-side;
- stable item UUIDs used throughout;
- an MCP integration pattern has already been validated end-to-end by upstream contributors.

This means Moirai does not need to own the wardrobe database, gallery, upload flow, background processing, recommendation history or feedback plumbing merely to obtain a bespoke stylist brain.

## 3. Product fit matrix

| Capability | Classification | Audit finding |
|---|---|---|
| Garment capture and photo workflow | **FIT** | Single and bulk upload, JPEG/PNG/WebP/HEIC validation, pHash duplicate detection, durable bulk-upload support, image resizing, multiple item images, rotation and optional background removal already exist. |
| Automated multimodal tagging | **FIT** | Structured tagging covers type, subtype, colors, primary color, pattern, material, formality, style, season and fit. Internal AI supports OpenAI-compatible endpoints; external tagging can own the lifecycle when internal vision is disabled. |
| Shoes | **FIT** | Shoes, sneakers, boots and sandals are first-class garment vocabulary roles under footwear. |
| Bags / belts / scarves / hats | **FIT** | Present as accessory vocabulary and available to recommendation prompts. |
| Jewellery detail | **MODIFY** | Recommendation prompt explicitly understands jewellery, but inventory vocabulary only has generic `accessories`; earrings, necklaces, bracelets and rings are not first-class types. For V0, generic `accessories` + subtype/manual/external tags is sufficient. Explicit jewellery vocabulary can be added later, preferably upstream or as a contained patch. |
| Outfit builder / persisted outfits | **FIT** | Outfit rows store ordered real item IDs, source, occasion, reasoning, style notes, season, formality, palette and notes; manual/studio and generated flows already exist. |
| Item-first styling | **FIT** | v1.10 added base-item selection and a 3-look comparison flow. Pairing APIs also support styling around a source item. |
| Weather-aware recommendations | **FIT** | Weather context is built in and uses Open-Meteo. |
| Basic occasion/time context | **FIT** | Occasion, time of day, weather and user preference inputs are represented in the recommendation flow. |
| Rich conversational context | **BUILD OUTSIDE CORE** | “I have a health-sector meeting, then a flight, then an informal dinner” is richer than Wardrowbe’s native context model. This belongs in the Moirai reasoning layer, which can select real item IDs and persist the resulting outfit via the external authoring API. |
| Wear history | **FIT** | Per-item wear counts, dates and linked outfit history are first-class data. |
| Feedback | **FIT** | Acceptance, overall rating, comfort, style, comment, whether actually worn, modifications and “wore instead” are stored. |
| Learning | **FIT / REUSE** | Existing learning profile computes color/style/occasion/weather/temporal preferences and item-pair compatibility. Moirai should reuse these signals rather than duplicate them. |
| Analytics | **FIT** | Wardrobe usage and outfit performance plumbing already exist. |
| Mobile browser usability | **VERIFY IN PILOT** | Next.js/Tailwind UI is designed as a web application and contains mobile-oriented flows, but this audit does not treat actual mobile ergonomics as proven without hands-on use. |
| PWA/offline installability | **MODIFY / NON-BLOCKING** | No first-class PWA manifest/service-worker surface was identified in the audited frontend. Not required for V0. |
| Spanish UI | **MODIFY / NON-BLOCKING** | Current upstream UI ships 8 locales but not Spanish. It already uses `next-intl`, so Spanish is a bounded future contribution rather than an architecture reason to fork. |

## 4. Data and architecture matrix

| Capability | Classification | Audit finding |
|---|---|---|
| Stable item identity | **FIT** | `clothing_items.id` is a UUID primary key and outfits reference UUID item IDs. |
| Structured wardrobe metadata | **FIT** | Core item fields plus JSONB tags; explicit colors/style/season arrays and lifecycle/use metadata. |
| Photo storage | **FIT** | Original/main/medium/thumbnail paths, additional images and original-image backup are represented. |
| Database | **FIT** | PostgreSQL 15 with SQLAlchemy/Alembic. Appropriate for Moirai scale; no vector store needed. |
| Async processing | **FIT** | Redis + arq already handle tagging/image jobs and retry/cancellation concerns. |
| REST API | **FIT** | FastAPI endpoints with Swagger/ReDoc; inventory, outfits, pairings, preferences, analytics, weather and learning have API surfaces. |
| Model-provider abstraction | **FIT** | Internal AI works with OpenAI-compatible APIs and separate vision/text model configuration. Internal AI can be switched off per capability or globally. |
| External tagging | **FIT** | Items can remain `tagging_status=pending`; external writes through the item API mark tagging provenance and complete the lifecycle. |
| External outfit authoring | **FIT** | `POST /outfits/suggestions` persists externally-authored recommendations against owned real item IDs. |
| External pairings | **FIT** | Externally-authored item-centred pairings are supported and persisted as normal outfits. |
| Deterministic real-item validation | **FIT** | External outfit persistence validates item ownership and works on item UUIDs. This satisfies Moirai D-007 at the persistence boundary. |
| MCP/agent bridge | **REUSE CANDIDATE** | Upstream’s external-authoring work was explicitly validated with an MCP client. A current MIT project, `saya6k/mcp-wardrowbe`, exposes Wardrowbe as an MCP server with tools for items/outfits/wear/analytics. Audit it before making it a hard dependency. |
| Self-hosting | **FIT** | Official Docker Compose flow and versioned GHCR images; Kubernetes manifests also exist. |
| Data ownership | **FIT** | PostgreSQL and image storage are self-hosted. |
| User-facing export/import | **MODIFY** | No dedicated portable wardrobe export/import workflow was identified. Operational backup remains straightforward because data and images are self-hosted. Add a portable export only if the pilot shows a real need. |
| Licence | **FIT** | MIT permits use, modification, distribution and private forks while retaining licence notice. |
| Maintenance health | **FIT with upstream risk** | Active project: current v1.10.3 release published 2026-10-03; frequent 2026 releases and active issue/PR activity. Still an external dependency with maintainer/community continuity risk, so pin versions and own backups. |

## 5. Moirai-specific gaps

### 5.1 Rich personal style profile — REUSE + BUILD OUTSIDE CORE

Wardrowbe already has:

- `style_profile` JSONB;
- occasion preferences;
- favourite/avoided colours;
- comfort/temperature preferences;
- variety/repeat preferences;
- learned style, colour, occasion, weather and temporal patterns.

That is enough as a substrate, but Moirai needs a richer explicit profile including desired identity, silhouette preferences, practical constraints, “never wear” rules, work/occasion semantics and nuanced language.

Do **not** extend the Wardrowbe schema yet. Keep `SIL_STYLE_PROFILE` as Moirai authority and map only useful operational fields into Wardrowbe preferences. The stylist agent can consume both.

### 5.2 Wardrobe audit lifecycle — BUILD THIN LAYER

Required Moirai states:

- KEEP — core;
- KEEP — secondary;
- RESTYLE;
- ALTER / REPAIR;
- REPLACE;
- EXIT;
- UNCERTAIN.

Wardrowbe only has normal/archive lifecycle and `archive_reason`; it does not model this decision process.

For the first audit, avoid a schema fork. Store audit decisions in Moirai-owned structured data or a controlled tags convention. Promote to a dedicated table only if the workflow becomes durable enough to justify it.

### 5.3 Travel capsule optimisation — BUILD IN MOIRAI AGENT

Wardrowbe can supply:

- owned item IDs;
- attributes;
- pair compatibility;
- wear history;
- weather;
- outfit persistence.

What is missing is optimisation across **multiple days/events simultaneously** with garment reuse and luggage constraints. This is reasoning/orchestration, not wardrobe CRUD. Build it outside the core and persist selected outfits back into Wardrowbe.

### 5.4 Shopping-gap intelligence — BUILD IN MOIRAI AGENT

Wardrowbe tracks purchase metadata but does not answer:

> “Would buying this candidate materially increase the useful combination space of my existing wardrobe?”

That is a Moirai differentiator. It should reason over the real inventory and distinguish explicitly between owned items and proposed purchases.

### 5.5 Jewellery and finishing layer — MODIFY LATER IF NEEDED

Wardrowbe already supports accessory reasoning at a generic level, but Moirai’s desired specificity requires earrings/necklaces/etc. to become reliably distinguishable.

V0 path:

- use `type=accessories`;
- use subtype/tags/name for `earrings`, `necklace`, etc.;
- let the external agent reason over those fields.

Only extend the canonical garment vocabulary after the pilot proves that generic accessories cause real failures.

### 5.6 Explanation layer — FIT

Wardrowbe outfit records already provide `reasoning`, `style_notes`, `notes` and related recommendation explanation fields. The external Moirai stylist can write richer explanations without changing the core.

## 6. Recommendation engine quality: good substrate, not accepted as final stylist

Wardrowbe’s internal recommendation prompt is substantially better than a naive color matcher. It reasons about:

- color harmony;
- texture;
- proportion and silhouette;
- layering;
- weather;
- time of day;
- optional bag/accessories;
- avoiding duplicate item slots;
- returning multiple distinct looks.

However, its internal stylist must **not** become Moirai’s semantic authority before user validation.

Why:

1. Moirai’s goal is personal fit, not generic fashion correctness.
2. The target contexts are richer than a simple occasion label.
3. The style profile is not yet defined.
4. The user needs explanations and constraints tuned to real behaviour.
5. Travel and shopping are multi-step optimisation problems, not single-outfit generation.

Therefore internal Wardrowbe AI is useful as a **baseline comparator** during the pilot, not automatically the final brain.

## 7. Agent architecture decision

Recommended initial topology:

```text
User / conversational interface
          |
          v
   Moirai stylist brain
   - style profile
   - rich context
   - wardrobe audit
   - travel optimisation
   - shopping reasoning
          |
          | Wardrowbe API / candidate MCP bridge
          v
      Wardrowbe
   - items + photos
   - UUID identity
   - outfits
   - usage history
   - feedback
   - learning signals
   - weather/basic preferences
          |
          v
 PostgreSQL + image storage
```

Wardrowbe’s internal AI may remain enabled during the pilot for A/B comparison, or capability-by-capability disabled once the external stylist takes ownership.

Hermes Agent remains optional. Nothing found in this audit makes Hermes a prerequisite.

## 8. Candidate MCP bridge

A current open-source project was found:

- `saya6k/mcp-wardrowbe`;
- MIT;
- exposes Wardrowbe over Streamable HTTP / SSE / stdio;
- advertises 22 tools covering suggestions, item browsing, wear/wash logging, acceptance, analytics and notifications;
- includes `list_items`, `get_item`, `get_outfit` helpers and an agent skill bundle.

This is highly aligned with Moirai, but it is smaller/less mature than Wardrowbe itself. Treat it as **REUSE CANDIDATE**, not trusted infrastructure until a focused audit and smoke test are completed.

Also note that Wardrowbe PR #156 states that the upstream external-authoring path was tested end-to-end against an MCP client, including creation of external suggestions and pairings.

## 9. Deployment recommendation

For the pilot:

1. **Do not fork Wardrowbe.**
2. Deploy a pinned upstream release, initially `wardrowbe-v1.10.3` unless a newer release is intentionally re-audited before deployment.
3. Keep database and photos under our control and backed up.
4. Start with internal AI available as a baseline if a compatible no-extra-cost endpoint is available.
5. Evaluate the existing MCP bridge before building any custom agent adapter.
6. Build the Moirai style profile before judging recommendation quality.
7. Ingest only a representative 15–25-item pilot wardrobe first.
8. Run structured test cases and capture actual user acceptance/rejection.
9. Do not add Spanish, jewellery vocabulary, portable export or custom UI until the pilot proves each is worth the cost.

## 10. Available AI capacity and cost principle

Moirai already has access to external model capacity through existing subscriptions/resources, including Snapbuilder capacity described by the user. Exact model names, quotas and API compatibility must be verified at integration time rather than encoded from memory.

Architecture rule:

> Prefer already-paid or free/local capacity. Do not add a new paid model dependency merely because Wardrowbe’s README examples use OpenAI or Ollama.

If an available provider exposes an OpenAI-compatible endpoint, it is a direct candidate for Wardrowbe internal AI. Otherwise the external-agent path avoids coupling the wardrobe service to that provider.

## 11. Risks

### R1 — Upstream dependency

Mitigation: pin releases, back up PostgreSQL + image storage, keep Moirai-specific semantics outside core where possible.

### R2 — Recommendation quality may be generic

Mitigation: Wardrowbe is not being accepted as the final stylist. Validate against the user’s style profile and use an external brain when needed.

### R3 — Accessory vocabulary may be too coarse

Mitigation: generic accessory + subtype in V0; extend only after observed failure.

### R4 — Moirai-specific metadata could pollute upstream fields

Mitigation: do not stuff every concept into arbitrary tags. Use a small Moirai sidecar contract if wardrobe-audit/travel/shopping state becomes durable.

### R5 — MCP bridge maturity

Mitigation: audit/smoke-test separately; direct REST API remains available and prevents lock-in to the MCP adapter.

### R6 — Mobile ergonomics unknown

Mitigation: include phone upload/use in the pilot acceptance test before bulk inventorying.

## 12. FIT / REUSE / MODIFY / BUILD summary

### FIT

- wardrobe/photo source of truth;
- item identity;
- bulk capture;
- multimodal tagging substrate;
- shoes/basic accessories;
- outfit persistence;
- item-first styling;
- weather/basic occasion context;
- wear history;
- feedback;
- learning signals;
- analytics;
- API;
- model-provider abstraction;
- external tagging;
- external outfit authoring;
- deterministic real-item persistence;
- self-hosting;
- licence.

### REUSE

- existing learning model;
- preference model;
- external-agent contract;
- optional MCP bridge;
- recommendation/pairing patterns;
- background removal and image pipeline.

### MODIFY only if measured

- Spanish UI;
- richer jewellery vocabulary;
- PWA/offline behaviour;
- portable wardrobe export/import;
- any missing mobile UX details discovered in pilot.

### BUILD — outside Wardrowbe core

- `SIL_STYLE_PROFILE` authority;
- rich conversational stylist reasoning;
- KEEP/RESTYLE/REPAIR/REPLACE/EXIT audit workflow;
- travel capsule optimiser;
- shopping-gap intelligence;
- candidate-purchase evaluation;
- Moirai-specific longitudinal rules that do not belong in the generic upstream product.

### DROP for V0

- custom wardrobe web app;
- fork-first strategy;
- vector database;
- RAG;
- multi-agent swarm;
- fashion-specific embedding infrastructure;
- bespoke photo pipeline;
- bespoke recommendation-history database.

## 13. Final verdict

**VERDICT: GO — deploy + configure Wardrowbe upstream; no fork.**

Wardrowbe should become the first operational substrate for Moirai.

The next validation frontier is not architecture. It is **product truth**:

1. define Silvia’s style profile;
2. deploy the pinned candidate;
3. ingest a small representative wardrobe;
4. test whether its native recommendations are genuinely wearable;
5. audit the MCP bridge;
6. introduce the Moirai external stylist only where native behaviour is insufficient.

The project should not reopen “build our own wardrobe platform?” unless this pilot produces concrete evidence that the Wardrowbe substrate blocks a required Moirai capability.

## Evidence references

- Wardrowbe repo: https://github.com/Anyesh/wardrowbe
- Wardrowbe latest release API: https://api.github.com/repos/Anyesh/wardrowbe/releases/latest
- Item model: https://github.com/Anyesh/wardrowbe/blob/main/backend/app/models/item.py
- Outfit model: https://github.com/Anyesh/wardrowbe/blob/main/backend/app/models/outfit.py
- Learning model: https://github.com/Anyesh/wardrowbe/blob/main/backend/app/models/learning.py
- Preference model: https://github.com/Anyesh/wardrowbe/blob/main/backend/app/models/preference.py
- Garment vocabulary: https://github.com/Anyesh/wardrowbe/blob/main/backend/app/data/garment_vocabulary.json
- Clothing analysis prompt: https://github.com/Anyesh/wardrowbe/blob/main/backend/app/prompts/clothing_analysis.txt
- Recommendation prompt: https://github.com/Anyesh/wardrowbe/blob/main/backend/app/prompts/recommendation.txt
- External outfit service: https://github.com/Anyesh/wardrowbe/blob/main/backend/app/services/external_outfit_service.py
- External authoring PR #156: https://github.com/Anyesh/wardrowbe/pull/156
- Candidate MCP bridge: https://github.com/saya6k/mcp-wardrowbe
- Wardrowbe licence: https://github.com/Anyesh/wardrowbe/blob/main/LICENSE
