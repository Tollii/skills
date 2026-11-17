---
name: test-audit
description: Keep tests that prove behavior and cut the ones that don't. Use when writing, changing or reviewing a test, and when auditing or pruning a test suite. Rejects duplicate proof, tests coupled to implementation, and production code that exists only for tests.
---

# Test audit

Every test should protect something a user, caller or other system depends on, and should fail when that thing breaks. This skill has three uses:

- **Writing or changing a test:** pass the [authoring gate](#authoring-gate) first.
- **Auditing existing tests:** remove low-value tests in coherent batches, one production owner at a time, following [references/audit.md](references/audit.md). The goal is better proof, never a deletion count.
- **Pruning a whole subsystem's tests in one PR:** read [references/campaign.md](references/campaign.md) first.

## Authoring gate

Before adding a test, answer:

1. **What does it protect?** An observable behavior, an invariant, or a contract someone else relies on.
2. **What regression would make it fail?** It should be one that could credibly happen.
3. **Why doesn't existing proof catch that already?** Each contract has one primary test owner, at the strongest boundary that can see it. A second layer earns a test only if it adds its own risk, such as a different transport or lifecycle. Prefer adding a row to an existing table or shared fixture over a new test, and consolidate repeated setup.
4. **Does it need an export, flag, wrapper or injection hook that production doesn't use?** Then test through the real boundary instead.

Then:

- Follow the project's testing guide, if it has one.
- A regression test must fail before the fix, for the intended reason, and pass after it. If you can't show the failing baseline, say so.
- Cover a bug once, at its owner. Don't replay it at every layer it passes through.
- A refactor that preserves behavior should not break the test.

## Junk patterns

Reject these in new tests. When auditing, use the [full checklist](references/audit.md#junk-patterns).

- Comparing a value with itself, or computing the expected value with the code under test.
- Copied inventories (lists of exports, files, manifest entries) and greps of source text.
- Assertions on implementation details: which private function was called, with what arguments, in what order.
- The same contract proven again in another layer.
- Test-only seams in production code.
- Mocks that supply the very behavior the test asserts.
- Fixtures that fabricate what the code under test should produce (acknowledgements, ordering, IDs), or assert persistence against a path the code never writes.
- Negative tests that pass because of a different guard than the one named.
- Test names that promise behavior the test never exercises.

## Retention bar

Some tests look like junk but guard real contracts. Keep tests of:

- public APIs, SDKs, protocols, config formats, migrations and storage formats;
- security rules, platform differences and defaults;
- exact output bytes others depend on (prompts, generated code, wire formats);
- packaging, release and architecture rules;
- ordering that users or callers can observe.

Static or slow is not a reason to delete. A test that inspects source is fine when it is the cheapest independent guard of a user-facing contract and survives renaming identifiers. A test that already fails may be catching a real bug: fix the bug, don't delete the test.

## Audit and validation

- Gather the [candidate evidence](references/audit.md#candidate-evidence) before any edit, and write down what proof remains after each deletion.
- Delete production seams that only tests used, together with those tests.
- Follow the project's testing policy. Don't edit files while a test run or watcher is using them. Run the owner's and its siblings' tests plus the project's checks for changed files, then review the diff.
- Report what you found and why it existed, which contracts you kept, the proof you ran and its cost, production versus test lines removed, and follow-ups. Commit, push or merge only if the task allows it.
