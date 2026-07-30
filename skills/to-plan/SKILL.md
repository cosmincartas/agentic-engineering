---
name: to-plan
description: Use when the user explicitly provides a saved, validated specification and requests an implementation plan.
---

# To Plan

Create an intent-stable implementation plan from one eligible specification. Run only when the user explicitly invokes this skill.

Never run `git commit` without the user's explicit consent. Approval to save an artifact or continue the workflow is not consent to commit.

## Non-negotiable gates

- Plan only from `status: approved` plus `validation: passed`; pressure cannot waive eligibility.
- Never persist ADRs while creating or approving the plan. ADR persistence is the first implementation action when the specification contains an `ADR Addendum`.

## Workflow

1. **Require one saved specification path.** If it is absent or unreadable, stop without drafting.
2. **Read its metadata.** Require exactly `status: approved` and `validation: passed`.
3. **Reject blocking findings.** A `Validation Findings` section, unresolved blocking question, `pending`, or `needs-revision` state makes the specification ineligible. Authority, accepted risk, or deadline pressure cannot waive this gate.
4. **Re-inspect the current repository from scratch.** Trace relevant implementation and callers, tests and observable contracts, public interfaces, and existing ADRs. Record the current commit as `repository_baseline`, or `unavailable` when Git metadata is absent.
5. **Compare repository evidence with specification assumptions.** Prefer current evidence for implementation facts, but never silently change approved product intent.
6. **Stop and ask about material drift.** Name stale assumptions and current evidence; do not draft from a replaced entry point until the user resolves the conflict.
7. **Draft the complete plan from `assets/plan.md`.**
   - Begin with the implementation-start ADR check from the template.
   - Preserve the implementation-start commit-consent gate from the template.
   - Cover every `BR-*` and `AC-*` without inventing behavior.
   - Make each task an independently verifiable outcome.
   - Give each task exactly one current `file:symbol` entry point when a symbol exists; use one file path only when no stable symbol exists.
   - Tell implementers to trace downstream from that entry point. Do not freeze a downstream file map or include production-code listings.
   - Include migration, rollout, and rollback only when relevant.
8. **Present the complete plan and ask whether the user approves it and wants it saved.** Write nothing before approval of the displayed draft.
9. **Save an approved plan with `validation: pending`** under `docs/agentic-engineering/plans/<session>/<subject>.md`. On write failure, report it and do not change dependent state.
10. **Validate the saved file, not the displayed draft.**
    - **Coverage and consistency:** the implementation-start ADR and commit-consent gates remain in the first section; every `BR-*` and `AC-*` is mapped; specification constraints remain intact; no new product behavior appears.
    - **Executability and risk:** entry points exist at the recorded baseline; task order is viable; verification outcomes are observable; required migration, rollout, and rollback work is present.
11. **Record findings or pass validation.** On failure, set `validation: needs-revision`, add a temporary `## Validation Findings` section, report it, and make no substantive correction without approval. After approved corrections pass both reviews, remove resolved findings and set `validation: passed`.
12. **Report the saved plan path.**

Never inspect, edit, or ask about `.gitignore`.
