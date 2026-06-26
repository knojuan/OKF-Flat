# OKF-Flat

**OKF-Flat** is a profile of the [Open Knowledge Format (OKF) v0.1](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md)
that replaces directory hierarchy with explicit link-based navigation.
Every concept file lives in a single flat directory. All organization is
carried by cross-links and typed index files.

> **Status:** Draft — v0.1.2
> **License:** Apache 2.0
> **Upstream:** [OKF v0.1](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md) by Google Cloud

---

## Why Flat?

OKF leaves directory structure to the producer. That freedom is useful but
it creates a subtle tension: a directory hierarchy implies a primary taxonomy
that may not reflect how concepts actually relate. A concept that belongs to
three groups can only live in one directory.

OKF-Flat resolves this by making the link graph the sole organizational
structure. Concepts live in one flat directory. Grouping is expressed through
typed `index_<grouping>.md` files that enumerate members. A concept can belong
to multiple groups by appearing in multiple index files — no duplication on
disk, no forced taxonomy.

This also enables a precise four-level progressive disclosure protocol for
agentic navigation: agents read only what they need, stopping at the level
of detail the task requires.

---

## What Changes from OKF

OKF-Flat makes two targeted changes to OKF:

| OKF behavior | OKF-Flat behavior | Reason |
|---|---|---|
| Directory structure left to producer | No subdirectories permitted | Filesystem hierarchy implies a primary taxonomy; flat structure forces all relationships to be expressed as explicit links |
| `description` optional on all files | `description` required on group index files | Group index descriptions are the frontmatter-scan signal in the four-level navigation protocol; without them agents must read full bodies to evaluate relevance |

Everything else — frontmatter schema, cross-linking rules, reserved
filenames, permissive consumption rules — is inherited unchanged from OKF.

**An OKF-Flat bundle is a valid OKF bundle.**
An OKF bundle is OKF-Flat conformant only if it uses no subdirectories.

---

## Quick Example

```
bundle/
├── index.md                  ← root index, no frontmatter
├── index_sops.md             ← group index, type: Index, description required
├── index_decisions.md        ← group index
├── onboarding_checklist.md   ← concept file
├── approval_policy.md        ← concept file
├── okf-flat-reference.md     ← built-in reference, ships in every bundle
└── log.md                    ← change history
```

A group index:

```markdown
---
type: Index
title: SOPs
description: Standard operating procedures and repeatable workflows. Use
  this group to answer questions about when a process starts, what steps
  to follow, and how exceptions are handled.
tags: [index, sop, process]
timestamp: 2026-06-23T00:00:00Z
---

# SOPs

- [New Hire Onboarding Checklist](onboarding_checklist.md) — steps for preparing access, equipment, and first-week orientation
- [Vendor Approval Process](vendor_approval.md) — workflow for reviewing and approving a new vendor
```

A concept file:

```markdown
---
type: SOP
title: New Hire Onboarding Checklist
description: Repeatable checklist for preparing a new hire before and during
  their first week.
resource: policy://people/onboarding
tags: [people, onboarding, operations]
timestamp: 2026-06-23T00:00:00Z
---

# Steps

1. Confirm start date and manager.
2. Prepare required account access.
3. Schedule first-week orientation sessions.

## Related

- [Access Policy](access_policy.md) — access rules used during onboarding
- [Equipment Request SOP](equipment_request.md) — hardware request workflow
```

---

## Four-Level Navigation Protocol

Agents navigate OKF-Flat bundles using progressive disclosure:

1. **Read `index.md`** — understand bundle scope and available groups
2. **Scan frontmatter of `index_*.md` files** — read `title` and `description`
   only, no body reads, to identify relevant groups
3. **Read bodies of relevant group indexes** — get member concept lists
4. **Read targeted concept files** — open only what the task requires

Most tasks complete at level 3 or 4 without reading the full bundle.

---

## Using OKF-Flat

**In a new bundle:** Copy `spec/okf-flat-reference.md` into your bundle
directory. Create `index.md`, your group index files, and your concept files
following the conventions in the spec.

**In a maintained knowledge base:** Also copy `tools/okf-flat-maintenance.md`
into the bundle as `okf-flat-maintenance.md` and adapt it to your domain:
SOPs, business operations, research notes, application documentation,
personal ideas, or any other knowledge collection.

**Tooling:** See `tools/validate.md` for the conformance validator spec.
See `tools/audit-prompt.md` for the periodic audit prompt.

---

## Contributing

Issues and pull requests welcome. The spec is at `spec/okf-flat-spec.md`.
All proposals should include a rationale that explains why the change is
necessary given OKF-Flat's design principles, and should update the
divergence table in the spec if the change affects OKF compatibility.
