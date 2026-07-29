# Agentic Documentation Workflow

Two model-agnostic agent workflows turn development requests into resumable, validated documentation while keeping every persistence decision with the user.

The instructions are plain Markdown and can be loaded by any model or agent runtime. Named-skill invocation syntax varies by runtime.

## Workflow

1. **`brainstorming`** may run implicitly. It inspects repository evidence, separates and classifies coherent subjects, saves an approved triage map, explores one subject at a time, and produces a validated specification.
2. **`to-plan`** requires explicit invocation and a saved specification with `status: approved` and `validation: passed`. It re-inspects the live repository, stops on material drift, and produces a validated implementation plan.
3. ADR candidates are persisted only after plan validation. Existing ADRs are immutable: equivalent decisions are reused and changed decisions create a numbered superseding ADR.

## Usage

```text
Use the brainstorming workflow to clarify and scope this development request.
```

After the specification is saved and validation passes:

```text
Use the to-plan workflow with docs/agentic-engineering/specs/<session>/<subject>.md.
```

Runtimes that support named Agent Skills may invoke these as `$brainstorming` and `$to-plan`.

## Artifacts

| Artifact | Location | Lifecycle |
|---|---|---|
| Triage | `docs/agentic-engineering/triage/` | Disposable workflow state |
| Specification | `docs/agentic-engineering/specs/` | Disposable planning input |
| Implementation plan | `docs/agentic-engineering/plans/` | Disposable execution input |
| ADR | `docs/adr/NNNN-<domain>-<decision>.md` | Durable project decision |

No triage, specification, or plan is written before its approval gate. Saved specifications and plans are validated before handoff; plans cover every `BR-*` and `AC-*` with exactly one current repository entry point per task.

The skills never inspect or change `.gitignore`. If desired, ignore the disposable artifacts manually:

```gitignore
docs/agentic-engineering/
```

## Validation

This repository uses Codex's bundled structural validator; the workflow files themselves remain model-agnostic.

```bash
python "${CODEX_HOME:-$HOME/.codex}/skills/.system/skill-creator/scripts/quick_validate.py" skills/brainstorming
python "${CODEX_HOME:-$HOME/.codex}/skills/.system/skill-creator/scripts/quick_validate.py" skills/to-plan
```
