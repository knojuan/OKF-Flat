## Knowledge Bundle

This project maintains an OKF-Flat knowledge bundle at `knowledge/`.

Before contributing to the bundle, read:

- `knowledge/okf-f-reference.md` — navigation and structure conventions
- `knowledge/okf-f-maintenance.md` — this project's maintenance conventions

### Session start

Run `git diff okf-audit HEAD --name-only -- knowledge/` at the start of every session. If the output is non-empty, ask the user:

> "The knowledge bundle has unreviewed changes. Run an audit before we proceed, or save it for later?"

If yes, follow the instructions in `tools/okf-flat_audit.md`. If no, proceed and do not raise the audit question again this session.

### Inline maintenance

Update the bundle as part of any task that creates or changes application knowledge. Do not defer updates to the audit. Consult `knowledge/okf-f-maintenance.md` for what triggers an update and how to apply one.

## Codebase Understanding

The knowledge bundle at `knowledge/` is the first stop for any question about this codebase. It contains pre-reasoned concept files covering architecture, data models, endpoints, decisions, compliance requirements, and operational processes — with direct links to the relevant source files.

**Before exploring code:**

1. Navigate the bundle using the four-level protocol in `knowledge/okf-f-reference.md`. Start at `knowledge/index.md`, scan group index frontmatter, then read targeted concept files.
2. Use the `resource` field and `## Implementation` sections in concept files to locate the specific source files relevant to your task.
3. Follow cross-links between concepts to understand relationships before reading any code.

**Escalate to direct code exploration when:**

- The bundle has no concept covering the component you need
- The concept's `resource` path leads to code whose behavior differs from what the concept describes — this is a documentation gap; note it
- You need implementation detail below the level the concept captures (specific function signatures, internal logic, error handling)
- The task requires understanding code added since the last bundle update

**When you escalate:**

- Read only the files the bundle directed you to, plus their immediate dependencies — do not scan the full codebase unless the bundle gave you no starting point
- If the code reveals something the bundle should capture, flag it as a documentation gap and update the bundle as part of the task per the inline maintenance obligation above

**What the bundle cannot replace:**

- Current compiler errors or test failures — always check live output
- Generated files — read from disk, not from concept descriptions
- The exact current state of a file under active development