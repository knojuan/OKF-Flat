---
type: Reference
title: Bundle Maintenance Conventions
description: How this bundle is maintained, how application files are referenced, and what triggers a documentation update. Read this before adding or changing bundle content.
tags: [reference, maintenance, conventions]
timestamp: 2026-06-23T00:00:00Z
---

# Bundle Maintenance Conventions

This file describes how this bundle stays current with the application it
documents. It is written for both humans and agents contributing to the
bundle.

For navigation conventions, see [How This Bundle Works](okf-flat-reference.md).

---

## What This Bundle Documents

[DESCRIBE THE APPLICATION HERE — its purpose, primary components, and the
scope of what this bundle covers. Example: "This bundle documents the
internal API, data models, background jobs, and key business processes for
the Billing Service."]

---

## When to Update the Bundle

Update the bundle whenever you add or change something that other developers
or agents would need to understand to work with this application:

- Adding an endpoint, route, controller action, or handler
- Adding or modifying a data model, schema, or migration
- Adding or changing a background job, worker, or scheduled task
- Adding or modifying an external service integration or API dependency
- Changing a significant business rule, workflow, or process
- Renaming, moving, or removing any of the above

When in doubt, document it.

---

## How to Reference Application Files

The `resource` field in a concept's frontmatter points to the primary
application file the concept describes.

**Rules:**
- Derive file paths from the repo's directory structure directly. Do not
  guess or assume conventions — read the repo.
- Always use paths relative to the repo root. Absolute paths break across
  development environments.
- Use file-level paths only. Do not reference line numbers or method names —
  these change frequently and silently invalidate the reference.
- If a concept spans multiple files, set `resource` to the most important
  one and list the others in an `## Implementation` section in the body.
- Do not set `resource` to a file that does not currently exist.

**Non-obvious path exceptions:**

[Note any paths that cannot be reliably inferred from the directory
structure — generated files that should not be referenced directly, or
service roots in a monorepo that are not at the repo root. Leave blank
if none apply.]

---

## Concept Types Used in This Bundle

The following `type` values are used consistently. Use these when creating
new concept files. Document new types in this table when introduced.

| Type | What it covers |
|---|---|
| `Endpoint` | A single API route or controller action |
| `Model` | A data model and its schema |
| `Job` | A background job or scheduled task |
| `Integration` | An external service dependency |
| `Process` | A multi-step business or operational workflow |
| `Decision` | An architectural decision record (ADR) |
| `Compliance` | A regulatory or internal compliance requirement |
| `Term` | A domain term or definition |
| `Index` | A group index file (reserved) |
| `Reference` | A built-in reference document (reserved) |

---

## Cross-Linking Expectations

Cross-links are the primary organizational structure of this bundle.
Apply them liberally.

A concept SHOULD link to another when:
- One concept's `resource` file imports or calls another concept's file
- One concept feeds data to or depends on the output of another
- Understanding one concept requires understanding another
- Two concepts are frequently used or changed together
- A compliance requirement applies to a concept

Links are bidirectional. If `a.md` links to `b.md`, `b.md` should link
back to `a.md` unless the relationship is trivially obvious in one
direction only.

---

## Group Conventions

Groups are defined by the `index_*.md` files currently in this bundle.
To discover available groups, list files matching that pattern and read
their `title` and `description` frontmatter fields.

Every concept must belong to at least one group. A concept may belong to
more than one group — list it in each applicable group index, pointing to
the same concept file.

Creating a new group is warranted when:
- An existing group would misrepresent a concept by implying it belongs
  to a category it does not genuinely fit
- A cross-cutting relationship exists across concepts in multiple groups
  that no current group makes visible
- A concept is genuinely novel and no existing group captures what it is

When proposing a new group, draft its `description` field first. If you
cannot write a clear description that answers what belongs here and what
questions this group helps answer, the group is not yet well-defined.
Propose it to the team before creating the file.

---

## Log Format

Append an entry to `log.md` whenever you change the bundle:

```markdown
## YYYY-MM-DD
- Added `<filename>.md` — <one line description>
- Updated `<filename>.md` — <what changed and why>
- Added cross-link from `<a>.md` to `<b>.md` — <relationship>
- Updated `index_<grouping>.md` — <what was added or changed>
```

Keep entries factual and brief. The log is a change record, not a
narrative.

---

## Audit Process

This bundle is audited periodically. The audit reviews changes since the
last audit using `git diff okf-audit HEAD`, checks for broken resource
references, missing cross-links, orphaned concepts, and documentation gaps.

The `okf-audit` git tag marks the point of the last completed audit. Do
not move this tag manually — it is advanced by the audit process after
each completed audit.
