# Spec Review Layout Rules

These rules decide how `spec.md` is laid out so that a reviewer can read it quickly. They do not change what the spec covers: it still describes WHAT and WHY, not HOW.

Apply them whenever `spec.md` is written or edited, by `/speckit.specify`, `/speckit.clarify`, or by hand. Where they conflict with generic formatting guidance in a command (for example "add a bullet under Edge Cases"), follow these rules.

## 1. General

- Keep every `##` and `###` heading text exactly as in the template. Tools find sections by heading text.
- Write the content in the language the user asks for. Keep all fixed text in English, exactly as the template and this file give it, whatever the content language:
  - headings and the H1 prefix `# Feature Specification:`
  - bold labels, diagram titles, and the `Note:` prefix
  - table column names
  - markers such as `— (cannot occur)` and `(removed YYYY-MM-DD: reason)`
  - IDs such as `US1`, `FR-012`, `SC-003`, and the `[NEEDS CLARIFICATION: …]` marker
- Text in square brackets in the template, such as `[Label]`, `[Condition]`, or `[Screen]`, is a placeholder for content. Replace it with text in the content language.
- Remove every HTML comment from `spec.md` once the sections are written.
- Use one name for one thing across the whole spec, including diagram labels and table cells.

## 2. Overview Map

- Place `## Overview Map` directly before `## User Scenarios & Testing`.
- Fill the Overview Map after the user stories and functional requirements are drafted, so that every ID in a diagram exists in the body.
- Draw diagrams in Mermaid. Do not embed images.
- Keep the diagrams that fit the feature and delete the others. Keep at least one.

  | Diagram | Keep when |
  |---|---|
  | Screen flow (`flowchart LR`) | The feature has screens or pages |
  | Data sources (`flowchart LR`) | The feature reads or writes stored data |
  | Interactions (`sequenceDiagram`) | Actors or systems exchange requests and responses |
  | Lifecycle (`stateDiagram-v2`) | An entity moves between states |

- Labels carry names and IDs only (`US1`, `FR-012`). Put conditions, values, and messages in the Functional Requirements, not in the diagram.
- Give each edge that stands for a behavior the FR ID that defines it. Give each screen node the user stories that use it.
- Use the same names as the body text. Use business names for data, not table names, endpoints, or products.
- When the spec has a state × action table (section 6), draw the Lifecycle diagram with exactly the same states under the same names, and one transition for each table cell that holds a resulting state.
  - The start and end markers `[*]` may be drawn. They are not states, and their transitions need no cell in the table.
- Mermaid syntax: wrap flowchart labels in double quotes, break lines with `<br>`, and do not put `{` or `}` inside flowchart label text.
- Add the `Screen layout` table only when the feature has a screen. One row per region, top to bottom. List the fields and buttons in the region, and the FRs that define them.
- Do not put diagrams inside User Story sections.
- When an FR, screen, or state is added, removed, or renamed, update the map in the same edit.

## 3. Tables

- One row holds one thing: one case, one entity, one condition combination.
- To list several items in one cell, separate them with `<br>` and start each with `• `.
- Leave no cell empty. Write `— (cannot occur)` for an impossible combination and `—` for "not applicable".
- Do not put a literal `|` inside a cell. Rephrase or write `\|`.
- A `Refs` cell holds IDs only (`FR-012`, `US2`, `SC-003`). Write the reasons elsewhere.

## 4. Acceptance scenarios

- Do not add explanatory notes to the spec about how to read diagrams or tables.
- Write each story's scenarios as a table with these columns, in this order: `# | Given | When | Then | Refs`.
- One row is one case. Do not merge cases with "or" or "etc.".
- `Given`: one declarative statement of the starting state ("The list has one approved row and one pending row").
- `When`: exactly one action by an actor, or exactly one event that is not a user action (the network disconnects, a timer expires, an external service returns an error). Do not combine an action and an event in one cell.
- `Then`: one checkable outcome per `•`, with concrete values. Quote on-screen messages exactly.
- Do not put a second action or event in `Then`. Split it into rows such as `5a` and `5b`.
- Mark example values as examples. For values that depend on the current date, give the rule and an example: "one year before today (e.g. 2025-05-15)".
- Put sample test data in a separate `Example data US<n>-<m>` table, only when a scenario needs it.
- Put notes on how to verify below the table, starting with `Note:`.
- To remove a scenario, keep its row and number. Write `(removed YYYY-MM-DD: reason)` in `Given` and `—` in the other cells.
- Write `Independent Test` as three bullets: `Setup`, `Action`, `Check`.

## 5. Edge cases

- Write edge cases as a table with these columns, in this order: `Case | Outcome | Refs`.
- Add new edge cases as table rows, never as bullets below the table.
- State the outcome with values or exact messages.

## 6. Functional requirements

- Keep the line form `- **FR-###**: ...`. Never renumber. Tools find FRs by this text.
- The FR line says what happens in one or two sentences.
- Put details as child bullets in the form `  - **Label**: rule`. One rule per bullet.
- When the same kind of rule appears under several FRs, use the same labels in the same order.
- To point at one child rule from elsewhere, write the FR ID and its label: `FR-012 (Sort)`.
- When the result depends on a combination of conditions (judgment, permission, handling per input value), put a table directly under the FR, indented by two spaces. List every combination. Do not add a `Refs` column: the FR itself is the requirement.
- When a feature changes the state of something, put a state × action table under the FR that owns the states. Columns can be user actions or events. Each cell holds one of these:
  - the resulting state, such as `→ Approved`
  - `— (not allowed)` when a user action is blocked in that state
  - `— (cannot occur)` when an event cannot happen in that state
  - The table is the requirement. Do not restate it in prose.
  - Do not add a second summary table elsewhere when FRs per action already exist.
- When an FR has both a table and child bullets, put the table first, directly under the FR line, and the child bullets after the table.
- When there are many FRs, group them under `#### Category` headings. Do not write ID ranges in these headings.
- To remove an FR, keep its line and write `(removed YYYY-MM-DD: reason)`.

## 7. Key entities

- Write key entities as a table with these columns, in this order: `Entity | One record is | Attributes | Used by`.
- List one attribute per line in `Attributes`. Do not write data types.
- In `Used by`, list the FRs that read or write the entity.

## 8. Review layout check

After writing or editing `spec.md`, check each item below and fix any failure before reporting.

When `checklists/requirements.md` is created, add a `## Review Layout` section that lists each item below as a checkbox, and check them during Specification Quality Validation. Keep the item text in English and write the notes in the language the user asked for.

- `## Overview Map` exists before `## User Scenarios & Testing`, with at least one Mermaid diagram.
- Diagram labels contain names and IDs only, and every ID in a diagram exists in the body.
- Acceptance scenarios, edge cases, and key entities are tables. No table cell is empty.
- Every `When` cell holds one action or one event. No `Then` cell contains a second action or event.
- Tables under FRs have no `Refs` column. Where an FR has both, its table comes before its child bullets.
- The Lifecycle diagram has the same states and transitions as the state × action table, not counting `[*]` markers.
- Fixed text is in English exactly as in the template. The content is in the language the user asked for.
- Every FR line keeps the `- **FR-###**:` form. Child rules use `**Label**:` bullets.
- No HTML comments remain.
