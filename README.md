# Spec Review Layout

English | [简体中文](README.zh-CN.md) | [日本語](README.ja.md)

A [Spec Kit](https://github.com/github/spec-kit) preset that lays out `spec.md` for human review. It adds an overview map of Mermaid diagrams, turns acceptance scenarios, edge cases, and key entities into tables, and writes each functional requirement as one line followed by labeled sub-rules and condition tables. What the spec covers does not change.

## What changes in spec.md

| Section | Layout |
|---|---|
| Overview Map (new, before User Scenarios & Testing) | Screen flow, Data sources, Interactions, and Lifecycle diagrams. Only the diagrams that fit the feature are kept, at least one. Diagram labels hold names and IDs (`US1`, `FR-012`) only. The Lifecycle diagram has the same states as the state × action table, and `[*]` start and end markers are allowed. A Screen layout table is added when the feature has a screen. |
| Acceptance scenarios | A table with the columns `# \| Given \| When \| Then \| Refs`. One row is one case. `When` holds one action or one event, such as a network disconnect. Each `•` in `Then` is one outcome to check. |
| Edge Cases | A table with the columns `Case \| Outcome \| Refs`. |
| Functional Requirements | Each FR keeps the `- **FR-###**:` line. Details go in `**Label**:` sub-rules. Condition combinations and state × action tables sit directly under the FR, before its sub-rules. State tables use `→ State`, `— (not allowed)` for a blocked user action, and `— (cannot occur)` for an impossible event. Point at one sub-rule as `FR-012 (Sort)`. |
| Key Entities | A table with the columns `Entity \| One record is \| Attributes \| Used by`. |

Removed scenarios and FRs keep their numbers and read `(removed YYYY-MM-DD: reason)`.

The content follows the language the user asks for. Fixed text (headings, labels, column names, markers, and diagram titles) stays in English as the template gives it. Text in square brackets in the template is a placeholder for content.

## Contents

| File | Type | Strategy | Purpose |
|---|---|---|---|
| `templates/spec-template.md` | template | replace | Spec Kit v1.4.2 spec template with the Overview Map, scenario tables, and structured FRs |
| `commands/speckit.specify.md` | command | append | Reads the layout rules before writing `spec.md`. The template points to the same rules |
| `commands/speckit.clarify.md` | command | append | Keeps the layout when clarify writes answers back, for example adding table rows instead of bullets |
| `rules/spec-layout.md` | rules | — | The layout rules, read by both commands |

The rules are kept in `rules/spec-layout.md` rather than in template comments. Comments are removed from `spec.md` after it is written, so `/speckit.clarify` would not see rules placed only there.

## When to use it

- Specs are reviewed by people, in a team review or a pull request.
- Features have screens, state changes, or many condition combinations.

Do not use it when:

- You rely on extensions that parse Given/When/Then sentences, such as `bdd`, `reqnroll-bdd`, or `test-first-governance`.
- You want upstream changes to `spec-template` to apply automatically. This preset replaces the whole template.

## Requirements

- Spec Kit CLI 1.0.0 or later. Tested with 1.0.6 and 1.4.2.
- A project initialized with `specify init`.
- Tested with the Claude Code integration (skills). The generic integration does not register preset command overrides, so only the template applies there.
- No extension dependencies.

## Install

From the project root:

```bash
specify preset add --from https://github.com/ryukirin/spec-kit-preset-review-layout/archive/refs/tags/v0.6.1.zip
```

To install from a local copy:

```bash
specify preset add --dev <path-to-this-folder>
```

`.presetignore` keeps `.git/` out of the installed copy on Spec Kit 1.2.4 or later. Earlier versions copy `.git/` from a cloned repository, and on Windows `specify preset remove` then fails on read-only git files. With those versions, use the release archive above or a copy of the folder without `.git/`.

## Stacking with other presets

This preset replaces `spec-template` and appends to `speckit.specify` and `speckit.clarify`. Spec Kit resolves each template and command separately. A smaller priority number ranks higher, and presets with the same priority are ordered by ID. A `replace` layer hides every layer below it, while `append` and `wrap` layers stack.

With the default priority (10) for both presets, the results are as follows. The examples come from the community catalog as of 2026-11.

| Other preset | Examples | Result |
|---|---|---|
| Replaces `spec-template` and ranks higher | `mde`, `screenwriting`, `sicario-core` | This preset's template is not used |
| Replaces `spec-template` and ranks lower | `toc-navigation` | The other preset's template is not used |
| Appends to or wraps `spec-template` and ranks higher | `a11y-governance`, `okr-bet`, `security-governance` | Its content is added to this preset's template. Both apply |
| Appends to or wraps `spec-template` and ranks lower | `specassay`, `test-first-governance`, `workflow-preset` | Its content is dropped |
| Replaces `speckit.specify` and ranks higher | `canon-core`, `command-density` | This preset's specify instructions are dropped. The template still points to the layout rules, so the layout applies if that command uses the spec template |
| Replaces `speckit.clarify` and ranks higher | `canon-core`, `claude-ask-questions`, `command-density` | This preset's clarify rules are dropped, and clarify no longer keeps the layout when it writes answers back |

Other presets that append to or wrap `speckit.specify` or `speckit.clarify` stack with this preset in either order.

To change the order, set `--priority` at install time. `specify preset set-priority` does not update command and skill files that are already delivered (github/spec-kit#5087), so remove the preset and add it again with the new priority. When two presets replace the same template or command, only one of them applies, whatever the order.

The bundled `lean` preset is not supported. Its specify command does not use the spec template.

## Check the installation

```bash
specify preset resolve spec-template
```

The output ends with `(top layer from: spec-review-layout v0.6.1)`. With Claude Code, `.claude/skills/speckit-specify/SKILL.md` and `.claude/skills/speckit-clarify/SKILL.md` end with a `## Review Layout (spec-review-layout preset)` section.

## Usage

Run `/speckit.specify` and `/speckit.clarify` as usual (`/speckit-specify` and `/speckit-clarify` in Claude Code). After `/speckit.specify`, `checklists/requirements.md` has a Review Layout section listing the layout checks.

## Known limitations

- The template is replaced as a whole. When Spec Kit changes other sections of `spec-template`, merge them by hand. A section-level strategy is planned upstream (github/spec-kit#5421).
- A project override at `.specify/templates/overrides/spec-template.md` hides this preset's template (github/spec-kit#5337).
- Command overrides are written into the agent's command or skill files at install time. `specify preset disable` does not remove them. Use `specify preset remove`.
- On Windows, when `python3` is the Microsoft Store app execution alias, Spec Kit's `resolve-template.ps1` picks it and fails with `Invalid preset manifest`. This happens with any installed preset. From Spec Kit 1.4, `/speckit.specify` stops when the resolver fails. Turn off the `python3` alias in Settings > Apps > Advanced app settings > App execution aliases.

## Uninstall

```bash
specify preset remove spec-review-layout
```

The spec template and both commands return to the next layer (another preset or Spec Kit core).

## License

MIT
