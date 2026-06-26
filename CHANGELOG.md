# Changelog

All notable changes to OKF-Flat are recorded here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).
Versioning follows `<major>.<minor>.<patch>-<status>`.

---

## v0.1.2-draft - 2026-06-26

Draft documentation update.

### Changed
- Reframed OKF-Flat documentation around general knowledge bases by default,
  with application documentation as one supported use case.
- Updated maintenance, audit, and agent templates to refer to source
  artifacts and coverage gaps instead of app-specific file terminology.
- Made audit guidance Git-aware but not Git-dependent.
- Replaced the maintained concept-type table expectation with guidance to
  prefer existing types and add new types as needed.
- Parameterized the audit Git tag/checkpoint as `[AUDIT_REF]`.

---

## v0.1.1-draft - 2026-06-26

Draft specification update.

### Changed
- Changed `log.md` guidance to append-only chronological order.
- Expanded the audit prompt with generic latent-concept, new-group, and
  group-split discovery checks.

---

## v0.1.0-draft - 2026-06-23

Initial draft specification.

### Added
- Full normative specification (`spec/okf-flat-spec.md`)
- Built-in bundle reference file (`spec/okf-flat-reference.md`)
- Audit prompt template (`tools/audit-prompt.md`)
- Bundle maintenance template (`tools/okf-flat-maintenance.md`)
- Conformance validator specification (`tools/validate.md`)

### Divergences from OKF v0.1
- No subdirectories permitted in a bundle
- `description` field required on group index files (`index_*.md`)

All sections subject to change while status is draft.
