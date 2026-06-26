---
type: Reference
title: How This Bundle Works
description: Operational guide for navigating and contributing to this bundle. Read this first.
tags: [reference, navigation, bundle]
source: https://github.com/YOUR-ORG/okf-flat/blob/main/spec/okf-flat-reference.md
timestamp: 2026-06-26T00:00:00Z
---

# How This Bundle Works

This document describes the structure and conventions of this bundle.
Whether you are a human reading in an editor or an agent processing files,
everything you need to navigate and contribute to this bundle is here.

---

## What This Bundle Is

This bundle is a flat directory of markdown files. Each file represents
one concept - an SOP, policy, metric, source document, research note,
term, decision, application component, or idea. Files link to each other
to express relationships. The directory has no subfolders. All
organization is carried by links and index files.

---

## Start Here: How to Navigate

Navigation always begins at `index.md`. Go only as deep as your task
requires — most tasks do not need the full bundle.

**Level 1 — Read `index.md`**
Understand what this bundle covers and what groups are available. This
alone may tell you the bundle is not relevant to your task.

**Level 2 — Scan frontmatter of group index files**
Find files named `index_<grouping>.md`. Read their `title` and
`description` fields only — do not read their bodies yet. Use these to
identify which groups are relevant and rule out those that are not.
This is a cheap operation: frontmatter only, no full file reads.

**Level 3 — Read the body of relevant group indexes**
For groups identified at level 2, open the full file to get the list
of member concepts and their short descriptions.

**Level 4 — Read specific concept files**
Open only the concept files identified at level 3. Follow cross-links
within them only as far as your task requires.

Stop as soon as you have what you need.

---

## File Types

### Concept Files

The primary unit of knowledge. Each concept file covers one topic.

```
---
type: <what kind of concept this is>
title: <human-readable name>
description: <one or two sentence summary>
resource: <canonical source or supporting artifact, if any>
tags: [<labels for filtering>]
timestamp: <date of last meaningful update>
---

# Body content in free-form markdown

Cross-links to related concepts appear as standard markdown links.
```

Only `type` is required. All other frontmatter fields are optional.

### Group Index Files (`index_<grouping>.md`)

Group indexes enumerate all concepts in one logical grouping. Their
`description` frontmatter field is required — it is the signal used at
level 2 of navigation to decide whether a group is worth exploring
without reading the full body. The description answers two questions:
what kinds of concepts live here, and what kinds of questions this group
can help answer.

A concept may appear in more than one group index — both entries point
to the same file, no duplication on disk.

### The Root Index (`index.md`)

The entry point for the bundle. Links to all group indexes. No
frontmatter. Always read this first.

### The Change Log (`log.md`)

An optional append-only file recording bundle changes in chronological
order. No frontmatter. Read it to understand what has changed recently.

---

## How Concepts Relate

Concepts link to each other using standard markdown links:

```markdown
See the [Access Policy](access_policy.md) for access rules.
```

The link graph is the real structure of this bundle. A concept may be
related to concepts across multiple groups — those relationships live in
the concept body as links, not in directory location. When reading a
concept, follow its links to discover related knowledge.

A link whose target does not exist is not an error. It may represent
knowledge not yet written.

---

## Finding What You Need

**By group:** Read `index.md`, identify the relevant group, open that
group index, scan the member list.

**By tag:** The `tags` field is consistent across the bundle. Scan
frontmatter and filter by tag to narrow candidates.

**By type:** The `type` field identifies the kind of concept. Scan
frontmatter and filter by type to find concepts of a specific category.

**By link:** If you have one relevant concept, follow its cross-links
to reach related concepts without returning to the index.

---

## Contributing

**Adding a concept:**
- Create a single `.md` file at the bundle root. Do not create
  subdirectories.
- Include at minimum a `type` field in the frontmatter.
- Add a description and tags to make the concept discoverable.
- Link to related concepts in the body.
- Add the file to at least one group index. A concept not listed in
  any group index is unreachable by progressive navigation.

**Adding a group:**
- Create an `index_<grouping>.md` file with `type: Index` and a
  non-empty `description` in its frontmatter.
- The description must answer: what belongs here, and what questions
  does this group help answer.
- Add a link to the new group index in `index.md`.

**Updating a concept:**
- Update the `timestamp` field to today's date.
- Record a summary of the change in `log.md`.

**Linking:**
- Use relative links: `[Title](filename.md)`.
- Because the bundle is flat, most internal links should point to files
  in the same directory, such as `[Title](filename.md)` or
  `[Title](./filename.md)`.
- Link in both directions when two concepts reference each other.
- Annotate links with brief context so a reader understands the
  relationship without opening the target file.

---

## About This File

This file is a built-in reference that ships in every OKF-Flat bundle.
It describes how to work with the bundle it lives in, not an external
standard. It is the only file in the bundle exempt from the no-orphan
rule — it does not need to be listed in any group index.

The canonical version of this file is maintained at:
https://github.com/YOUR-ORG/okf-flat/blob/main/spec/okf-flat-reference.md

Replace `YOUR-ORG` with the actual organization name when using this file.
