# MyOTA

MyOTA is open infrastructure for geographic amateur-radio activation
programmes. It provides reusable identity, programme configuration, geospatial
data, activation, QSO, award, approval, and map capabilities. Each programme
defines its own charter, eligibility, rules, awards, minimum QSOs,
jurisdictions, themes, and content; MyOTA does not copy or inherit rules from
POTA, MPOTA, or another programme.

The project is motivated by a simple accessibility problem: many operators
live close to legitimate local or municipal parks but far from the entities
recognized by existing activation programmes. MyOTA aims to make nearby
places usable for short, urban, QRP, VHF/UHF, satellite, and spontaneous
activations while remaining complementary to other programmes.

Read the detailed [purpose, motivation, and working charter](https://github.com/myota-platform/myota-docs/blob/main/docs/project-charter.md)
and the [current charter gap analysis](https://github.com/myota-platform/myota-docs/blob/main/docs/charter-gap-analysis.md).

The organization login is `myota-platform`; the visible organization name is **MyOTA**.

## Current engineering decisions

The [NATS JetStream migration plan](https://github.com/myota-platform/myota-docs/blob/main/docs/operations/messaging/nats-event-migration-plan.md)
tracks the phased event/work-queue migration. NATS remains cluster-internal,
with no broker authentication or TLS requirement while it is exposed only as a
ClusterIP service. Phases 0–4 are complete within their recorded evidence
bounds. Phase 5 has moved the four Geodata work kinds to
`MYOTA_GEODATA_WORK`, installed migration 021, and deployed retry-safe
partial-deletion recovery.

The production Geodata workers subscribe to four exact WorkQueue durables.
No accepted Geodata work was available to exercise at cutover. The four former
Geodata durable definitions remain inactive and empty through the 24-hour
rollback observation, ending no earlier than 20:58:36 UTC on 11 October. The
shared Interest-retained `MYOTA_EVENTS` stream and Activity notification
durable remain active. Helm revision 189 is deployed and Fleet reports
Ready=True with 60/60 resources. A disposable two-database cascade retry passed
after NAK/redelivery with one Activity cascade fact. The Activity idempotency
source fix is committed and mirrored but its image deployment remains open.
The cancellation race and expiry-to-completion chains passed in the isolated
K3s namespace, which has been cleaned up. The 24-hour rollback observation,
final legacy durable retirement, and Phase 6 fact-stream transition remain
open. See the [Phase 5 evidence](https://github.com/myota-platform/myota-docs/blob/main/docs/operations/messaging/evidence/phase5-geodata-work-2026-10-10.md),
[Phase 5 plan](https://github.com/myota-platform/myota-docs/blob/main/docs/operations/messaging/nats-event-migration-plan.md),
[Phase 4 evidence](https://github.com/myota-platform/myota-docs/blob/main/docs/operations/messaging/evidence/phase4-activity-work-2026-10-10.md),
and [Activity work runbook](https://github.com/myota-platform/myota-docs/blob/main/docs/operations/messaging/activity-work-queues.md).

## Platform at a glance

```text
Universal programme UI
        │
API gateway / ingress
        ├── Identity service       accounts, callsigns, SWLs, scopes, OIDC mappings
        ├── Programme service      programmes, shared categories, assignments, rules, themes
        ├── Operations service     real JetStream status and persistent sampled history
        ├── Geodata service        PostGIS, imports, provenance, conflation, review
        └── Activity service       activations, QSOs, award progress, certificates
                 │
        ├── myota_core       PostgreSQL
        ├── myota_activity  PostgreSQL
        └── myota_geo       PostgreSQL + PostGIS
```

The geodata lifecycle is:

```text
authoritative/imported source ─┐
community proposal ────────────┴→ candidate source
                                  │
                         pre-process + validate
                                  │
                               CANDIDATE ──→ APPROVED ──→ RETIRED
                                   └───────→ REJECTED
```

Approved entities are public programme references. Candidates remain visibly distinct until reviewed by an approver with the correct jurisdiction and entity-type scope; platform-wide candidates can be reviewed before a programme is assigned.

## Repositories

| Repository | Responsibility |
|---|---|
| [`myota-platform`](https://github.com/myota-platform/myota-platform) | Runnable integration bootstrap, dependency-free vertical slice, local gateway, tests, API contracts, migrations, and cross-service smoke path. |
| [`myota-contracts`](https://github.com/myota-platform/myota-contracts) | OpenAPI HTTP contracts, event envelopes, compatibility rules, and future generated clients. |
| [`myota-identity-service`](https://github.com/myota-platform/myota-identity-service) | Amateur-radio-native accounts, operator/SWL participation, multiple callsigns, primary callsign, verification lifecycle, roles, scopes, and optional external identity mappings. |
| [`myota-programme-service`](https://github.com/myota-platform/myota-programme-service) | Programme configuration, shared entity-category master data and assignments, programme-owned rules, jurisdictions, themes, and optional per-programme OIDC settings. |
| [`myota-geodata-service`](https://github.com/myota-platform/myota-geodata-service) | PostGIS entities, source provenance, two-stage GeoJSON/KML/GPX/Shapefile/OSM/ParkServe intake, import runs, deduplication/conflation, candidate review, and approval workflow. |
| [`myota-activity-service`](https://github.com/myota-platform/myota-activity-service) | Activations, QSOs, server-side award progress, programme-owned award definitions, SeaweedFS/S3 assets, certificate rendering, requests, issuance records, and award events on the shared activity port 8004. |
| [`myota-web`](https://github.com/myota-platform/myota-web) | Universal, programme-themed browser experience with approved/candidate map distinction, programme switching, published award progress, and participant level requests. |
| [`myota-operations-service`](https://github.com/myota-platform/myota-operations-service) | Authenticated NATS/JetStream and SeaweedFS status inspection, timestamped durable history, operational metrics and per-account Grafana role resolution. Domain workers remain separate. |
| [`myota-admin-web`](https://github.com/myota-platform/myota-admin-web) | Authenticated global administration web with grouped workspaces, explicit programme context, geodata review/import/entity-management flows, identity administration, award design, signature/background assets, and activity operations. |
| [`myota-deploy`](https://github.com/myota-platform/myota-deploy) | PostgreSQL/PostGIS bootstrap, Docker Compose manifests, SeaweedFS/S3 configuration, Helm chart, authenticated observability stack, service routing, health probes, and deployment configuration. |
| [`myota-docs`](https://github.com/myota-platform/myota-docs) | Project charter, motivation, architecture, ADRs, storage-topology decision, QGIS workflow, threat notes, gap analyses, migration strategy, source inspection, diagrams, and repository map. |

## Storage and geodata

The platform uses three service-owned database targets:

- `myota_core` (plain PostgreSQL): identity, programmes, shared configuration, permissions, and core outbox data.
- `myota_activity` (plain PostgreSQL): activations, QSOs, aggregates, awards, jobs, notifications, and activity outbox data.
- `myota_geo` (PostgreSQL + PostGIS): entities and geometry, imported snapshots, source references, conflation candidates, and review records.

Local development runs these as three database containers; the Helm chart can
run three persistent StatefulSets or connect to externally managed database
endpoints. Only `myota_geo` requires PostGIS. Separate targets isolate service
migrations, backup/restore, and capacity planning without requiring three
separate database products. Cross-service references use IDs and events rather
than cross-database foreign keys. QGIS is the recommended graphical PostGIS
tool for geometry inspection and controlled editing; lifecycle transitions
remain API-owned and audited. See the [storage ADR](https://github.com/myota-platform/myota-docs/blob/main/docs/adr/0007-three-database-migration.md), [service-boundary diagram](https://github.com/myota-platform/myota-docs/blob/main/docs/diagrams/service-boundaries.md), and [Fleet operations guide](https://github.com/myota-platform/myota-docs/blob/main/docs/operations.md#rancher-fleet-on-k3s).

S3-compatible object storage uses distinct buckets for geodata imports,
participant ADIF logs, editable award backgrounds, manager signatures, and
issued award certificates. Geodata imports use their own 30-day expunge policy;
completed and failed ADIF source files are removed after 15 days while import
results stay in PostgreSQL. Queued/processing ADIF uploads and award assets,
signatures, and issued certificates are excluded from those cleanup jobs. See the [bucket policy and upgrade
guide](https://github.com/myota-platform/myota-docs/blob/main/docs/operations.md#object-storage-bucket-boundaries).

Supported adapter designs include ParkServe US, OpenStreetMap tags (`leisure=park`, `leisure=nature_reserve`, `boundary=protected_area`, and `landuse=recreation_ground`), local-government GIS feeds, and manual proposals. The dedicated Geodata imports page accepts pasted GeoJSON/KML/GPX/WFS/ArcGIS JSON and uploaded text or binary files, loads the complete shared category catalogue from the database, keeps imports programme-independent, and now stops after durable pre-processing. Normalized records are checked for identical geometry or existing entities within 50 metres; possible duplicates are warnings with map comparison, not automatic merges. Administrators validate a compact paged selection in the visible pre-processing queue and explicitly queue confirmed records to `CANDIDATE` or `APPROVED` through the NATS-backed promotion path; only promoted candidates appear in Geodata Review. Uploaded objects are malware-scanned, stored in SeaweedFS through its S3 API, and queued through the geodata outbox/NATS path. Import sources and lifecycle state are durable: a geodata restart requeues queued or interrupted preprocessing runs before dispatching recovery workers, while unsupported binary adapters remain visibly queued.

## Local development

```bash
cd /Users/Volker_Kerkhoff/Documents/Projects/MyOTA/myota-platform
python3 -m unittest discover -s tests -v
python3 services/dev_server.py
```

Open <http://127.0.0.1:8080>. This starts the gateway and the four service boundaries without third-party Python packages. For the containerized PostGIS path, start Colima and use the Compose manifest in [`myota-deploy`](https://github.com/myota-platform/myota-deploy).

## Design principles

- API-first and programme-agnostic.
- Explicit service/data ownership; services communicate through APIs and versioned events.
- No Keycloak dependency.
- No copied POTA rules or charter language.
- PostgreSQL/PostGIS as the geospatial source of truth.
- Idempotent mutations, auditability, scoped approvals, health checks, and observable deployment boundaries.
- Admin-only Grafana access through the MyOTA identity token; Prometheus, Alertmanager, Tempo, and telemetry ingestion stay cluster-internal.
- MPOTA is retained only as synthetic sample data; it is not the platform definition.
- The public story starts with nearby places and useful participant outcomes;
  microservices are an enabling detail, not the product promise.

## Roadmap

The roadmap is intentionally platform-first. A programme supplies its own charter, policy, eligibility, awards, thresholds, jurisdictions, and content; no roadmap item should be interpreted as copying POTA, MPOTA, or another programme's rules.

### 0. Foundation and bootstrap — complete

- [x] Create the MyOTA organization and establish the repository split.
- [x] Publish the integration bootstrap and all service, contract, web, deployment, and documentation repositories.
- [x] Define OpenAPI-first HTTP contracts and versioned event envelopes.
- [x] Define service ownership for identity, programmes, geodata, and activity.
- [x] Implement dependency-free service boundaries, health endpoints, idempotency handling, and audit-event primitives.
- [x] Implement operator/SWL accounts, multiple callsigns, one primary callsign, lifecycle states, and verification fields.
- [x] Implement programme listing/configuration, shared entity categories and programme assignments, programme-owned rules, themes, and optional OIDC configuration.
- [x] Implement the candidate → approved/rejected geodata lifecycle, with approved → retired protection.
- [x] Implement provenance-aware adapter contracts for ParkServe US, OSM, government GIS, and manual proposals.
- [x] Define PostgreSQL/PostGIS migrations, the three-database core/activity/geodata topology, QGIS views, and the storage ADR.
- [x] Implement activation and QSO primitives with idempotent mutation paths.
- [x] Implement the universal public programme view with programme switching and approved/candidate distinction.
- [x] Add Helm, Compose, CI, threat notes, migration notes, and local Colima smoke coverage.

### 1. Production-grade platform core

Delivered in the integration/runtime and deployment repositories; see `docs/production-core.md` for the operational boundary and known projection-to-relational migration path.

- [x] Replace prototype JSONB activity state with a service-owned relational PostgreSQL schema, indexed tables, migrations, bounded pools, and transactional repository methods.
- [x] Add connection pooling, transaction boundaries, bounded retry policies, optimistic-safe idempotent writes, and graceful shutdown.
- [x] Add a durable outbox in each service-owned database and connect it to NATS JetStream.
- [x] Add existing event-consumer primitives for broker redelivery,
  event-ID deduplication, dead-letter handling, and schema-major checks;
  historical fact replay and supported dead-letter redrive remain migration work.
- [x] Generate a checked-in typed client and define server validation/compatibility rules from the versioned OpenAPI contract.
- [x] Standardize API errors, pagination, request/correlation IDs, body limits, API version headers, and deprecation policy.
- [x] Add programme-policy versioning so historical activation and award decisions remain reproducible.
- [x] Add migration automation, rollback guidance, seed separation, and backup/restore verification.

The checklist above describes the existing delivery baseline, not completion of
the cross-service NATS migration. Phases 0–3 are complete within their evidence
bounds. Phase 3 deployed Activity's exact-filter `activity-notifications-v1`
durable, atomic local notification/checkpoint transaction, audited poison-event
redrive, and bounded consumer metrics. Payload/schema enforcement, the Activity
and Geodata work migrations, and final production topology cutover remain open.
The live broker still uses the mixed Interest-retained stream. See the
[migration plan](https://github.com/myota-platform/myota-docs/blob/main/docs/operations/messaging/nats-event-migration-plan.md),
[Activity notification runbook](https://github.com/myota-platform/myota-docs/blob/main/docs/operations/messaging/activity-notification-consumer.md),
[Phase 2 evidence](https://github.com/myota-platform/myota-docs/blob/main/docs/operations/messaging/evidence/phase2-relay-hardening-2026-10-10.md),
[Phase 3 evidence](https://github.com/myota-platform/myota-docs/blob/main/docs/operations/messaging/evidence/phase3-domain-consumers-2026-10-10.md),
and [current/target topology diagrams](https://github.com/myota-platform/myota-docs/blob/main/docs/architecture/diagrams/nats-event-migration.md).

### 2. Identity, authentication, and authorization

Delivered in the identity service and shared auth contract; external OIDC code exchange remains provider-adapter work behind the stored programme mappings.

- [x] Build login, registration, account recovery, session, and logout flows without Keycloak.
- [x] Implement signed short-lived access tokens, refresh-token rotation, revocation, and service-to-service authentication.
- [x] Implement callsign verification workflows, evidence/source recording, lifecycle dates, retirement, and reassignment safeguards.
- [x] Implement programme-scoped roles and scopes for participants, approvers, programme administrators, geodata operators, award administrators, and global operators.
- [x] Implement jurisdiction/entity-type approver scopes with explicit authorization checks in the geodata service.
- [x] Implement optional per-programme OAuth/OIDC provider mappings to internal MyOTA accounts.
- [x] Add account privacy controls, data export, deactivation/anonymization, retention rules, and security audit views.
- [x] Add rate limits, abuse detection, login alerts, session management, and security event notifications.

### 3. General administration web — initial slice complete

The initial administration web is available in `myota-admin-web` and is served on port 8090 by the local Compose stack. It is a deliberately programme-agnostic control surface: programme owners supply their own rules, awards, entity types, themes, and content.

- [x] Create an authenticated admin shell with navigation, programme context, role-aware menus, breadcrumbs, filters, and audit context.
- [x] Add a dashboard for service health, pending reviews, active programmes, protected activity, and recent audit events.
- [x] Add programme administration: create/edit/archive programmes, manage themes, entity types, policy versions, and stored OIDC settings.
- [x] Add rule and award administration using programme-owned schemas and drafts; require explicit publication and effective dates.
- [x] Add identity administration: accounts, privacy export, deactivation/anonymization, roles/scopes returned by the identity API, and security events.
- [x] Add geodata administration: manual import launch, source metadata, provenance, and candidate/proposal review queue.
- [x] Support shared, database-backed entity categories: entities may have multiple categories, categories may be assigned to multiple programmes, and the first ordered category remains the compatibility primary.
- [x] Replace catalogue selection-to-scroll editing with a tabbed, keyboard-accessible dialog, previous/next navigation, draft guards and a focused geometry map; preserve filters, batch selection, revision checks and protected deletion. See the [editor guide](https://github.com/myota-platform/myota-docs/blob/main/docs/entity-catalogue-editor.md).
- [x] Move dataset intake into a dedicated Geodata imports page with database-backed shared category selection, pasted text, file upload, an explicit OpenStreetMap GeoJSON option, SeaweedFS storage, NATS queue events, and programme-independent staged promotion semantics.
- [x] Add the two-stage import safety boundary: a dedicated visible pre-processing queue with durable records, candidate counts, paged administrator validation with select-all, and an explicit NATS promotion queue targeting CANDIDATE or APPROVED before Geodata Review.
- [x] Add map-based approver review with candidate/approved/rejected layers, geometry editing, source comparison, editable audited names and shared categories (including programme-independent entities), review notes, and audit history.
- [x] Add activation/QSO administration with protected activation status and QSO counts; ADIF processing remains a later activity milestone.
- [x] Add translation/content administration with draft, review, publish, fallback, and locale coverage reporting.
- [x] Reorganize administration into permission-aware searchable workspaces, shared headings/refresh, one programme selector, read-only header context and consistent labels; preserve stable routes. See the [workspace guide](https://github.com/myota-platform/myota-docs/blob/main/docs/domain/administration/navigation-reorganization.md).
- [x] Replace the blocking import summary dialog with a responsive detail workspace that keeps preprocessing and history visible while records are validated.
- [x] Separate Users, Roles & permissions and Security events into tabs; preserve scoped grants on user saves and clarify published/built-in read-only records. See [delivery evidence](https://github.com/myota-platform/myota-docs/blob/main/docs/domain/administration/evidence/admin-workspaces-2026-10-09.md).
- [x] Add an accessible responsive foundation with keyboard-friendly controls, labels, visible loading/error states, and mobile navigation.

### 4. Geodata production pipeline

- [x] Implement ParkServe US ingestion with licensed-source metadata, snapshot manifests, refresh scheduling, and source-change detection.
- [x] Implement OSM extraction/filtering for the required tags, attribution preservation, geometry normalization, and regional refresh jobs.
- [x] Implement configurable local-government GIS adapters for WFS, GeoJSON, Shapefile, and ArcGIS FeatureServer sources.
- [x] Implement manual proposal import and map drawing with geometry validity, CRS normalization, size limits, and attachment metadata.
- [x] Implement spatial deduplication/conflation scoring using name, source identifiers, containment, overlap, distance, and jurisdiction, plus a pre-processing warning for identical or sub-50-metre existing-entity matches with map comparison.
- [x] Implement reviewable merge/keep-separate/ignore decisions with full provenance and reversible history.
- [x] Implement source disappearance semantics: unchanged, stale, retired, or review-required according to programme policy.
- [x] Integrate QGIS staging/edit workflows with least-privilege roles and API-owned approval transitions.
- [x] Add tile/vector-tile delivery, bounding-box queries, spatial indexes, caching, and map performance budgets.
- [x] Add KML, GPX, Shapefile archives, OSM PBF, and ParkServe binary intake alongside GeoJSON/WFS/ArcGIS formats.
- [x] Add global-admin entity deletion impact warnings, linked-QSO cascade deletion, aggregate rebuild, award-progress recalculation, and audit/conflation cleanup.

Implemented in the geodata service/API vertical slice. Network fetching and long-running OSM/ParkServe binary decoding remain deployment-worker responsibilities; the intake stores their source object and durable queued run while GeoJSON/KML/GPX/Shapefile decoding is available at the service boundary.

### Geodata horizontal scaling — database authority, uploads and workers

The [geodata horizontal-scaling roadmap](https://github.com/myota-platform/myota-docs/blob/main/docs/geodata-horizontal-scaling-roadmap.md)
is the detailed source of truth. Phases 2 and 3 have implementation work in
place, but are not considered closed until their integration and failure tests
pass. Phase 1 database authority is now implemented and verified, including two
running API containers; see the [concurrency evidence and migration/rollout record](https://github.com/myota-platform/myota-docs/blob/main/docs/geodata-phase1-relational-authority.md).
Infrastructure, forced-failure, memory and production canary gates remain open.

The [7 October delivery reconciliation and published CI links](https://github.com/myota-platform/myota-docs/blob/main/docs/geodata-horizontal-scaling-roadmap.md#latest-delivery-and-evidence--7-october-2026)
separates implemented capabilities from the remaining qualification gates;
checked implementation items do not authorize production replica increases.

- [x] Replace mutable process snapshots with request/job-scoped, database-authoritative
  row repositories, indexed pagination, transactionally coupled audit/events and
  database-enforced idempotency.
- [x] Protect concurrent entity edits with revisions and `If-Match`; reject stale
  writes and fence obsolete snapshot writers during rollout.
- [x] Verify migration replay, promotion replay, rollback and independent-instance
  concurrency in local PostGIS and two real API containers; run these in CI.
- [x] Improve admin resumable uploads with pause/resume, checksum-checked recovery,
  fresh repeat submissions, correct worker counts and automatic status refresh.
- [x] Add authenticated [NATS/JetStream status and sampled history](https://github.com/myota-platform/myota-docs/blob/main/docs/operations/messaging/jetstream-admin-status.md)
  through a domain-neutral operations service; provision it in Compose and Helm.
- [x] Add the [SeaweedFS status/history page and Grafana Editor access](https://github.com/myota-platform/myota-docs/blob/main/docs/seaweedfs-admin-status.md)
  for GLOBAL_OPERATOR/GLOBAL_ADMIN; provision all dashboards with a 30-minute
  window and 30-second refresh, and retain Viewer access for other readers.

- [x] Replace whole-file API upload buffering/shared spool dependency with
  user-bound, resumable SeaweedFS multipart upload sessions and bounded parts.
- [x] Persist session ownership, idempotency key, expected size/checksum,
  received-part checksums, object key, expiry, and completion state in the
  geodata-owned schema.
- [x] Verify part and completed-object checksums, scan the stored object, and
  persist import/outbox state before acknowledging completion.
- [x] Split durable import/preprocessing, promotion and confirmed entity-deletion consumers into a
  separately deployable geodata-owned JetStream worker; API replicas do not
  submit durable jobs to in-process executors.
- [x] Use durable pull consumers, explicit ACKs, bounded pending delivery,
  database leases/heartbeats, retry limits, terminal failure records, and
  stable identities for idempotent replay.
- [x] Remove the upload-spool PVC from Compose and Helm; retain only bounded
  per-part temporary scratch files.
- [x] Exercise SeaweedFS multipart create/upload/complete/read-checksum/delete
  and abort against the pinned local image; the exact digest and test are
  recorded in the [scaling roadmap](https://github.com/myota-platform/myota-docs/blob/main/docs/geodata-horizontal-scaling-roadmap.md#phase-2--make-upload-handoff-durable-without-a-shared-pod-volume).
- [ ] Test resumable API-session recovery across API termination and SeaweedFS
  restart against the production image.
- [x] Remove authoritative whole-catalogue snapshot hydration/rewrite from
  durable API and worker paths; retain the old JSON snapshot only as an archive.
- [ ] Stream/parse large sources in bounded feature batches and bound broad
  candidate/spatial traversals in worker memory.
- [x] Implement graceful SIGTERM/SIGINT worker drain; see the
  [operations runbook](https://github.com/myota-platform/myota-docs/blob/main/docs/operations.md#import-recovery).
- [x] Verify atomic promotion/result/audit persistence and duplicate promotion
  replay in PostGIS; see the [Phase 1 tests](https://github.com/myota-platform/myota-docs/blob/main/docs/geodata-phase1-relational-authority.md#recorded-validation).
- [ ] Test forced worker termination, lease recovery and concurrent multi-worker
  JetStream delivery; record failure-injection evidence in the roadmap.
- [ ] Close Phases 2 and 3 only after their exit criteria and the above
  verification gates are met.

### 5. Activity, awards, and programme execution

- [x] Expose activation execution and award management through the same activity-service process and port 8004.
- [x] Implement activation start/close, validity windows, location checks, operator/callsign authorization inputs, and programme rule evaluation with retained rule snapshots.
- [x] Implement ADIF upload, object storage, malware scanning gates, parsing, validation, deduplication, and asynchronous processing jobs.
- [x] Delete completed and failed ADIF source objects after 15 days while retaining import results and excluding queued and processing uploads; keep this policy isolated from award assets and certificates. See the [object-storage retention policy](https://github.com/myota-platform/myota-docs/blob/main/docs/operations.md#object-storage-bucket-boundaries).
- [x] Implement QSO normalization, worked-station identity, band/mode validation, time-window rules, PostgreSQL COPY batches, and correction workflows.
- [x] Implement programme-linked, programme-owned award definitions with explicit draft, review, approval, publication, and effective-date lifecycle.
- [x] Implement recursive programme-owned QSO/activity conditions with AND, OR, NOT, and supported metric/entity leaves.
- [x] Implement hunter and activator categories with administrator-configured incremental achievement levels.
- [x] Implement A4/Letter print profiles, aspect-ratio/resolution validation, normalized WYSIWYG certificate field placement, and award-manager signature metadata.
- [x] Fix programme detail loading and aligned identifier/name fields; preserve programme-owned metadata during edits. Restore default award placement fields, named PNG/JPEG uploads and selectable backgrounds/signatures; generate bounded mock-data preview PDFs in a separate window. See the [editor/design guide](https://github.com/myota-platform/myota-docs/blob/main/docs/programme-and-award-design.md).
- [x] Standardize operational times on UTC across effective-date inputs, publication APIs, status/history displays, provisioned Grafana dashboards and retention schedules; no programme-local timezone override. See the [UTC policy](https://github.com/myota-platform/myota-docs/blob/main/docs/utc-time-policy.md).
- [x] Implement SeaweedFS/S3-compatible background/signature asset registration, presigned uploads, small API uploads, and certificate-bucket targets.
- [x] Implement server-side award progress from activity-owned data, participant identity-scoped level requests, permanent issuance records, and higher-level re-requests.
- [x] Implement PDF certificate rendering when assets are available, retry rendering, and expiring download URLs; preserve issuance history when rendering is deferred.
- [x] Implement versioned award-rule recalculation jobs for historical definitions and explicit retirement semantics.
- [x] Add activator, hunter, entity, programme, and jurisdiction statistics with reproducible aggregation jobs.
- [x] Add public programme award listing, participant sign-in, server-side award progress, and level request workflow.
- [x] Add public activation history, leaderboards, JSON/CSV downloadable results, and privacy-aware callsign display.
- [x] Add notifications for proposal decisions, import failures, award qualification, and account security events through durable notification jobs and event consumption.

### Charter-derived product and community gap analysis — 2026-09-27

The internal platform slice is substantially ahead of the public participant
experience. The following items come from the project-purpose and launch
conversation and are intentionally not marked complete merely because an API
primitive or local demo exists. The detailed analysis and owners are in
[`myota-docs/docs/charter-gap-analysis.md`](https://github.com/myota-platform/myota-docs/blob/main/docs/charter-gap-analysis.md).

- [ ] Publish a map-first **MyOTA Explorer** with nearby search, clear
  Candidate/Approved/Rejected distinction, entity details, and a “propose a
  place” path.
- [ ] Seed and license a useful Sevilla/Andalucía dataset, then run a small
  real-operator beta that measures parks activated, QSOs, distance to parks,
  repeat participation, and software failures.
- [ ] Finish participant workflows in `myota-web`: production authentication,
  profile/callsign management, proposals, activation logging, ADIF/QSO flows,
  public history, shareable results, and privacy-aware leaderboards.
- [ ] Publish a governance and contributor model: reviewer handbook, local
  stewardship/Park Steward proposal, escalation/SLA policy, country/region
  coordinators, and programme onboarding guidance.
- [ ] Make interoperability tangible with public API examples, generated SDK
  releases, external-reference endpoints, bulk/change-feed documentation, and
  at least one logger/client integration.
- [ ] Decide and document software/data licences, open-data publication rules,
  source attribution, and a reproducible regional-data workflow.
- [ ] Complete the Internet-facing beta gates: load/soak tests for large QSO
  volumes, observability/SLOs, backups and restore drills, security review,
  secret/key management, supply-chain controls, and incident runbooks.
- [ ] Define the shared participant mobile API/offline/push model, then build
  Android and iOS applications for user workflows only; administration remains
  in the web control plane.

These gaps are product and delivery work, not default programme rules. Every
programme still supplies its own policy and charter.

### API consistency and REST consolidation — Phases 0–4 complete

- [x] Reconcile and freeze the canonical OpenAPI document with the generated
  root and platform integration copies.
- [x] Add low-risk resource updates for accounts, programmes, category
  memberships, primary callsigns, content, policy drafts, and awards.
- [x] Preserve legacy action routes as audited compatibility aliases with
  deprecation/sunset headers; verify route registration and contract parity in
  CI.
- [x] Consolidate the geodata resource model: import representations,
  proposals, metadata, categories, geometry, reviews, bbox-filtered entity
  collections, and confirmation-based deletion jobs.
- [x] Add activity and award job resources for activation closure, QSO
  ingestion, statistics, award evaluation/recalculation, issuance, rendering,
  and certificate artifacts, with owner authorization, version-scoped
  recalculation, and deterministic statistics snapshots.
- [x] Migrate the checked-in typed clients and admin/public web clients, including
  identity role creation, account deactivation, and geodata deletion workflows,
  to the preferred resource APIs; migrate geodata-to-activity deletion calls as
  well, with route-usage telemetry. See the
  [Phase 4 client and operational migration record](https://github.com/myota-platform/myota-docs/blob/main/docs/api-phase4-client-operational-migration.md).
- [x] Add Prometheus/Grafana operational dashboards for deprecated alias
  traffic, job lag, failed jobs, pending corrections, and HTTP errors; run the
  durable-stack verification gate before local releases.

The endpoint-by-endpoint table, contract links, and implementation record are
maintained in the
[myota-docs REST API consolidation plan](https://github.com/myota-platform/myota-docs/blob/main/docs/api-rest-consolidation-plan.md)
and [Phase 1 resource update record](https://github.com/myota-platform/myota-docs/blob/main/docs/api-phase1-resource-updates.md),
plus the [Phase 2 geodata resource model](https://github.com/myota-platform/myota-docs/blob/main/docs/api-phase2-geodata-resource-model.md).
The Phase 3 implementation is recorded in the
[activity and award job record](https://github.com/myota-platform/myota-docs/blob/main/docs/api-phase3-activity-award-jobs.md).
Phase 4 operations, dashboards, and the verification gate are recorded in the
[Phase 4 implementation record](https://github.com/myota-platform/myota-docs/blob/main/docs/api-phase4-client-operational-migration.md)
and [operations diagram](https://github.com/myota-platform/myota-docs/blob/main/docs/diagrams/api-phase4-operational-migration.md).

### 6. Public web experience

- [ ] Add production authentication and account/profile pages.
- [ ] Add entity detail pages with geometry, source/provenance summary, rules, access notes, activation history, and community content.
- [ ] Add search by name, reference, country, locality, entity type, programme, and map extent.
- [ ] Add proposal creation/editing, status tracking, requested changes, evidence, and reviewer communication.
- [ ] Add activation logging, ADIF upload, QSO progress, award progress, and programme-specific public workflows.
- [ ] Add per-programme theme/content configuration, locales, accessibility checks, and mobile-first map behavior.
- [ ] Add offline-friendly map/data behavior where programme operations require unreliable connectivity.
- [ ] Build an Android participant application for user workflows only: account and callsign management, programme discovery, map/entity browsing, activation start/close, QSO capture/import, award progress and certificate requests, notifications, and privacy settings. Do not include global administration, programme configuration, geodata approval, role management, or other admin functions.
- [ ] Build an iOS participant application with the same user-only scope and programme-aware experience as Android. Keep administration, moderation, imports, and programme management in the web control plane.
- [ ] Define a shared mobile API/SDK, authentication/session model, offline queue and sync behavior, push-notification contracts, minimum supported OS versions, accessibility requirements, and programme-theme/content delivery before native implementation.

### 7. Reliability, security, and operations

- [x] Add service-owned real metrics, OpenTelemetry request traces, collector-based Prometheus/Grafana dashboards, Tempo storage, per-service/per-route/per-method API performance graphs, Prometheus/Alertmanager availability and latency alerts, and equivalent Grafana-managed rules. See the [observability architecture](https://github.com/myota-platform/myota-docs/blob/main/docs/observability.md). Structured log retention and formal SLOs remain production hardening work.
- [x] Add bounded, fixture-cleaning geodata load profiles for uploads, concurrent edits, preprocessing, promotion, and queue backlog; add PostGIS query timing/slow-query metrics, guarded `EXPLAIN (ANALYZE, BUFFERS)` evidence, JetStream consumer-lag metrics, and dedicated Grafana views. Profiles can target production only with explicit environment acknowledgement, exact host allowlisting, hard workload caps, and separately enabled production cleanup. See the [geodata load/query evidence runbook](https://github.com/myota-platform/myota-docs/blob/main/docs/geodata-load-test-and-query-evidence.md).
- [x] Update large-upload load tests to the resumable API, verify binary parts/checksums and abort recovery, and clean exact-tag terminal upload sessions/parts. Gate image publication on harness regressions; see the [local verification record](https://github.com/myota-platform/myota-docs/blob/main/docs/geodata-load-test-upload-verification.md). Representative non-production capacity qualification remains open.
- [ ] Execute the mutating geodata profiles in a planned test window, review query plans at representative cardinality, and retain the evidence with an operator-approved test run. Production execution must use the dedicated test administrator, hard caps, and verified cleanup.
- [ ] Add Kubernetes readiness/liveness behavior, autoscaling guidance, network policies, pod disruption budgets, and resource profiles.
- [ ] Add secret management, key rotation, image signing, SBOM generation, dependency scanning, and supply-chain verification.
- [ ] Add API gateway authentication, quotas, WAF/rate-limit policy, CORS/CSRF policy, and request-size limits.
- [ ] Add disaster-recovery runbooks, independent core/activity/geodata backups, restore drills, and cluster-split procedures.
- [ ] Complete load, soak, spatial-query, import-throughput, failover, and migration compatibility testing; the bounded geodata profiles and query-evidence tooling exist, but non-production execution and the remaining domain-wide campaigns are outstanding.
- [ ] Add security review for identity, OIDC, callsign evidence, uploaded ADIF, geometry uploads, and admin actions.

### 8. Programme onboarding and migration

- [ ] Define a programme onboarding checklist covering charter, policy ownership, contacts, jurisdictions, entity types, awards, locales, sources, and moderation.
- [ ] Provide programme configuration templates and validation without imposing platform-default eligibility rules.
- [ ] Migrate legacy MPOTA/source records as candidates with provenance; never silently promote them to approved references.
- [ ] Provide import/reconciliation reports, duplicate review, source licensing checks, and programme-owner sign-off.
- [ ] Onboard a second non-MPOTA sample programme end-to-end to prove configuration and policy isolation.
- [ ] Publish operator, approver, programme-admin, geodata-operator, and developer runbooks.

### Release gates

- [ ] **Alpha:** persistent core/geo storage, authenticated users, programme admin draft flow, candidate review, and reproducible local deployment.
- [ ] **Beta:** production geodata adapters, durable events, ADIF processing, award evaluation, admin web, observability, backups, and security review.
- [ ] **Production:** documented programme onboarding, independent recovery testing, accessibility/localization review, load testing, incident runbooks, and programme-owner approval.
