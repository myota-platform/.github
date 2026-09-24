# MyOTA

MyOTA is a programme-agnostic Outdoor Activation Platform for amateur-radio initiatives. It provides reusable identity, programme configuration, geospatial data, activation, QSO, award, approval, and map capabilities. Each programme defines its own charter, eligibility, rules, awards, minimum QSOs, jurisdictions, themes, and content; MyOTA does not copy or inherit rules from POTA or another programme.

The organization login is `myota-platform`; the visible organization name is **MyOTA**.

## Platform at a glance

```text
Universal programme UI
        │
API gateway / ingress
        ├── Identity service       accounts, callsigns, SWLs, scopes, OIDC mappings
        ├── Programme service      programmes, entity types, rules, awards, themes
        ├── Geodata service        PostGIS, imports, provenance, conflation, review
        └── Activity service       activations, QSOs, award evaluation primitives
                 │
        PostgreSQL + PostGIS / event outbox
```

The geodata lifecycle is:

```text
authoritative/imported source → CANDIDATE → PROPOSED → APPROVED
```

Approved entities are public programme references. Candidates remain visibly distinct until reviewed by an approver with the correct programme, jurisdiction, and entity-type scope.

## Repositories

| Repository | Responsibility |
|---|---|
| [`myota-platform`](https://github.com/myota-platform/myota-platform) | Runnable integration bootstrap, dependency-free vertical slice, local gateway, tests, API contracts, migrations, and cross-service smoke path. |
| [`myota-contracts`](https://github.com/myota-platform/myota-contracts) | OpenAPI HTTP contracts, event envelopes, compatibility rules, and future generated clients. |
| [`myota-identity-service`](https://github.com/myota-platform/myota-identity-service) | Amateur-radio-native accounts, operator/SWL participation, multiple callsigns, primary callsign, verification lifecycle, roles, scopes, and optional external identity mappings. |
| [`myota-programme-service`](https://github.com/myota-platform/myota-programme-service) | Programme configuration, programme-owned entity types, rules, awards, jurisdictions, themes, and optional per-programme OIDC settings. |
| [`myota-geodata-service`](https://github.com/myota-platform/myota-geodata-service) | PostGIS entities, source provenance, import runs, ParkServe/OSM/government/manual adapter contracts, deduplication/conflation, candidate review, and approval workflow. |
| [`myota-activity-service`](https://github.com/myota-platform/myota-activity-service) | Activations, QSO primitives, idempotent ingestion, audit events, and programme-specific award evaluation inputs. |
| [`myota-web`](https://github.com/myota-platform/myota-web) | Universal, programme-themed browser experience with approved/candidate map distinction and programme switching. |
| [`myota-admin-web`](https://github.com/myota-platform/myota-admin-web) | Authenticated global administration web for programme configuration, geodata review, identity administration, and activity operations. |
| [`myota-deploy`](https://github.com/myota-platform/myota-deploy) | PostgreSQL/PostGIS bootstrap, Docker Compose manifests, Helm chart, service routing, health probes, and deployment configuration. |
| [`myota-docs`](https://github.com/myota-platform/myota-docs) | Architecture, ADRs, storage-topology decision, QGIS workflow, threat notes, migration strategy, source inspection, and repository map. |

## Storage and geodata

The initial production topology is one PostgreSQL cluster with two databases:

- `myota_core`: identity, programme, activity, awards, permissions, audit, and outbox data.
- `myota_geo`: PostGIS geometry, imported snapshots, source references, conflation candidates, and review records.

This keeps operations simple while giving geodata an independent backup, scaling, and eventual cluster-split path. QGIS is the recommended graphical PostGIS tool for geometry inspection and controlled editing; lifecycle transitions remain API-owned and audited.

Supported adapter designs include ParkServe US, OpenStreetMap tags (`leisure=park`, `leisure=nature_reserve`, `boundary=protected_area`, and `landuse=recreation_ground`), local-government GIS feeds, and manual proposals. Imports preserve source IDs, licensing, attribution, retrieval time, geometry, and refresh semantics.

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
- MPOTA is retained only as synthetic sample data; it is not the platform definition.

## Roadmap

The roadmap is intentionally platform-first. A programme supplies its own charter, policy, eligibility, awards, thresholds, jurisdictions, and content; no roadmap item should be interpreted as copying POTA, MPOTA, or another programme's rules.

### 0. Foundation and bootstrap — complete

- [x] Create the MyOTA organization and establish the repository split.
- [x] Publish the integration bootstrap and all service, contract, web, deployment, and documentation repositories.
- [x] Define OpenAPI-first HTTP contracts and versioned event envelopes.
- [x] Define service ownership for identity, programmes, geodata, and activity.
- [x] Implement dependency-free service boundaries, health endpoints, idempotency handling, and audit-event primitives.
- [x] Implement operator/SWL accounts, multiple callsigns, one primary callsign, lifecycle states, and verification fields.
- [x] Implement programme listing/configuration, programme-owned entity types, rules, themes, and optional OIDC configuration.
- [x] Implement the candidate → proposed → approved geodata lifecycle.
- [x] Implement provenance-aware adapter contracts for ParkServe US, OSM, government GIS, and manual proposals.
- [x] Define PostgreSQL/PostGIS migrations, core/geodata database topology, QGIS views, and the storage ADR.
- [x] Implement activation and QSO primitives with idempotent mutation paths.
- [x] Implement the universal public programme view with programme switching and approved/candidate distinction.
- [x] Add Helm, Compose, CI, threat notes, migration notes, and local Colima smoke coverage.

### 1. Production-grade platform core

Delivered in the integration/runtime and deployment repositories; see `docs/production-core.md` for the operational boundary and known projection-to-relational migration path.

- [x] Replace in-memory runtime stores with service-owned PostgreSQL-backed state repositories and migrations.
- [x] Add connection pooling, transaction boundaries, bounded retry policies, optimistic-safe idempotent writes, and graceful shutdown.
- [x] Add a durable outbox in each service-owned database and connect it to NATS JetStream.
- [x] Add event consumer primitives with replay, event-ID deduplication, dead-letter handling, and schema-major compatibility checks.
- [x] Generate a checked-in typed client and define server validation/compatibility rules from the versioned OpenAPI contract.
- [x] Standardize API errors, pagination, request/correlation IDs, body limits, API version headers, and deprecation policy.
- [x] Add programme-policy versioning so historical activation and award decisions remain reproducible.
- [x] Add migration automation, rollback guidance, seed separation, and backup/restore verification.

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
- [x] Add map-based approver review with candidate/proposed/approved layers, geometry editing, source comparison, review notes, and audit history.
- [x] Add activation/QSO administration with protected activation status and QSO counts; ADIF processing remains a later activity milestone.
- [x] Add translation/content administration with draft, review, publish, fallback, and locale coverage reporting.
- [x] Add an accessible responsive foundation with keyboard-friendly controls, labels, visible loading/error states, and mobile navigation.

### 4. Geodata production pipeline

- [ ] Implement ParkServe US ingestion with licensed-source metadata, snapshot manifests, refresh scheduling, and source-change detection.
- [ ] Implement OSM extraction/filtering for the required tags, attribution preservation, geometry normalization, and regional refresh jobs.
- [ ] Implement configurable local-government GIS adapters for WFS, GeoJSON, Shapefile, and ArcGIS FeatureServer sources.
- [ ] Implement manual proposal import and map drawing with geometry validity, CRS normalization, size limits, and attachment metadata.
- [ ] Implement spatial deduplication/conflation scoring using name, source identifiers, containment, overlap, distance, and jurisdiction.
- [ ] Implement reviewable merge/keep-separate/ignore decisions with full provenance and reversible history.
- [ ] Implement source disappearance semantics: unchanged, stale, retired, or review-required according to programme policy.
- [ ] Integrate QGIS staging/edit workflows with least-privilege roles and API-owned approval transitions.
- [ ] Add tile/vector-tile delivery, bounding-box queries, spatial indexes, caching, and map performance budgets.

### 5. Activity, awards, and programme execution

- [ ] Implement activation start/close, validity windows, location checks, operator/callsign authorization, and programme rule evaluation.
- [ ] Implement ADIF upload, object storage, malware scanning, parsing, validation, deduplication, and asynchronous processing.
- [ ] Implement QSO normalization, worked-station identity, band/mode validation, time-window rules, and correction workflows.
- [ ] Implement programme-owned award definitions, award-rule versions, qualification evaluation, publication, retirement, and recalculation.
- [ ] Add activator, hunter, entity, programme, and jurisdiction statistics with reproducible aggregation jobs.
- [ ] Add public activation history, award progress, leaderboards, downloadable results, and privacy-aware callsign display.
- [ ] Add notifications for proposal decisions, import failures, award qualification, and account security events.

### 6. Public web experience

- [ ] Add production authentication and account/profile pages.
- [ ] Add entity detail pages with geometry, source/provenance summary, rules, access notes, activation history, and community content.
- [ ] Add search by name, reference, country, locality, entity type, programme, and map extent.
- [ ] Add proposal creation/editing, status tracking, requested changes, evidence, and reviewer communication.
- [ ] Add activation logging, ADIF upload, QSO progress, award progress, and programme-specific public workflows.
- [ ] Add per-programme theme/content configuration, locales, accessibility checks, and mobile-first map behavior.
- [ ] Add offline-friendly map/data behavior where programme operations require unreliable connectivity.

### 7. Reliability, security, and operations

- [ ] Add structured logs, metrics, distributed traces, dashboards, alerts, and SLOs per service.
- [ ] Add Kubernetes readiness/liveness behavior, autoscaling guidance, network policies, pod disruption budgets, and resource profiles.
- [ ] Add secret management, key rotation, image signing, SBOM generation, dependency scanning, and supply-chain verification.
- [ ] Add API gateway authentication, quotas, WAF/rate-limit policy, CORS/CSRF policy, and request-size limits.
- [ ] Add disaster-recovery runbooks, independent core/geodata backups, restore drills, and cluster-split procedures.
- [ ] Add load, soak, spatial-query, import-throughput, failover, and migration compatibility tests.
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
