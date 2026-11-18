---
name: code-design-review
description: Review a diff, PR or module for code design problems that carry real risk. Treats code smells as evidence and reports only those that lead to a concrete failure or a costly next change, each with a fix or a better design. Use when asked to review a change for design, before refactoring a module, or when a change touched far more files than it should have.
---

# Code design review

A code smell is a hint, not a finding. This review reports a smell only when you can name what it will break: the input or sequence that causes the bug, or the next change that will land in the wrong place. Everything else is left out, so the report stays short and every line is worth acting on.

## What to look for

| Smell | Risk to check |
| --- | --- |
| Duplicated registration, mapping, validation or branching | A side effect runs twice, a service is instantiated twice, or the copies drift apart. Fix: a single owner or an idempotent composition point. |
| Check now, act later on mutable state | A stale read or a race between the check and the write. Fix: a conditional write, a transaction, or a re-read that tells "missing" from "changed". |
| One feature change needs edits across many unrelated files | A missing slice boundary or registration point. Fix: move the responsibility, or add the registration point. |
| Behavior in the wrong place: a controller that knows a feature's internals, a test mock that carries production logic, a service reaching through another object for data | The next change lands in the wrong place, or the test passes on logic production doesn't run. Fix: move the behavior to its owner; tell, don't ask. |
| Stale names, leftover imports, old and new patterns side by side after a move | The boundary is hard to see, so the next change crosses it. Fix: clean up, or note it where cleanup is out of scope. |
| A broad helper extracted to share repeated validation | A grab-bag with no contract that every caller bends. Fix: a named responsibility with a clear contract. |

These are the common ones, not a complete list. Any smell qualifies if you can name its risk.

## Procedure

1. **Read the change and its surroundings.** Read enough of the code around a diff to know where its boundaries are: who calls it, what it registers, what owns the state it touches. If the project has architecture docs, a glossary or an AGENTS.md, read them first.
2. **Collect smells as evidence.** Note each one with its location. Don't report yet.
3. **Name the risk for each.** Write the concrete failure: what input, timing or next change makes it go wrong, and what the user or the next developer sees. If you can't, drop the smell.
4. **Dig deeper with the specialist skill.** When a finding falls in another skill's area, invoke that skill on the code involved and follow its procedure, rather than stopping at a pointer:
   - A missing type: repeated guards, optional fields that only some states have, flags or sentinels, a status checked in many places. Invoke `type-design`.
   - Mocked vendor or framework types, deep mock graphs, interaction tests that mirror structure, collaborators with no clear role. Invoke `mock-roles-not-objects`.
   - Tests that can't fail, duplicate proof, test-only seams in production code. Invoke `test-audit`.
   - A race or ordering bug between users, requests, retries or events that is hard to see by reading. Invoke `statechart` to show the path to the bad state.
   - A boundary that one external change would break in several places. Invoke `residuality` for a contagion check.

   Use what the skill finds to sharpen the risk and the fix. If a skill isn't installed, apply its idea from the bullet above and say it wasn't available.
5. **Propose the fix, or a better design.** Start with the smallest change that removes the risk. Then ask whether a different design would remove the risk *and* make the code simpler, less error-prone or better typed: a type that makes the bad state impossible, one owner in place of several copies, a role interface in place of a mock graph, a conditional write in place of check-then-act. When it would, propose it with a short sketch of the new shape and what it deletes, next to the small fix, and say which you recommend and why. Don't propose a redesign that only moves the risk around or adds a layer without removing checks.
6. **Rank by harm**: data loss, wrong results and races first; changes that will go to the wrong place next; readability last.

## Output

One entry per finding, most harmful first:

- **Risk**: the concrete failure, in one sentence.
- **Evidence**: file and line, and the smell.
- **Fix**: the smallest change that removes the risk.
- **Better design** (when there is one): the sketch, what it removes or makes impossible, and whether you recommend it over the fix.
- **Skill used** (when one was invoked): which, and what it added.

End with anything you could not judge without more context. If nothing qualifies, say so; an empty review is a valid result. Don't pad it with style comments.
