# Agent instructions

For implementation and CI work in MyOTA's artifact-producing repositories,
follow the authoritative [contributor and branch workflow](CONTRIBUTING.md).
Start from `testing`, use a short-lived `feature/<name>` or `fix/<name>` branch,
and open a pull request into `testing`. Run and inspect the repository's style,
test, and artifact-build checks. Do not treat missing or skipped checks as
passing. Enable or complete auto-merge only when every configured required check,
branch protection, and required review is satisfied; never bypass a gate.

Keep test artifacts isolated from release tags and production deployment. Do not
publish testing output as `latest`, and do not deploy from `testing`. Preserve
existing branch history; reconcile stale branches through pull requests rather
than resetting or force-pushing them.

Use repository-specific `AGENTS.md` instructions when present. The root
workspace `AGENTS.md` may add host-specific operating and deployment guidance.
