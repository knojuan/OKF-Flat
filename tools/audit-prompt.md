# OKF-Flat Bundle Audit Prompt

Use this prompt when running a periodic audit of an OKF-Flat bundle.
The audit is scoped to changes since the last audit tag where possible,
but it may scan bundle frontmatter across the full bundle to identify
candidate groupings.

Replace `[BUNDLE_PATH]` and `[APPLICATION_PATH]` with the actual paths
for your project before using this prompt.

---

## Prompt

```
You are auditing an OKF-Flat knowledge bundle at [BUNDLE_PATH].

Read the following before proceeding:
1. [BUNDLE_PATH]/okf-flat-reference.md
2. [BUNDLE_PATH]/okf-flat-maintenance.md

These files are the authoritative source for bundle conventions. The
steps below govern the audit procedure only.

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
to identify which application files changed over the same period. Exclude
files under [BUNDLE_PATH]. The remaining files are candidates for missing
concept coverage in Step 4.

---

## Step 2: Review changed concept files

For each concept file in the bundle diff:

1. Read the current file.
2. Check `resource` validity: if set, verify the path exists in the
   repo. Flag broken paths.
3. Check `## Implementation` file paths: if present, verify each path
   exists. Flag missing paths.
4. Check cross-links: for each linked concept, verify the reverse link
   exists. Note missing reverse links.
5. Check group membership: confirm the concept appears in at least one
   `index_*.md` file. Flag any concept with no group index entry.

---

## Step 3: Review affected group indexes

For each group index whose members include a changed concept:

1. Read the group index.
2. Check that the member list is current: new concepts that fit this
   group should be listed; changed concepts should have current inline
   descriptions matching their frontmatter `description`.
3. Check the `description` field still answers: what belongs here, and
   what questions does this group help answer. Flag descriptions that no
   longer fit.
4. If the group index has grown large, assess whether its description
   still accurately scopes all listed members. If a meaningful subset of
   members shares a relationship the current description does not capture,
   and that subset would independently meet the conditions for a new
   group, flag it as a split candidate. Apply the same member threshold
   guidance as Step 5b.

---

## Step 4: Check for missing concept coverage

For each changed application file identified in Step 1 outside
[BUNDLE_PATH], check whether a concept file covers it using the `resource`
fields across bundle concept files. Flag changed application files with
no concept coverage as documentation gaps.

Present gaps to the user and ask which to address. Do not create concept
files automatically.

---

## Step 4b: Check for latent concepts and groups inside one concept

A documentation gap is not only an uncovered file. It can also be a
covered file documented at too coarse a grain.

Scan changed concept files, and any concept files covering changed
application paths, for bodies that enumerate several named peer items in
prose where each item has its own source artifact. Common examples include
a directory of sibling configuration files, providers, migrations, schemas,
rules, jobs, endpoints, or generated artifacts.

When such a concept lists three or more peer items each backed by its own
artifact, flag it as:

1. a documentation-depth gap: each item may merit its own concept file.
   Step 4 will not catch this because the parent artifact is already
   covered at the file or directory level.
2. a latent new-group candidate: the would-be concepts, once created,
   form a cluster to evaluate against Step 5b.

Present these alongside the Step 4 gaps. Do not create the per-item
concepts or the group without approval.

---

## Step 5: Check for new cross-link opportunities

For all concept files touched in this audit, identify pairs that should
cross-link but do not. Signals: shared `resource` path prefix, overlapping
`tags`, shared group membership, or direct implementation dependency.

Add cross-links where the relationship is clear. Present ambiguous
candidates to the user before adding them.

---

## Step 5b: Identify candidate new group indexes

Collect all concept files in the bundle, not only those touched in this
audit. Read frontmatter only at first. This step may scan frontmatter
across the full bundle, but full-body reads are limited to candidate
clusters that survive cheap filtering.

Cluster concepts by any of these frontmatter signals:

- a shared `type` field. A group is often defined by what kind of thing
  its members are, so `type` is a first-class clustering signal.
- two or more shared tags.
- a significant shared title noun phrase.
- latent concept clusters identified in Step 4b.

For each cluster, look at how its members are distributed across the
existing group indexes:

- A cluster whose members are predominantly listed in a single existing
  group index is already served. Discard it unless Step 3 identified that
  group as a split candidate.
- A cluster whose members are scattered across two or more existing group
  indexes, with none predominant, is a stronger candidate. Scatter is the
  operational sign that an existing group may be misrepresenting the
  concept category or hiding a cross-cutting navigational surface. Note
  the scatter explicitly when carrying the cluster forward.

Member threshold guidance:

Three confirmed members is a heuristic floor that filters out pair-noise.
A two-concept relationship is usually better expressed as a bidirectional
cross-link than as a new index file. This is not a hard minimum for a
group to exist: the qualitative conditions in the maintenance file can
justify a smaller group when it is a distinct navigational surface or
type. The count gates noise; the qualitative warrant decides.

For each remaining cluster with three or more confirmed members, or with
a smaller but clearly justified navigational warrant:

1. Read the full content of each member concept to verify the shared
   relationship is substantive, not incidental.
2. Evaluate the cluster against the group-creation conditions in
   [BUNDLE_PATH]/okf-flat-maintenance.md. If none applies, discard it.
3. Attempt to draft a two-sentence description that answers: what kinds
   of concepts belong here, and what questions does this group help
   answer? If this description cannot be written clearly, the cluster is
   not yet well-defined. Do not propose it.
4. If the cluster passes steps 2 and 3, add it to the Step 6 report as a
   candidate new group, including the drafted description, member count,
   current group spread, and qualifying rationale.

Do not create new index files without approval.

---

## Step 6: Produce the audit report

Before making any changes, present:

  ## Audit Report

  ### Broken resource paths
  <list, or "None">

  ### Missing reverse links
  <list, or "None">

  ### Orphaned concepts
  <concepts not reachable from index.md within two hops, or "None">

  ### Group index issues
  <stale descriptions or missing members, or "None">

  ### Documentation gaps
  <changed application files with no concept coverage, or "None">

  ### Latent concepts (depth gaps)
  <concepts that enumerate three or more peer items each backed by their
  own artifact, per Step 4b, or "None">

  ### Suggested new cross-links
  <candidate concept pairs, or "None">

  ### Proposed new groups
  First, include a "clusters considered" table. Include every cluster
  evaluated in Step 5b or Step 4b so a "None" verdict shows its work:

  | Cluster signal | Members | Current group spread | Kept or discarded + why |
  |---|---|---|---|

  Then, for each kept cluster, include: candidate name, drafted
  description, member count, current group spread, and which maintenance
  condition applies. A "None" verdict is valid only when the table above
  is present and every row is discarded.

  ### Group split candidates
  <existing group name, proposed split, drafted description for each
  resulting group, and confirmed member count on each side, or "None">

Ask: "Should I apply all of these, or would you like to review them
individually?"

---

## Step 7: Apply approved changes

For each approved change:
- Update the relevant concept file or group index
- Update `timestamp` on any modified file
- Append to [BUNDLE_PATH]/log.md

Then append a single audit summary entry to [BUNDLE_PATH]/log.md:

  ## YYYY-MM-DD - Audit
  - Audit completed covering changes since <last audit date>
  - <N> resource paths verified, <N> broken references fixed
  - <N> cross-links added
  - <N> group index descriptions updated
  - <N> documentation gaps identified, <N> addressed
  - <N> latent concept clusters identified, <N> addressed
  - <N> new groups proposed, <N> created
  - <N> group splits proposed, <N> applied

---

## Step 8: Advance the audit tag

After [BUNDLE_PATH]/log.md is committed, run:

  git tag -f okf-audit
  git push origin okf-audit --force

Confirm with the user before pushing if their workflow requires it.
```
