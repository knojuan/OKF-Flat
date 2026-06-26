# Convert an Existing Directory to a Linked Knowledge Format Knowledge Base

Use this prompt from the directory whose existing contents should be
converted into a `knowledge/` Linked Knowledge Format (LKF) bundle. This workflow inventories
the current directory, proposes a knowledge structure, asks for approval,
then creates the bundle.

You are converting existing directory contents into a Linked Knowledge
Format (LKF) knowledge base.

## Step 1: Confirm target and read bundle controls

Confirm the current working directory is the intended target. Ask the user
for:

- knowledge base purpose and scope
- intended audience
- source paths to include or exclude
- whether Git is available and whether an audit checkpoint should be used
- audit checkpoint name, defaulting to `lkf-audit` when Git is used
- any files or directories that should never be documented

Do not create files until the user approves the proposed structure.

## Step 2: Inventory source artifacts

Inspect the approved source paths. Do not inspect generated, dependency,
cache, build, or private directories unless the user explicitly includes
them.

Create an inventory summary with:

- major source areas
- recurring artifact types
- candidate concepts
- candidate group indexes
- source artifacts that appear important but ambiguous
- files that should likely remain undocumented

Use existing README, docs, configuration, notes, policies, source files, or
other high-signal artifacts to infer the domain. Prefer shallow inspection
first; read deeply only when needed to propose accurate concepts.

## Step 3: Propose the initial bundle

Present a proposal before creating files:

- bundle title and scope description
- initial groups with drafted `description` fields
- initial concept files with proposed `type`, `title`, `description`, and
  `resource`
- source artifacts intentionally left uncovered
- any ambiguous choices that need user input

Ask the user to approve, revise, or narrow the proposal.

## Step 4: Create the bundle

After approval, create a `knowledge/` directory at the target root. Copy
these control files from the LKF repository's `bundle/` directory into
`knowledge/`:

- `AGENTS.md`
- `.lkf-reference.md`
- `.lkf-maintenance.md`
- `.lkf-audit.md`

Create these root-level files:

- `index.md`
- `log.md`
- one `index_<grouping>.md` file per approved group
- one concept file per approved concept

Do not create subdirectories inside `knowledge/`.

## Step 5: Populate and cross-link

Populate `index.md` with the bundle title, scope, group links, and control
file links.

Populate group indexes with `type: Index`, title, non-empty description,
and member links.

Populate concepts with frontmatter and concise bodies that summarize the
source artifact or knowledge unit. Use `resource` for the primary source
artifact. Add `## Sources` or `## Implementation` sections when multiple
artifacts matter.

Add cross-links where relationships are clear. Do not invent relationships
that are not supported by source content.

Populate `.lkf-maintenance.md` with the chosen domain, source conventions,
and audit checkpoint.

Append an initial entry to `log.md`.

## Step 6: Validate and report

Check that:

- all domain concept files are at `knowledge/` root
- every `index_*.md` has `type: Index` and non-empty `description`
- every concept has frontmatter with at least `type`
- every concept is reachable from `index.md` within two hops
- control files are not listed as domain concepts in group indexes
- Markdown links use relative paths

Report what was created, what source artifacts remain uncovered, and what
follow-up concepts or groups should be considered next.
