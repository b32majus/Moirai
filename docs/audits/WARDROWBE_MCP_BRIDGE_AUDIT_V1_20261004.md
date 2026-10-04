# Wardrowbe MCP Bridge Audit V1 — 2026-10-04

**Status:** CLOSED — REUSE WITH THIN EXTERNAL-AUTHORING ADAPTER

## Decision

Use **`jansitarski/wardrowbe-mcp` pinned to commit `f2f172d6ae309c9ec9478ee75444642ffe611baa`** as the preferred MCP bridge for the Moirai pilot, but **do not use its `wardrowbe_create_outfit` tool as the canonical Moirai recommendation write path**.

Add only the missing Wardrowbe external-authoring surface — either as two thin MCP tools or as direct REST calls owned by the Moirai integration layer:

- `POST /api/v1/outfits/suggestions`
- `POST /api/v1/pairings/item/{source_item_id}`

No fork of Wardrowbe itself is justified.

## Why this candidate

### `saya6k/mcp-wardrowbe`

Strengths:
- MIT;
- Streamable HTTP, SSE and stdio;
- solid basic wardrobe/read/wear/wash/outfit tool surface;
- direct ancestor of later implementations.

Limitations for Moirai:
- upstream head is old (`cb156513...`, 2026-05-27);
- 22-tool baseline does not expose the richer vision/write-back surface needed for a strong external stylist loop;
- no canonical Wardrowbe external suggestion/pairing authoring surface.

### `Gravitom/mcp-wardrowbe`

Strengths:
- derived from saya6k and updated through 2026-09;
- adds item creation from product-page URL, image URL and local path;
- 23 tools;
- MIT inheritance;
- HTTP/SSE/stdio.

Limitations for Moirai:
- still follows the smaller saya6k tool model;
- outfit generation delegates to Wardrowbe native `/outfits/suggest`;
- does not expose the richer image-inspection/write-back/external-stylist workflow of the Jan implementation.

### `jansitarski/wardrowbe-mcp`

Strengths:
- MIT;
- Go static binary + published container/release path;
- Streamable HTTP and stdio;
- 35-tool surface;
- item browsing/search and analytics;
- external-tagging queue (`tagging_status=pending`), retagging and `skip_ai` create semantics;
- garment image access for agent vision;
- structured item write-back (`update_item`, `set_item_tags`, `set_item_description`);
- item creation from URL/base64;
- wear/wash/archive lifecycle;
- feedback and outfit history;
- explicit-item outfit persistence using real Wardrowbe item IDs;
- bounded pagination and compact outfit reads;
- hardened HTTP surface and pinned binary/container deployment options.

Current upstream compatibility evidence:
- Wardrowbe `v1.10.3` still supports `skip_ai`, `tagging_status`, retag/write-back lifecycle and `/outfits/studio`;
- Wardrowbe capabilities explicitly advertise `external_tagging`, `external_suggestions` and `external_pairings`;
- Wardrowbe `v1.10.3` contains the dedicated `ExternalOutfitService` for externally authored suggestions and pairings.

Maintenance caveat:
- latest Jan functional master commit is `f2f172d6...` (2026-07-21), although repository automation/dependency activity continued later;
- latest tagged release is `v1.1.0` (2026-07-15);
- **pin master commit `f2f172d6...` rather than the tagged release for this pilot**, because that post-release commit renames item-creation semantics from legacy `auto_tag` to Wardrowbe's current `skip_ai` flag.

## Critical semantic gap

`jansitarski/wardrowbe-mcp` exposes `wardrowbe_create_outfit`, which writes to:

`POST /api/v1/outfits/studio`

This is useful for manual/studio compositions but is **not semantically equivalent** to an externally-authored recommendation.

In Wardrowbe `v1.10.3`, Studio creation:
- writes `source=manual`;
- creates synthetic feedback with `accepted=True`;
- therefore can feed the learning layer as if the outfit had already been accepted.

Using that path for Moirai-generated recommendations would contaminate learning and blur the distinction between:

> "Moirai suggested this"

and

> "Sil manually created/accepted this."

Wardrowbe already provides the correct path through `ExternalOutfitService`:
- `source=external`;
- `status=pending`;
- real ordered item UUIDs;
- optional reasoning/style notes/formality/palette/notes;
- dedicated pairing authoring with `source_item_id`.

Therefore Moirai recommendations must use the external-authoring endpoints, not Studio.

## Required thin adapter

The minimum addition is deliberately small.

### Tool 1 — create external suggestion

Inputs should map directly to Wardrowbe:
- ordered `item_ids`;
- `occasion`;
- optional `name`;
- optional `scheduled_for`;
- optional `reasoning`;
- optional `style_notes`;
- optional `season`;
- optional `formality`;
- optional `palette`;
- optional `notes`.

Target:

`POST /api/v1/outfits/suggestions`

### Tool 2 — create external pairing

Inputs:
- `source_item_id`;
- partner `item_ids`;
- same optional semantic fields where supported.

Target:

`POST /api/v1/pairings/item/{source_item_id}`

The bridge must return Wardrowbe's real outfit/item IDs without translating them into a parallel Moirai identity model.

## Pilot integration boundary

### Reuse from Jan MCP
- browse/filter garments;
- fetch garment details;
- inspect garment images;
- ingest representative garments with `skip_ai=true` when external tagging is desired;
- list pending tagging queue;
- write structured tags/descriptions back;
- log wear/wash;
- read outfit history/analytics;
- submit real feedback.

### Do not use as Moirai recommendation authority
- `wardrowbe_suggest_outfit` is a baseline comparator only;
- `wardrowbe_create_outfit` is Studio/manual semantics and must not persist Moirai suggestions.

### Moirai owns
- style reasoning against `SIL_STYLE_PROFILE_V0`;
- context/formality calibration;
- preference-vs-habit distinction;
- accessory reminders;
- choosing actual Wardrowbe item IDs;
- external suggestion/pairing authoring through the thin adapter.

## Deployment recommendation

1. Wardrowbe: pin audited upstream `v1.10.3` / `f9664a693eaaf65daa2a09ddedc57956c206eaa2` for the pilot unless a newer version is reviewed first.
2. MCP: pin `jansitarski/wardrowbe-mcp@f2f172d6ae309c9ec9478ee75444642ffe611baa`.
3. Add only the two external-authoring operations above; prefer an upstream contribution or sidecar/thin wrapper over maintaining a broad fork.
4. Start with 15–25 representative real garments.
5. Keep private wardrobe photos/data outside the public `b32majus/Moirai` repository.
6. Run `STYLE_EVALUATION_SCENARIOS_V0` before any shopping or full wardrobe purge.

## Verdict

**REUSE WITH THIN ADAPTER.**

This is preferable to both alternatives:
- **REST DIRECT for everything** would discard a mature 35-tool agent bridge we can reuse;
- **fork/build our own MCP** would create maintenance work for capabilities already implemented.

The only custom integration needed for the V0 pilot is the semantic gap between Studio/manual outfit creation and Wardrowbe's existing external suggestion/pairing APIs.