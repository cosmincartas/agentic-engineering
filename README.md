# Agentic Documentation Workflow

Two model-agnostic agent workflows turn development requests into validated documentation while keeping every persistence decision with the user.

The instructions are plain Markdown and can be loaded by any model or agent runtime. Named-skill invocation syntax varies by runtime.

## Workflow

1. **`brainstorming`** may run implicitly. It inspects repository evidence, clarifies the request through focused questions, and produces two user-validated artifacts in sequence: a PRD (problem, goals, constraints, `FR-*`/`NFR-*` requirements, `AC-*` acceptance criteria) and a design document (design, alternatives considered, contracts, data models, error handling, verification). Each artifact is saved as `status: draft` the moment it is drafted, then validated by the user before the flow proceeds.
2. **`to-plan`** requires explicit invocation and consumes a validated design document. It re-inspects the live repository, stops on material drift, and produces a user-validated, resumable implementation plan: granular tasks that each carry a status line, one repository entry point, requirement IDs, and an observable verification outcome. The plan is a self-contained handoff — any later session resumes from the first task not yet `Done`.

## Usage

```text
Use the brainstorming workflow to clarify and scope this development request.
```

After the design document is saved and the user validates it:

```text
Use the to-plan workflow with docs/agentic-engineering/specs/<session>/<subject>.md.
```

Named-skill invocation syntax varies by runtime.

## Install

The same `skills/` directory is packaged for Codex, Claude Code, GitHub Copilot, and Pi:

```bash
# Codex
codex plugin marketplace add cosmincartas/agentic-engineering
codex plugin add agentic-workflow@agentic-workflow

# Claude Code
claude plugin marketplace add cosmincartas/agentic-engineering
claude plugin install agentic-workflow@agentic-workflow

# GitHub Copilot CLI
copilot plugin install cosmincartas/agentic-engineering

# Pi
pi install git:github.com/cosmincartas/agentic-engineering
```

Explicit invocation syntax is host-specific: `$agentic-workflow:to-plan` in Codex, `/agentic-workflow:to-plan` in Claude Code, `/agentic-workflow/to-plan` in Copilot, and `/skill:to-plan` in Pi.

## Releases

Keep the versions in `.codex-plugin/plugin.json` and `.claude-plugin/plugin.json` equal, tag the commit as `v<version>`, and publish a GitHub Release. Users can watch GitHub Releases for cross-agent update notifications.

## Artifacts

| Artifact | Location | Lifecycle |
|---|---|---|
| PRD | `docs/agentic-engineering/prd/` | Disposable planning input |
| Design document | `docs/agentic-engineering/specs/` | Disposable planning input |
| Implementation plan | `docs/agentic-engineering/plans/` | Resumable execution input |

PRDs, design documents, and plans are saved automatically as drafts and validated by the user before the workflow proceeds; nothing is committed to git without explicit consent. Plans additionally carry execution state: implementers update each task's `Status` line as work proceeds.

The skills never inspect or change `.gitignore`. If desired, ignore the disposable artifacts manually:

```gitignore
docs/agentic-engineering/
```

## Validation

This repository uses Codex's bundled structural validator; the workflow files themselves remain model-agnostic.

```bash
python "${CODEX_HOME:-$HOME/.codex}/skills/.system/skill-creator/scripts/quick_validate.py" skills/brainstorm
python "${CODEX_HOME:-$HOME/.codex}/skills/.system/skill-creator/scripts/quick_validate.py" skills/to-plan
```
