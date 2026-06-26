# Changelog

All notable changes to Linked Knowledge Format (LKF) are recorded here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).
Versioning follows `<major>.<minor>-<status>` while the spec is in draft.

---

## v0.1-draft - 2026-06-26

Draft specification in progress. All sections remain subject to change while
status is draft.

### Added
- Added full normative specification in `spec/linked-knowledge-format-spec.md`.
- Added conformance validator behavior specification in `tools/validate.md`.
- Added `bundle/` for copyable knowledge-base control files.
- Added bootstrap prompts for initializing an empty knowledge base and
  converting an existing directory.

### Changed
- Reframed LKF documentation around general knowledge bases by default,
  with application documentation as one supported use case.
- Reframed the OKF relationship as an OKF-inspired profile with
  OKF-compatible domain concepts and profile-specific control files.
- Defined `AGENTS.md` and `.lkf-*.md` as reserved control files.
- Moved bundle-local reference, maintenance, audit, and agent files into
  `bundle/`.
- Clarified that root index frontmatter is only for bundle metadata, source
  artifacts live outside bundle conformance, control-file local links may be
  warning-checked, and bootstrap prompts are not conformance requirements.
- Changed `log.md` guidance to append-only chronological order.
- Updated maintenance, audit, and agent templates to refer to source
  artifacts and coverage gaps instead of app-specific file terminology.
- Made audit guidance Git-aware but not Git-dependent.
- Replaced the maintained concept-type table expectation with guidance to
  prefer existing types and add new types as needed.
- Parameterized the audit Git tag/checkpoint as `[AUDIT_REF]`.
- Expanded the audit prompt with generic latent-concept, new-group, and
  group-split discovery checks.

### Divergences from OKF v0.1
- No subdirectories permitted in a bundle.
- `description` field required on group index files (`index_*.md`).
- `AGENTS.md` and `.lkf-*.md` reserved as optional LKF control files.
