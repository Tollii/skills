# Adopting existing observability

This runs when the project already has tracing or logging that this skill did not build: a vendor agent, an APM SDK, inline spans, a logger with scattered lines, or a half-finished OpenTelemetry setup. The goal is the same end state as a full setup, reached by keeping what works and changing the rest in reviewable steps. It is not a rewrite.

## 1. Inventory before judging

From the survey and the code, write down:

- **Where telemetry goes now,** and who looks at it: dashboards, alerts, on-call runbooks, saved queries. These are users of the current setup, and they break if field or span names change without warning.
- **What produces it:** auto-instrumentation, a vendor agent, a vendor SDK called from code, inline spans, a logger, `console`.
- **How much there is:** inline spans, log calls and `console` calls per package (the survey counts them).
- **What already matches this skill** and needs no change.

## 2. Write the conventions doc for what is there

Start [the conventions doc](../assets/conventions-doc.md) by describing the current setup truthfully, including what it does not do yet. Add a **Gaps** section listing each difference from the target in steps 4 and 5 of `SKILL.md`, with the plan for it. This doc is the adoption plan the user reviews, and it becomes the normal conventions doc as the gaps close.

## 3. Ask before replacing anything people depend on

Keep the existing backend, logger library and vendor agent unless they block the target. Ask, with a recommendation, before:

- **Replacing a vendor SDK or agent with OpenTelemetry.** Most APM vendors accept OTLP, so the usual path is to keep the backend and change only how data reaches it. Run both side by side for a while if dashboards depend on the vendor's own span names.
- **Changing the logger library.** The rules in `logging-best-practices` can usually be met with the logger the project has.
- **Renaming fields or spans that dashboards and alerts use.** List what would break in the PR.

## 4. Close the gaps in the usual split

The work still lands as tracing first and logging second, on the same branch names. Each PR closes the gaps of its half:

- **Tracing:** add the helper next to whatever exists, convert inline spans to definitions, add request context and propagation, decide sampling. Remove a vendor's manual span calls only where the helper now covers them.
- **Logging:** add the wide-event middleware, then convert log calls to `enrich()` package by package, starting with the busiest paths.

A large codebase may need several PRs per half. Split by package or service, and keep each one complete: a package is either converted or not, never half.

## Lint rules on an existing codebase

A rule that reports 2,000 violations on day one either blocks the PR or gets turned off. Land it with a **baseline** instead:

1. Add the rule as written in [tracing.md](tracing.md#8-a-lint-rule-that-fails-the-build) or [wide-events.md](wide-events.md#step-4-replace-what-is-there-and-keep-it-replaced).
2. Record the existing violations in a baseline file the linter or a small script reads (ESLint's suppressions, a `.baseline` list, a ratchet in the test). New code must pass; old violations are tolerated.
3. The baseline may only shrink. CI fails if it grows, and every conversion PR removes its entries.
4. Record the baseline and its current size in the conventions doc. The maintenance pass checks that it shrinks.

The "done when" lists in the references count a baselined rule as running, as long as the baseline exists, only shrinks, and new code passes.

## 5. Finish

When the gaps list is empty and any baseline is gone, the project is no different from one this skill set up. Remove the Gaps section, set `Last swept`, and from then on use the [maintenance pass](maintenance.md).

The final report adds: what was kept and why, what was replaced with whose approval, what still depends on old names, and the baseline sizes before and after.
