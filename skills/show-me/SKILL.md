---
name: show-me
description: "Explain the current topic with a concise diagram, code sketch, or focused HTML artifact when a visual makes the relationships clearer."
---

# Visual explanation

Choose the smallest view that explains the user's question:

| Question                              | Useful form                                      |
| ------------------------------------- | ------------------------------------------------ |
| What happens in which order?          | Pseudocode or a sequence diagram                 |
| Who calls or owns what?               | Call tree, component tree, or a shallow file map |
| What changes?                         | A small diff or before/after view                |
| How do choices affect an outcome?     | An interactive visual when the host supports it  |
| What should this interface look like? | A focused HTML artifact or actual UI             |

Use real names and only the relationships needed for the explanation. Keep visuals next to the short prose they support. Label assumptions and uncertainty. Prefer plain notation or Mermaid for small static engineering diagrams; avoid building an HTML page for a single fact.

For an artifact, use the host's supported preview and provide a link. Preserve the user's requested format and the product's visual system when relevant. Do not add a publishing workflow merely to show a local explanation.

For concrete call-tree, component, file-layout, state-transition, and diff examples, read [visual examples](references/visual-examples.md).
