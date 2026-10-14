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

GitHub Actions schedule delivery proved unreliable for digest polling. The selected replacement is a Deploy-owned CronJob in isolated namespace `myota-test` that will call `workflow_dispatch` on `.github` `main`; this is planned, not deployed. The current Actions schedule is temporary and will be removed after the CronJob passes recurring dispatch verification. A dedicated dispatch App will be installed only on `.github` with Actions read/write permission, separate from the existing Deploy-only digest-writer App. See [.github configuration issue #6](https://github.com/myota-platform/.github/issues/6), [Deploy implementation issue #57](https://github.com/myota-platform/myota-deploy/issues/57), and [Docs runbook issue #12](https://github.com/myota-platform/myota-docs/issues/12). The prior schedule-only issue [#5](https://github.com/myota-platform/.github/issues/5) was closed as superseded. The [test deployment record](https://github.com/myota-platform/myota-docs/blob/main/docs/operations/test-deployment.md) links current evidence and remaining gates.

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
- [Email service](https://github.com/myota-platform/myota-email-service) —
  planned standalone NATS-backed email delivery; implementation is tracked in
  the linked open issues below.
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

Snapshot: **2026-10-14**. The current open work backlog is in [`myota-platform/myota`](https://github.com/myota-platform/myota/issues), which contains 49 issues migrated from the former service and deployment repositories. Each source issue was closed as migrated with reciprocal links; the original issue remains available for history. Docs and organization governance work retain their owners in `myota-docs` and `.github` while being tracked here. Check GitHub for live state.

| Workstream | Issue | Labels |
| --- | --- | --- |
| **Production digest release** |  |  |
|  | [#5: Deployment: Update production image digests through the protected release path](https://github.com/myota-platform/myota/issues/5) | `bug` |
|  | [#4: Geodata: Queue location metadata enrichment when entities enter candidate or approved status](https://github.com/myota-platform/myota/issues/4) | `bug` |
| **Testing image digest automation** |  |  |
|  | [#6: Docs: Document the Testing digest CronJob dispatch and App setup](https://github.com/myota-platform/myota/issues/6) | `documentation` |
|  | [#7: Deployment: Dispatch Testing digest sync from a myota-test CronJob](https://github.com/myota-platform/myota/issues/7) | `enhancement` |
|  | [#8: Governance: Configure a least-privilege App for Testing digest dispatch](https://github.com/myota-platform/myota/issues/8) | `enhancement` |
|  | [#30: Governance: Set up the scoped App for automatic testing digest updates](https://github.com/myota-platform/myota/issues/30) | `enhancement` |
| **Shared user profile fields and images** |  |  |
|  | [#11: Contracts: Define shared user profile fields and image upload contract](https://github.com/myota-platform/myota/issues/11) | `enhancement` |
|  | [#9: Participant Web: Edit participant profile details and crop profile pictures](https://github.com/myota-platform/myota/issues/9) | `enhancement` |
|  | [#10: Docs: Document user profile fields and SeaweedFS image handling](https://github.com/myota-platform/myota/issues/10) | `documentation` |
|  | [#12: Deployment: Provision private SeaweedFS buckets for user profile images](https://github.com/myota-platform/myota/issues/12) | `enhancement` |
|  | [#13: Identity: Add profile fields and SeaweedFS image handling to shared users](https://github.com/myota-platform/myota/issues/13) | `enhancement` |
| **Shared Identity roles and account creation** |  |  |
|  | [#14: Admin Web: Manage Identity roles and gate Admin UI permissions by role](https://github.com/myota-platform/myota/issues/14) | `enhancement` |
|  | [#15: Contracts: Define shared account, role, and participant registration contracts](https://github.com/myota-platform/myota/issues/15) | `enhancement` |
|  | [#16: Participant Web: Add future participant self-registration using the shared Identity account](https://github.com/myota-platform/myota/issues/16) | `enhancement` |
|  | [#17: Deployment: Safely expose participant registration after Identity safeguards](https://github.com/myota-platform/myota/issues/17) | `enhancement` |
|  | [#18: Docs: Document the shared Identity account and role-based access model](https://github.com/myota-platform/myota/issues/18) | `documentation` |
|  | [#19: Identity: Unify participants and administrators under role-based Identity](https://github.com/myota-platform/myota/issues/19) | `enhancement` |
|  | [#31: Admin Web: Create participant and administrator accounts](https://github.com/myota-platform/myota/issues/31) | `enhancement` |
|  | [#33: Deployment: Verify Gateway support for privileged account creation](https://github.com/myota-platform/myota/issues/33) | `enhancement` |
|  | [#32: Contracts: Define the secure contract for administrator-created accounts](https://github.com/myota-platform/myota/issues/32) | `enhancement` |
|  | [#34: Identity: Provide a secure API for creating participant and administrator accounts](https://github.com/myota-platform/myota/issues/34) | `enhancement` |
|  | [#35: Deployment: Deploy Identity database-backed authentication schema safely](https://github.com/myota-platform/myota/issues/35) | `enhancement` |
|  | [#36: Identity: Make PostgreSQL authoritative for participant and administrator authentication state](https://github.com/myota-platform/myota/issues/36) | `enhancement` |
| **Structured logging and Grafana dashboards** |  |  |
|  | [#20: Docs: Document actionable Grafana log and trace dashboards](https://github.com/myota-platform/myota/issues/20) | `documentation` |
|  | [#22: Deployment: Add actionable Grafana dashboards for MyOTA logs and traces](https://github.com/myota-platform/myota/issues/22) | `enhancement` |
|  | [#21: Docs: Record MyOTA operations dashboard availability in Testing](https://github.com/myota-platform/myota/issues/21) | `documentation` |
|  | [#24: Deployment: Deploy the MyOTA operations dashboard in Testing](https://github.com/myota-platform/myota/issues/24) | `bug` |
|  | [#48: Deployment: Close structured logging deployment and acceptance gaps](https://github.com/myota-platform/myota/issues/48) | `enhancement` |
| **Transactional email delivery** |  |  |
|  | [#26: Governance: Apply testing-branch release policy to myota-email-service](https://github.com/myota-platform/myota/issues/26) | `enhancement` |
|  | [#25: Docs: Document the email delivery architecture and operating procedures](https://github.com/myota-platform/myota/issues/25) | `documentation` |
|  | [#23: Deployment: Deploy the email worker with isolated JetStream and SMTP configuration](https://github.com/myota-platform/myota/issues/23) | `enhancement` |
|  | [#27: Identity: Publish safe participant and administrator email intents](https://github.com/myota-platform/myota/issues/27) | `enhancement` |
|  | [#28: Contracts: Define the versioned JetStream email work contract](https://github.com/myota-platform/myota/issues/28) | `enhancement` |
|  | [#29: Email: Implement asynchronous SMTP delivery for participants and administrators](https://github.com/myota-platform/myota/issues/29) | `enhancement` |
| **Public coming-soon site** |  |  |
|  | [#37: Deployment: Deploy the coming-soon landing page at myota.top](https://github.com/myota-platform/myota/issues/37) | `enhancement` |
|  | [#38: Participant Web: Add a public coming-soon landing page image](https://github.com/myota-platform/myota/issues/38) | `enhancement` |
| **Programme governance and policy** |  |  |
|  | [#39: Docs: Define phased delivery decisions for programme configuration](https://github.com/myota-platform/myota/issues/39) | `documentation` |
|  | [#40: Docs: Decompose charter gaps into owner-scoped delivery roadmaps](https://github.com/myota-platform/myota/issues/40) | `documentation` |
|  | [#41: Docs: Complete Phase 0 decisions for the future award-condition model](https://github.com/myota-platform/myota/issues/41) | `documentation` |
|  | [#42: Admin Web: Implement Admin UI internationalization after Phase 0 decisions](https://github.com/myota-platform/myota/issues/42) | `enhancement` |
|  | [#43: Docs: Complete Admin UI internationalization Phase 0 policy and inventory](https://github.com/myota-platform/myota/issues/43) | `documentation` |
|  | [#44: Geodata: Enforce published programme geodata eligibility and moderation policy](https://github.com/myota-platform/myota/issues/44) | `enhancement` |
|  | [#45: Activity: Evaluate activity against immutable published programme policy versions](https://github.com/myota-platform/myota/issues/45) | `enhancement` |
|  | [#46: Identity: Bind programme governance to scoped identity and OIDC controls](https://github.com/myota-platform/myota/issues/46) | `enhancement` |
|  | [#47: Contracts: Define contracts for versioned programme policy and publication snapshots](https://github.com/myota-platform/myota/issues/47) | `enhancement` |
|  | [#50: Admin Web: Add audited programme governance and policy configuration workflows](https://github.com/myota-platform/myota/issues/50) | `enhancement` |
|  | [#51: Programme: Add versioned programme governance, jurisdiction, and eligibility policy](https://github.com/myota-platform/myota/issues/51) | `enhancement` |
| **Geodata capacity qualification** |  |  |
|  | [#52: Geodata: Qualify Geodata capacity and write-contention limits before expansion](https://github.com/myota-platform/myota/issues/52) | `enhancement` |
|  | [#49: Deployment: Qualify Geodata scaling and recovery deployment gates before expansion](https://github.com/myota-platform/myota/issues/49) | `enhancement` |
