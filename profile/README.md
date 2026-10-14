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

The [current system state](https://github.com/myota-platform/myota-docs/blob/main/docs/architecture/current-state.md) remains the dated source for deployed revisions and live K3s evidence. The existing production and Testing releases are still sourced from the split repositories; this monorepo migration has not changed either deployment.

The new [myota monorepo](https://github.com/myota-platform/myota) was initialized from the current `main` snapshots of the authoritative application, contract, web, and deployment repositories. Its first quality-gated integration pull request merged into `testing`; PR checks passed on the feature branch. The merge triggered fresh component CI and image workflows on `testing`. Component and deployment test jobs passed, but every image publish currently fails with GHCR `permission_denied: write_package` because the existing packages do not grant Actions write access to `myota`. Track the settings and rerun in [myota #53](https://github.com/myota-platform/myota/issues/53). Do not treat the migration as deployed until images publish, the digest path reconciles in `myota-test`, and source owners/mirrors are verified.

The open issue backlog is now in `myota`. The 49 source issues were closed as migrated with reciprocal links, and target issues retain their acceptance criteria. The previous service/deployment repositories remain available as transition sources. They must not be archived until the monorepo testing and release flows, source ownership, image publication, and live deployment are verified. GitHub connector access does not currently provide the repository archival or branch-protection administration write operations.

## Project principles

- Programme policies and governance are programme-owned and versioned.
- PostgreSQL is the source of truth for domain state, job state, and recovery.
- Services own their data and expose versioned APIs and event contracts.
- NATS JetStream is bounded delivery and replay infrastructure, not an archive.
- Admin capabilities remain separate from participant workflows.
- Accessibility, auditability, idempotency, and privacy are core requirements.

## Repositories

- [myota](https://github.com/myota-platform/myota) — active monorepo for
  Identity, Programme, Geodata, Activity, Gateway, contracts, deployment,
  integration tests, and Admin/Participant web applications.
- [myota-docs](https://github.com/myota-platform/myota-docs) — architecture,
  decisions, operations, evidence, and work tracking.
- [.github](https://github.com/myota-platform/.github) — organization
  governance, contribution policy, and shared automation.
- [myota-contracts](https://github.com/myota-platform/myota-contracts) —
  transition source; contracts have been copied into the monorepo and remain
  separate until parity and retirement are verified.
- Former service, web, Deploy, and Platform repositories are transition
  sources pending validation and archival. [Operations](https://github.com/myota-platform/myota-operations-service)
  is already archived and retired.

Read the [project charter](https://github.com/myota-platform/myota-docs/blob/main/docs/governance/project-charter.md)
and [charter gap analysis](https://github.com/myota-platform/myota-docs/blob/main/docs/governance/charter-gap-analysis.md)
for the product purpose and longer-term delivery gaps.

## Open GitHub issues

Snapshot: **2026-10-14**. The current open work backlog is in [`myota-platform/myota`](https://github.com/myota-platform/myota/issues), which contains 50 issues migrated from the former service and deployment repositories. Each source issue was closed as migrated with reciprocal links; the original issue remains available for history. Docs and organization governance work retain their owners in `myota-docs` and `.github` while being tracked here. Check GitHub for live state.

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
|  | [#53: Deployment: Grant monorepo Actions write access to GHCR images](https://github.com/myota-platform/myota/issues/53) | `bug` |

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
