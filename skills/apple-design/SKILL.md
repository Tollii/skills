---
name: apple-design
description: "Design or review Apple-style interfaces, including product purpose, user control, navigation, gesture motion, materials, typography, and accessibility."
---

# Apple interface design

Use the product's existing platform, design system, and animation tools. The web examples in this skill need adaptation for native UI.

Give immediate press feedback and continuous gesture feedback. Preserve the grab position, current displayed value, and relevant velocity when an interaction changes direction. Keep controls available during transitions. Enter and exit paths should explain the same spatial relationship.

Use springs where physical response and interruption help. Match feedback to the action; avoid adding bounce, haptics, or sound to every interaction. Respect reduced motion, reduced transparency, contrast, and text-size preferences. State changes must remain understandable without motion.

Read only the needed detail:

- [Design foundations and process](references/design-foundations.md): purpose, agency, responsibility, familiarity, flexibility, simplicity, craft, feedback, navigation, and design iteration. Read for overall interface design.
- [Gesture motion and accessibility](references/motion.md): dragging, momentum, interruption, boundaries, and accessible alternatives.
- [Materials and typography](references/materials-and-type.md): depth, translucency, optical sizing, and text hierarchy.

For a general UI consistency review, use `make-interfaces-feel-better` when available. This skill supplies deeper interaction guidance, not a second mandatory review. Check relevant states and gesture reversals in the running interface; identify device or accessibility checks that were not performed.
