---
name: brainstorm
description: Use when a development prompt contains a new feature, feature change, or bug report that needs clarification before implementation. Produces a validated PRD and design document before any code is written.
---

# Brainstorming

Turn a development prompt into a validated Product Requirements Document and design document through natural collaborative dialogue. Run the three stages in order; never skip a validation gate.

## Invariants

These hold in every stage:

- Never run `git commit` without the user's explicit consent. Approval to save an artifact or continue the workflow is not consent to commit.
- Never create an implementation plan.
- Never inspect, edit, or ask about `.gitignore`. After saving the first artifact, you may say exactly: `Consider adding docs/agentic-engineering/ to .gitignore manually.`

## Stage 1 — Understanding

- Check out the current project state first (files, docs, recent commits). If no repository exists, state that limitation and use the available evidence.
- Before asking detailed questions, assess scope: if the request describes multiple independent subsystems (e.g., "build a platform with chat, file storage, billing, and analytics"), flag this immediately. Don't spend questions refining details of a project that needs to be decomposed first.
- If the project is too large for a single PRD, help the user decompose into sub-projects: what are the independent pieces, how do they relate, what order should they be built? Then run the normal flow on the first sub-project. Each sub-project gets its own PRD → design → plan cycle.
- Ask one focused question at a time until the problem, goals, constraints, and success criteria are materially clear.
- For every clarifying question, provide at least two concrete options. Mark one as **Recommended** and briefly explain why it best fits the evidence, constraints, or simplest path; keep an open-ended response available when the options do not cover the user's intent.

## Stage 2 — PRD

1. Draft the PRD from `assets/prd.md`.
   - Preserve the Stage 1 conclusions; do not invent scope.
   - Give functional requirements unique, stable `FR-*` IDs and non-functional requirements unique, stable `NFR-*` IDs.
   - Give acceptance criteria unique, stable `AC-*` IDs, map each to `FR-*`/`NFR-*` IDs, and make every criterion observable.
   - Support every Current Behavior claim with repository evidence; omit the section for greenfield subjects.
   - Remove inapplicable optional sections instead of writing `N/A`.
2. Save it immediately to `docs/agentic-engineering/prd/<session>/<subject>.md` with `status: draft`. `<session>` is the current date `YYYY-MM-DD`; `<subject>` is concise kebab-case, with `-2`, `-3`, and so on for collisions. Set the frontmatter `subject` to that same slug. On write failure, report it and stop.
3. Report the saved path with a short summary and ask the user to validate the document.
4. Apply requested changes and ask again until the user approves.
5. On approval, set `status: validated` and proceed to Stage 3.

## Stage 3 — Design

1. Ask design-level questions only — contracts, data shapes, error handling, verification strategy. Do not re-ask what the validated PRD already answers. Follow the Stage 1 questioning style.
2. Draft the design document from `assets/design.md`.
   - Set `prd:` to the validated PRD path and map its `FR-*`/`NFR-*`/`AC-*` IDs in Goal & PRD Reference and Verification & Testing.
   - Record rejected approaches and rationale under Alternatives Considered.
   - Remove inapplicable optional sections instead of writing `N/A`.
3. Save it immediately to `docs/agentic-engineering/specs/<session>/<subject>.md` with `status: draft`. Reuse the PRD's `<session>` and `<subject>` so the two artifacts pair by name; apply the same collision and write-failure rules as Stage 2.
4. Report the saved path with a short summary and ask the user to validate the document.
5. Apply requested changes and ask again until the user approves.
6. On approval, set `status: validated` and report the design-doc path as the input for the planning phase.
