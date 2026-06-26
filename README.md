# Linked Knowledge Format

**Linked Knowledge Format (LKF)** is an OKF-inspired profile for flat,
agent-navigable knowledge bundles. Domain concept files follow the
[Open Knowledge Format (OKF) v0.1](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md)
frontmatter and linking conventions. LKF adds layout constraints,
group-index requirements, and optional profile-specific control files for
agent operation.

> **Status:** Draft — v0.1-draft
> **License:** Apache 2.0
> **Upstream:** [OKF v0.1](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md) by Google Cloud

---

## Why Flat?

OKF leaves directory structure to the producer. That freedom is useful but
it creates a subtle tension: a directory hierarchy implies a primary taxonomy
that may not reflect how concepts actually relate. A concept that belongs to
three groups can only live in one directory.

LKF resolves this by making the link graph the sole organizational
structure. Concepts live in one flat directory. Grouping is expressed through
typed `index_<grouping>.md` files that enumerate members. A concept can belong
to multiple groups by appearing in multiple index files — no duplication on
disk, no forced taxonomy.

This also enables a precise four-level progressive disclosure protocol for
agentic navigation: agents read only what they need, stopping at the level
of detail the task requires.

---

## What Changes from OKF

LKF makes targeted changes relative to OKF:

| OKF behavior | LKF behavior | Reason |
|---|---|---|
| Directory structure left to producer | No subdirectories permitted | Filesystem hierarchy implies a primary taxonomy; flat structure forces all relationships to be expressed as explicit links |
| `description` optional on all files | `description` required on group index files | Group index descriptions are the frontmatter-scan signal in the four-level navigation protocol; without them agents must read full bodies to evaluate relevance |
| Only OKF reserved filenames are defined | `AGENTS.md` and `.lkf-*.md` are reserved as optional control files | Agents need predictable local instructions, maintenance guidance, and audit procedures without mixing operational files into domain concepts |

LKF domain concept files are OKF-compatible. The complete LKF
directory may also include profile-specific control files that are part of
LKF but are not domain concepts.

---

## Quick Example

```
bundle/
├── index.md                  ← root index, optional bundle metadata only
├── index_sops.md             ← group index, type: Index, description required
├── index_decisions.md        ← group index
├── onboarding_checklist.md   ← concept file
├── approval_policy.md        ← concept file
├── AGENTS.md                 ← agent operating guide
├── .lkf-reference.md         ← built-in navigation guide
├── .lkf-maintenance.md       ← maintenance conventions
├── .lkf-audit.md             ← audit procedure
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

Agents navigate LKF bundles using progressive disclosure:

1. **Read `index.md`** — understand bundle scope and available groups
2. **Scan frontmatter of `index_*.md` files** — read `title` and `description`
   only, no body reads, to identify relevant groups
3. **Read bodies of relevant group indexes** — get member concept lists
4. **Read targeted concept files** — open only what the task requires

Most tasks complete at level 3 or 4 without reading the full bundle.

---

## Using LKF

**Bootstrap with an agent:** Open an agent in the target directory and point
it at one of the prompts in `prompts/`:

- `prompts/init-empty-knowledgebase.md` starts a new knowledge base from
  user-provided purpose, audience, groups, and seed concepts.
- `prompts/convert-directory-to-knowledgebase.md` inventories an existing
  directory, proposes concepts and groups, and creates an approved bundle.

Both prompts create a `knowledge/` directory and copy the control files from
`bundle/`: `AGENTS.md`, `.lkf-reference.md`, `.lkf-maintenance.md`, and
`.lkf-audit.md`.

Bootstrap prompts are convenience tooling, not conformance requirements.
Any manually or automatically created bundle is valid if it satisfies the
spec.

**Manual setup:** Create `index.md`, group indexes, concepts, and `log.md`
following the spec. Copy and adapt the control files from `bundle/` when
agents or maintainers need local operating guidance.

**Repository layout:** `spec/` contains the normative spec, `bundle/`
contains files intended to be copied into a knowledge base, `prompts/`
contains agent bootstrap prompts, and `tools/` contains tool behavior specs.

**Tooling:** See `tools/validate.md` for the conformance validator spec.

---

## Contributing

Issues and pull requests welcome. The spec is at `spec/linked-knowledge-format-spec.md`.
All proposals should include a rationale that explains why the change is
necessary given LKF's design principles, and should update the
divergence table in the spec if the change affects OKF compatibility.
