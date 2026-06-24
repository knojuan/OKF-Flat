# OKF-Flat Conformance Validator Specification

This document specifies the behavior of a conformance validator for
OKF-Flat bundles. It is a prose specification — not an implementation —
sufficient for any developer to build a validator in any language.

---

## Overview

A validator accepts a directory path as input and produces a structured
report of conformance errors and warnings. Errors indicate violations of
normative requirements (`MUST`). Warnings indicate violations of
recommendations (`SHOULD`) that do not affect conformance but reduce
bundle quality.

A bundle with zero errors is OKF-Flat conformant. Warnings do not affect
conformance status.

---

## Input

```
validate <bundle-directory-path>
```

The validator reads all `.md` files in the given directory. It does not
recurse into subdirectories (subdirectory presence is itself an error).

---

## Output

A structured report with two sections: Errors and Warnings. Each item
includes:

- **Rule ID** — a short identifier for the rule violated
- **File** — the file where the violation was found, or `(bundle)` for
  bundle-level violations
- **Description** — a plain-language description of the violation

Exit code `0` if zero errors. Exit code `1` if one or more errors.
Warnings do not affect exit code.

**Example output:**

```
OKF-Flat Conformance Report
Bundle: /path/to/bundle
Status: NON-CONFORMANT (2 errors, 1 warning)

ERRORS
  E-SUBDIR   (bundle)           Subdirectory found: tables/
  E-NODESC   index_metrics.md   Group index missing required description field

WARNINGS
  W-NOTIMESTAMP  mrr.md   Concept missing recommended timestamp field
```

---

## Error Rules

### E-SUBDIR — Subdirectory present

**Trigger:** Any subdirectory exists within the bundle directory.
**Message:** `Subdirectory found: <directory-name>/`
**Note:** Flag every subdirectory found, not just the first.

---

### E-NOINDEX — Root index missing

**Trigger:** No `index.md` file exists at the bundle root.
**Message:** `Required file index.md not found`

---

### E-NOFRONTMATTER — Concept file missing frontmatter

**Trigger:** A `.md` file that is not `index.md`, `log.md`, or
`okf-flat-reference.md` has no YAML frontmatter block (no opening `---`).
**Message:** `Concept file has no frontmatter block`

---

### E-NOTYPE — Concept file missing required type field

**Trigger:** A concept file has a frontmatter block but no `type` field,
or `type` is present but empty.
**Message:** `Concept file missing required frontmatter field: type`

---

### E-INDEXTYPE — Group index missing type: Index

**Trigger:** A file named `index_*.md` has frontmatter but `type` is
not `Index`.
**Message:** `Group index file has type '<value>' — must be 'Index'`

---

### E-INDEXDESC — Group index missing required description

**Trigger:** A file named `index_*.md` has no `description` field in
its frontmatter, or `description` is present but empty.
**Message:** `Group index missing required description field`
**Note:** This is OKF-Flat divergence D2. A non-empty description is a
conformance requirement on group index files, not a recommendation.

---

### E-ORPHAN — Concept not reachable within two hops

**Trigger:** A concept file is not linked from `index.md` directly, and
is not linked from any group index file that is itself linked from
`index.md`. Files exempt from this rule: `okf-flat-reference.md`,
`log.md`.
**Message:** `Concept is not reachable from index.md within two hops`
**Algorithm:**
1. Parse `index.md` and collect all linked `.md` filenames (one-hop set).
2. For each `index_*.md` in the one-hop set, parse its body and collect
   all linked `.md` filenames (two-hop set).
3. Any concept file not in the one-hop set or two-hop set is an orphan.

---

## Warning Rules

### W-NODESCRIPTION — Concept missing recommended description

**Trigger:** A concept file (not a group index) has no `description`
field, or `description` is empty.
**Message:** `Concept missing recommended frontmatter field: description`

---

### W-NOTIMESTAMP — Concept missing recommended timestamp

**Trigger:** A concept file has no `timestamp` field, or `timestamp`
is empty.
**Message:** `Concept missing recommended frontmatter field: timestamp`

---

### W-NOTITLE — Concept missing recommended title

**Trigger:** A concept file has no `title` field, or `title` is empty.
**Message:** `Concept missing recommended frontmatter field: title`

---

### W-BROKENLINK — Cross-link target does not exist

**Trigger:** A markdown link in a concept body points to a `.md` file
that does not exist in the bundle directory.
**Message:** `Broken cross-link: target '<filename>.md' not found`
**Note:** Per the spec, broken links are not conformance errors.
Consumers must tolerate them. This warning helps producers maintain
link integrity without making broken links blocking.

---

### W-NOGROUP — Concept has no group index entry

**Trigger:** A concept file does not appear as a link target in any
`index_*.md` file body.
**Message:** `Concept has no group index entry — unreachable by group navigation`
**Note:** This is a weaker version of E-ORPHAN. It fires for concepts
reachable from `index.md` directly (one-hop) but listed in no group
index, flagging them as invisible to group-based discovery without
triggering an orphan error.

---

### W-NOREVERSE — Cross-link appears to be one-directional

**Trigger:** Concept `a.md` links to `b.md`, but `b.md` does not link
back to `a.md`.
**Message:** `One-way link from <a>.md to <b>.md — consider adding reverse link`
**Note:** This is a heuristic warning. Some one-way links are intentional
(e.g., a reference document linking to concepts without those concepts
linking back to the reference). Producers may suppress this warning for
specific links by adding a comment in the concept body.

---

## Files Exempt from Validation

The following files are present in every bundle but are not subject to
all rules:

| File | Exempt from |
|---|---|
| `index.md` | E-NOFRONTMATTER, E-NOTYPE, E-ORPHAN |
| `log.md` | E-NOFRONTMATTER, E-NOTYPE, E-ORPHAN, all W- rules |
| `okf-flat-reference.md` | E-ORPHAN, W-NOGROUP |

---

## Implementation Notes

- Parse frontmatter by detecting the opening `---` on the first line and
  the closing `---` on a subsequent line. Content between is YAML.
- Parse markdown links using the pattern `[text](target)`. Extract only
  links where target ends in `.md` and contains no `://` (relative links
  only). Ignore external URLs.
- The validator should not attempt to validate frontmatter field values
  beyond presence and non-emptiness. Type vocabulary is producer-defined
  (except `Index` on group indexes).
- Unknown frontmatter keys MUST NOT produce warnings. OKF and OKF-Flat
  both require consumers to tolerate unknown keys.
