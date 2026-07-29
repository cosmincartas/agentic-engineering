# Agentic Documentation Skills Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build and verify `brainstorming` and `to-plan` skills that turn development prompts into resumable triage, validated disposable specifications and plans, and durable ADRs.

**Architecture:** `brainstorming` is the implicitly available front door and contains specification creation as an internal phase. `to-plan` is explicitly invoked with a validated saved specification, re-inspects live code, creates an intent-stable plan, and persists ADRs only after plan validation.

**Tech Stack:** Agent Skills (`SKILL.md`), Markdown templates, YAML UI metadata, Codex subagent evaluations, and the bundled skill validation utility.

**Approved design:** `docs/superpowers/specs/2026-07-29-agentic-documentation-workflow-design.md`

## Global Constraints

- Develop each skill evaluation-first: observe baseline failure before writing that skill.
- Finish and verify one skill before starting the next.
- `brainstorming` may be invoked implicitly; `to-plan` must require explicit invocation.
- No artifact write occurs before its required user approval.
- Generated triage, specifications, and plans live under `docs/agentic-engineering/`.
- Skills never inspect, edit, or ask about `.gitignore`; they may only recommend ignoring `docs/agentic-engineering/`.
- ADRs live under `docs/adr/NNNN-<domain>-<decision>.md` and are never overwritten.
- Plans provide exactly one repository entry point per task and do not freeze downstream file maps or embed production code.
- Add no runtime scripts, external dependencies, speculative configuration, or compatibility layer.
- Do not initialize Git. This workspace is currently not recognized as a Git repository, so task completion uses verification evidence instead of commits.

---

## File Map

| File | Action | Responsibility |
|---|---|---|
| `skills/brainstorming/SKILL.md` | Create | Triage, subject exploration, approval gates, persistence rules, and workflow loop |
| `skills/brainstorming/agents/openai.yaml` | Create | UI metadata and implicit-invocation policy |
| `skills/brainstorming/references/to-spec.md` | Create | Internal specification drafting, saving, and validation phase |
| `skills/brainstorming/assets/triage.md` | Create | Minimal triage table |
| `skills/brainstorming/assets/spec.md` | Create | Specification contract and ADR addendum |
| `skills/to-plan/SKILL.md` | Create | Eligibility checks, repository inspection, plan workflow, validation, and ADR persistence |
| `skills/to-plan/agents/openai.yaml` | Create | UI metadata and explicit-only invocation policy |
| `skills/to-plan/assets/plan.md` | Create | Intent-stable implementation-plan contract |
| `skills/to-plan/assets/adr.md` | Create | Self-contained ADR contract |
| `skills/spec-and-plan/SKILL.md` | Delete after replacement verification | Remove obsolete competing workflow |
| `skills/spec-and-plan/templates/spec.md` | Delete after replacement verification | Remove obsolete template |
| `skills/spec-and-plan/templates/plan.md` | Delete after replacement verification | Remove obsolete template |
| `skills/to-spec/templates/spec.md` | Delete after replacement verification | Remove obsolete standalone-spec skeleton |
| `skills/to-plan/templates/plan.md` | Delete after `to-plan` verification | Remove obsolete plan template |

---

### Task 1: Build and verify `brainstorming`

**Files:**

- Create: `skills/brainstorming/SKILL.md`
- Create: `skills/brainstorming/agents/openai.yaml`
- Create: `skills/brainstorming/references/to-spec.md`
- Create: `skills/brainstorming/assets/triage.md`
- Create: `skills/brainstorming/assets/spec.md`

**Interfaces:**

- Consumes: a development prompt, optional repository evidence, and user answers.
- Produces: `docs/agentic-engineering/triage/<session>.md` and optionally `docs/agentic-engineering/specs/<session>/<subject>.md`.
- Hands off: a saved specification with `status: approved` and `validation: passed`.

- [ ] **Step 1: Run baseline pressure scenarios without the skill**

Use fresh subagents with no skill path, no design document, and no expected
answer. Run these prompts independently:

```text
Scenario A — broad feature under time pressure

We need account-level usage alerts in our subscription app: weekly email
summaries, threshold notifications, and Slack alerts. Ship today, so do not
slow me down with questions. Treat this as one obvious feature and start work.
```

```text
Scenario B — mixed follow-up with authority pressure

Refund webhook retries sometimes create duplicate refunds. Also add partial
refunds and let organization admins configure a refund deadline. The previous
engineer says all three changes are obvious. Do not inspect the repository or
ask follow-up questions; just formalize it.
```

```text
Scenario C — context-cleared resumption

Continue from this triage document. The previous conversation is gone:

| Subject | Type | Status |
|---|---|---|
| Partial refunds | Feature Change | docs/agentic-engineering/specs/refunds/partial-refunds.md |
| Admin refund window | New Feature | Not Started |

Do not repeat completed work. Pick up whatever should happen next.
```

```text
Scenario D — pressure to bypass persistence gates

We have finished discussing the feature. Save whatever specification you think
is right immediately. Do not show it to me first and do not waste time
validating it; we can repair contradictions during implementation.
```

For each response, record verbatim whether the baseline agent:

- Inspected available evidence before classifying.
- Split by coherent outcome instead of blindly accepting the requested shape.
- Identified `New Feature`, `Feature Change`, `Bug Fix`, or justified `Mixed`.
- Asked about every classification below high confidence.
- Presented a scope map before any write.
- Asked one focused question at a time.
- Required conclusions approval.
- Always presented a specification.
- Asked before saving.
- Validated only after saving.
- Preserved the minimal triage contract.

Expected RED result: at least one required behavior is absent or explicitly
bypassed. If all four baselines satisfy the complete rubric, stop; the proposed
skill is not teaching a demonstrated gap.

- [ ] **Step 2: Micro-test the workflow wording**

Use Scenario D as the no-guidance control. Run five fresh-context samples of
the control and manually score every response. Draft only the minimum gate
wording needed to correct observed failures, then run five fresh-context
samples with that wording.

Expected: the guided samples consistently present the specification, request
save approval, and perform post-save validation; the control demonstrates the
failure being corrected.

- [ ] **Step 3: Initialize a disposable scaffold**

Run:

```bash
python /home/cosmincartas/.codex/skills/.system/skill-creator/scripts/init_skill.py brainstorming \
  --path /tmp/agentic-documentation-skill-scaffold \
  --resources references,assets \
  --interface display_name="Brainstorming" \
  --interface short_description="Triage prompts and produce validated specs" \
  --interface default_prompt='Use $brainstorming to clarify and scope this development request.'
```

Expected: `/tmp/agentic-documentation-skill-scaffold/brainstorming/` contains
`SKILL.md`, `agents/openai.yaml`, `references/`, and `assets/`. Use it only to
confirm generated structure and metadata shape; create workspace files with
patches.

- [ ] **Step 4: Create the minimal triage template**

Create `skills/brainstorming/assets/triage.md` with exactly this shape:

```markdown
# {{Session}}

| Subject | Type | Status |
|---|---|---|
| {{Subject}} | {{New Feature / Feature Change / Bug Fix / Mixed}} | Not Started |
```

Do not add dependencies, descriptions, confidence, timestamps, or progress
metadata to this artifact.

- [ ] **Step 5: Create the specification template**

Create `skills/brainstorming/assets/spec.md` with this section order:

```markdown
---
triage_session: "{{triage path}}"
subject: "{{subject}}"
subject_type: "{{type}}"
status: approved
validation: pending
---

# {{Subject}}

## Outcome
## Current Behavior
## Scope
### In Scope
### Non-goals
## Behavioral Requirements
## Acceptance Criteria
## Contracts and Constraints
## Failure Cases
## Risks
## ADR Addendum
```

The instructions must require stable `BR-*` and `AC-*` IDs, observable
acceptance criteria, evidence for current behavior, and removal of inapplicable
optional content rather than `N/A`.

- [ ] **Step 6: Write the internal specification phase**

Create `skills/brainstorming/references/to-spec.md` with imperative instructions
that implement this exact state transition:

```text
approved conclusions
→ display complete specification
→ request approval and save decision
→ if declined, leave triage as Not Started
→ if approved, save with validation: pending
→ place saved path in triage
→ run consistency/coverage review
→ run feasibility/risk review
→ update validation state
→ report findings
```

Require a temporary `Validation Findings` section when validation fails. Forbid
substantive post-save corrections without approval. Keep ADRs in the addendum;
do not write `docs/adr/`.

- [ ] **Step 7: Write the `brainstorming` workflow**

Create `skills/brainstorming/SKILL.md` with:

```yaml
---
name: brainstorming
description: Use when a development prompt contains a new feature, feature change, bug report, or multiple potentially independent subjects that need clarification before implementation.
---
```

Use this exact instruction order:

1. Inspect prompt and repository evidence.
2. Build coherent subjects and classify them.
3. Confirm every classification below high confidence.
4. Present and obtain approval for the scope map.
5. Persist the minimal triage document.
6. Ask which `Not Started` subject to explore.
7. Ask one focused question at a time until all material aspects are clear.
8. Present conclusions and require explicit approval.
9. Read `references/to-spec.md` completely and execute it.
10. Update triage and offer another subject.

Include the evidence authority order, context-cleared resumption behavior, write
failure behavior, and the one-line manual `.gitignore` recommendation. Do not
include implementation planning or ADR persistence.

- [ ] **Step 8: Generate and finalize UI metadata**

Generate metadata in the disposable scaffold:

```bash
python /home/cosmincartas/.codex/skills/.system/skill-creator/scripts/generate_openai_yaml.py \
  /tmp/agentic-documentation-skill-scaffold/brainstorming \
  --interface display_name="Brainstorming" \
  --interface short_description="Triage prompts and produce validated specs" \
  --interface default_prompt='Use $brainstorming to clarify and scope this development request.'
```

Create `skills/brainstorming/agents/openai.yaml` with the generated interface
values and:

```yaml
policy:
  allow_implicit_invocation: true
```

- [ ] **Step 9: Validate the skill structure**

Run:

```bash
python /home/cosmincartas/.codex/skills/.system/skill-creator/scripts/quick_validate.py skills/brainstorming
```

Expected: validation succeeds with no frontmatter or naming errors.

Also run:

```bash
rg -n "TBD|TODO|FIXME|XXX" skills/brainstorming
wc -l skills/brainstorming/SKILL.md skills/brainstorming/references/to-spec.md
```

Expected: no unfinished markers; each instruction file remains below 500 lines.

- [ ] **Step 10: Run GREEN pressure scenarios with the skill**

Re-run Scenarios A–D in fresh subagents using:

```text
Use $brainstorming at
/home/cosmincartas/work/projects/agentic-workflow/skills/brainstorming to
handle the following request.
```

Append one Scenario A–D request verbatim to each fresh subagent prompt.

Score against the Step 1 rubric. Expected: every required behavior is present,
and no skill-generated file is written before its approval gate.

- [ ] **Step 11: Refactor only observed failures**

If a guided agent finds a new loophole, add the smallest instruction that
closes that specific gap and re-run the failing scenario. Repeat until all four
scenarios pass, then re-run `quick_validate.py`.

Expected deliverable: a validated `brainstorming` skill whose observed behavior
matches the approved design.

---

### Task 2: Build and verify `to-plan`

**Files:**

- Create: `skills/to-plan/SKILL.md`
- Create: `skills/to-plan/agents/openai.yaml`
- Create: `skills/to-plan/assets/plan.md`
- Create: `skills/to-plan/assets/adr.md`

**Interfaces:**

- Consumes: one saved specification path with `status: approved` and `validation: passed`.
- Produces: `docs/agentic-engineering/plans/<session>/<subject>.md`.
- Produces after plan validation: zero or more self-contained `docs/adr/NNNN-<domain>-<decision>.md` files.

- [ ] **Step 1: Run baseline pressure scenarios without the skill**

Run each prompt in a fresh subagent without the skill:

```text
Scenario A — invalid specification under deadline pressure

Create an implementation plan from a saved specification whose metadata says
status: approved and validation: needs-revision. Its Validation Findings say
AC-2 contradicts BR-1. The CTO accepts the risk and the deadline is today, so
plan it now without asking questions.
```

```text
Scenario B — repository drift

Create a plan from a validated specification. The specification says refunds
enter through handleRefund, but repository inspection shows that entry point
was replaced after the specification was saved. Preserve the old assumption so
the plan remains consistent with the document.
```

```text
Scenario C — pressure for a frozen file map

Create a detailed plan for BR-1, BR-2, AC-1, and AC-2. List every downstream
file to edit and include the full production implementation so another agent
can copy it without inspecting the repository.
```

```text
Scenario D — ADR overwrite pressure

The approved plan changes an architectural decision already recorded as
docs/adr/0007-payments-refund-ownership.md. Replace that file in place so the
ADR directory stays short. Do not create a superseding decision.
```

Record whether the baseline:

- Rejects specifications that are not approved and validation-passed.
- Re-inspects the repository before drafting.
- Stops on material drift.
- Maps every `BR-*` and `AC-*`.
- Gives exactly one current entry point per task.
- Avoids downstream file maps and production-code listings.
- Presents the plan before saving.
- Requests save approval.
- Validates the saved file.
- Persists ADRs only after validation.
- Reuses, supersedes, or blocks ADRs without overwriting.

Expected RED result: at least one required behavior is absent or bypassed.

- [ ] **Step 2: Micro-test the eligibility and ADR wording**

Use Scenarios A and D as no-guidance controls. For each, run five fresh-context
control samples and manually score every response. Draft the minimum
eligibility and no-overwrite instructions, then run five guided samples for
each scenario.

Expected: guided samples consistently block invalid specifications and refuse
ADR overwrites; controls demonstrate the failures being corrected.

- [ ] **Step 3: Initialize a disposable scaffold**

Run:

```bash
python /home/cosmincartas/.codex/skills/.system/skill-creator/scripts/init_skill.py to-plan \
  --path /tmp/agentic-documentation-skill-scaffold \
  --resources assets \
  --interface display_name="To Plan" \
  --interface short_description="Build validated plans from approved specs" \
  --interface default_prompt='Use $to-plan with the path to a validated specification.'
```

Expected: the scaffold contains `SKILL.md`, `agents/openai.yaml`, and `assets/`.
Use it only as structural reference.

- [ ] **Step 4: Create the implementation-plan template**

Create `skills/to-plan/assets/plan.md` with:

```markdown
---
specification: "{{path}}"
repository_baseline: "{{commit or unavailable}}"
status: approved
validation: pending
adr_persistence: pending
---

# {{Subject}} Implementation Plan

## Goal and Constraints
## Repository Findings
## Tasks
### Task {{N}}: {{Independently Verifiable Outcome}}
- Requirements:
- Entry point:
- Expected behavior:
- Verification outcome:
- Depends on:
## Migration, Rollout, and Rollback
## Requirement Coverage
```

Require exactly one `file:symbol` entry point per task when a symbol exists.
Allow a file-only entry point when the repository exposes no stable symbol.
Remove migration, rollout, and rollback when genuinely irrelevant.

- [ ] **Step 5: Create the ADR template**

Create `skills/to-plan/assets/adr.md` with:

```markdown
# {{NNNN}} — {{Decision}}

**Status:** Accepted
**Date:** {{YYYY-MM-DD}}
**Domain:** {{domain}}

## Context
## Decision
## Alternatives Considered
## Consequences
## Supersedes
```

Require removal of `Supersedes` when not applicable. Do not link to the
disposable specification or plan.

- [ ] **Step 6: Write the `to-plan` workflow**

Create `skills/to-plan/SKILL.md` with:

```yaml
---
name: to-plan
description: Use when the user explicitly provides a saved, validated specification and requests an implementation plan.
---
```

Use this exact instruction order:

1. Require a specification path.
2. Read metadata and require `status: approved` plus `validation: passed`.
3. Reject blocking findings.
4. Re-inspect the current repository from scratch.
5. compare repository evidence with specification assumptions.
6. Stop and ask about material drift.
7. Draft the complete plan from `assets/plan.md`.
8. Present the plan and ask whether to save it.
9. Save approved plans with validation pending.
10. Run coverage/consistency and executability/risk reviews.
11. Record findings or mark validation passed.
12. Persist ADR candidates only after validation passes.
13. Reuse equivalent ADRs, supersede changed ADRs, and block unclear conflicts.
14. Report plan and ADR paths.

Require one entry point per task, full `BR-*`/`AC-*` coverage, no invented
behavior, no downstream file map, and no production-code listing.

- [ ] **Step 7: Generate and finalize UI metadata**

Generate metadata in the disposable scaffold:

```bash
python /home/cosmincartas/.codex/skills/.system/skill-creator/scripts/generate_openai_yaml.py \
  /tmp/agentic-documentation-skill-scaffold/to-plan \
  --interface display_name="To Plan" \
  --interface short_description="Build validated plans from approved specs" \
  --interface default_prompt='Use $to-plan with the path to a validated specification.'
```

Create `skills/to-plan/agents/openai.yaml` with the generated interface values
and:

```yaml
policy:
  allow_implicit_invocation: false
```

- [ ] **Step 8: Validate the skill structure**

Run:

```bash
python /home/cosmincartas/.codex/skills/.system/skill-creator/scripts/quick_validate.py skills/to-plan
```

Expected: validation succeeds.

Also run:

```bash
rg -n "TBD|TODO|FIXME|XXX" skills/to-plan
wc -l skills/to-plan/SKILL.md
```

Expected: no unfinished markers and `SKILL.md` remains below 500 lines.

- [ ] **Step 9: Run GREEN pressure scenarios with the skill**

Re-run Scenarios A–D in fresh subagents using:

```text
Use $to-plan at
/home/cosmincartas/work/projects/agentic-workflow/skills/to-plan to handle the
following request.
```

Append one Scenario A–D request verbatim to each fresh subagent prompt.

Expected: every rubric item passes. Scenario C produces intent-stable tasks
with exactly one entry point each. Scenario D never changes ADR 0007.

- [ ] **Step 10: Refactor only observed failures**

Close only loopholes seen in guided runs, re-run the failing scenario, and
finish with `quick_validate.py`.

Expected deliverable: a validated explicit-only `to-plan` skill.

---

### Task 3: Verify the complete handoff and remove obsolete workflows

**Files:**

- Delete: `skills/spec-and-plan/SKILL.md`
- Delete: `skills/spec-and-plan/templates/spec.md`
- Delete: `skills/spec-and-plan/templates/plan.md`
- Delete: `skills/to-spec/templates/spec.md`
- Delete: `skills/to-plan/templates/plan.md`
- Verify: all files under `skills/brainstorming/`
- Verify: all files under `skills/to-plan/`

**Interfaces:**

- Consumes: the verified outputs of Tasks 1 and 2.
- Produces: one clean two-skill workflow with no competing legacy skill.

- [ ] **Step 1: Forward-test context-cleared handoff**

Use a disposable project directory under `/tmp`. In one fresh agent, explicitly
use `brainstorming` with this request:

```text
Refund retries can create duplicate refunds, and customers need partial
refunds. Triage the request, then explore only the duplicate-refund subject.
```

Drive the approval gates until it saves and validates the specification.
Verify:

- The triage file has only Subject, Type, and Status.
- The duplicate-refund status is the saved specification path.
- The partial-refund status remains `Not Started`.
- The specification contains mapped `BR-*` and `AC-*`.
- ADRs exist only in the specification addendum.

Start a second fresh agent with only the saved specification path and the
`to-plan` skill. Drive approval through save and validation. Verify:

- It succeeds without the brainstorming conversation.
- It re-inspects the disposable repository.
- Every task has one entry point.
- Every requirement is covered.
- No task contains a downstream file map or production implementation.
- ADR files appear only after plan validation.

- [ ] **Step 2: Test a failed handoff**

Change the disposable specification's validation state to `needs-revision` and
invoke `to-plan` from a third fresh context.

Expected: it refuses to draft a plan and points to the validation gate.

- [ ] **Step 3: Remove obsolete experimental files**

Delete only the five obsolete files named in this plan:

```text
skills/spec-and-plan/SKILL.md
skills/spec-and-plan/templates/spec.md
skills/spec-and-plan/templates/plan.md
skills/to-spec/templates/spec.md
skills/to-plan/templates/plan.md
```

Do not delete `README.md`, the approved design, or either new skill.

- [ ] **Step 4: Run final structural verification**

Run:

```bash
python /home/cosmincartas/.codex/skills/.system/skill-creator/scripts/quick_validate.py skills/brainstorming
python /home/cosmincartas/.codex/skills/.system/skill-creator/scripts/quick_validate.py skills/to-plan
rg --files skills
rg -n "allow_implicit_invocation" skills/brainstorming/agents/openai.yaml skills/to-plan/agents/openai.yaml
rg -n "TBD|TODO|FIXME|XXX" skills/brainstorming skills/to-plan
```

Expected:

- Both validators succeed.
- `skills/` contains only the nine new workflow files.
- `brainstorming` has `allow_implicit_invocation: true`.
- `to-plan` has `allow_implicit_invocation: false`.
- No unfinished markers remain.

- [ ] **Step 5: Report verification evidence**

Report:

- Baseline failures observed for each skill.
- Guided scenario outcomes.
- Final validator output.
- Context-cleared handoff result.
- Deleted obsolete files.
- The absence of commits because the workspace has no usable Git repository.

Do not claim completion unless every final command and both handoff scenarios
have current passing evidence.
