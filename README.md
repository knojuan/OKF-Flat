# OKF-Flat

**OKF-Flat** is a profile of the [Open Knowledge Format (OKF) v0.1](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md)
that replaces directory hierarchy with explicit link-based navigation.
Every concept file lives in a single flat directory. All organization is
carried by cross-links and typed index files.

> **Status:** Draft — v0.1.1
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
├── index_metrics.md          ← group index, type: Index, description required
├── index_tables.md           ← group index
├── mrr.md                    ← concept file
├── subscriptions.md          ← concept file
├── okf-flat-reference.md     ← built-in reference, ships in every bundle
└── log.md                    ← change history
```

A group index:

```markdown
---
type: Index
title: Metrics
description: Business and operational metrics. Use this group to answer
  questions about how key figures are calculated, what their sources are,
  and how they relate to each other.
tags: [index, metrics]
timestamp: 2026-06-23T00:00:00Z
---

# Metrics

- [Monthly Recurring Revenue](mrr.md) — recurring subscription revenue normalized to a monthly period
- [Churn Rate](churn_rate.md) — percentage of subscribers lost in a period
```

A concept file:

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
- [Churn Rate](churn_rate.md) — inverse health signal
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

**In an application:** Also copy `spec/okf-flat-reference.md` and create
an `okf-flat-maintenance.md` file describing your application-specific
conventions (concept types, resource path rules, group structure).

**Tooling:** See `tools/validate.md` for the conformance validator spec.
See `tools/audit-prompt.md` for the periodic audit prompt.

---

## Contributing

Issues and pull requests welcome. The spec is at `spec/okf-flat-spec.md`.
All proposals should include a rationale that explains why the change is
necessary given OKF-Flat's design principles, and should update the
divergence table in the spec if the change affects OKF compatibility.
