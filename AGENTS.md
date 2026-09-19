# Repository Agent Guide

This file is the canonical contract for any coding agent (opencode, Claude Code, Copilot, or human) working in this repository. Read it fully before writing code. When a conflict arises between your defaults and this document, this document wins — unless a maintainer explicitly overrides it for a specific issue.

## Prime rule: no work without an issue

Every unit of change starts from a GitHub issue. Issues are the spec and the tracking unit; there is no parallel task board, TODO list, or chat thread that outranks them. If you intend to do something not covered by an open issue, stop and create one first (or ask your human for one).

## Issue lifecycle & protocol

1. **Intake.** An issue must be created with the repo's intake template (`task` for features/work, `bug` for defects). Required fields:
   - Context — what is being changed and why; link to motivating conversation or decision if any
   - Acceptance criteria (AC) — a list of independently verifiable statements. Each AC must be checkable by running something (a test command, a CLI invocation, an observable output), not "looks right" / "feels correct"
   - Out of scope — explicit non-goals so agents do not gold-plate
2. **Agent-ready flag.** New issues arrive auto-labelled `triage`. An issue is promoted to `agent-ready` (by the reporter or a maintainer, after intake review, not by default) once it also names the commands to run for verification (in AC or a Verification section). If an agent cannot state *how* it will demonstrate "done" when looking at an `agent-ready` issue, that's a spec defect — flag it on the thread instead of proceeding on assumptions.
3. **Claim.** Before modifying working files, comment your identity (agent name + session) and note any assumptions. Prefer one active modifier per file set; if two agents touch overlapping areas, coordinate via issue comments rather than both editing.
4. **Execute — incremental commits.** Commit frequently with small logical units. Every commit message must reference the driving issue id:

   ```text
   <imperative summary> (#NN)
   ```

   The id is the last component of any AC/branch/PR that this change serves; if a commit spans multiple issues, prefer splitting it instead.
5. **Verify.** Before declaring done or opening/updating a PR, re-run each acceptance criterion and record the evidence (command + observable result) on an issue comment labelled `verification`. A claim of "done" without recorded verification is invalid.

## Verification standard (the oracle)

- Automated (tests/lint/typecheck via CI in `.github/workflows/ci.yml`) beats manual; if a step only has a manual check, say so explicitly on the issue.
- Prefer adding automated checks to the repo over relying on re-runs; every time you manually check something twice is an argument for writing it as a test.
- A passing AC must pass *reproducibly*: clean checkout + the commands named in the issue = same green outcome.

## PR rules

1. Use the standard repo template (`.github/PULL_REQUEST_TEMPLATE.md`). The first body line **must** be `Closes #NN` so the issue auto-resolves on merge; if a single PR covers multiple issues, list each on its own `Closes` line — never bury them in prose.
2. One AC → at least one concrete evidence item (test name or CI check) linking back to it.
3. If reviewers/agents request changes, do not force-push over agreed history; respond point-by-point and re-verify affected ACs before re-requesting review.

## Conflict & safety rules

1. **Never overwrite uncommitted user work.** If you find dirty files that appear to be in-progress by someone else (another agent or a human), read the diff first, then either finish it if trivially completable or leave it and open an issue describing what remains.
2. Secrets must not be committed: no keys/tokens/passwords in code, configs, or examples; use environment-variable placeholders when showing configuration snippets.
3. When you change public-facing behaviour (CLI flags, file formats, API surface), update the relevant documentation in the **same commit** — an AC with "docs updated" must exist and pass before merge.

## Scope discipline / anti-Gold-plating

1. Implement only what the issue's acceptance criteria require; do not refactor adjacent code unless that refactoring is itself an AC or explicitly requested on the issue.
2. If you discover a genuinely better approach than the one described in the issue, surface it as a comment + linked sub-issue before deviating — silent deviation is a defect even if the result improves.