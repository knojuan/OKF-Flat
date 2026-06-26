## Knowledge Bundle

This project maintains an LKF knowledge bundle at `[BUNDLE_PATH]`.

Before contributing to the bundle, read:

- `[BUNDLE_PATH]/.lkf-reference.md` - navigation and structure conventions
- `[BUNDLE_PATH]/.lkf-maintenance.md` - this bundle's maintenance conventions

### Audit Awareness

Suggest an audit when there is evidence the bundle may be stale or has
unreviewed changes. Do not assume Git is available.

Suggest an audit when any of these are true:

- The user says the bundle, source artifacts, or domain knowledge changed
  since the last review
- You notice changed, new, moved, or deleted bundle files
- You notice changed, new, moved, or deleted source artifacts that concepts
  may cover
- A concept's `resource`, `## Sources`, or `## Implementation` reference is
  broken, stale, or inconsistent with the current source
- A task reveals knowledge that should be captured but is not represented in
  the bundle
- The user asks for a broad review, cleanup, validation, or knowledge-base
  health check

When suggesting an audit, ask:

> "This may have affected the knowledge bundle. Run an audit now, or save it for later?"

If yes, follow the instructions in `[BUNDLE_PATH]/.lkf-audit.md`. If no, proceed
and do not raise the audit question again for the same evidence during this
session.

### Inline Maintenance

Update the bundle as part of any task that creates or changes knowledge
covered by the bundle. Do not defer clear updates to the audit. Consult
`[BUNDLE_PATH]/.lkf-maintenance.md` for what triggers an update and how
to apply one.

## Knowledge Domain Understanding

The knowledge bundle at `[BUNDLE_PATH]` is the first stop for any question
about the domain it covers. It contains pre-reasoned concept files for
procedures, policies, decisions, definitions, metrics, notes, artifacts,
systems, or other domain knowledge, with links to relevant sources where
available.

**Before exploring primary sources:**

1. Navigate the bundle using the four-level protocol in
   `[BUNDLE_PATH]/.lkf-reference.md`. Start at `[BUNDLE_PATH]/index.md`,
   scan group index frontmatter, then read targeted concept files.
2. Use the `resource` field and `## Sources` or `## Implementation` sections
   in concept files to locate specific source artifacts relevant to your task.
3. Follow cross-links between concepts to understand relationships before
   reading additional sources.

**Escalate to direct source exploration when:**

- The bundle has no concept covering the topic you need
- The concept's `resource` points to a source whose current content differs
  from what the concept describes; this is a coverage gap
- You need detail below the level the concept captures
- The task requires understanding material added since the last bundle update

**When you escalate:**

- Read only the sources the bundle directed you to, plus immediate related
  sources needed for the task. Do not scan the full source set unless the
  bundle gave you no starting point.
- If a source reveals something the bundle should capture, flag it as a
  coverage gap and update the bundle as part of the task per the inline
  maintenance obligation above.

**What the bundle cannot replace:**

- Current live system output, compiler errors, test failures, or runtime logs
- Generated artifacts that must be read from disk or their source system
- The exact current state of any source under active development
