# OKF-Flat Bundle Audit Prompt

Use this prompt when running a periodic audit of an OKF-Flat bundle.
The audit is scoped to changes since the last audit tag — it does not
require reading the entire bundle on every run.

Replace `[BUNDLE_PATH]` and `[APPLICATION_PATH]` with the actual paths
for your project before using this prompt.

---

## Prompt

```
You are auditing an OKF-Flat knowledge bundle at [BUNDLE_PATH].

Read the following before proceeding:
1. [BUNDLE_PATH]/okf-flat-reference.md
2. [BUNDLE_PATH]/okf-flat-maintenance.md

---

## Step 1: Establish the delta

Run:
  git diff okf-audit HEAD -- [BUNDLE_PATH]

This produces the full diff of every bundle file changed since the last
audit. If the command fails because the okf-audit tag does not exist,
run:
  git log --oneline -- [BUNDLE_PATH]
and treat the entire bundle as the delta.

Also run:
  git diff okf-audit HEAD --name-only -- [APPLICATION_PATH]
to identify which application files changed over the same period.

---

## Step 2: Review changed concept files

For each concept file in the bundle diff:

1. Read the current file.
2. Check resource validity: if a resource path is set, verify the file
   exists in the repo at that path. Flag broken paths.
3. Check Implementation section links: if the body lists file paths in
   an Implementation section, verify each path exists. Flag missing paths.
4. Review cross-links: for each linked concept, check whether the reverse
   link exists. Note missing reverse links.
5. Check group membership: confirm the concept appears in at least one
   group index. Flag any concept with no group index entry.

---

## Step 3: Review affected group indexes

For each group index whose members include a changed concept:

1. Read the group index.
2. Check that the member list is current — new concepts that fit this
   group should be listed; changed concepts should have current
   inline descriptions.
3. Check the group description: if membership changed substantially,
   assess whether the description still answers what belongs here and
   what questions this group can answer.

---

## Step 4: Check for missing concept coverage

Review the list of changed application files from Step 1. For each
changed application file, check whether a concept file covers it using
the resource fields across bundle concept files. Flag changed application
files with no concept coverage as documentation gaps.

Present gaps to the user and ask which to address. Do not create concept
files automatically.

---

## Step 5: Check for new cross-link opportunities

For all concept files touched in this audit, identify pairs that should
cross-link but do not. Signals: shared resource path prefix, overlapping
tags, shared group membership.

Add cross-links where the relationship is clear. Present ambiguous
candidates to the user.

---

## Step 6: Produce the audit report

Before making any changes, present:

  ## Audit Report

  ### Broken resource paths
  <list, or "None">

  ### Missing reverse links
  <list, or "None">

  ### Orphaned concepts
  <list of concepts not reachable from index.md within two hops, or "None">

  ### Group index issues
  <stale descriptions or missing members, or "None">

  ### Documentation gaps
  <changed application files with no concept coverage, or "None">

  ### Suggested new cross-links
  <candidate concept pairs, or "None">

Ask: "Should I apply all of these, or would you like to review them
individually?"

---

## Step 7: Apply approved changes

For each approved change:
- Update the relevant concept file or group index
- Update timestamp on any modified file
- Append to log.md

Then append a single audit summary entry to log.md:

  ## YYYY-MM-DD — Audit
  - Audit completed covering changes since <last audit date>
  - <N> resource paths verified, <N> broken references fixed
  - <N> cross-links added
  - <N> group index descriptions updated
  - <N> documentation gaps identified, <N> addressed

---

## Step 8: Advance the audit tag

After log.md is committed, run:

  git tag -f okf-audit
  git push origin okf-audit --force

Confirm with the user before running the push if their workflow requires
it.
```
