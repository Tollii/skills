# Assessment modes and handoff detail

Choose the mode requested by the user. No mode requires a fixed model, agent count, or finding quota.

| Mode                                   | Scope and output                                                                                                             |
| -------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| Quick                                  | Changed or high-risk paths; strongest confirmed findings and explicit coverage limits.                                       |
| Standard                               | Relevant packages, weighted toward active code and consequential behavior.                                                   |
| Deep                                   | Broad requested scope, with evidence and coverage recorded by area.                                                          |
| Focus, such as security or performance | Relevant category in the audit reference plus callers and supporting contracts.                                              |
| Branch                                 | Compare with the intended base; inspect changed files and affected callers. Separate introduced findings from existing debt. |
| Next / features / roadmap              | Ground optional product directions in user intent and source evidence. Explain tradeoffs separately from defects.            |
| Plan a named change                    | Skip a general audit. Investigate enough to produce the requested self-contained plan.                                       |
| Review a plan                          | Check source, assumptions, dependencies, scope, and observable completion criteria.                                          |
| Execute / reconcile / publish issues   | Read [follow-through](closing-the-loop.md); these actions require the corresponding user request.                            |

Read relevant product documents, architecture decisions, conventions, CI configuration, and task commands. Record the source revision for plans; an executor needs enough context to detect drift without reading this conversation. Do not require every historical document for a local review.

For each proposed finding, verify the source location and concrete consequence. Reject by-design behavior, mistaken attribution, and duplicate symptoms. Preserve the reason for rejection when it prevents repeated investigation. Missing verification can itself be a prerequisite, but absence of a particular tool is not automatically the highest-priority defect.

For handoff plans, include exact paths and symbols, the pattern to follow, important before/after behavior, dependency order, relevant checks and expected observations, material risks, and maintenance implications. Inline context that otherwise exists only in the conversation; do not copy whole files when a focused excerpt suffices. Reconcile an existing plan index before creating another plan.

Present findings ordered by impact, confidence, effort, and risk. Keep optional feature ideas separate. Write plans for the scope already requested. If a selection is needed, ask for it; do not treat an unanswered question as approval. Independent review can help an ambiguous or consequential plan when permitted, but it is not required for a routine edit.
