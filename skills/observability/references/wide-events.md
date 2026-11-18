# PR 2: wide events

Branch: `observability/wide-events`. If PR 1 is still open, branch from it and set PR 1's branch as the base, so the diff shows only the logging work. If PR 1 is merged, branch from the default branch.

## Who decides what

The logging rules are not this skill's. They belong to Boris Tane's `logging-best-practices` skill, embedded here exactly as he wrote it. This file does not restate, summarize or adjust them. It covers only what his skill cannot know: how the event ties to the tracing from PR 1, how the work is enforced and proven, and how it ships.

If anything in this skill seems to disagree with one of his rules, his rule wins, with one exception: the rule below about what stays out of an event. Say so in the final report, so this skill can be corrected.

## Step 1: read all of his skill

A verbatim copy travels with this skill, in [vendor/logging-best-practices](../vendor/logging-best-practices/SKILL.md), so there is nothing to install. Read it in full before writing any logging code: its `SKILL.md`, then every file under `rules/`:

- [wide-events.md](../vendor/logging-best-practices/rules/wide-events.md): the pattern, request correlation, emitting in `finally`.
- [context.md](../vendor/logging-best-practices/rules/context.md): cardinality, business context, environment fields.
- [structure.md](../vendor/logging-best-practices/rules/structure.md): single logger, middleware, JSON, levels.
- [pitfalls.md](../vendor/logging-best-practices/rules/pitfalls.md): the common mistakes.

Do not edit that folder. [UPSTREAM.md](../vendor/logging-best-practices/UPSTREAM.md) records where it comes from and how to update it.

## Step 2: build the logging his skill describes

Apply his skill to every unit of work the survey found. What an event contains, how it is structured, the logger, the levels and the middleware are all decided there.

One clarification for this workflow: PR 1 gave every unit of work a root span, not only HTTP requests. Each of them gets its event too: a job run, a consumed message, a webhook, a CLI command.

## Step 3: tie the event to the trace

This is the part his skill leaves open, because it does not assume tracing exists. [assets/wide-event-middleware.md](../assets/wide-event-middleware.md) shows the few lines involved.

- **The root-span middleware runs first.** The event middleware takes the span that is active when it starts as the root span. That only holds if the middleware from PR 1 is registered before it and wraps the rest of the request.
- **Trace and span ids on the event.** The event carries the trace id and span id of the unit of work's root span, so an event leads to its trace and back, plus `trace_sampled`, so an event whose trace was sampled away says so.
- **One `enrich()` for handlers.** It writes each field to the wide event his middleware created, never to a second object, and to the root span, so the two always agree and nobody maintains two sets of fields. The event keeps the shape his rules give the fields, nested objects included; the span gets the same fields flattened to dotted keys, because a span attribute cannot hold an object. The middleware keeps a reference to the root span, because handlers call `enrich()` from inside traced helpers, where the active span is a child.
- **The request context is on the event too.** The values PR 1 puts on every span (request id, user id, tenant id, route, entity ids) also appear on the event. Name and shape them there as his rules say, not as the span does; the conventions doc records which event field matches which span attribute.

## Step 4: replace what is there, and keep it replaced

Go through every existing `console.*` and logger call and bring it under his rules. Use the project's logger if it has one. If it has none, the one his examples use or a few lines that write JSON are both fine; say which in the PR.

Check the framework's own error handling too. A default error handler that prints a stack trace through `console` bypasses the logger, and a framework that swallows handler errors means his middleware's `catch` never runs: read the error where the framework keeps it (Hono: `c.error`, after `next()`), and replace the default handler so the error is recorded on the event instead. Then add a lint rule in the project's linter, so the work does not erode: outside the logger module, `console.*` (or `Console.Write*`, `System.out`, `print`, `fmt.Print*`) and any logger other than the shared one are errors. This mirrors the tracing rule from PR 1. On an existing codebase with many calls, land it with a baseline as [adopt.md](adopt.md#lint-rules-on-an-existing-codebase) describes.

Browser, mobile and edge code are covered in [backend.md](backend.md#browser-mobile-and-edge): there, the client's root span is the event, and the lint rule applies to them too.

## Where the event lives

The event can live in one of two places. Both follow his model: one event per unit of work, enriched as the work runs, complete when it ends. Boris writes that "ideally, your wide events ARE your trace spans"; his skill writes them through a logger so that it works with any backend. Pick by what the backend queries well, and record the choice in the conventions doc.

| | Root span is the event | Logged event beside the root span |
| --- | --- | --- |
| **Pick it when** | The tracing backend filters and groups by any span attribute: Application Insights (`requests` and `customDimensions`), Honeycomb, Axiom, ClickHouse-based tools | The stack is log-first: Loki, ELK, CloudWatch Logs, a log archive, or spans are hard to query |
| **Written by** | The root span when it ends; nothing goes to a log | The shared logger, in the middleware's `finally` |
| **`enrich()` writes to** | The root span only | The event, and the root span flattened to dotted keys |
| **Shape** | Flat, dotted keys (`payment.retries`); span attributes cannot hold objects | Nested objects allowed |
| **Watch for** | The SDK's span attribute limit (often 128; raise `OTEL_SPAN_ATTRIBUTE_COUNT_LIMIT`) and the backend's limits on attribute length | Backends that drop logs along with sampled-out traces (Azure Monitor's trace-based log sampling): with a logged event, the same tail-sampling rules should decide both, so leave that coupling on only if it matches |

When a project already made this choice (an ADR, the conventions doc), it stands while it still fits the backend. Everything else in this file applies to both: the request-context fields, trace ids on the logged event, the content rule, the lint rule against stray logging, tail sampling, and the tests (read "the event" as "the root span" where it is the event).

## One addition that is not from his skill

His skill does not cover what must stay out of an event, and this skill does not leave that open. Record ids, not personal data. Keep passwords, tokens, auth headers, payment details and request or response bodies out of events. Prefer a user id to an email address. Where a field is useful but sensitive, leave it out and note the decision in the conventions doc.

This rule is the skill owner's, not Boris Tane's. His examples log an email address and an article title to show the pattern; they are not a list of fields to copy. Where an example logs something this rule excludes, this rule wins. For a field that is neither clearly safe nor clearly personal, such as a user agent, keep what his middleware keeps and list it in the final report for the owner to decide.

## Sampling

Tail sampling is the default, following the rules in [Logging Sucks](https://loggingsucks.com/): decide after the unit of work completes, from its outcome.

1. **Always keep errors:** every 5xx, exception and failed job.
2. **Always keep slow ones:** above the p99 latency, as a fixed threshold the conventions doc records.
3. **Always keep specific users and flags:** VIP or enterprise customers, internal test accounts, flagged sessions, and requests with a feature flag that is being rolled out.
4. **Randomly keep a share of the rest:** 1–5% of healthy, fast traffic at scale. At low volume set it to 100%: the rules stay in place and nothing is dropped until volume calls for it.

Put the share used on each kept event as `sample_rate` (1 for always-kept ones), so counts can be scaled back up.

Where the decision runs depends on where the event lives:

- **Root span is the event:** in an OpenTelemetry Collector's `tail_sampling` processor in front of the backend (policies for status code, latency, string attributes and a probabilistic share), or in the backend if it samples by outcome. The SDK's own samplers decide at the start of a request and cannot keep errors. Services keep `parentbased_always_on`, so a trace is either kept whole or dropped whole.
- **Logged event:** in the middleware, just before the event is written, with a `shouldSample(event)` function in the shape of his example. Base the random share on the trace id rather than a fresh random number, and give the Collector the same rules, so a kept event almost always has its trace. Rules 1–3 always agree; the random share can differ slightly between the two.

Record the rules, the threshold, the user and flag lists, and the share in the conventions doc.

## Prove it works

Tests show that:

- A successful unit of work emits exactly one event.
- One that throws still emits exactly one event, with its error recorded.
- The event carries the trace id of the root span.
- A field added through `enrich()` appears on both the event and the root span (or on the root span, when it is the event).
- The sampling rule keeps an error, a slow request and a VIP request, and drops a healthy one when the share is 0.

Then run lint, typecheck and the test suite.

## Done when

- [ ] The lint rule runs in CI and reports nothing.
- [ ] The tests above pass.
- [ ] The conventions doc holds where the event lives, its schema, the sampling rules and the fields left out for privacy.
- [ ] The PR description says whether it is stacked on PR 1.
