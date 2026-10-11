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

The [current system state](https://github.com/myota-platform/myota-docs/blob/main/docs/architecture/current-state.md)
is the dated source for repository revisions, deployed images, live K3s status,
architecture, and remaining evidence gates. The latest read-only reconciliation
found Helm revision 246 deployed, Fleet Ready with 67/67 resources, and 23/23
MyOTA Deployments Ready on a single-node K3s host. These readiness checks do not
establish production qualification or off-node recovery.

- Cross-repository review Phases 0–4 are complete, including duplicate YAML key
  rejection and the final handler/mirror check. See
  [REVIEW.md](https://github.com/myota-platform/myota-docs/blob/main/REVIEW.md).
- NATS migration Phases 0–6 are complete within their recorded evidence bounds.
  The Phase 5 24-hour observation was waived and closed early, not completed as
  a full-day window. Six unresolved Geodata application DLQ records remain
  preserved. PostgreSQL remains authoritative; off-node NATS recovery is
  deferred.
- NATS Surveyor and eleven Grafana dashboards are deployed. Operations has no
  NATS connection; it retains storage status/history and Grafana role
  resolution.
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
  storage status/history and Grafana role resolution.
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

Snapshot: **2026-10-11 18:11 UTC**. The organization-wide GitHub issue search returned 36 open issues across all repositories. Related issues in different repositories are grouped under a shared workstream below. This is a point-in-time index; check GitHub for current state.

| Workstream | Repository | Issue | Open task |
| --- | --- | --- | --- |
| **NATS migration evidence and readiness** |  |  |  |
|  | [myota-contracts](https://github.com/myota-platform/myota-contracts) | [#1](https://github.com/myota-platform/myota-contracts/issues/1) | Define JetStream envelope and event/consumer registry |
|  | [myota-identity-service](https://github.com/myota-platform/myota-identity-service) | [#1](https://github.com/myota-platform/myota-identity-service/issues/1) | Confirm Identity event subscriber dispositions |
|  | [myota-programme-service](https://github.com/myota-platform/myota-programme-service) | [#1](https://github.com/myota-platform/myota-programme-service/issues/1) | Confirm Programme event subscriber dispositions |
|  | [myota-activity-service](https://github.com/myota-platform/myota-activity-service) | [#1](https://github.com/myota-platform/myota-activity-service/issues/1) | Close Activity notification and worker evidence gates |
|  | [myota-geodata-service](https://github.com/myota-platform/myota-geodata-service) | [#2](https://github.com/myota-platform/myota-geodata-service/issues/2) | Close Geodata transaction and recovery evidence gates |
|  | [myota-deploy](https://github.com/myota-platform/myota-deploy) | [#3](https://github.com/myota-platform/myota-deploy/issues/3) | Qualify JetStream limits, relay DLQ recovery, and deployment controls |
|  | [myota-operations-service](https://github.com/myota-platform/myota-operations-service) | [#1](https://github.com/myota-platform/myota-operations-service/issues/1) | Provide read-only JetStream operational evidence |
| **Geodata Website and Description fields** |  |  |  |
|  | [myota-geodata-service](https://github.com/myota-platform/myota-geodata-service) | [#3](https://github.com/myota-platform/myota-geodata-service/issues/3) | Add Website and Description metadata to geodata entities |
|  | [myota-contracts](https://github.com/myota-platform/myota-contracts) | [#3](https://github.com/myota-platform/myota-contracts/issues/3) | Add Website and Description to Geodata API schemas |
|  | [myota-admin-web](https://github.com/myota-platform/myota-admin-web) | [#2](https://github.com/myota-platform/myota-admin-web/issues/2) | Add Website and Description fields to Geodata administration |
| **Geodata capacity qualification** |  |  |  |
|  | [myota-geodata-service](https://github.com/myota-platform/myota-geodata-service) | [#4](https://github.com/myota-platform/myota-geodata-service/issues/4) | Qualify Geodata capacity and write-contention limits before expansion |
|  | [myota-deploy](https://github.com/myota-platform/myota-deploy) | [#5](https://github.com/myota-platform/myota-deploy/issues/5) | Qualify Geodata scaling and recovery deployment gates before expansion |
| **Programme governance, jurisdiction, and eligibility** |  |  |  |
|  | [myota-docs](https://github.com/myota-platform/myota-docs) | [#4](https://github.com/myota-platform/myota-docs/issues/4) | Define phased delivery decisions for programme configuration |
|  | [myota-programme-service](https://github.com/myota-platform/myota-programme-service) | [#2](https://github.com/myota-platform/myota-programme-service/issues/2) | Add versioned programme governance, jurisdiction, and eligibility policy |
|  | [myota-contracts](https://github.com/myota-platform/myota-contracts) | [#4](https://github.com/myota-platform/myota-contracts/issues/4) | Define contracts for versioned programme policy and publication snapshots |
|  | [myota-admin-web](https://github.com/myota-platform/myota-admin-web) | [#3](https://github.com/myota-platform/myota-admin-web/issues/3) | Add audited programme governance and policy configuration workflows |
|  | [myota-identity-service](https://github.com/myota-platform/myota-identity-service) | [#2](https://github.com/myota-platform/myota-identity-service/issues/2) | Bind programme governance to scoped identity and OIDC controls |
|  | [myota-geodata-service](https://github.com/myota-platform/myota-geodata-service) | [#5](https://github.com/myota-platform/myota-geodata-service/issues/5) | Enforce published programme geodata eligibility and moderation policy |
|  | [myota-activity-service](https://github.com/myota-platform/myota-activity-service) | [#2](https://github.com/myota-platform/myota-activity-service/issues/2) | Evaluate activity against immutable published programme policy versions |
| **Structured logging and request-to-worker correlation** |  |  |  |
|  | [myota-deploy](https://github.com/myota-platform/myota-deploy) | [#6](https://github.com/myota-platform/myota-deploy/issues/6) | Close structured logging deployment and acceptance gaps |
|  | [myota-identity-service](https://github.com/myota-platform/myota-identity-service) | [#3](https://github.com/myota-platform/myota-identity-service/issues/3) | Propagate request correlation through Identity outbox work |
|  | [myota-programme-service](https://github.com/myota-platform/myota-programme-service) | [#4](https://github.com/myota-platform/myota-programme-service/issues/4) | Propagate request correlation through Programme outbox events |
|  | [myota-activity-service](https://github.com/myota-platform/myota-activity-service) | [#3](https://github.com/myota-platform/myota-activity-service/issues/3) | Propagate request correlation through Activity outbox events |
|  | [myota-geodata-service](https://github.com/myota-platform/myota-geodata-service) | [#6](https://github.com/myota-platform/myota-geodata-service/issues/6) | Propagate request correlation through Geodata outbox work |
| **Identity and Programme metrics coverage** |  |  |  |
|  | [myota-identity-service](https://github.com/myota-platform/myota-identity-service) | [#4](https://github.com/myota-platform/myota-identity-service/issues/4) | Restore Identity service metrics coverage |
|  | [myota-programme-service](https://github.com/myota-platform/myota-programme-service) | [#3](https://github.com/myota-platform/myota-programme-service/issues/3) | Restore Programme service metrics coverage |
|  | [myota-deploy](https://github.com/myota-platform/myota-deploy) | [#7](https://github.com/myota-platform/myota-deploy/issues/7) | Scrape and dashboard Identity and Programme service metrics |
| **Admin UI internationalization** |  |  |  |
|  | [myota-docs](https://github.com/myota-platform/myota-docs) | [#1](https://github.com/myota-platform/myota-docs/issues/1) | Complete Admin UI internationalization Phase 0 policy and inventory |
|  | [myota-admin-web](https://github.com/myota-platform/myota-admin-web) | [#4](https://github.com/myota-platform/myota-admin-web/issues/4) | Implement Admin UI internationalization after Phase 0 decisions |
| **Future award-condition model** |  |  |  |
|  | [myota-docs](https://github.com/myota-platform/myota-docs) | [#2](https://github.com/myota-platform/myota-docs/issues/2) | Complete Phase 0 decisions for the future award-condition model |
| **Charter gap roadmap decomposition** |  |  |  |
|  | [myota-docs](https://github.com/myota-platform/myota-docs) | [#3](https://github.com/myota-platform/myota-docs/issues/3) | Decompose charter gaps into owner-scoped delivery roadmaps |
| **SeaweedFS monitoring retirement** |  |  |  |
|  | [myota-deploy](https://github.com/myota-platform/myota-deploy) | [#8](https://github.com/myota-platform/myota-deploy/issues/8) | Make SeaweedFS Grafana observability the sole monitoring path |
|  | [myota-operations-service](https://github.com/myota-platform/myota-operations-service) | [#2](https://github.com/myota-platform/myota-operations-service/issues/2) | Retire SeaweedFS sampler, API, and sampled-history database table |
|  | [myota-contracts](https://github.com/myota-platform/myota-contracts) | [#5](https://github.com/myota-platform/myota-contracts/issues/5) | Retire the Operations SeaweedFS monitoring API contract |
|  | [myota-admin-web](https://github.com/myota-platform/myota-admin-web) | [#5](https://github.com/myota-platform/myota-admin-web/issues/5) | Remove the SeaweedFS monitoring page from the Admin UI |
|  | [myota-docs](https://github.com/myota-platform/myota-docs) | [#5](https://github.com/myota-platform/myota-docs/issues/5) | Document retirement of the SeaweedFS Admin monitoring page |
