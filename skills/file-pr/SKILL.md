---
name: file-pr
description: File a concise pull request with a clear title and a body that shows the change, proves it works and says how risky it is to merge. Use when the user asks to file, open or create a PR, or to write a PR description.
metadata:
  harness: [claude, codex]
  platform: [darwin, linux]
---

# File PR

## Before filing

- Check whether a PR for this branch already exists. If it does, update it instead of opening another.
- Review the diff locally against `origin/main` and make sure its contents match the goal. Nothing unrelated, nothing missing.

## Title

PR titles usually become commit messages, so follow the repository's conventions: look at recently merged PRs and the Git history. Write a concise, human-readable title that says why the change matters, not what mechanism it uses.

BAD

> ❌ perf(server): negotiate permessage-deflate on the websocket

GOOD

> ✅ perf(server): cut websocket frame size by 70%+ with gzipping

## Body

Open with the problem in plain words, based on what the user originally asked for, then the solution in a sentence or two. Don't lead with an inventory of what was changed.

BAD

> ❌ Removed implicit workspace carry-over from every "new thread" entry point (cmd+n / cmd+shift+o, sidebar v1/v2 buttons, command palette). New threads inherit only the project from context; branch, worktree, and env mode always come from the configured defaults. Deleted buildContextualThreadOptions, startNewThreadInProjectFromContext, and the v1 sidebar's seed-context machinery.

GOOD

> ✅ My "new worktree" default was ignored when starting new threads on existing worktrees. Super unintuitive. Now your preferences always apply.

Then use this template. Skip preambles, keep prose brief, and use the project's domain language (its glossary, if it has one).

```markdown
<problem and solution, 1–3 sentences>

## Summary

<the smallest visual that makes the key point clear>

## Evidence

- **Before:** <screenshot, output or failing test>
  **After:** <screenshot, output or passing test>

## Merge danger

**Door:** <one-way or two-way>. <optional: why>
**Blast radius:** <one word>. <optional: what could break>
```

### Summary

Pick the one view that answers "what changed?" fastest, and put each visual next to the sentence it supports. Usually one is enough; never use them all.

- **Pseudocode** for logic or an algorithm.
- **Call tree** for runtime control flow (`submitForm → createSession → persistPrompt`).
- **Component tree** for UI structure, with the state and module boundaries that matter.
- **Shallow file tree** for file responsibilities or a broad refactor.
- **Mermaid** sequence or flow diagram for interaction between components or services.
- **`diff`** of any of the above when the surrounding shape already exists and the point is what changes:

```diff
 submitForm
   createSession
     persistPrompt
+    expandSkillMention
     launchAgent
```

Show a whole code block only when most of it is new, or when leaving out context would hide ownership or order. Keep only the calls, files, props and states the reviewer needs.

### Evidence

Show concretely that the change works, as before and after. A screenshot is best when the change is visible and the environment allows it. Otherwise use execution: the exact test that failed and now passes, or console output.

### Merge danger

- **Door:** a two-way door can be walked back cheaply (revert and redeploy). A one-way door can't: destructive migrations, deleted data, published contracts or anything external that others start depending on.
- **Blast radius:** what the change could affect if it's wrong. Consider consumers of changed APIs, layout shift, mobile, performance, data.

## Filing

Open a real PR, not a draft, so review bots run. If the user also asked for follow-up (watching CI, answering review comments), do that within the scope they asked for.
