# Changelog

## 0.6.1 - 2026-11-19

### Fixed

- `speckit.clarify`: a new acceptance-scenario row may hold one action or one event in `When`, as the layout rules already allow. The command text still said one action only.
- `spec-template`: the Lifecycle placeholder diagram pointed at `FR-006`, which the template does not define. It now points at `FR-003`, the template's state table.
- `spec-template`: the comment wrote "state x action". It now writes "state × action", as the rules do.
- `.presetignore`: keeps `.git/` out of `--dev` installs from a cloned repository (Spec Kit 1.2.4 or later). Without it, `specify preset remove` failed on Windows because git object files are read-only.

### Documentation

- README: a section on stacking with other presets, in English, Simplified Chinese, and Japanese.

## 0.6.0 - 2026-11-19

First public release.

### Added

- `spec-template` (replace): the Spec Kit v1.4.2 spec template with an Overview Map section, table-based acceptance scenarios, edge cases, and key entities, and functional requirements with `**Label**:` sub-rules and condition tables.
- `speckit.specify` (append): reads the layout rules before writing `spec.md`. The spec template points to the same rules.
- `speckit.clarify` (append): keeps the layout when clarify writes answers back into `spec.md`.
- `rules/spec-layout.md`: the layout rules shared by both commands.
- README in English, Simplified Chinese, and Japanese.

### Tested

- Spec Kit CLI 1.0.6 and 1.4.2 with the Claude Code integration (skills).
- Install with `--dev` and from a GitHub-style ZIP archive with `--from`, template and command resolution, and removal.
- A real Python project with Spec Kit 1.4.2 (Python scripts): `/speckit-specify`, `/speckit-clarify`, `/speckit-plan`, and `/speckit-tasks` in sequence. The clarify answers were written as table rows and FR sub-rules, and plan and tasks read the tables and sub-rules without extra steps.

### Not supported

- The bundled `lean` preset. Its specify command does not use the spec template.
