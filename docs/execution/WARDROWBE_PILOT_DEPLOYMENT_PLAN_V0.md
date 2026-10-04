# Wardrowbe Pilot Deployment Plan V0

**Status:** PREPARED — execution pending remote host access  
**Issue:** #3  
**Purpose:** make the first real Moirai wardrobe pilot reproducible without storing private wardrobe data or secrets in the public repository.

## 1. Fixed points

### Wardrowbe

- Repository: `Anyesh/wardrowbe`
- Release: `v1.10.3`
- Audited commit: `f9664a693eaaf65daa2a09ddedc57956c206eaa2`
- Backend image tag: `ghcr.io/anyesh/wardrowbe:backend-1.10.3`
- Frontend image tag: `ghcr.io/anyesh/wardrowbe:frontend-1.10.3`

Do not deploy `backend-latest` / `frontend-latest` in the pilot.

### MCP bridge

- Repository: `jansitarski/wardrowbe-mcp`
- Pilot commit: `f2f172d6ae309c9ec9478ee75444642ffe611baa`
- Reason for commit pin instead of `v1.1.0`: the post-release commit aligns creation semantics with Wardrowbe's current `skip_ai` flag.

### Style authority

- `docs/style/SIL_STYLE_PROFILE_V0.md`
- `docs/style/STYLE_EVALUATION_SCENARIOS_V0.md`

## 2. Privacy boundary

The public `b32majus/Moirai` repository is documentation/config-template only.

Never commit:

- `.env`;
- database credentials;
- MCP bearer/API keys;
- OIDC secrets/tokens;
- wardrobe photographs;
- PostgreSQL dumps;
- image-storage backups;
- personal exports.

All persistent wardrobe assets remain on private runtime storage.

## 3. Runtime topology

Use the upstream Wardrowbe production topology rather than inventing a new application stack:

```text
private user access
       |
       v
Wardrowbe nginx / frontend
       |
       +--> FastAPI backend
       |       |
       |       +--> PostgreSQL   [persistent]
       |       +--> Redis        [queue/runtime]
       |       +--> uploads      [persistent private photos]
       |
       +--> worker
       +--> image-worker

Moirai reasoning runtime
       |
       v
jansitarski/wardrowbe-mcp
       |
       +--> Wardrowbe API
       +--> thin external-authoring surface
              - POST /api/v1/outfits/suggestions
              - POST /api/v1/pairings/item/{source_item_id}
```

The MCP service is not a second database and must preserve Wardrowbe item/outfit IDs.

## 4. Host pre-flight

Before writing anything on the deployment host:

1. identify existing project/runtime directories;
2. verify free disk space;
3. verify Docker + Compose versions;
4. identify existing reverse proxy / ports / TLS / Tailscale or private-access path;
5. verify no existing service conflicts on proposed ports;
6. choose a dedicated private runtime directory, e.g. `/srv/moirai/wardrowbe` only after inspecting host conventions;
7. confirm backup destination has sufficient space;
8. do not expose Wardrowbe publicly merely to make setup easier.

No destructive cleanup belongs in this ticket.

## 5. Wardrowbe source and compose

Clone the exact audited release/source rather than copying fragments manually:

```bash
git clone https://github.com/Anyesh/wardrowbe.git wardrowbe
cd wardrowbe
git checkout f9664a693eaaf65daa2a09ddedc57956c206eaa2
```

Use upstream `docker-compose.prod.yml` and preserve its service topology.

Create a private runtime override or patched local compose that replaces:

```text
ghcr.io/anyesh/wardrowbe:backend-latest
```

with:

```text
ghcr.io/anyesh/wardrowbe:backend-1.10.3
```

and:

```text
ghcr.io/anyesh/wardrowbe:frontend-latest
```

with:

```text
ghcr.io/anyesh/wardrowbe:frontend-1.10.3
```

Do not modify upstream source solely to pin images.

## 6. Private environment

Create the runtime `.env` only on the private host.

Generate at minimum:

```bash
openssl rand -hex 32   # POSTGRES_PASSWORD
openssl rand -hex 32   # SECRET_KEY
openssl rand -hex 32   # NEXTAUTH_SECRET
openssl rand -hex 32   # MCP_API_KEY when HTTP MCP is used
```

Do not paste generated values into GitHub issues/docs.

### AI mode for first boot

The first infrastructure boot should **not depend on an AI provider**.

Preferred initial setting:

```text
AI_INTERNAL_ENABLED=false
```

Reason:
- Wardrowbe explicitly supports booting without internal AI;
- the pilot wants to validate external-agent tagging/reasoning;
- provider/model selection can be added after the operational substrate is healthy;
- this avoids blocking infrastructure on Snapbuilder/model API compatibility.

Representative uploads intended for external tagging should use `skip_ai=true`, leaving them in `tagging_status=pending` until the external agent writes tags.

Internal AI can later be enabled as a baseline comparator without changing the data model.

## 7. Authentication / exposure gate

Do not choose auth blindly before inspecting the host.

Accepted order of preference for the pilot:

1. existing secure OIDC / forward-auth already used on the host, if available;
2. private-network-only access with dev login for a strictly bounded single-user pilot;
3. public exposure only with a deliberate auth/TLS configuration.

Never expose DEBUG/dev auth broadly to the public internet.

Wardrowbe production compose binds its nginx entrypoint to loopback by default (`127.0.0.1:8080:80`), which is a useful safe starting point behind an existing private/proxy layer.

## 8. Persistence

The upstream production topology includes named volumes:

- `postgres_data` — authoritative structured wardrobe data;
- `uploads_data` — private source/derived wardrobe images;
- `redis_data` — runtime queue state.

For Moirai, PostgreSQL + uploads are the critical durable pair.

Do not begin full wardrobe ingestion until their backup/restore path has been smoke-tested.

## 9. Backup contract

Minimum V0 backup must capture both:

1. PostgreSQL logical dump;
2. Wardrowbe upload/image storage.

Example pattern only — final paths/names are host-specific:

```bash
mkdir -p "$BACKUP_ROOT/$STAMP"
docker compose -f docker-compose.prod.yml exec -T postgres \
  pg_dump -U "$POSTGRES_USER" "$POSTGRES_DB" \
  > "$BACKUP_ROOT/$STAMP/wardrowbe.sql"
```

Image backup must copy/archive the actual `uploads_data` volume through a disposable helper container or host-mounted backup path after the runtime topology is confirmed.

A backup is not considered valid until at least metadata/file presence is verified; a later pilot checkpoint should test restore into an isolated target.

## 10. Database migration / boot verification

After services are healthy:

```bash
docker compose -f docker-compose.prod.yml exec backend alembic upgrade head
docker compose -f docker-compose.prod.yml exec backend alembic current
docker compose -f docker-compose.prod.yml logs backend frontend worker image-worker --tail=100
```

Verify at minimum:

- frontend reachable through intended private route;
- `/api/v1/health` healthy;
- `/api/v1/capabilities` reports external tagging/suggestions/pairings;
- DB migrations current;
- upload persistence survives container recreation;
- no public unauthenticated route accidentally exists.

## 11. MCP deployment

Clone/pin the exact chosen bridge commit:

```bash
git clone https://github.com/jansitarski/wardrowbe-mcp.git wardrowbe-mcp
cd wardrowbe-mcp
git checkout f2f172d6ae309c9ec9478ee75444642ffe611baa
```

Because the required commit is post-`v1.1.0`, prefer building the binary/container from that exact source instead of silently using the older tagged image.

Runtime requirements:

- `WARDROWBE_URL` points to the private Wardrowbe backend path;
- MCP bearer key stays in private env/secret storage;
- MCP should be privately reachable by the chosen reasoning runtime;
- preserve real Wardrowbe UUIDs in tool results.

Smoke-test:

- health/readiness;
- session identity;
- `list_items`;
- image retrieval;
- pending tagging queue;
- tag write-back;
- feedback read/write.

## 12. Thin external-authoring adapter

### Why it exists

Do not use Jan MCP `wardrowbe_create_outfit` for Moirai recommendations. It writes through `/outfits/studio`, whose Wardrowbe semantics are manual/studio creation with synthetic accepted feedback.

### Required operation A — external suggestion

Map directly to:

```text
POST /api/v1/outfits/suggestions
```

Preserve Wardrowbe fields and response IDs. Minimum request:

```json
{
  "items": ["<wardrowbe-item-uuid>", "<wardrowbe-item-uuid>"],
  "occasion": "work"
}
```

Optional semantic fields may include name, scheduled date, reasoning, style notes, season, formality, palette and notes according to the upstream schema.

### Required operation B — external pairing

Map directly to:

```text
POST /api/v1/pairings/item/{source_item_id}
```

Use this for requests such as:

> “I want to wear these green trousers; build something around them.”

The source item remains a real Wardrowbe item and must not be replaced by a Moirai-local identifier.

### Implementation preference

1. small upstream contribution to Jan MCP if clean and accepted;
2. otherwise two-tool thin wrapper/sidecar;
3. otherwise direct REST from the Moirai reasoning runtime.

Do not fork Wardrowbe and do not build a parallel wardrobe API.

## 13. Representative ingest

Only after infrastructure + backup + bridge smoke tests pass, ingest 15–25 items.

Selection should include:

- 3–5 bottoms: slim/skinny, tailored/no-pleat, chino, jeans;
- 5–7 tops: colourful top/blouse/knit + simpler basics;
- 3–4 layers: relaxed blazer, cardigan, short jacket;
- 3–5 shoes: ankle boot, sandal, modern smart sneaker, optionally a heeled pair;
- 1–3 accessories/belts if useful.

Include:

- several current favourites/repeat items;
- several underused garments that may be hard to combine;
- enough colour to test Moirai's combination-expansion role.

Do not start with only easy favourites; that would make the pilot artificially easy.

## 14. Tagging loop

For each pilot item:

1. upload photo privately to Wardrowbe with external-tagging flow;
2. preserve returned item UUID;
3. inspect image through MCP vision surface;
4. write objective garment metadata;
5. user corrects only when needed;
6. separate objective tags from subjective style evidence;
7. item enters the recommendation pool only when usable metadata is present.

Do not encode subjective conclusions such as “Sil loves this” as objective garment facts.

## 15. Outfit evaluation loop

Run `STYLE_EVALUATION_SCENARIOS_V0` against real items.

For each suggestion record at least:

- context;
- item IDs;
- accepted/rejected;
- actually worn or not;
- comfort/style feedback when useful;
- rejection reason;
- whether unfamiliarity was true dislike or merely a combination the user would not have invented;
- whether a forgotten accessory would help;
- for ordinary work, whether habitual baseline or one-step-more-polished alternative wins.

A recommendation generated by Moirai must first persist as `source=external` / pending, then receive real user feedback.

## 16. Exit criteria for Issue #3

Issue #3 can close only when:

1. Wardrowbe `1.10.3` is running on persistent private storage;
2. backup path exists and has been smoke-verified;
3. Jan MCP pinned commit is reachable and core tools smoke-test successfully;
4. external suggestion/pairing authoring works without Studio synthetic acceptance;
5. 15–25 real representative items are safely ingested;
6. at least a small evaluation set has been run against actual inventory;
7. measured product gaps are documented.

## 17. Explicitly deferred

Until the above passes, do not:

- ingest the full wardrobe;
- buy recommended gap-fillers;
- perform full KEEP/EXIT cleanup;
- add a custom frontend;
- add vector search/RAG;
- add agent swarms;
- fork Wardrowbe;
- optimize travel packing.
