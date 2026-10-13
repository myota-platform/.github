# Contributing to MyOTA

MyOTA keeps `main` as the default branch and uses `testing` as the integration
branch for repositories that build deployable artifacts. Read this page before
changing implementation code or CI in those repositories.

## Branch and pull-request workflow

Use this workflow in these active artifact repositories:

- `myota-activity-service`
- `myota-admin-web`
- `myota-deploy`
- `myota-geodata-service`
- `myota-identity-service`
- `myota-platform`
- `myota-programme-service`

`myota-web` has static-site validation, an Actions artifact, and a static-site
container image build. Apply the same branch and PR rules; make any future
deployable-artifact build a required check before publishing or deploying it.

1. Start each work item from the repository's `testing` branch.
2. Use a short-lived `feature/<name>` or `fix/<name>` branch. Do not make direct
   work commits to `testing` or `main`.
3. Open a pull request into `testing`. Every implementation change must enter
   `testing` through a pull request. Keep `main` as the default branch; promote
   a validated `testing` state to `main` only through a release pull request
   whose source is the same repository's `testing` branch. Main protection
   requires the `Main PR source policy` check, which rejects every other source.
   The `testing` branch remains an integration branch and is not deployed.
4. Require the repository's style/lint, test, and artifact-build checks to
   finish successfully. A check that is missing, skipped, cancelled, or
   failing is not a pass. If a repository lacks a suitable check, add one
   before relying on it as a quality gate.
5. Main requires the `Main PR source policy` check and resolved review
   conversations. An approving review is optional: any user with repository
   write access may merge their own PR after all required checks pass. Reviews
   may still be requested as a human quality practice, but they are not a merge
   gate. Force pushes and branch deletion are disabled, and administrators
   cannot bypass the configured protections.
6. Enable auto-merge only for a pull request whose configured required checks
   have passed and whose branch protections and any required reviews are
   satisfied. Use the repository's configured merge method. Never use an admin
   bypass or merge around a failed, missing, or required review check.
7. Keep testing builds isolated. After all required checks pass on a push to
   `testing` (including a merged pull request), publish both a mutable `testing`
   image tag and a unique `testing-<full-commit-sha>` tag. Add OCI source,
   revision, and ref-name labels; the ref-name label is `testing` for these
   images. The `testing` tag is a convenience alias; use the unique tag or
   immutable digest to identify an image for deployment. Feature-branch and
   pull-request builds validate images without publishing. Never publish
   testing output as `latest`, a release tag, or another tag consumed by the
   deployed stack. Do not trigger production deployment from `testing`.

The applicable repositories keep `main` as the default release branch. A
repository whose `testing` branch is behind or has additional work must be
reconciled through pull requests; do not reset, force-push, or silently retarget
an existing pull request.

## Repositories outside this policy

- `myota-docs` and `.github` are documentation/governance repositories; they do
  not build deployable artifacts.
- `myota-contracts` validates and publishes contract files but does not build a
  deployable artifact.
- `myota-operations-service` is archived and retired from runtime use. GitHub's
  archived state makes its repository read-only; no runtime image is built or
  published.

`myota-platform` publishes its CI/bootstrap image as
`ghcr.io/myota-platform/myota-platform-ci:testing` plus the unique SHA tag only
from `testing`; it is separate from the Deploy-owned runtime images and is not
used by the live stack. Web publishes its static-site image from `main` and
`testing`; it is not deployed until Deploy adds an explicit runtime integration.

The main protections are active in Activity, Admin Web, Deploy, Geodata,
Identity, Platform, Programme, and Web. `myota-docs`, `.github`, and
`myota-contracts` are excluded. Operations is archived and read-only, so GitHub
rejected a branch-protection update; the archive itself blocks writes. If that
repository is ever unarchived, configure equivalent protection before enabling
writes. If repository responsibilities or write-capable reviewers change,
update this list and recheck the protections.
