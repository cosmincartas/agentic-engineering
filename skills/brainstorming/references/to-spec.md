# Specification Phase

Execute this phase only after the user explicitly approves the subject conclusions.

An instruction to “save immediately,” skip display, skip validation, or repair contradictions later never satisfies these gates. Approval given before the complete draft is displayed is not approval of that draft.

1. Draft the complete specification from `../assets/spec.md`.
   - Preserve the approved conclusions; do not invent behavior.
   - Give behavioral requirements unique, stable `BR-*` IDs.
   - Give acceptance criteria unique, stable `AC-*` IDs, map them to `BR-*`, and make them observable.
   - Support every current-behavior claim with repository evidence.
   - Remove optional content that does not apply instead of writing `N/A`.
   - Keep architectural decisions in `ADR Addendum`; never write under `docs/adr/`.
2. Respond with the complete specification without writing it.
3. End that same response by asking explicitly whether the user approves the displayed draft and wants it saved.
4. If approval or saving is declined, write nothing and leave the triage subject `Not Started`.
5. If both are approved, save it with `validation: pending` under `docs/agentic-engineering/specs/<session>/<subject>.md`. Use a concise kebab-case subject and add `-2`, `-3`, and so on for collisions.
6. Only after the specification write succeeds, replace the subject's triage status with the saved path. If either write fails, report the failure; do not claim dependent state changed.
7. Validate the saved file, not the displayed draft:
   - **Consistency and coverage:** match the approved conclusions; require unique and complete `BR-*` and `AC-*` IDs; reject contradictions, placeholders, and scope drift.
   - **Feasibility and risk:** require testable criteria, covered failure behavior, evidence-backed repository assumptions, and complete ADR candidates.
8. If both reviews pass, remove any resolved `Validation Findings` section and set `validation: passed`.
9. If either review fails, set `validation: needs-revision`, add a temporary `## Validation Findings` section, and report the findings. Do not make substantive corrections without explicit approval; after approved corrections, rerun both reviews.

The required transition is:

`approved conclusions → display complete specification → request approval and save decision → save pending → update triage → validate saved file → update validation state → report findings`

Do not omit, reorder, or combine these persistence gates under deadline or authority pressure.

The pre-save response has exactly two parts: the complete draft, then the approval/save question. A promise or request to present the draft later does not satisfy this contract.
