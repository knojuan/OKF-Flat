# OKF-Flat Specification

**Version:** 0.1.0-draft
**Status:** Draft
**Published:** 2026-06-23
**License:** Apache 2.0
**Upstream:** [OKF v0.1](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md)

---

## Part 1: Preamble

### 1.1 Purpose

OKF-Flat is a profile of the Open Knowledge Format (OKF) v0.1 optimized
for two specific use cases: link-graph navigation and agentic progressive
disclosure. It addresses a structural tension in OKF's otherwise open
design — directory hierarchy implies a primary taxonomy, but the most
meaningful organizational structure in a knowledge bundle is the link
graph between concepts, not the filesystem tree.

OKF-Flat resolves this by prohibiting subdirectories entirely. All
organization is expressed through typed group index files and cross-links.
A concept can belong to multiple groups without duplication. An agent can
evaluate whether a group is relevant by reading frontmatter alone, without
loading full file content.

### 1.2 Intended Audience

OKF-Flat is for teams and individuals who:

- Are building knowledge bundles that will be consumed by AI agents
- Want explicit, traversable navigation rather than implicit filesystem
  organization
- Are documenting application codebases where cross-cutting concerns span
  natural directory boundaries
- Want a deterministic navigation contract they can give to any agent
  without per-bundle instructions

### 1.3 Versioning

OKF-Flat versions follow `<major>.<minor>`:
- A minor bump introduces backward-compatible additions.
- A major bump may make breaking changes.

Bundles MAY declare their OKF-Flat version in `index.md` frontmatter:

```yaml
okf_flat_version: "0.1"
```

---

## Part 2: Relationship to OKF

### 2.1 Inheritance

OKF-Flat inherits the following from OKF v0.1 unchanged:

- The frontmatter schema (`type`, `title`, `description`, `resource`,
  `tags`, `timestamp`) and its permissive consumption rules
- The cross-linking model (standard relative markdown links)
- Reserved filenames (`index.md`, `log.md`)
- The producer/consumer independence principle
- The requirement that consumers tolerate unknown frontmatter keys,
  unknown type values, and broken cross-links
- The Apache 2.0 license

### 2.2 Compatibility

**An OKF-Flat bundle is a valid OKF bundle.**

Any tool, agent, or system that can consume OKF can consume OKF-Flat
without modification.

An OKF bundle is OKF-Flat conformant only if it uses no subdirectories
and all group index files carry a non-empty `description` field.

### 2.3 Divergence Table

OKF-Flat makes two targeted changes to OKF. Both are motivated by
OKF-Flat's specific design commitment to link-graph navigation and
agentic progressive disclosure.

| # | OKF v0.1 behavior | OKF-Flat behavior | Rationale |
|---|---|---|---|
| D1 | Directory structure left to producer | No subdirectories permitted | A filesystem hierarchy implies a primary taxonomy. Concepts that belong to multiple groups can only physically live in one directory, forcing an arbitrary placement. Prohibiting subdirectories makes the link graph the sole organizational structure and removes the implied taxonomy entirely. |
| D2 | `description` optional on all files | `description` required on `index_*.md` files | The four-level progressive disclosure protocol (§9) requires agents to evaluate group relevance by reading frontmatter only, without loading full file bodies. A group index without a `description` forces a full body read, defeating the purpose of the frontmatter scan layer. The requirement is scoped narrowly to group index files only — concept file `description` remains optional, consistent with OKF. |

No other changes are made. OKF-Flat does not add new required frontmatter
fields to concept files, does not restrict type values, and does not add
new reserved filenames beyond those inherited from OKF.

---

## Part 3: Specification

### 3.1 Bundle Structure

An OKF-Flat bundle is a single flat directory of `.md` files. No
subdirectories are used.

```
bundle/
├── index.md                    # Root index. Required. No frontmatter.
├── index_<grouping>.md         # Group index. One per logical grouping.
├── <concept>.md                # Concept file.
├── log.md                      # Optional. Chronological change history.
├── okf-flat-reference.md       # Recommended. Built-in navigation guide.
└── ...
```

The only required file is `index.md`. All other filenames are
author-assigned except as noted in §3.3.

### 3.2 No Subdirectories

An OKF-Flat bundle MUST contain no subdirectories. All `.md` files live
at the bundle root. This is a conformance requirement (§3.10).

Tooling that validates OKF-Flat bundles MUST flag subdirectories as
conformance errors, not warnings.

### 3.3 Reserved Filenames

The following filenames have defined meaning and MUST NOT be used for
concept documents:

| Filename | Purpose |
|---|---|
| `index.md` | Root index. Entry point for the bundle. Required. |
| `index_*.md` | Group index files. The `index_` prefix is reserved. |
| `log.md` | Chronological history of bundle changes. |

Files named `index_*.md` MUST be group index files as described in §3.5.

### 3.4 Concept Files

Every concept is a single `.md` file at the bundle root. A concept can
represent any unit of knowledge: a table, metric, API, runbook, process,
term, decision, compliance requirement, or idea.

#### 3.4.1 Frontmatter

Every concept file MUST open with a YAML frontmatter block.

```yaml
---
type: <string>          # Required. The kind of concept this file describes.
title: <string>         # Recommended. Human-readable name.
description: <string>   # Recommended. One or two sentence summary.
resource: <uri>         # Optional. Canonical source (URL, repo path, etc).
tags: [<string>, ...]   # Optional. Free-form labels for filtering.
timestamp: <iso8601>    # Optional. Last meaningful update (YYYY-MM-DDTHH:MM:SSZ).
---
```

Only `type` is required. Producers MAY add additional frontmatter keys.
Consumers MUST tolerate unknown keys without rejecting the file.

#### 3.4.2 Body

The markdown body is free-form. Concepts SHOULD include cross-links to
related concepts using standard relative markdown links. Section headings
and body structure are at the producer's discretion.

**Example concept file — `mrr.md`:**

```markdown
---
type: Metric
title: Monthly Recurring Revenue
description: Recurring subscription revenue normalized to a monthly period.
resource: dashboard://revenue/mrr
tags: [revenue, saas, finance]
timestamp: 2026-06-23T00:00:00Z
---

# Calculation

MRR is the sum of all active subscription amounts normalized to one month.

## Related

- [Subscriptions table](subscriptions.md) — source data
- [ARR](arr.md) — annual equivalent
- [Revenue Review Playbook](playbook_revenue_review.md) — how this is used
```

### 3.5 Group Index Files

Group index files enumerate the concepts in one logical grouping. They
are concept files with additional structural requirements.

#### 3.5.1 Naming

Group index files are named `index_<grouping>.md`. The grouping label is
author-assigned and should reflect the domain of the concepts within.

#### 3.5.2 Frontmatter

Group index files MUST include a frontmatter block with:
- `type: Index` — required
- `description` — **required** (divergence D2 from OKF; see §2.3)
- `title`, `tags`, `timestamp` — recommended

The `description` MUST answer two questions: what kinds of concepts live
in this group, and what kinds of questions this group can help answer.
This description is the signal agents use in navigation step 2 (§3.9) to
evaluate group relevance without reading the full body.

```yaml
---
type: Index
title: Metrics
description: Business and operational metrics. Use this group to answer
  questions about how key figures are calculated, what their sources are,
  and how they relate to each other.
tags: [index, metrics]
timestamp: 2026-06-23T00:00:00Z
---
```

#### 3.5.3 Body

The body of a group index MUST be a list of links to member concepts.
Each entry SHOULD include a short description drawn from the concept's
own `description` frontmatter field.

```markdown
# Metrics

- [Monthly Recurring Revenue](mrr.md) — recurring subscription revenue normalized to a monthly period
- [Annual Recurring Revenue](arr.md) — annualized equivalent of MRR
- [Churn Rate](churn_rate.md) — percentage of subscribers lost in a period
```

#### 3.5.4 Multi-group membership

A concept MAY appear in more than one group index. Both entries point to
the same concept file — no duplication on disk. This is intentional:
a concept that genuinely belongs to two groups is listed in both.

### 3.6 Root Index (`index.md`)

The root index is the required entry point for any OKF-Flat bundle.

- It MUST exist at the bundle root.
- It MUST NOT contain frontmatter. (Bundles MAY include `okf_flat_version`
  in a frontmatter block as an exception to this rule.)
- It MUST link to all group index files.
- It SHOULD include a brief description of the bundle's purpose and scope.

```markdown
# Bundle Title

Short description of what this bundle covers and who it is for.

## Groups

- [Metrics](index_metrics.md) — business and operational metrics
- [Tables](index_tables.md) — database tables and views
- [Runbooks](index_runbooks.md) — operational procedures

## Reference

- [How This Bundle Works](okf-flat-reference.md)
```

### 3.7 Cross-Linking

Concepts link to each other using standard relative markdown links.

```markdown
See the [Subscriptions table](subscriptions.md) for source data.
```

Links MAY be annotated with inline context describing the relationship.
Links SHOULD be bidirectional: if `a.md` links to `b.md`, `b.md` SHOULD
link back to `a.md` unless the relationship is trivially one-directional.

Consumers MUST tolerate broken links. A link whose target does not exist
is not a malformed bundle.

### 3.8 The No-Orphan Rule

Every concept file (excluding `okf-flat-reference.md`) MUST be reachable
from `index.md` via at most two hops:

```
index.md → index_<grouping>.md → concept.md    (grouped concept)
index.md → concept.md                           (ungrouped concept)
```

A concept not reachable within two hops is an orphan. Orphans are
invisible to the progressive disclosure protocol and SHOULD be resolved
by adding the concept to an appropriate group index or to the root index
directly.

`okf-flat-reference.md` is exempt from the no-orphan rule. It is a
built-in reference that describes the bundle format itself rather than
the bundle's knowledge domain.

### 3.9 Agent Navigation Protocol

Agents consuming an OKF-Flat bundle SHOULD follow this traversal order,
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

### 3.10 Conformance

#### 3.10.1 A bundle is OKF-Flat conformant when:

- All `.md` files are at the bundle root (no subdirectories).
- A root `index.md` exists.
- Every file named `index_*.md` has a frontmatter block with `type: Index`
  and a non-empty `description` field.
- Every concept file has a frontmatter block with at least a `type` field.
- Every concept file (except `okf-flat-reference.md`) is reachable from
  `index.md` within two hops.

#### 3.10.2 Consumers MUST NOT reject a bundle for:

- Missing optional frontmatter fields on concept files.
- Unknown `type` values.
- Unknown additional frontmatter keys.
- Broken cross-links.
- Concepts appearing in more than one group index.
- Presence of `okf-flat-reference.md` without a group index entry.

### 3.11 The `log.md` File

An optional `log.md` at the bundle root records changes in reverse
chronological order. It has no frontmatter. Entries use ISO 8601 dates.

```markdown
# Change Log

## 2026-06-23
- Added `churn_rate.md` — subscriber loss rate metric
- Updated `mrr.md` — added forward to churn_rate.md

## 2026-06-17
- Initial bundle created
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
single primary category. The file `metrics/mrr.md` implies MRR belongs
to "metrics" as its defining characteristic. But MRR might equally belong
to "revenue," "saas-kpis," or "billing" depending on the reader's frame.
The filesystem forces a choice that the link graph does not.

By prohibiting subdirectories, OKF-Flat removes the implicit taxonomy and
makes all organizational relationships explicit. A concept belongs to
whatever groups its index files say it belongs to. Groups are cheap to
add, free to overlap, and can be restructured without moving files.

### 4.2 Why group indexes instead of directories?

Directories and group indexes solve the same problem — "where do concepts
of type X live?" — but with different properties.

Directories are enforced by the filesystem: a concept must live somewhere.
Group indexes are explicit documents: a concept is listed where it
genuinely belongs. The difference matters most for cross-cutting concepts.
A compliance requirement that applies to an endpoint, a model, and a job
can appear in three group indexes simultaneously. In a directory structure,
it would have to live in one directory and be reached through a less
natural path from the others.

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
producers for no protocol benefit — OKF deliberately leaves concept
richness to the producer, and OKF-Flat inherits that stance everywhere
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

### 4.5 The `okf-flat-reference.md` exemption

The reference file describes the bundle format itself, not the bundle's
knowledge domain. It is a meta-document — useful to any reader who wants
to understand how to navigate the bundle, but not a subject-matter concept
that belongs in any group. Making it exempt from the no-orphan rule lets
it serve its purpose without requiring producers to create an artificial
group index entry for it.
