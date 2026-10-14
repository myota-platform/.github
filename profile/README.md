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

Engineering contributions use short-lived feature/fix branches and quality-
gated pull requests. See the organization [contributor workflow](https://github.com/myota-platform/.github/blob/main/CONTRIBUTING.md).

The [current system state](https://github.com/myota-platform/myota-docs/blob/main/docs/architecture/current-state.md) is the dated source for repository revisions, deployed images, live K3s status, architecture, and remaining evidence gates. As of 2026-10-13 19:56 UTC, production Helm revision 288 is deployed and Fleet reports 71/71 resources Ready.

The isolated release is deployed in namespace `myota-test` at Helm revision 37. Fleet tracks Deploy `testing` commit [`e4791b4`](https://github.com/myota-platform/myota-deploy/commit/e4791b4b1dd13fd4bd4d3c2672c2d42cdd73ac56) and reports Ready. PR [#49](https://github.com/myota-platform/myota-deploy/pull/49) updated the testing image digests after Admin Web, Geodata, Gateway, and Platform images were published; follow-up PR [#50](https://github.com/myota-platform/myota-deploy/pull/50) removed the duplicate test-branch poller. All 24 test Deployments and 3 StatefulSets have their desired replicas available, and both test UIs return HTTP 200 at [admin.test.myota.top](https://admin.test.myota.top) and [test.myota.top](https://test.myota.top). The non-Identity relational seed and re-copied object bucket are verified. After the initial Identity omission was found, one requested account, its password hash, and administrator role were copied to test with explicit authorization. See the [test deployment record](https://github.com/myota-platform/myota-docs/blob/main/docs/operations/test-deployment.md) for image digests, verification, and exclusions.

The [testing digest scheduler](https://github.com/myota-platform/.github/blob/main/.github/workflows/sync-testing-image-digests.yml) runs every 15 minutes from `.github`'s default branch and proposes digest-only PRs to Deploy `testing`. It uses a short-lived GitHub App token installed only on `myota-deploy`; it does not broaden the organization-wide `GITHUB_TOKEN` PR policy. The workflow safely skips until `TESTING_DIGEST_APP_ID` and `TESTING_DIGEST_APP_PRIVATE_KEY` are configured. Follow [issue #3](https://github.com/myota-platform/.github/issues/3) for the step-by-step App setup and activation checks. See the [test deployment record](https://github.com/myota-platform/myota-docs/blob/main/docs/operations/test-deployment.md) for deployment evidence and exclusions.

Deploy #15/#16, Activity #4/#5, and Geodata #8/#9 are complete and closed; all three databases were rebuilt from their authoritative baselines, restored from verified archives, and validated in the live cluster. Archive checksums, restore rehearsals, row/catalog checks, API health, and cleanup evidence are in the [database rebuild record](https://github.com/myota-platform/myota-docs/blob/main/docs/operations/databases/database-rebuild-evidence-2026-10-13.md). Operations runtime and observability ownership are retired; GitHub archived the Operations repository on 2026-10-13 and closed issue #3. Remaining logging acceptance gates and other evidence limits are recorded in current-state and the linked work items.

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
  repository archived on 2026-10-13; issue #3 is closed.
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

Snapshot: **2026-10-14 07:05 UTC**. The organization-wide GitHub issue list returned 23 open issues across all 12 organization repositories, including archived Operations. Related issues in different repositories are grouped under a shared workstream below. Labels use the organization-wide GitHub defaults consistently: `enhancement` marks product, runtime, contract, deployment, and infrastructure work; `documentation` marks Docs-owned inventory, decision, and roadmap work. This is a point-in-time index; check GitHub for current state.

| Workstream | Repository | Issue | Labels |
| --- | --- | --- | --- |
| **Public coming-soon landing page** |  |  |  |
|  | [myota-web](https://github.com/myota-platform/myota-web) | [#5: Add a public coming-soon landing page image](https://github.com/myota-platform/myota-web/issues/5) | `enhancement` |
|  | [myota-deploy](https://github.com/myota-platform/myota-deploy) | [#26: Deploy the coming-soon landing page at myota.top](https://github.com/myota-platform/myota-deploy/issues/26) | `enhancement` |
| **Geodata capacity qualification** |  |  |  |
|  | [myota-geodata-service](https://github.com/myota-platform/myota-geodata-service) | [#4: Qualify Geodata capacity and write-contention limits before expansion](https://github.com/myota-platform/myota-geodata-service/issues/4) | `enhancement`, `scalability` |
|  | [myota-deploy](https://github.com/myota-platform/myota-deploy) | [#5: Qualify Geodata scaling and recovery deployment gates before expansion](https://github.com/myota-platform/myota-deploy/issues/5) | `enhancement`, `scalability` |
| **Programme governance, jurisdiction, and eligibility** |  |  |  |
|  | [myota-docs](https://github.com/myota-platform/myota-docs) | [#4: Define phased delivery decisions for programme configuration](https://github.com/myota-platform/myota-docs/issues/4) | `documentation` |
|  | [myota-programme-service](https://github.com/myota-platform/myota-programme-service) | [#2: Add versioned programme governance, jurisdiction, and eligibility policy](https://github.com/myota-platform/myota-programme-service/issues/2) | `enhancement` |
|  | [myota-contracts](https://github.com/myota-platform/myota-contracts) | [#4: Define contracts for versioned programme policy and publication snapshots](https://github.com/myota-platform/myota-contracts/issues/4) | `enhancement` |
|  | [myota-admin-web](https://github.com/myota-platform/myota-admin-web) | [#3: Add audited programme governance and policy configuration workflows](https://github.com/myota-platform/myota-admin-web/issues/3) | `enhancement` |
|  | [myota-identity-service](https://github.com/myota-platform/myota-identity-service) | [#2: Bind programme governance to scoped identity and OIDC controls](https://github.com/myota-platform/myota-identity-service/issues/2) | `enhancement` |
|  | [myota-geodata-service](https://github.com/myota-platform/myota-geodata-service) | [#5: Enforce published programme geodata eligibility and moderation policy](https://github.com/myota-platform/myota-geodata-service/issues/5) | `enhancement` |
|  | [myota-activity-service](https://github.com/myota-platform/myota-activity-service) | [#2: Evaluate activity against immutable published programme policy versions](https://github.com/myota-platform/myota-activity-service/issues/2) | `enhancement` |
| **Database-backed participant and administrator authentication** |  |  |  |
|  | [myota-identity-service](https://github.com/myota-platform/myota-identity-service) | [#9: Make PostgreSQL authoritative for participant and administrator authentication state](https://github.com/myota-platform/myota-identity-service/issues/9) | `enhancement` |
|  | [myota-deploy](https://github.com/myota-platform/myota-deploy) | [#45: Deploy Identity database-backed authentication schema safely](https://github.com/myota-platform/myota-deploy/issues/45) | `enhancement` |
| **Administrator-created participant accounts** |  |  |  |
|  | [myota-identity-service](https://github.com/myota-platform/myota-identity-service) | [#10: Provide a secure administrator API for creating participant accounts](https://github.com/myota-platform/myota-identity-service/issues/10) | `enhancement` |
|  | [myota-contracts](https://github.com/myota-platform/myota-contracts) | [#7: Define the secure contract for administrator-created user accounts](https://github.com/myota-platform/myota-contracts/issues/7) | `enhancement` |
|  | [myota-deploy](https://github.com/myota-platform/myota-deploy) | [#46: Verify Gateway support for administrator user creation](https://github.com/myota-platform/myota-deploy/issues/46) | `enhancement` |
|  | [myota-admin-web](https://github.com/myota-platform/myota-admin-web) | [#11: Add administrator user creation to the Admin UI](https://github.com/myota-platform/myota-admin-web/issues/11) | `enhancement` |
| **Structured logging and request-to-worker correlation** |  |  |  |
|  | [myota-deploy](https://github.com/myota-platform/myota-deploy) | [#6: Close structured logging deployment and acceptance gaps](https://github.com/myota-platform/myota-deploy/issues/6) | `enhancement` |
| **Admin UI internationalization** |  |  |  |
|  | [myota-docs](https://github.com/myota-platform/myota-docs) | [#1: Complete Admin UI internationalization Phase 0 policy and inventory](https://github.com/myota-platform/myota-docs/issues/1) | `documentation` |
|  | [myota-admin-web](https://github.com/myota-platform/myota-admin-web) | [#4: Implement Admin UI internationalization after Phase 0 decisions](https://github.com/myota-platform/myota-admin-web/issues/4) | `enhancement` |
| **Future award-condition model** |  |  |  |
|  | [myota-docs](https://github.com/myota-platform/myota-docs) | [#2: Complete Phase 0 decisions for the future award-condition model](https://github.com/myota-platform/myota-docs/issues/2) | `documentation` |
| **Charter gap roadmap decomposition** |  |  |  |
|  | [myota-docs](https://github.com/myota-platform/myota-docs) | [#3: Decompose charter gaps into owner-scoped delivery roadmaps](https://github.com/myota-platform/myota-docs/issues/3) | `documentation` |
| **Testing image digest automation** |  |  |  |
|  | [.github](https://github.com/myota-platform/.github) | [#3: Set up the scoped App for automatic testing digest updates](https://github.com/myota-platform/.github/issues/3) | `enhancement` |
