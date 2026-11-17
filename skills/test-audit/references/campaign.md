# Test-pruning campaign

A campaign prunes one subsystem's whole test surface in one PR: a plugin, a package, or one core area. The authoring gate, retention bar, candidate evidence and validation in [SKILL.md](../SKILL.md) and [audit.md](audit.md) apply to every part of it. This file adds the order of work. Each step ends on its completion criterion; don't start the next step early.

## 1. Baseline

At a pinned `main` commit, record the subsystem's test and support line counts and whether each test file passes or fails. Keep baseline failures in their own list. They are often real bugs rather than stale tests.

**Done when** every in-scope test file has a recorded baseline result.

## 2. Lanes and inventory

Split the tests into **lanes** along production ownership, not file names. A messaging integration might split into accounts, commands, inbound, outbound, persistence, transport, shared code and test harness. Include the subsystem's cases in shared core suites, and its end-to-end and QA scenarios.

**Done when** every test file and scenario the subsystem owns belongs to exactly one lane.

## 3. Read-only ledger per lane

Give each lane to its own read-only agent, or work through them one at a time. Read every test in full, including parameter tables, plus the production owners, their entry points, callers, history and how CI runs them. Each test declaration goes into a written **ledger** with one mark. A parameterized test (`it.each`, `@pytest.mark.parametrize`, table tests) counts as one declaration unless its rows need different marks.

- `R` retain: name the contract and the bug it catches. A test that only moves to a better file stays `R`, with the move noted.
- `F` fix: keep the contract but repair the assertion, such as a negative test that passes when only one of several items is missing.
- `C` consolidate: name the owner that takes over the assertion first, whether a sibling table row, a stronger boundary suite or a shared owner in another package.
- `D` delete: name the proof that remains, or why there is no contract.

Judge a test by its assertions, not its name. A test named for clearing something may in fact assert that it was *not* cleared.

**Done when** every declaration in the lane has a mark and a line of evidence.

## 4. Layer plan per lane

The ledger is input, not the edit list. A second read-only pass starts from it and looks for a redundant **layer**: for example, several suites that each drive the same shared component through a mock, next to a stronger suite that drives it through the real stream. Name the **keeper** suite for each contract. Prefer the real transport boundary with a fake network over a mocked collaborator. Fix any ledger mistakes this pass finds.

**Done when** each lane's plan names the files it retires, the keeper for each contract, the assertions to carry into keepers, and the test-only production seams it frees.

## 5. Cutover

Edit lane by lane. Changes to shared harnesses and support files go through one person or agent at a time. With each lane, remove the test-only production seams it frees: injection parameters, getters, reset exports and needless indirection. Register moved suites wherever CI and test inventories list them. Update line-count baselines, if the project keeps them. Write durable test-ownership rules into the subsystem's AGENTS.md, drawn from mistakes this campaign actually found.

**Done when** every lane plan is applied and each lane's keepers pass.

## 6. Preservation review

Before claiming the campaign is done, have independent reviewers compare the deleted coverage against the keepers, one reviewer per boundary group. They look for contracts that lost their only proof, and for new assertions that can't fail, such as a rejection case production never reaches.

For each restored contract, make one deliberate **mutation** of the production owner and confirm the keeper goes red. Then restore the source exactly.

**Done when** every reported gap is restored or rejected with evidence from the source, and every restored contract has caught a mutation.

## 7. Product defects

A baseline failure that survives into a keeper is a bug report. Fix it at its owner in a separate commit and prove it through the real user flow, with a **control** run that reverts the fix and shows the old behavior. Record unrelated product problems you notice as follow-ups instead of fixing them in the campaign.

**Done when** each repaired defect has a failing control and a passing candidate on the same harness.

## 8. Reconcile and hand off

A campaign outlives many commits to `main`. Merge `main` in rather than rebasing a long branch. When `main` changed a file the campaign deleted, keep the deletion, port the new contract into its keeper, and check that every new regression test from `main` still has a home. CI reruns the subsystem's suites on the merged head; locally, rerun only tests the merge touched or CI failed, and repeat end-to-end proof when merged changes affect it.

Review tools may show a truncated file list on a diff this large. Record maintainer decisions about compatibility flags in the PR description rather than editing the checks.

Hand off with the report from [SKILL.md](../SKILL.md#audit-and-validation), plus:

- baseline and final test and support line counts, with production lines counted separately;
- lanes, retired layers and keepers;
- preservation gaps found, and the mutations that confirmed them;
- product defects, with control and candidate proof.
