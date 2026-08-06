---
subject: "{{subject}}"
design: "{{design path}}"
prd: "{{prd path}}"
repository_baseline: "{{commit or unavailable}}"
status: draft
---

# {{Subject}} Implementation Plan

## Execution Protocol
Instructions for any implementing agent, in any session:

1. Execute only when this plan's `status` is `validated`.
2. Work tasks in dependency order: pick the first task whose `Status` is not `Done` and whose dependencies are all `Done`.
3. Before continuing a task marked `In Progress`, re-verify its current state against its verification outcome; finish or redo it before moving on.
4. Set a task's `Status` to `In Progress` when starting it and to `Done` only after observing its verification outcome.
5. Leave the project building and its tests passing after every completed task.
6. Trace downstream work from each task's entry point; this plan intentionally freezes no downstream file map.
7. Never run `git commit` without the user's explicit consent.

## Goal & Design Reference
<!-- One-paragraph goal, the design and PRD paths, and the FR-*/NFR-*/AC-* IDs this plan covers. -->
## Repository Findings
<!-- Evidence gathered at repository_baseline that the tasks rely on. -->
## Tasks
### Task {{N}}: {{Independently Verifiable Outcome}}
- Status: Not Started
- Requirements: {{FR-*/NFR-*/AC-* IDs}}
- Entry point: {{file:symbol}}
- Expected behavior:
- Verification outcome:
- Depends on:
<!-- One independently verifiable outcome per task, completable in a single session. Exactly one current file:symbol entry point; a file-only entry point only when no stable symbol exists. Status is Not Started, In Progress, or Done. -->
## Migration, Rollout, and Rollback
<!-- Remove this section when migration, rollout, and rollback are genuinely irrelevant. -->
## Requirement Coverage
<!-- Map every FR-*, NFR-*, and AC-* ID to a task and its observable verification outcome. -->
<!-- Remove inapplicable optional sections instead of writing N/A. -->
