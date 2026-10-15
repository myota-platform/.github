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

The accepted asynchronous email design, current worker/deployment status,
privacy boundaries, and operator procedure are recorded in the
[email delivery architecture and operations guide](https://github.com/myota-platform/myota-docs/blob/main/docs/operations/messaging/email-delivery.md).
Work tracking remains in the canonical `myota` issues; this profile is not an
issue index.

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
