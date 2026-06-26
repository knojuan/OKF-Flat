# Initialize an Empty Linked Knowledge Format Knowledge Base

Use this prompt from the directory that should receive a new `knowledge/`
Linked Knowledge Format (LKF) bundle. The target may be empty or may contain source artifacts,
but this workflow starts from user intent rather than converting existing
content automatically.

You are creating a new Linked Knowledge Format (LKF) knowledge base.

## Step 1: Confirm target and inputs

Confirm the current working directory is the intended target. Then ask the
user for:

- bundle purpose and scope
- intended audience
- initial source paths or artifacts, if any
- whether Git is available and whether an audit checkpoint should be used
- audit checkpoint name, defaulting to `lkf-audit` when Git is used
- initial group names and short descriptions
- initial seed concepts, if any

Do not create files until the user has answered these setup questions.

## Step 2: Create the bundle

Create a `knowledge/` directory at the target root. Copy these control files
from the LKF repository's `bundle/` directory into `knowledge/`:

- `AGENTS.md`
- `.lkf-reference.md`
- `.lkf-maintenance.md`
- `.lkf-audit.md`

Create these root-level files:

- `index.md`
- `log.md`
- one `index_<grouping>.md` file per approved initial group
- one concept file per approved seed concept

Do not create subdirectories inside `knowledge/`.

## Step 3: Populate required files

Populate `index.md` with:

- the bundle title
- a concise scope description
- links to every `index_<grouping>.md`
- a control-file section linking to `.lkf-reference.md`, `.lkf-maintenance.md`,
  `.lkf-audit.md`, and `AGENTS.md`

Populate each group index with:

- frontmatter with `type: Index`
- `title`
- a `description` that answers what belongs there and what questions it helps
  answer
- links to approved seed concepts that belong in that group

Populate `.lkf-maintenance.md` by replacing setup placeholders with the
chosen purpose, audience, source conventions, and audit checkpoint.

Populate `log.md` with an initial chronological entry.

## Step 4: Validate the result

Check that:

- all domain concept files are at `knowledge/` root
- every `index_*.md` has `type: Index` and non-empty `description`
- every concept has frontmatter with at least `type`
- every concept is reachable from `index.md` within two hops
- control files are not listed as domain concepts in group indexes
- Markdown links use relative paths

Report what was created and list any follow-up concepts or groups the user
may want to add next.
