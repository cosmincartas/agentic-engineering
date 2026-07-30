---
name: brainstorming
description: Use when a development prompt contains a new feature, feature change, bug report, or multiple potentially independent subjects that need clarification before implementation.
---

# Brainstorming

Turn a development prompt into approved, resumable subject specifications. Preserve user control at every write boundary.

Never run `git commit` without the user's explicit consent. Approval to save an artifact or continue the workflow is not consent to commit.

## Evidence and classification

When a repository exists, inspect relevant implementation and callers, tests and test names, public interfaces, UI copy, validation and errors, history or changelog, and existing ADRs. Do not classify from prompt wording alone.

Use this authority order and surface conflicts:

1. Current user clarification
2. Approved durable decisions
3. Tests and observable contracts
4. Current code behavior

Classify each coherent, independently specifiable outcome:

- **New Feature:** no existing capability or accepted contract covers it.
- **Feature Change:** accepted behavior intentionally needs to change.
- **Bug Fix:** implementation violates accepted behavior.
- **Mixed:** tightly coupled behaviors would become less coherent if separated.

Do not split mechanically by type.

## Workflow

For every subject-clarifying question, try to provide at least two concrete options. Mark one as **Recommended** and briefly explain why it best fits the evidence, constraints, or simplest path; keep an open-ended response available when the options do not cover the user's intent.

1. **Inspect the prompt and repository evidence.** If no repository exists, state that limitation and use the available evidence.
2. **Build coherent subjects and classify them.** Include every requested outcome without inventing scope.
3. **Confirm every classification below high confidence.** Ask one focused classification question at a time; never silently choose between conflicting evidence.
4. **Present and obtain explicit approval for the scope map.** Show only `Subject`, `Type`, and `Status`, with each status `Not Started`. Write nothing before approval.
5. **Persist approved subjects in the daily triage document.** Use `docs/agentic-engineering/triage/YYYY-MM-DD.md`. If it does not exist, fill `assets/triage.md` with the date and approved subject rows. If it exists, require the same three-column table, preserve every existing row and status, and append only approved `(Subject, Type)` pairs not already present, each with status `Not Started`. Never create another triage file for the same date or add columns or metadata. On read, parse, or write failure, report it and stop without changing dependent state.
6. **Ask which `Not Started` subject from the current approved scope map to explore.** When resuming after context loss, treat saved specification paths as completed handoffs, do not repeat them, and select only from relevant `Not Started` rows unless the user directs otherwise.
7. **Ask one focused question at a time** until goals, current behavior, boundaries, constraints, failure behavior, risks, and architectural decisions are materially clear. Re-inspect relevant current evidence when resuming.
8. **Present consolidated conclusions and require explicit approval.** Resolve material open questions before proceeding.
9. **Read `references/to-spec.md` completely and execute it.** The pre-save response is the complete specification followed by its approval/save question. “Save immediately” is not approval of an unseen draft; never save before approval given after display, and never skip post-save validation.
10. **Update the matching `(Subject, Type)` triage row and offer another subject.** A declined save remains `Not Started`; a saved specification path remains even when its validation needs revision.

Never create an implementation plan or persist an ADR. Never inspect, edit, or ask about `.gitignore`.

After saving triage, you may say exactly: `Consider adding docs/agentic-engineering/ to .gitignore manually.`
