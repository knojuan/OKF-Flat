# Linked Knowledge Format Specification

**Version:** 0.1-draft
**Status:** Draft
**Published:** 2026-06-26
**License:** Apache 2.0
**Upstream:** [OKF v0.1](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md)

---

## Part 1: Preamble

### 1.1 Purpose

Linked Knowledge Format (LKF) is an OKF-inspired profile for flat,
agent-navigable knowledge bundles. Domain concept files follow the Open
Knowledge Format (OKF) v0.1 frontmatter and linking conventions. LKF adds
layout constraints, group-index requirements, and optional
profile-specific control files for agent operation.

LKF addresses a structural tension in OKF's otherwise open design:
directory hierarchy implies a primary taxonomy, but the most meaningful
organizational structure in a knowledge bundle is the link graph between
concepts, not the filesystem tree.

LKF resolves this by prohibiting subdirectories entirely. All
organization is expressed through typed group index files and cross-links.
A concept can belong to multiple groups without duplication. An agent can
evaluate whether a group is relevant by reading frontmatter alone, without
loading full file content.

### 1.2 Intended Audience

LKF is for teams and individuals who:

- Are building knowledge bundles that will be consumed by AI agents
- Want explicit, traversable navigation rather than implicit filesystem
  organization
- Are documenting SOPs, business processes, research notes, application
  internals, personal ideas, or other knowledge collections where
  cross-cutting concerns span natural directory boundaries
- Want a deterministic navigation contract they can give to any agent
  without per-bundle instructions

### 1.3 Versioning

LKF versions follow `<major>.<minor>`:
- A minor bump introduces backward-compatible additions.
- A major bump may make breaking changes.

Bundles MAY declare their LKF version in `index.md` frontmatter:

```yaml
lkf_version: "0.1"
```

---

## Part 2: Relationship to OKF

### 2.1 OKF Lineage

LKF adopts the following OKF v0.1 conventions for domain concepts:

- The frontmatter schema (`type`, `title`, `description`, `resource`,
  `tags`, `timestamp`) and its permissive consumption rules
- The cross-linking model (standard relative markdown links)
- Reserved filenames (`index.md`, `log.md`)
- The producer/consumer independence principle
- The requirement that consumers tolerate unknown frontmatter keys,
  unknown type values, and broken cross-links
- The Apache 2.0 license

### 2.2 Compatibility

**LKF domain concept files are OKF-compatible.**

The complete LKF directory may include profile-specific control files
such as `AGENTS.md` and `.lkf-*.md`. Those files are part of LKF, but
they are not domain concepts and are not required to behave like OKF
concept files.

Generic OKF consumers can read LKF concept files using OKF rules.
LKF-aware consumers additionally understand the flat-directory
constraint, group-index requirements, no-orphan rule, and control-file
exemptions.

An OKF bundle is LKF conformant only if it satisfies the LKF
conformance rules in §3.11.

### 2.3 Divergence Table

LKF makes the following targeted changes relative to OKF. They are
motivated by LKF's design commitment to link-graph navigation,
agentic progressive disclosure, and local agent operation.

| # | OKF v0.1 behavior | LKF behavior | Rationale |
|---|---|---|---|
| D1 | Directory structure left to producer | No subdirectories permitted | A filesystem hierarchy implies a primary taxonomy. Concepts that belong to multiple groups can only physically live in one directory, forcing an arbitrary placement. Prohibiting subdirectories makes the link graph the sole organizational structure and removes the implied taxonomy entirely. |
| D2 | `description` optional on all files | `description` required on `index_*.md` files | The four-level progressive disclosure protocol (§3.10) requires agents to evaluate group relevance by reading frontmatter only, without loading full file bodies. A group index without a `description` forces a full body read, defeating the purpose of the frontmatter scan layer. The requirement is scoped narrowly to group index files only — concept file `description` remains optional, consistent with OKF. |
| D3 | Only OKF reserved filenames are defined | `AGENTS.md` and `.lkf-*.md` are reserved as optional LKF control files | Agents need predictable local instructions, maintenance guidance, and audit procedures. Reserving these names keeps operational files separate from domain concepts and allows validators to exempt them from concept reachability rules. |

LKF does not add new required frontmatter fields to domain concept
files and does not restrict domain concept type values.

---

## Part 3: Specification

### 3.1 Bundle Structure

An LKF bundle is a single flat directory of `.md` files. No
subdirectories are used.

```
bundle/
├── index.md                    # Root index. Required. Optional bundle metadata only.
├── index_<grouping>.md         # Group index. One per logical grouping.
├── <concept>.md                # Concept file.
├── log.md                      # Optional. Chronological change history.
├── AGENTS.md                   # Optional. Agent operating guide.
├── .lkf-reference.md           # Optional. Built-in navigation guide.
├── .lkf-maintenance.md         # Optional. Maintenance conventions.
├── .lkf-audit.md               # Optional. Audit procedure.
└── ...
```

The only required file is `index.md`. All other filenames are
author-assigned except as noted in §3.3.

### 3.2 No Subdirectories

An LKF bundle MUST contain no subdirectories. All `.md` files live
at the bundle root. This is a conformance requirement (§3.11).

Tooling that validates LKF bundles MUST flag subdirectories as
conformance errors, not warnings.

Source artifacts referenced by `resource` fields, source sections, or
control files may live outside the bundle and may be referenced by URL,
absolute path, relative path, dashboard identifier, policy identifier, or
other locator. They are not part of LKF conformance unless they are
inside the bundle directory. Because LKF bundles prohibit
subdirectories, local artifact directories should be kept outside the
bundle root.

### 3.3 Reserved Filenames

The following filenames have defined meaning and MUST NOT be used for
concept documents:

| Filename | Purpose |
|---|---|
| `index.md` | Root index. Entry point for the bundle. Required. |
| `index_*.md` | Group index files. The `index_` prefix is reserved. |
| `log.md` | Chronological history of bundle changes. |
| `AGENTS.md` | Agent operating guide. Reserved control file. |
| `.lkf-*.md` | LKF control files. Reserved for bundle operations. |

Files named `index_*.md` MUST be group index files. Files named
`AGENTS.md` or `.lkf-*.md` are control files.

### 3.4 Control Files

Control files are root-level operational documents that describe how agents,
humans, and tools should work with the bundle. They are not domain concepts.
The reserved control files are:

| Filename | Purpose |
|---|---|
| `AGENTS.md` | Agent operating guide. Kept unhidden to match agent discovery conventions. |
| `.lkf-reference.md` | Navigation and structure guide for this bundle. |
| `.lkf-maintenance.md` | Bundle-specific maintenance conventions. |
| `.lkf-audit.md` | Audit procedure for bundle health checks. |
| `.lkf-*.md` | Future LKF control files. |

Control files MAY include frontmatter. They are exempt from group membership
and no-orphan requirements, MUST NOT be required in group indexes, and do
not participate in concept reachability. Consumers SHOULD read relevant
control files before modifying a bundle. Local Markdown links inside
control files MAY be checked for broken-link warnings, but MUST NOT be
used for concept reachability, group membership, or reverse-link analysis.

### 3.5 Concept Files

Every concept is a single `.md` file at the bundle root. A concept can
represent any unit of knowledge: an SOP, policy, metric, source document,
research note, term, decision, compliance requirement, application
component, or idea.

#### 3.5.1 Frontmatter

Every concept file MUST open with a YAML frontmatter block.

```yaml
---
type: <string>          # Required. The kind of concept this file describes.
title: <string>         # Recommended. Human-readable name.
description: <string>   # Recommended. One or two sentence summary.
resource: <uri>         # Optional. Canonical source or supporting artifact.
tags: [<string>, ...]   # Optional. Free-form labels for filtering.
timestamp: <iso8601>    # Optional. Last meaningful update (YYYY-MM-DDTHH:MM:SSZ).
---
```

Only `type` is required. Producers MAY add additional frontmatter keys.
Consumers MUST tolerate unknown keys without rejecting the file.

#### 3.5.2 Body

The markdown body is free-form. Concepts SHOULD include cross-links to
related concepts using standard relative markdown links. Section headings
and body structure are at the producer's discretion.

**Example concept file — `onboarding_checklist.md`:**

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
- [First Week Orientation](first_week_orientation.md) — agenda for the first week
```

### 3.6 Group Index Files

Group index files enumerate the concepts in one logical grouping. They
are concept files with additional structural requirements.

#### 3.6.1 Naming

Group index files are named `index_<grouping>.md`. The grouping label is
author-assigned and should reflect the domain of the concepts within.

#### 3.6.2 Frontmatter

Group index files MUST include a frontmatter block with:
- `type: Index` — required
- `description` — **required** (divergence D2 from OKF; see §2.3)
- `title`, `tags`, `timestamp` — recommended

The `description` MUST answer two questions: what kinds of concepts live
in this group, and what kinds of questions this group can help answer.
This description is the signal agents use in navigation step 2 (§3.10) to
evaluate group relevance without reading the full body.

```yaml
---
type: Index
title: SOPs
description: Standard operating procedures and repeatable workflows. Use
  this group to answer questions about when a process starts, what steps
  to follow, and how exceptions are handled.
tags: [index, sop, process]
timestamp: 2026-06-23T00:00:00Z
---
```

#### 3.6.3 Body

The body of a group index MUST be a list of links to member concepts.
Each entry SHOULD include a short description drawn from the concept's
own `description` frontmatter field.

```markdown
# Metrics

- [New Hire Onboarding Checklist](onboarding_checklist.md) — steps for preparing access, equipment, and first-week orientation
- [Vendor Approval Process](vendor_approval.md) — workflow for reviewing and approving a new vendor
- [Equipment Request SOP](equipment_request.md) — workflow for requesting and issuing equipment
```

#### 3.6.4 Multi-group membership

A concept MAY appear in more than one group index. Both entries point to
the same concept file — no duplication on disk. This is intentional:
a concept that genuinely belongs to two groups is listed in both.

### 3.7 Root Index (`index.md`)

The root index is the required entry point for any LKF bundle.

- It MUST exist at the bundle root.
- It SHOULD NOT contain frontmatter unless the frontmatter is limited to
  bundle-level metadata such as `lkf_version`.
- Frontmatter on `index.md` MUST NOT change the root index navigation
  requirements.
- It MUST link to all group index files.
- It SHOULD include a brief description of the bundle's purpose and scope.

```markdown
# Bundle Title

Short description of what this bundle covers and who it is for.

## Groups

- [SOPs](index_sops.md) — standard operating procedures and workflows
- [Policies](index_policies.md) — rules and requirements that govern decisions
- [Ideas](index_ideas.md) — notes, hypotheses, and exploratory thoughts

## Reference

- [How This Bundle Works](.lkf-reference.md)
```

### 3.8 Cross-Linking

Concepts link to each other using standard relative markdown links.

```markdown
See the [Access Policy](access_policy.md) for access rules.
```

Links MAY be annotated with inline context describing the relationship.
Links SHOULD be bidirectional: if `a.md` links to `b.md`, `b.md` SHOULD
link back to `a.md` unless the relationship is trivially one-directional.

Consumers MUST tolerate broken links. A link whose target does not exist
is not a malformed bundle.

### 3.9 The No-Orphan Rule

Every concept file, excluding control files, MUST be reachable from
`index.md` via at most two hops:

```
index.md → index_<grouping>.md → concept.md    (grouped concept)
index.md → concept.md                           (ungrouped concept)
```

A concept not reachable within two hops is an orphan. Orphans are
invisible to the progressive disclosure protocol and SHOULD be resolved
by adding the concept to an appropriate group index or to the root index
directly.

Control files such as `AGENTS.md` and `.lkf-*.md` are exempt from the
no-orphan rule. They describe how to work with the bundle rather than the
bundle's knowledge domain.

### 3.10 Agent Navigation Protocol

Agents consuming an LKF bundle SHOULD follow this traversal order,
proceeding only as deep as the task requires:

**Level 1 — Read `index.md`**
Understand bundle scope and the names of available groups. This alone
may be sufficient to determine that a bundle is not relevant to the
current task.

**Level 2 — Scan frontmatter of `index_*.md` files**
Read the `title` and `description` fields of group indexes without
reading their bodies. Use these to identify relevant groups and rule out
irrelevant ones. This is a cheap operation — frontmatter only, no body
content loaded.

**Level 3 — Read bodies of relevant group indexes**
For groups identified at level 2, read the full body to get the list of
member concepts and their inline descriptions.

**Level 4 — Read targeted concept files**
Open only the specific concept files identified at level 3, and follow
cross-links within those concepts only as far as the task requires.

Agents MUST NOT assume that reading all files is required. Most tasks
complete at level 3 or 4 without reading the full bundle.

### 3.11 Conformance

#### 3.11.1 A bundle is LKF conformant when:

- All `.md` files are at the bundle root (no subdirectories).
- A root `index.md` exists.
- Every file named `index_*.md` has a frontmatter block with `type: Index`
  and a non-empty `description` field.
- Every concept file has a frontmatter block with at least a `type` field.
- Every concept file, excluding control files, is reachable from `index.md`
  within two hops.

#### 3.11.2 Consumers MUST NOT reject a bundle for:

- Missing optional frontmatter fields on concept files.
- Unknown `type` values.
- Unknown additional frontmatter keys.
- Broken cross-links.
- Concepts appearing in more than one group index.
- Presence of control files without group index entries.

Bootstrap prompts, generators, validators, and repository tooling are not
part of bundle conformance. A conformant bundle may be created manually or
by any tool, and it does not need to preserve or reference the prompt that
created it.

### 3.12 The `log.md` File

An optional `log.md` at the bundle root records changes in append-only
chronological order. It has no frontmatter. Entries use ISO 8601 dates.
New entries are added to the end of the file.

```markdown
# Change Log

## 2026-06-17
- Initial bundle created

## 2026-06-23
- Added `access_policy.md` — access requirements used during onboarding
- Updated `onboarding_checklist.md` — added forward link to [Access Policy](access_policy.md)
```

---

## Part 4: Design Notes

*This section is non-normative. It explains the reasoning behind key
decisions in the spec. Nothing here changes how the spec works.*

### 4.1 Why flat?

OKF's permissive stance on directory structure is correct for a general
format. But for a format optimized around agentic navigation and link-graph
organization, directory hierarchy introduces friction rather than removing
it.

The core problem is that a directory placement implies membership in a
single primary category. The file `sops/onboarding_checklist.md` implies
the onboarding checklist belongs to "SOPs" as its defining characteristic.
But the same concept might equally belong to "People," "Compliance," or
"First Week" depending on the reader's frame. The filesystem forces a
choice that the link graph does not.

By prohibiting subdirectories, LKF removes the implicit taxonomy and
makes all organizational relationships explicit. A concept belongs to
whatever groups its index files say it belongs to. Groups are cheap to
add, free to overlap, and can be restructured without moving files.

### 4.2 Why group indexes instead of directories?

Directories and group indexes solve the same problem — "where do concepts
of type X live?" — but with different properties.

Directories are enforced by the filesystem: a concept must live somewhere.
Group indexes are explicit documents: a concept is listed where it
genuinely belongs. The difference matters most for cross-cutting concepts.
A compliance requirement that applies to an SOP, a policy, and an
application component can appear in three group indexes simultaneously.
In a directory structure, it would have to live in one directory and be
reached through a less natural path from the others.

Group indexes also become first-class knowledge objects. They have
frontmatter, can be queried by type, and carry a `description` that
explicitly states what they contain. A directory name carries none of
this.

### 4.3 Why require `description` on group indexes but not concepts?

The four-level navigation protocol relies on agents being able to evaluate
group relevance at level 2 (frontmatter scan) before committing to a full
body read at level 3. The `description` on a group index is the data that
makes this evaluation possible.

The requirement is scoped to group indexes because they serve a navigation
function that concept files do not. A concept without a `description` is
less discoverable but still accessible via its group index. A group index
without a `description` forces a full body read on every navigation pass,
degrading the protocol's efficiency guarantee.

Requiring `description` on concept files would impose additional burden on
producers for no protocol benefit. OKF deliberately leaves concept
richness to the producer, and LKF adopts that stance everywhere
except where the navigation model specifically depends on it.

### 4.4 Why the two-hop rule?

The two-hop rule formalizes the navigation model into a testable
conformance requirement. Without it, a bundle could be technically valid
— every file has frontmatter, no subdirectories — while still breaking
progressive disclosure by having concepts reachable only through deep
chains of cross-links.

Two hops is the minimum that supports the group index pattern:
`index.md → index_<grouping>.md → concept.md`. It also allows ungrouped
concepts to be linked directly from the root index. More than two hops
would allow group indexes to link to other group indexes rather than
concepts, which would create navigation depth without structure benefit.

### 4.5 Control file exemptions

Control files describe the bundle format, maintenance conventions, audit
procedure, and agent operating guidance. They are useful to readers and
agents, but they are not subject-matter concepts that belong in domain
groups. Making them exempt from the no-orphan rule lets them serve their
operational purpose without requiring producers to create artificial group
index entries for them.
