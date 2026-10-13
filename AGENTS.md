# Agent instructions

For MyOTA changes, first read the workspace `AGENTS.md` and the relevant
repository guidance. Keep each task within its agreed scope, preserve system
boundaries, and report evidence before marking work complete.

For implementation or CI work in a repository that builds deployable artifacts,
follow [`CONTRIBUTING.md`](CONTRIBUTING.md). It defines the applicable
repositories, feature/fix branches based on `testing`, pull-request quality
gates, isolated testing artifacts, and exclusions. Do not duplicate or weaken
those repository-specific rules here.

Use the repository's authoritative source files and decisions; treat identified
mirrors as synchronized copies. Make documentation and evidence updates in the
owning repositories. Run focused checks, document exact results and remaining
gaps, and commit and push completed work with meaningful messages. Test deployed
systems only when needed, non-destructively, and clean up test data afterward.
