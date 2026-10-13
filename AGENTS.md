# Agent instructions

Before starting work, read the applicable workspace `AGENTS.md` when one is
available, plus the guidance in the repository being changed. These instructions
apply to work in this repository and do not replace repository-specific rules.

## Scope and decisions

- For phased work, use one self-contained, copyable prompt per phase, aligned
  with that phase's tasks and exit criteria. Keep prompt text readable; place
  longer reusable prompts in a dedicated file linked from the roadmap.
- Establish context and scope. Identify owning repositories and services, read
  the relevant roadmap and decisions, and distinguish authoritative sources
  from synchronized mirrors.
- Preserve system boundaries and invariants, including data ownership, API
  behavior, security, compatibility, and failure handling. Label proposals as
  proposals until implemented and verified.
- Keep work within the authorized phase. If a prerequisite or approved decision
  is missing, report the gap rather than treating a proposal as implemented.

## Evidence and delivery

- Run focused checks appropriate to the work. Report exact results, changed
  files and repositories, remaining gaps, and supporting evidence.
- Mark checklist items complete only after their acceptance criteria are
  verified. Update documentation and evidence in the owning repositories;
  maintain accurate links from `.github` to current project documentation.
- Test deployed systems only when needed, non-destructively, and clean up test
  data afterward.
- Commit and push completed work with meaningful messages. Group related
  changes into coherent commits and use multiline messages when they clarify
  scope or rationale.

## Artifact-repository branch policy

For implementation or CI work in repositories that build deployable artifacts,
follow [`CONTRIBUTING.md`](CONTRIBUTING.md). It defines the applicable
repositories and exclusions, feature/fix branches based on `testing`, pull
requests, quality gates, auto-merge conditions, and isolated testing artifacts.
Do not duplicate or weaken those repository-specific rules here.
