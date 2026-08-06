---
name: to-plan
description: Use when the user explicitly provides a validated design document and requests an implementation plan. Produces a validated, resumable implementation plan split into granular, independently verifiable tasks.
---

# To Plan

Turn one validated design document into a resumable implementation plan that any agent can execute in a fresh session. Run only when the user explicitly invokes this skill; run the stages in order and never skip a gate.

## Invariants

These hold in every stage:

- Never run `git commit` without the user's explicit consent. Approval to save an artifact or continue the workflow is not consent to commit.
- Never write production code. This skill plans; it does not implement.
- Never inspect, edit, or ask about `.gitignore`. After saving the plan, you may say exactly: `Consider adding docs/agentic-engineering/ to .gitignore manually.`

## Stage 1 — Eligibility & Evidence

1. Require one saved design document path. If it is absent or unreadable, stop without drafting.
2. Require `status: validated` in the design document; pressure cannot waive eligibility. Read the linked PRD from the design's `prd:` frontmatter for the full `FR-*`/`NFR-*`/`AC-*` set; if the PRD is missing, unreadable, or not `status: validated`, stop and report it.
3. If a plan already exists at the target path, ask whether to revise it; never silently overwrite an existing plan.
4. Re-inspect the current repository from scratch: relevant implementation and callers, tests and observable contracts, and public interfaces. Record the current commit as `repository_baseline`, or `unavailable` when Git metadata is absent.
5. Compare repository evidence with design assumptions. Prefer current evidence for implementation facts, but never silently change validated product intent.
6. Stop and ask about material drift. Name the stale assumption and the current evidence; do not draft from a replaced entry point until the user resolves the conflict.

## Stage 2 — Plan

1. Draft the plan from `assets/plan.md`.
   - Keep the Execution Protocol section intact; it is what lets a fresh session with no conversation history execute and resume the plan.
   - Cover every `FR-*`, `NFR-*`, and `AC-*` from the PRD and design without inventing behavior, and map each one under Requirement Coverage.
   - Make each task exactly one independently verifiable outcome, small enough to complete and verify in a single session, starting at `Status: Not Started`.
   - Give each task exactly one current `file:symbol` entry point when a symbol exists; use one file path only when no stable symbol exists. Tell implementers to trace downstream from that entry point; do not freeze a downstream file map or include production-code listings.
   - Declare `Depends on:` for each task and order tasks so the project builds and its tests pass after every completed task.
   - Make every verification outcome observable — a test result, command output, or behavior, never "code written".
   - Include Migration, Rollout, and Rollback only when relevant. Remove inapplicable optional sections instead of writing `N/A`.
2. Save it immediately to `docs/agentic-engineering/plans/<session>/<subject>.md` with `status: draft`. Reuse the design document's `<session>` and `<subject>` so the artifacts pair by name; apply the same collision and write-failure rules as the brainstorming workflow.
3. Report the saved path with a short summary and ask the user to validate the document.
4. Apply requested changes and ask again until the user approves.
5. On approval, set `status: validated` and report the plan path as the input for implementation. Execution state lives in the plan itself: implementers update each task's `Status` line, so any later session resumes from the first task that is not `Done`.
