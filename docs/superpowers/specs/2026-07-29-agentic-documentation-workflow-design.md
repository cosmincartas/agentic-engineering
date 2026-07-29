# Agentic Documentation Workflow

**Date:** 2026-07-29  
**Status:** Pending written review

## Purpose

Create a documentation workflow that gives the user control over how a
development prompt becomes scoped work, a specification, an implementation
plan, and durable architectural decisions.

The workflow has two user-facing skills:

- `brainstorming`: triage, exploration, conclusions, and specification
  creation.
- `to-plan`: implementation planning and ADR persistence.

Specifications, plans, and triage sessions are disposable working artifacts.
ADRs are the only durable project documentation produced by the workflow.

## Goals

- Triage single- and multi-subject prompts before detailed exploration.
- Handle new features, feature changes, bug fixes, and mixed follow-up prompts.
- Resolve material ambiguity through active user questioning.
- Require user approval at every persistence boundary.
- Support clearing the conversation context between subjects and before
  planning.
- Produce detailed, traceable specifications without committing them as
  durable project documentation.
- Produce plans that remain useful when downstream code changes.
- Persist only approved, implementation-bound architectural decisions.

## Non-goals

- Automatically implement a plan.
- Automatically invoke `to-plan`.
- Preserve specifications, plans, or triage documents in version control.
- Infer whether the user has configured `.gitignore`.
- Maintain compatibility with existing experimental workflow files.
- Freeze every downstream file or symbol into an implementation plan.

## Skill Boundary

### `brainstorming`

Implicit invocation is allowed. Its description should match development
prompts that still need clarification, including new features, feature
changes, bug reports, and mixed requests.

The skill owns:

1. Prompt and repository inspection
2. Scope triage
3. Subject selection
4. Active questioning
5. Conclusions and approval
6. Mandatory specification drafting
7. Optional specification persistence
8. Post-save specification validation
9. Triage-session updates

Specification creation is an internal phase of `brainstorming`, not a separate
user-facing skill. The phase lives in `references/to-spec.md` and is loaded
after the user approves the conclusions for a subject.

### `to-plan`

Implicit invocation is disabled. The user must invoke it explicitly and
provide a saved specification path.

The skill owns:

1. Specification eligibility checks
2. Fresh repository inspection
3. Repository-drift detection
4. Implementation-plan drafting
5. Optional plan persistence
6. Post-save plan validation
7. ADR persistence

## Subject Triage

### Evidence

Do not classify from prompt wording alone. When a repository exists, inspect:

- Current implementation and callers
- Tests and test names
- Public interfaces, UI copy, validation, and error messages
- Git history and changelog when available
- Existing ADRs when relevant
- Current user clarifications

Use this authority order:

1. Current user clarification
2. Approved durable decisions
3. Tests and observable contracts
4. Current code behavior

Surface conflicts instead of silently choosing an interpretation.

### Classification

Classify each coherent, independently specifiable outcome as:

- `New Feature`: no existing capability or contract covers the outcome.
- `Feature Change`: an existing capability intentionally needs different
  accepted behavior.
- `Bug Fix`: implementation violates already accepted behavior.
- `Mixed`: separating tightly coupled behavior would make the subject less
  coherent.

The model decides subject boundaries from the prompt and repository evidence.
It should not split mechanically by type. Any classification below high
confidence requires explicit user confirmation.

### Persisted Triage

After the user approves the scope map, save:

```text
docs/agentic-engineering/triage/<session>.md
```

`<session>` is `YYYY-MM-DD-<prompt-slug>.md`. Add `-2`, `-3`, and so on only
when the name collides.

The document contains only:

```markdown
| Subject | Type | Status |
|---|---|---|
| Partial refunds | Feature Change | Not Started |
| Duplicate handling | Bug Fix | docs/agentic-engineering/specs/... |
```

`Status` is either `Not Started` or the saved specification path. If the user
declines to save a presented specification, leave the subject as
`Not Started`. If a specification is saved but later fails validation, retain
its path; the specification's validation state controls planning eligibility.

## `brainstorming` Workflow

1. Inspect the prompt and relevant repository evidence.
2. Build a scope map.
3. Resolve classifications below high confidence with the user.
4. Present the scope map for approval.
5. Persist the approved triage document.
6. Ask which `Not Started` subject to explore.
7. Ask one focused question at a time until the subject's goals, behavior,
   boundaries, constraints, risks, and decisions are clear.
8. Present consolidated conclusions.
9. Require explicit approval of the conclusions.
10. Load the internal specification phase.
11. Always draft and present a detailed specification.
12. Ask whether the user approves and wants to save it.
13. If declined, leave the triage status as `Not Started`.
14. If approved, save the specification with validation pending.
15. Put the saved specification path in the triage table.
16. Validate the saved file.
17. Update the specification's validation state and report findings.
18. Ask whether to explore another subject or stop.

No artifact is written before the user approves the triage map. A
specification is never written before the user approves the displayed draft.

## Specification Contract

### Location

```text
docs/agentic-engineering/specs/<session>/<subject>.md
```

### Metadata

```yaml
triage_session: <path>
subject: <subject>
subject_type: <type>
status: approved
validation: pending | passed | needs-revision
```

### Required content

- Outcome and current behavior
- Scope and non-goals
- Behavioral requirements with stable `BR-*` IDs
- Acceptance criteria with stable `AC-*` IDs
- Relevant contracts and constraints
- Failure cases and concrete risks
- ADR addendum

Acceptance criteria must be observable and map to behavioral requirements.
The specification describes required behavior, not a downstream file map or
production implementation.

### ADR Addendum

Each candidate records enough information to later create a self-contained
ADR:

- Domain
- Decision
- Context
- Alternatives considered
- Consequences
- Existing ADR relationship, when known

Candidates remain addenda only. Saving or validating a specification does not
create files under `docs/adr/`.

## `to-plan` Workflow

1. Require an explicit invocation and specification path.
2. Read the saved specification.
3. Require `status: approved` and `validation: passed`.
4. Reject unresolved blocking questions or findings.
5. Re-inspect the current repository from scratch.
6. Compare current evidence with specification assumptions.
7. Report material drift and request direction before drafting.
8. Draft and present the implementation plan.
9. Ask whether the user approves and wants to save it.
10. If approved, save the plan with validation pending.
11. Validate the saved file.
12. Update the plan's validation state and report findings.
13. After validation passes, persist the ADR addendum.
14. Report plan and ADR paths.

## Implementation Plan Contract

### Location

```text
docs/agentic-engineering/plans/<session>/<subject>.md
```

### Metadata

```yaml
specification: <path>
repository_baseline: <commit or unavailable>
status: approved
validation: pending | passed | needs-revision
adr_persistence: pending | persisted | not-required | needs-attention
```

### Required content

- Specification goal and constraints
- Ordered, independently verifiable tasks
- Covered `BR-*` and `AC-*` IDs for every task
- Exactly one repository entry point per task, expressed as a file path plus
  symbol when available
- Expected behavior
- Observable verification outcome
- Task dependencies and sequencing
- Migration, rollout, and rollback details only when relevant
- Complete requirement coverage table

The plan is intent-stable. It does not prescribe every downstream file and
does not embed full production code. The implementer must trace the current
flow from each task's entry point when implementation begins.

## ADR Contract

### Location and naming

```text
docs/adr/NNNN-<domain>-<decision>.md
```

`NNNN` is the next four-digit repository-wide sequence found by scanning
existing ADR filenames.

### Required content

- Status
- Date
- Domain
- Context
- Decision
- Alternatives considered
- Consequences
- `Supersedes`, when applicable

Every ADR is self-contained and must remain understandable after its source
specification and plan have been deleted.

### Persistence policy

Persist ADRs only after the implementation plan is approved, saved, and
validated.

- Equivalent existing decision: reuse it.
- Changed existing decision: create the next ADR and declare `Supersedes`.
- Unclear conflict: set ADR persistence to `needs-attention` and ask the user.
- Never overwrite an existing ADR.

## Post-save Validation

Both document types are saved with `validation: pending`. Validation operates
on the saved file, not the displayed draft.

### Specification passes

1. **Coverage and consistency**
   - Matches approved conclusions
   - Contains unique and complete `BR-*` and `AC-*` IDs
   - Has no contradictions, placeholders, or scope drift
2. **Feasibility and risk**
   - Acceptance criteria are testable
   - Failure behavior is covered
   - Repository assumptions have evidence
   - ADR candidates are complete

### Plan passes

1. **Coverage and consistency**
   - Covers every `BR-*` and `AC-*`
   - Introduces no new product behavior
   - Preserves specification constraints
2. **Executability and risk**
   - Entry points exist at the inspected baseline
   - Task order is viable
   - Verification outcomes are observable
   - Required migration, rollout, and rollback work is present

### Findings

When a problem is found:

1. Set `validation: needs-revision`.
2. Add a concise `Validation Findings` section to the document.
3. Present the findings to the user.
4. Do not change substantive content without approval.
5. Apply approved corrections.
6. Re-run both validation passes.
7. Remove resolved findings and set `validation: passed`.

## Context Clearing

The persisted triage table is the handoff between subject sessions. A new
context can read it, select a `Not Started` subject, inspect the repository,
and begin exploration.

The validated specification is the sole workflow artifact required by
`to-plan`. `to-plan` does not rely on the original discussion or triage
document for meaning.

The implementation plan is independently usable from its specification
reference and task entry points, while implementation still re-inspects live
code.

## Git Ignore Policy

The skills never inspect, edit, or ask about `.gitignore`.

They may display this brief recommendation:

```gitignore
docs/agentic-engineering/
```

Whether to apply it is entirely the user's responsibility. `docs/adr/` is not
part of that recommendation.

## Failure Handling

- Failed artifact write: report the failure and do not update dependent state.
- Invalid saved specification: retain its triage path but block `to-plan`.
- Invalid saved plan: do not persist ADRs.
- Material repository drift: stop planning and ask for direction.
- Missing Git metadata: use `repository_baseline: unavailable`; do not block
  repository inspection.
- ADR conflict: do not guess or overwrite; request user resolution.
- Context cleared before a specification is saved: the subject remains
  `Not Started`.

## Package Structure

```text
skills/
├── brainstorming/
│   ├── SKILL.md
│   ├── agents/openai.yaml
│   ├── references/to-spec.md
│   └── assets/
│       ├── triage.md
│       └── spec.md
└── to-plan/
    ├── SKILL.md
    ├── agents/openai.yaml
    └── assets/
        ├── plan.md
        └── adr.md
```

Invocation policy:

- `brainstorming`: implicit invocation allowed.
- `to-plan`: implicit invocation disabled.

No bundled scripts or external dependencies are required.

## Evaluation Strategy

Develop each skill evaluation-first:

1. Run representative scenarios without the skill and record failures.
2. Write the minimum instructions that address observed failures.
3. Run the same scenarios with the skill.
4. Refine instructions only when the evaluation exposes a gap.
5. Validate skill metadata and directory structure.
6. Forward-test handoffs in fresh contexts.

At minimum, cover:

- One new feature
- Multiple subjects in one prompt
- A follow-up containing a feature change and a bug report
- A low-confidence classification requiring confirmation
- Declining specification persistence
- Resuming another subject from a triage file
- Specification validation failure and correction
- Rejecting an unvalidated specification
- Repository drift before planning
- Planning with one entry point per task
- ADR reuse, supersession, and conflict
- Planning from a fresh context with no brainstorming history

## Success Criteria

- Broad prompts become user-approved, resumable scope maps.
- No low-confidence classification is silently accepted.
- Every explored subject produces a displayed specification.
- No specification or plan is saved without explicit user approval.
- Saved documents expose durable validation state.
- `to-plan` cannot use an invalid specification.
- Plans cover every requirement without freezing downstream implementation
  details.
- ADRs are persisted only after a plan is approved, saved, and validated.
- Deleting `docs/agentic-engineering/` does not make an ADR unintelligible.
