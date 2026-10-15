# Contributing to MyOTA

MyOTA keeps `main` as the default branch and uses `testing` as the integration
branch for repositories that build deployable artifacts. Read this page before
changing implementation code or CI in those repositories.

## Branch and pull-request workflow

The active implementation repository is
[myota](https://github.com/myota-platform/myota), a monorepo containing
Identity, Programme, Geodata, Activity, Gateway, the asynchronous email worker
(`apps/email`), deployment assets, contracts, and both web frontends. The
`myota-email-service` image is a deployable artifact owned by this monorepo;
it does not have a separate source repository or branch policy. Use the service
and package boundaries described in its root `AGENTS.md`.

1. Start each work item from `testing`.
2. Use a short-lived `feature/<name>` or `fix/<name>` branch. Do not make
   direct work commits to `testing` or `main`.
3. Open implementation pull requests into `testing`. Promote a validated
   `testing` state to `main` only through a release pull request from the
   same repository's `testing` branch.
4. Require the applicable style, lint, test, contract, Helm, and image-build
   checks to finish successfully. A check that is missing, skipped, cancelled,
   or failing is not a pass. Add a suitable check before relying on a component
   without one.
5. The repository contains a `Main PR source policy` workflow. Repository
   administrators must also configure branch/ruleset protections to require
   that check and prohibit direct pushes. Connector access did not expose the
   administrative write operation, so protection on `myota` remains to be
   verified and enabled.
6. Enable auto-merge only for a pull request whose configured required checks
   have passed and whose branch protections and required reviews are satisfied.
   Never bypass a failed or missing check or a required review.
7. A successful push to `testing`, including a merged PR, publishes both a
   mutable `testing` image tag and a unique
   `testing-<full-commit-sha>` tag for each changed deployable component.
   Feature and pull-request builds validate without publishing. The isolated
   release uses immutable image digests and is deployed only to namespace
   `myota-test`; never use `testing` images in production.
8. The email worker follows the same gates: feature and PR builds do not
   publish, while a successful `testing` push publishes
   `ghcr.io/myota-platform/myota-email-service:testing` and the immutable
   `testing-<full-commit-sha>` tag. Email delivery remains disabled in Testing
   until test-only credentials, a non-production SMTP target, and measured
   queue capacity are approved. See the [email delivery operations record](https://github.com/myota-platform/myota-docs/blob/main/docs/operations/messaging/email-delivery.md)
   and tracked [email workstream #58](https://github.com/myota-platform/myota/issues/58).
   Supporting issues are [Deploy #23](https://github.com/myota-platform/myota/issues/23),
   [Docs #25](https://github.com/myota-platform/myota/issues/25),
   [Governance #26](https://github.com/myota-platform/myota/issues/26),
   [Identity #27](https://github.com/myota-platform/myota/issues/27),
   [Contracts #28](https://github.com/myota-platform/myota/issues/28), and
   [Email #29](https://github.com/myota-platform/myota/issues/29).

The monorepo import is in progress. The first integration PR has merged into
`testing`; do not treat the source repositories as retired until the monorepo
release path, Testing reconciliation, production release policy, and source
ownership checks are verified.


## Repositories outside this policy

- `myota-docs` remains the project documentation and evidence owner.
- `.github` remains the organization governance and workflow owner.
- `myota-contracts` remains separate because it is contract-only and does not
  build a deployable artifact.
- The previous service, web, deployment, and platform repositories are
  transition sources. Their open issues were moved to `myota`; they are not
  considered archived until the migration is validated and GitHub confirms
  archival. Operations remains archived.
