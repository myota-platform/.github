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

The [current system state](https://github.com/myota-platform/myota-docs/blob/main/docs/architecture/current-state.md) is the dated source for repository revisions, deployed images, live K3s status, architecture, and remaining evidence gates. As of 2026-10-12 07:56 UTC, Helm revision 281 is deployed; Prometheus and Grafana are 1/1, all 23 Deployments / 27 replicas are ready, and the MyOTA Fleet GitRepo is Ready at Deploy commit `9d80741`; its BundleDeployment is Ready at sync generation 94/94 with matching staged/applied deployment IDs. Prometheus loaded 33 runbook-linked rules; Grafana mounted two alert runbook links and three dashboard links and logged successful dashboard provisioning. Remaining logging acceptance gates are authenticated navigation, representative capacity/recovery, and Compose runtime parity. See current-state and logging evidence for exact IDs. Operations has been removed from runtime, APIs, and observability ownership; only repository archival remains open in Operations issue #3. The NATS migration evidence/readiness issues are closed within their recorded evidence bounds; the [issue closeout record](https://github.com/myota-platform/myota-docs/blob/main/docs/operations/messaging/evidence/nats-readiness-issue-closeout-2026-10-11.md) records accepted limits. Single-node readiness does not establish production qualification or off-node recovery.

- Cross-repository review Phases 0–4 are complete, including duplicate YAML key
  rejection and the final handler/mirror check. See
  [REVIEW.md](https://github.com/myota-platform/myota-docs/blob/main/REVIEW.md).
- NATS migration Phases 0–6 are complete within their recorded evidence bounds.
  The Phase 5 24-hour observation was waived and closed early, not completed as
  a full-day window. Six unresolved Geodata application DLQ records remain
  preserved. PostgreSQL remains authoritative; off-node NATS recovery is
  deferred.
- NATS Surveyor and eleven Grafana dashboards are deployed. The Operations
  runtime, API, sampler, and history tables are retired. OpenTelemetry collects
  bounded read-only PostgreSQL/PostGIS metrics, while Gateway and Identity own
  Grafana authentication and role resolution. SeaweedFS monitoring uses the
  Collector and provisioned Grafana dashboard.
- Geodata Phase 5 was administratively closed by owner acceptance on
  11 October. Broad capacity, independent writes, node/storage failure, canary,
  rollback, and scaling above three replicas remain unverified and were
  accepted for closure. Reopen these gates before expanding capacity. See the
  [closure decision](https://github.com/myota-platform/myota-docs/blob/main/docs/geodata/evidence/phase5-owner-acceptance-2026-10-11.md)
  and [read-only evidence](https://github.com/myota-platform/myota-docs/blob/main/docs/geodata/evidence/phase5-read-only-follow-up-2026-10-11.md).
- Logging and request-to-worker correlation are deployed for Identity,
  Programme, Activity, and Geodata; their service-owned issues are closed.
  Deploy #6 remains open for authenticated Grafana navigation, representative
  capacity/recovery qualification, and Compose runtime parity. Helm/Fleet status
  has converged at revision 281 and sync 94/94. The corrected rule and dashboard
  assets are loaded and provisioned, but authenticated trace/log navigation is
  untested. Loki's three indexed labels,
  sampled payload exclusion, 14-day policy, and compactor activity are verified
  within the limits in the
  [12 October evidence](https://github.com/myota-platform/myota-docs/blob/main/docs/observability/evidence/logging-implementation-2026-10-12.md).
  Identity and Programme metrics coverage is also verified; see the
  [metrics evidence](https://github.com/myota-platform/myota-docs/blob/main/docs/observability/evidence/identity-programme-metrics-2026-10-11.md).
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
  retired historical implementation; runtime dependencies are removed and
  repository archival remains open in issue #3.
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

Snapshot: **2026-10-13 07:44 UTC**. The organization-wide GitHub issue search returned 31 open issues across all repositories. Related issues in different repositories are grouped under a shared workstream below. Labels use the organization-wide GitHub defaults consistently: `enhancement` marks product, runtime, contract, deployment, and infrastructure work; `documentation` marks Docs-owned inventory, decision, and roadmap work. This is a point-in-time index; check GitHub for current state.

| Workstream | Repository | Issue | Labels |
| --- | --- | --- | --- |
| **Testing branches and quality-gated pull requests** |  |  |  |
|  | [.github](https://github.com/myota-platform/.github) | [#2: Set workspace feature-branch, testing-branch, and quality-gated PR workflow](https://github.com/myota-platform/.github/issues/2) | `documentation` |
|  | [myota-activity-service](https://github.com/myota-platform/myota-activity-service) | [#6: Create protected testing branch for Activity service artifact builds](https://github.com/myota-platform/myota-activity-service/issues/6) | `enhancement` |
|  | [myota-admin-web](https://github.com/myota-platform/myota-admin-web) | [#7: Create protected testing branch for Admin Web artifact builds](https://github.com/myota-platform/myota-admin-web/issues/7) | `enhancement` |
|  | [myota-deploy](https://github.com/myota-platform/myota-deploy) | [#17: Create protected testing branch for Deploy artifact builds](https://github.com/myota-platform/myota-deploy/issues/17) | `enhancement` |
|  | [myota-geodata-service](https://github.com/myota-platform/myota-geodata-service) | [#10: Create protected testing branch for Geodata service artifact builds](https://github.com/myota-platform/myota-geodata-service/issues/10) | `enhancement` |
|  | [myota-identity-service](https://github.com/myota-platform/myota-identity-service) | [#5: Create protected testing branch for Identity service artifact builds](https://github.com/myota-platform/myota-identity-service/issues/5) | `enhancement` |
|  | [myota-operations-service](https://github.com/myota-platform/myota-operations-service) | [#4: Create protected testing branch for Operations service artifact builds](https://github.com/myota-platform/myota-operations-service/issues/4) | `enhancement` |
|  | [myota-platform](https://github.com/myota-platform/myota-platform) | [#2: Create protected testing branch for Platform artifact builds](https://github.com/myota-platform/myota-platform/issues/2) | `enhancement` |
|  | [myota-programme-service](https://github.com/myota-platform/myota-programme-service) | [#5: Create protected testing branch for Programme service artifact builds](https://github.com/myota-platform/myota-programme-service/issues/5) | `enhancement` |
|  | [myota-web](https://github.com/myota-platform/myota-web) | [#1: Create testing branch and PR gates for future Web artifacts](https://github.com/myota-platform/myota-web/issues/1) | `enhancement` |
| **Database migration baselines and data-preserving rebuilds** |  |  |  |
|  | [myota-deploy](https://github.com/myota-platform/myota-deploy) | [#15: Consolidate myota_core schema into one baseline migration](https://github.com/myota-platform/myota-deploy/issues/15) | `enhancement` |
|  | [myota-deploy](https://github.com/myota-platform/myota-deploy) | [#16: Back up and rebuild myota_core from its baseline, then restore data](https://github.com/myota-platform/myota-deploy/issues/16) | `enhancement` |
|  | [myota-activity-service](https://github.com/myota-platform/myota-activity-service) | [#4: Consolidate myota_activity schema into one baseline migration](https://github.com/myota-platform/myota-activity-service/issues/4) | `enhancement` |
|  | [myota-activity-service](https://github.com/myota-platform/myota-activity-service) | [#5: Back up and rebuild myota_activity from its baseline, then restore data](https://github.com/myota-platform/myota-activity-service/issues/5) | `enhancement` |
|  | [myota-geodata-service](https://github.com/myota-platform/myota-geodata-service) | [#8: Consolidate myota_geo schema into one PostGIS baseline migration](https://github.com/myota-platform/myota-geodata-service/issues/8) | `enhancement` |
|  | [myota-geodata-service](https://github.com/myota-platform/myota-geodata-service) | [#9: Back up and rebuild myota_geo from its PostGIS baseline, then restore data](https://github.com/myota-platform/myota-geodata-service/issues/9) | `enhancement` |
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
| **Admin UI internationalization** |  |  |  |
|  | [myota-docs](https://github.com/myota-platform/myota-docs) | [#1: Complete Admin UI internationalization Phase 0 policy and inventory](https://github.com/myota-platform/myota-docs/issues/1) | `documentation` |
|  | [myota-admin-web](https://github.com/myota-platform/myota-admin-web) | [#4: Implement Admin UI internationalization after Phase 0 decisions](https://github.com/myota-platform/myota-admin-web/issues/4) | `enhancement` |
| **Future award-condition model** |  |  |  |
|  | [myota-docs](https://github.com/myota-platform/myota-docs) | [#2: Complete Phase 0 decisions for the future award-condition model](https://github.com/myota-platform/myota-docs/issues/2) | `documentation` |
| **Charter gap roadmap decomposition** |  |  |  |
|  | [myota-docs](https://github.com/myota-platform/myota-docs) | [#3: Decompose charter gaps into owner-scoped delivery roadmaps](https://github.com/myota-platform/myota-docs/issues/3) | `documentation` |
| **Operations Service retirement and OpenTelemetry migration** |  |  |  |
|  | [myota-operations-service](https://github.com/myota-platform/myota-operations-service) | [#3: Retire Operations Service and archive its repository](https://github.com/myota-platform/myota-operations-service/issues/3) | `enhancement` |
