---
specification: "{{path}}"
repository_baseline: "{{commit or unavailable}}"
status: approved
validation: pending
---

# {{Subject}} Implementation Plan

## Implementation Start
Before changing production code, check the source specification for an `ADR Addendum`. If present, persist each candidate under `docs/adr/`. Reuse an equivalent accepted ADR. For a changed decision, create the next numbered ADR and name the prior ADR under `Supersedes`. Stop and ask about unclear conflicts; never modify an existing ADR. Keep persisted ADRs self-contained and do not link them to the disposable specification or plan.

Never run `git commit` without the user's explicit consent after implementation and verification. Approval of this plan is not consent to commit.

## Goal and Constraints
## Repository Findings
## Tasks
### Task {{N}}: {{Independently Verifiable Outcome}}
- Requirements:
- Entry point:
- Expected behavior:
- Verification outcome:
- Depends on:
<!-- Give exactly one current file:symbol entry point. Use a file-only entry point only when no stable symbol exists. Trace downstream work during implementation; do not list downstream files or production code here. -->
## Migration, Rollout, and Rollback
<!-- Remove this section when migration, rollout, and rollback are genuinely irrelevant. -->
## Requirement Coverage
<!-- Map every BR-* and AC-* ID to a task and observable verification outcome. -->
