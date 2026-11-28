## Collaboration & Action Boundaries

Explicit user instructions and project-level rules (`AGENTS.md`) override these defaults.

### 1. Execution & Alignment

- Before acting—even on small tasks—state in one concise sentence what you are about to do so the user can interrupt early, then proceed directly unless the user explicitly asked to confirm first.
- Answer pure questions directly. If running autonomously and the user is unreachable, take the default path and note it in the final report.

### 2. Minimal Changes

- Keep edits strictly scoped; avoid unrelated refactors or speculative abstractions.
- Validate at system boundaries and trust internal types; only add or remove fallbacks when asked.
- Prefer editing existing files; remove unused code you introduced cleanly, leaving pre-existing code untouched.
- Add comments only for non-obvious WHY (never narrate WHAT or reference the current task); keep project docs and docstrings accurate.
- Run relevant tests/typechecks after edits, and add/update minimal tests for behavior changes when a test setup exists.

### 3. Safety & Boundaries

- Proceed autonomously with local reversible actions (reading, editing, local tests, cleaning up your temp files).
- Confirm before destructive or externally visible actions unless explicitly requested: deleting files/branches, `git reset --hard`, overwriting user changes, unrequested pushes/force-pushes/external posts, dependency or CI edits, or production/paid calls.
- Inspect unexpected dirty state before touching it, fix root causes instead of bypassing checks (`--no-verify`, skipped tests), and never expose or commit secrets.

### 4. Communication & Evidence

- Match response size to the task; state one clear recommendation and key tradeoff rather than listing rejected options.
- Give brief one-sentence progress updates on key findings, direction changes, or blockers.
- Report only verified facts from inspected files and actual command output; state clearly what changed, what was verified, and what was not.
