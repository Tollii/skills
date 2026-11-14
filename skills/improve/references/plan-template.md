# Implementation plan

Write for a competent engineer or agent without this conversation. Use the repository's existing plan directory and numbering. If that directory serves another purpose, use a clearly named alternative. Keep one authoritative status index.

Include the information needed for this change:

- **Outcome and evidence:** the problem, affected behavior, exact source locations, and current revision. Include only code excerpts needed to explain a constraint.
- **Scope and dependencies:** affected responsibilities, prerequisite work, supported contracts, and exclusions that matter.
- **Approach:** ordered changes, ownership, meaningful alternatives, and the reason for the selected design. Preserve repository naming and conventions.
- **Verification:** relevant commands and observable expected behavior. Add tests for real rules, transitions, or failures; do not require source-text checks or a test for every step.
- **Completion:** measurable results, remaining device or deployment checks, and status update.
- **Risks and recovery:** genuine conditions requiring a new decision, migration/release order, and recovery where needed.

On execution, compare the current source with the recorded revision, including uncommitted changes. Refresh obsolete assumptions; ordinary drift is not an automatic blocker. Stop only dependent work when a material scope or authorization question is unresolved. Use judgment for routine implementation details.

An index lists plan title, priority, dependencies, and status. Record rejected or superseded findings only when the reason will prevent repeated work. Do not require a commit, push, PR, or deployment unless the task authorizes it.

For a handoff, identify the relevant existing test and implementation patterns by path. Explain what each required check establishes and what a successful result looks like. Include the constraint a code excerpt illustrates, not an unexplained block of copied source. A reader should be able to locate the affected responsibility and start the work without this conversation.

During review, check that prerequisites are actually complete, the proposed design resolves the stated problem, and completion criteria cover the meaningful failure cases. If execution changes a shared contract or prerequisite, update dependent plans as well as this plan. Record why a material adaptation was needed; routine implementation choices do not need a separate decision document.
