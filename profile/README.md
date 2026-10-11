# MyOTA

MyOTA is open infrastructure for geographic amateur-radio activation
programmes. It helps programmes manage places, activations, QSOs, awards, and
community review. Each programme defines its own charter, eligibility, rules,
awards, jurisdictions, and content. MyOTA does not copy rules from POTA, MPOTA,
or another programme.

The project is motivated by an accessibility gap: many operators live near
legitimate local places that existing activation programmes do not recognize.
MyOTA aims to make those places usable for short, urban, QRP, VHF/UHF, satellite,
and spontaneous activations while remaining complementary to other programmes.

## Current state

The [current system state](https://github.com/myota-platform/myota-docs/blob/main/docs/architecture/current-state.md) is the dated source for repository revisions, deployed images, live K3s status, architecture, and remaining evidence gates. Helm revision 253 is deployed from Deploy commit `2968cdaa2d785975c9eab29ce3ca9eda99df594b`; all 27/27 Deployments and 67/67 chart resources are ready. Fleet currently reports NotReady because the Geodata HPA could not read CPU utilization during rollout. The single-node readiness checks do not establish production qualification or off-node recovery.

- Cross-repository review Phases 0–4 are complete, including duplicate YAML key
  rejection and the final handler/mirror check. See
  [REVIEW.md](https://github.com/myota-platform/myota-docs/blob/main/REVIEW.md).
- NATS migration Phases 0–6 are complete within their recorded evidence bounds.
  The Phase 5 24-hour observation was waived and closed early, not completed as
  a full-day window. Six unresolved Geodata application DLQ records remain
  preserved. PostgreSQL remains authoritative; off-node NATS recovery is
  deferred.
- NATS Surveyor and eleven Grafana dashboards are deployed. Operations has no
  NATS connection and retains bounded read-only PostgreSQL/PostGIS metrics and
  Grafana role resolution. SeaweedFS monitoring now uses the Collector and
  provisioned Grafana dashboard; the Operations sampler, API, history table,
  and dedicated Admin page are retired.
- Geodata Phase 5 was administratively closed by owner acceptance on
  11 October. Broad capacity, independent writes, node/storage failure, canary,
  rollback, and scaling above three replicas remain unverified and were
  accepted for closure. Reopen these gates before expanding capacity. See the
  [closure decision](https://github.com/myota-platform/myota-docs/blob/main/docs/geodata/evidence/phase5-owner-acceptance-2026-10-11.md)
  and [read-only evidence](https://github.com/myota-platform/myota-docs/blob/main/docs/geodata/evidence/phase5-read-only-follow-up-2026-10-11.md).
- Logging/observability acceptance also remains open for authenticated Grafana
  navigation, request-to-worker correlation, Loki privacy/cardinality review,
  Compose parity, and Identity/Programme metrics coverage.
- Programme configuration, Admin UI internationalization, award-condition
  modeling, and charter delivery remain planned product work.

Use the Docs [work-in-progress index](https://github.com/myota-platform/myota-docs/blob/main/docs/work-in-progress/README.md),
[To do index](https://github.com/myota-platform/myota-docs/blob/main/docs/to-do/README.md),
and [prioritized backlog](https://github.com/myota-platform/myota-docs/blob/main/docs/to-do/prioritized-backlog.md)
for tasks. The linked roadmap owns its checkboxes and the dated evidence record
owns the result of each verification.

## Project principles

- Programme policies and governance are programme-owned and versioned.
- PostgreSQL is the source of truth for domain state, job state, and recovery.
- Services own their data and expose versioned APIs and event contracts.
- NATS JetStream is bounded delivery and replay infrastructure, not an archive.
- Admin capabilities remain separate from participant workflows.
- Accessibility, auditability, idempotency, and privacy are core requirements.

## Repositories

The [repository map](https://github.com/myota-platform/myota-docs/blob/main/docs/architecture/repository-map.md)
documents source ownership and synchronized mirrors. The main repositories are:

- [Contracts](https://github.com/myota-platform/myota-contracts) — OpenAPI and
  event contracts.
- [Identity](https://github.com/myota-platform/myota-identity-service) —
  accounts, callsigns, authentication, and authorization.
- [Programme](https://github.com/myota-platform/myota-programme-service) —
  programme configuration and policy.
- [Geodata](https://github.com/myota-platform/myota-geodata-service) — spatial
  entities, provenance, imports, and review.
- [Activity](https://github.com/myota-platform/myota-activity-service) —
  activations, QSOs, awards, and notifications.
- [Operations](https://github.com/myota-platform/myota-operations-service) —
  bounded read-only PostgreSQL/PostGIS metrics and Grafana role resolution.
- [Deploy](https://github.com/myota-platform/myota-deploy) — Compose, Helm,
  runtime configuration, and infrastructure.
- [Platform](https://github.com/myota-platform/myota-platform) — integration
  bootstrap and synchronized deployment/runtime mirrors.
- [Docs](https://github.com/myota-platform/myota-docs) — architecture,
  decisions, operations, evidence, and work tracking.
- [Participant web](https://github.com/myota-platform/myota-web) and
  [admin web](https://github.com/myota-platform/myota-admin-web).

Read the [project charter](https://github.com/myota-platform/myota-docs/blob/main/docs/governance/project-charter.md)
and [charter gap analysis](https://github.com/myota-platform/myota-docs/blob/main/docs/governance/charter-gap-analysis.md)
for the product purpose and longer-term delivery gaps.
## Open GitHub issues

Snapshot: **2026-10-11 19:35 UTC**. The organization-wide GitHub issue search returned 41 open issues across all repositories. Related issues in different repositories are grouped under a shared workstream below. Labels use the organization-wide GitHub defaults consistently: `enhancement` marks product, runtime, contract, deployment, and infrastructure work; `documentation` marks Docs-owned inventory, decision, and roadmap work. This is a point-in-time index; check GitHub for current state.

| Workstream | Repository | Issue | Labels |
| --- | --- | --- | --- |
| **NATS migration evidence and readiness** |  |  |  |
|  | [myota-contracts](https://github.com/myota-platform/myota-contracts) | [#1: Define JetStream envelope and event/consumer registry](https://github.com/myota-platform/myota-contracts/issues/1) | `enhancement` |
|  | [myota-identity-service](https://github.com/myota-platform/myota-identity-service) | [#1: Confirm Identity event subscriber dispositions](https://github.com/myota-platform/myota-identity-service/issues/1) | `enhancement` |
|  | [myota-programme-service](https://github.com/myota-platform/myota-programme-service) | [#1: Confirm Programme event subscriber dispositions](https://github.com/myota-platform/myota-programme-service/issues/1) | `enhancement` |
|  | [myota-activity-service](https://github.com/myota-platform/myota-activity-service) | [#1: Close Activity notification and worker evidence gates](https://github.com/myota-platform/myota-activity-service/issues/1) | `enhancement` |
|  | [myota-geodata-service](https://github.com/myota-platform/myota-geodata-service) | [#2: Close Geodata transaction and recovery evidence gates](https://github.com/myota-platform/myota-geodata-service/issues/2) | `enhancement` |
|  | [myota-deploy](https://github.com/myota-platform/myota-deploy) | [#3: Qualify JetStream limits, relay DLQ recovery, and deployment controls](https://github.com/myota-platform/myota-deploy/issues/3) | `enhancement` |
|  | [myota-operations-service](https://github.com/myota-platform/myota-operations-service) | [#1: Provide read-only JetStream operational evidence](https://github.com/myota-platform/myota-operations-service/issues/1) | `enhancement` |
| **Geodata Website and Description fields** |  |  |  |
|  | [myota-geodata-service](https://github.com/myota-platform/myota-geodata-service) | [#3: Add Website and Description metadata to geodata entities](https://github.com/myota-platform/myota-geodata-service/issues/3) | `enhancement` |
|  | [myota-contracts](https://github.com/myota-platform/myota-contracts) | [#3: Add Website and Description to Geodata API schemas](https://github.com/myota-platform/myota-contracts/issues/3) | `enhancement` |
|  | [myota-admin-web](https://github.com/myota-platform/myota-admin-web) | [#2: Add Website and Description fields to Geodata administration](https://github.com/myota-platform/myota-admin-web/issues/2) | `enhancement` |
| **Geodata capacity qualification** |  |  |  |
|  | [myota-geodata-service](https://github.com/myota-platform/myota-geodata-service) | [#4: Qualify Geodata capacity and write-contention limits before expansion](https://github.com/myota-platform/myota-geodata-service/issues/4) | `enhancement` |
|  | [myota-deploy](https://github.com/myota-platform/myota-deploy) | [#5: Qualify Geodata scaling and recovery deployment gates before expansion](https://github.com/myota-platform/myota-deploy/issues/5) | `enhancement` |
| **Programme governance, jurisdiction, and eligibility** |  |  |  |
|  | [myota-docs](https://github.com/myota-platform/myota-docs) | [#4: Define phased delivery decisions for programme configuration](https://github.com/myota-platform/myota-docs/issues/4) | `documentation` |
|  | [myota-programme-service](https://github.com/myota-platform/myota-programme-service) | [#2: Add versioned programme governance, jurisdiction, and eligibility policy](https://github.com/myota-platform/myota-programme-service/issues/2) | `enhancement` |
|  | [myota-contracts](https://github.com/myota-platform/myota-contracts) | [#4: Define contracts for versioned programme policy and publication snapshots](https://github.com/myota-platform/myota-contracts/issues/4) | `enhancement` |
|  | [myota-admin-web](https://github.com/myota-platform/myota-admin-web) | [#3: Add audited programme governance and policy configuration workflows](https://github.com/myota-platform/myota-admin-web/issues/3) | `enhancement` |
|  | [myota-identity-service](https://github.com/myota-platform/myota-identity-service) | [#2: Bind programme governance to scoped identity and OIDC controls](https://github.com/myota-platform/myota-identity-service/issues/2) | `enhancement` |
|  | [myota-geodata-service](https://github.com/myota-platform/myota-geodata-service) | [#5: Enforce published programme geodata eligibility and moderation policy](https://github.com/myota-platform/myota-geodata-service/issues/5) | `enhancement` |
|  | [myota-activity-service](https://github.com/myota-platform/myota-activity-service) | [#2: Evaluate activity against immutable published programme policy versions](https://github.com/myota-platform/myota-activity-service/issues/2) | `enhancement` |
| **Structured logging and request-to-worker correlation** |  |  |  |
|  | [myota-deploy](https://github.com/myota-platform/myota-deploy) | [#6: Close structured logging deployment and acceptance gaps](https://github.com/myota-platform/myota-deploy/issues/6) | `enhancement` |
|  | [myota-identity-service](https://github.com/myota-platform/myota-identity-service) | [#3: Propagate request correlation through Identity outbox work](https://github.com/myota-platform/myota-identity-service/issues/3) | `enhancement` |
|  | [myota-programme-service](https://github.com/myota-platform/myota-programme-service) | [#4: Propagate request correlation through Programme outbox events](https://github.com/myota-platform/myota-programme-service/issues/4) | `enhancement` |
|  | [myota-activity-service](https://github.com/myota-platform/myota-activity-service) | [#3: Propagate request correlation through Activity outbox events](https://github.com/myota-platform/myota-activity-service/issues/3) | `enhancement` |
|  | [myota-geodata-service](https://github.com/myota-platform/myota-geodata-service) | [#6: Propagate request correlation through Geodata outbox work](https://github.com/myota-platform/myota-geodata-service/issues/6) | `enhancement` |
| **Identity and Programme metrics coverage** |  |  |  |
|  | [myota-identity-service](https://github.com/myota-platform/myota-identity-service) | [#4: Restore Identity service metrics coverage](https://github.com/myota-platform/myota-identity-service/issues/4) | `enhancement` |
|  | [myota-programme-service](https://github.com/myota-platform/myota-programme-service) | [#3: Restore Programme service metrics coverage](https://github.com/myota-platform/myota-programme-service/issues/3) | `enhancement` |
|  | [myota-deploy](https://github.com/myota-platform/myota-deploy) | [#7: Scrape and dashboard Identity and Programme service metrics](https://github.com/myota-platform/myota-deploy/issues/7) | `enhancement` |
| **Observability dashboard enhancements** |  |  |  |
|  | [myota-deploy](https://github.com/myota-platform/myota-deploy) | [#11: Add a K3s workload health and capacity Grafana dashboard](https://github.com/myota-platform/myota-deploy/issues/11) | `enhancement` |
|  | [myota-deploy](https://github.com/myota-platform/myota-deploy) | [#12: Add an application workflow and JetStream delivery Grafana dashboard](https://github.com/myota-platform/myota-deploy/issues/12) | `enhancement` |
|  | [myota-deploy](https://github.com/myota-platform/myota-deploy) | [#13: Expand the PostgreSQL and PostGIS Grafana dashboard with database health panels](https://github.com/myota-platform/myota-deploy/issues/13) | `enhancement` |
| **Geodata OpenTelemetry TracerProvider warning** |  |  |  |
|  | [myota-geodata-service](https://github.com/myota-platform/myota-geodata-service) | [#7: Initialize the Geodata OpenTelemetry TracerProvider once per process](https://github.com/myota-platform/myota-geodata-service/issues/7) | `enhancement` |
|  | [myota-deploy](https://github.com/myota-platform/myota-deploy) | [#14: Roll out and verify the Geodata TracerProvider initialization fix](https://github.com/myota-platform/myota-deploy/issues/14) | `enhancement` |
| **Admin UI internationalization** |  |  |  |
|  | [myota-docs](https://github.com/myota-platform/myota-docs) | [#1: Complete Admin UI internationalization Phase 0 policy and inventory](https://github.com/myota-platform/myota-docs/issues/1) | `documentation` |
|  | [myota-admin-web](https://github.com/myota-platform/myota-admin-web) | [#4: Implement Admin UI internationalization after Phase 0 decisions](https://github.com/myota-platform/myota-admin-web/issues/4) | `enhancement` |
| **Future award-condition model** |  |  |  |
|  | [myota-docs](https://github.com/myota-platform/myota-docs) | [#2: Complete Phase 0 decisions for the future award-condition model](https://github.com/myota-platform/myota-docs/issues/2) | `documentation` |
| **Charter gap roadmap decomposition** |  |  |  |
|  | [myota-docs](https://github.com/myota-platform/myota-docs) | [#3: Decompose charter gaps into owner-scoped delivery roadmaps](https://github.com/myota-platform/myota-docs/issues/3) | `documentation` |
| **Operations Service retirement and OpenTelemetry migration** |  |  |  |
|  | [myota-operations-service](https://github.com/myota-platform/myota-operations-service) | [#3: Retire Operations Service and archive its repository](https://github.com/myota-platform/myota-operations-service/issues/3) | `enhancement` |
|  | [myota-deploy](https://github.com/myota-platform/myota-deploy) | [#9: Replace Operations workload with OpenTelemetry](https://github.com/myota-platform/myota-deploy/issues/9) | `enhancement` |
|  | [myota-contracts](https://github.com/myota-platform/myota-contracts) | [#6: Remove retired Operations API from contracts and clients](https://github.com/myota-platform/myota-contracts/issues/6) | `enhancement` |
|  | [myota-admin-web](https://github.com/myota-platform/myota-admin-web) | [#6: Move Grafana auth proxy off Operations](https://github.com/myota-platform/myota-admin-web/issues/6) | `enhancement` |
|  | [myota-docs](https://github.com/myota-platform/myota-docs) | [#6: Document complete Operations Service retirement and OpenTelemetry replacements](https://github.com/myota-platform/myota-docs/issues/6) | `documentation` |
