## Review Layout (spec-review-layout preset)

This project uses the spec-review-layout preset. Read `.specify/presets/spec-review-layout/rules/spec-layout.md` once before integrating the first accepted answer, and keep that layout in every write.

When applying an answer to the spec, use these placements instead of the matching ones above:

- Functional ambiguity: add or change a `**Label**:` child bullet or a condition-table row under the FR it belongs to. Add a new `- **FR-###**:` line only for a new behavior.
- User interaction or actor distinction: add or change a row in that story's acceptance-scenario table. Keep one case per row and one action or one event per `When`.
- Data shape or entities: add or change a row or an attribute in the Key Entities table.
- Edge case or negative flow: add a row to the Edge Cases table. Do not add bullets below the table.
- When an FR, screen, or state is added, removed, or renamed, update `## Overview Map` in the same write.

Before writing the updated spec back, run the "Review layout check" (section 8 of the rules file) on the sections you changed and fix any failure.

The `## Clarifications` section and its `- Q: <question> → A: <answer>` bullets stay as defined above.
