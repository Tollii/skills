# Audit workflow

Use this after the [authoring gate](../SKILL.md#authoring-gate). For a whole-subsystem prune, also follow [campaign.md](campaign.md).

## Junk patterns

Reject new tests that match these unless the [retention bar](#retention-bar) names an independent contract they protect. Audit existing tests for:

- **Proof that can't fail:** no assertions, self-comparisons, functions that copy their input back, expected values computed by the code under test.
- **Copied lists:** inventories of exports, files or manifest entries; exact greps of source, imports or strings.
- **Implementation coupling:** tests of private predicates or call shapes that a boundary test already covers; the same shared helper tested again from every caller.
- **Test-only seams:** exports, globals or wrappers that exist only for tests; production paths only tests call.
- **Mocks that lie:** a mock that implements the behavior being asserted, or pretends two different APIs behave the same.
- **Fabricated fixtures:** a fixture that supplies the acknowledgements, admission decisions or callback order the code under test should produce; persistence asserted against a path the code never writes.
- **Capability instead of outcome:** asserting a feature flag is set instead of checking that the delivery or acknowledgement it promises happens.
- **Misdirected negatives:** a negative test that passes on an unrelated guard, or on a rejection that production can never reach.
- **Misleading names:** names promising behavior that the inputs and assertions never exercise.

## Retention bar

- Keep independent contracts: public API, SDK, protocol, config, migration, storage, security, platform behavior, defaults, exact output bytes (prompts, generated cross-language code), packaging, release and architecture rules. Keep observable ordering and tests of credible regressions.
- A source-inspection test is valid when it is the cheapest independent guard of a user-facing key, byte or path, and survives renaming identifiers.
- Static or slow is not a reason to delete. A test that fails at baseline may have found a product bug: reproduce it and fix it at its owner. Never delete the test for it.

## Candidate evidence

Before editing, read the whole test and the production owner it covers: entry point, callers, callees, sibling tests, overlapping proof, how CI runs it, and its history. If a claim depends on a library, read the library's source or types. Read the project's agent instructions (AGENTS.md, CLAUDE.md) at the root and in the folder.

Record for each candidate:

- its exact name and location, and what failure it detects;
- non-test callers of the production or support code it covers;
- the stronger proof that remains, or why no proof is needed;
- why it was added (history) and what deleting it unlocks;
- the risk, and a focused command that validates the change.

Missing evidence means no deletion. Discovery is read-only: report candidates before editing. Prefer a few high-confidence candidates over many doubtful ones. Independent owners can be investigated in parallel.

When you act:

- Delete obsolete seams and dead paths along with their tests.
- Move retained regressions to the owner's suite.
- Consolidate generic package or dependency contracts in one place.
- Prefer changes that remove production lines too. Don't add new implementation-coupled tests to replace removed ones, and don't treat uncertainty as cleanup.

## Validation and handoff

- Follow the project's testing policy. Don't edit files while a test run or watcher is using them. Run the owner's and siblings' tests.
- If you removed a source grep or a plan assertion, run the real thing it described instead: the executable, or its dry run.
- Run the formatter on changed files, `git diff --check`, and the project's checks for the changed paths.
- Before landing a removal batch, run the project's dead-code checks, if it has them (unused dependencies, files and exports; e.g. knip, ts-prune, vulture, cargo-udeps). Removing tests often orphans support files and exports that an ordinary test run won't notice.
- Inspect `git diff --numstat` and get a final review.
- Report causes, removed categories and seams, candidates you kept because they turned out to matter, proof and its cost, production versus test and support lines, PR state and follow-ups.
- Commit, push or merge only when the task allows it. Before another audit batch, update from main and look again.
