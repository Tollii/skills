# PR 2: wide events

Branch: `observability/wide-events`. If PR 1 is still open, branch from it and set PR 1's branch as the base, so the diff shows only the event work. If PR 1 is merged, branch from the default branch.

## Who decides what

The model is Boris Tane's: one wide event per unit of work, enriched as the work runs, complete when it ends. His `logging-best-practices` skill is embedded here exactly as he wrote it, and this file does not restate, summarize or adjust it. This file covers only what his skill cannot know: where the event lives when tracing exists, how it ties to the tracing from PR 1, sampling, how the work is enforced and proven, and how it ships.

If anything here seems to disagree with one of his rules, his rule wins, with two exceptions: the rule below about what stays out of an event, and where the event lives (next section). Say so in the final report, so this skill can be corrected.

## Step 1: decide where the event lives

**Default: the root span is the event.** PR 1 already gives every unit of work a root span, and Boris writes that "ideally, your wide events ARE your trace spans, enriched with all the context you need" ([Logging Sucks](https://loggingsucks.com/)); his [wide events 101](https://boristane.com/blog/observability-wide-events-101/) post builds the event with `setAttributes` on the span alone. One emitter, one store, and the event sits at the top of its own trace.

**Exception: a logged event beside the root span**, when the backend is log-first (Loki, ELK, CloudWatch Logs, a log archive) or cannot filter and group by span attributes. His skill describes this shape: a logger writes the event as one JSON line.

| | Root span is the event (default) | Logged event (exception) |
| --- | --- | --- |
| **Written by** | The root span when it ends; no log line | The shared logger, in the middleware's `finally` |
| **`enrich()` writes to** | The root span only | The event, and the root span flattened to dotted keys |
| **Correlation** | `traceparent` (PR 1); his `x-request-id` rule is covered by it | `trace_id` and `span_id` on the event, plus his request-id rule |
| **Shape** | Flat dotted keys (`payment.retries`), primitives and arrays only | Nested objects allowed |
| **Watch for** | The SDK's attribute limit (128 by default; raise `OTEL_SPAN_ATTRIBUTE_COUNT_LIMIT`) and the backend's key and value length limits | Paying for every field twice, once on the event and once on the root span, and backends that drop logs along with sampled-out traces |

Record the choice and the reason in the conventions doc. When a project already made it (an ADR, the conventions doc), it stands while it still fits the backend.

**Which of his rules apply.** With a logged event, all of them. With the root span as the event, his rules on what an event contains (business context, cardinality, environment fields, one event per unit of work, emitting at the end) apply in full; his rules on the logger, JSON output and log levels have nothing to act on, and the root span's status takes the place of the `error` level.

## Step 2: build the event his skill describes

Apply his skill to every unit of work the survey found. What an event contains and how its fields are named and grouped are decided there.

PR 1 gave every unit of work a root span, not only HTTP requests. Each of them gets its event too: a job run, a consumed message, a webhook, a CLI command.

## Step 3: tie the event to the trace

[assets/wide-event-middleware.md](../assets/wide-event-middleware.md) shows the few lines involved.

- **One `enrich()` for handlers.** It writes each field to the root span, and with a logged event also to the event his middleware created, so the two always agree and nobody maintains two sets of fields. The middleware keeps a reference to the root span, because handlers call `enrich()` from inside traced helpers, where the active span is a child.
- **The root-span middleware runs first.** The event middleware takes the span that is active when it starts as the root span. That only holds if the middleware from PR 1 is registered before it and wraps the rest of the request.
- **Logged event only:** the event carries the trace id and span id of the root span, so an event leads to its trace and back. It keeps the shape his rules give the fields, nested objects included; the span gets the same fields flattened to dotted keys, because a span attribute cannot hold an object. The request context PR 1 puts on every span (request id, user id, tenant id, route, entity ids) also appears on the event, named as his rules say; the conventions doc records which event field matches which span attribute.

## Step 4: replace what is there, and keep it replaced

Go through every existing `console.*` and logger call. Inside a unit of work, turn it into an `enrich()` call or remove it. Outside one (startup, shutdown), keep it on the shared logger under his rules. Use the project's logger if it has one; if it has none, the one his examples use or a few lines that write JSON are both fine; say which in the PR.

Check the framework's own error handling too. A default error handler that prints a stack trace through `console` bypasses the logger, and a framework that swallows handler errors means the middleware's `catch` never runs: read the error where the framework keeps it (Hono: `c.error`, after `next()`), and replace the default handler so the error is recorded on the event instead. Then add a lint rule in the project's linter, so the work does not erode: outside the logger module, `console.*` (or `Console.Write*`, `System.out`, `print`, `fmt.Print*`) and any logger other than the shared one are errors. This mirrors the tracing rule from PR 1. On an existing codebase with many calls, land it with a baseline as [adopt.md](adopt.md#lint-rules-on-an-existing-codebase) describes.

Browser, mobile and edge code are covered in [backend.md](backend.md#browser-mobile-and-edge): there, the client's root span is the event, and the lint rule applies to them too.

## One addition that is not from his skill

His skill does not cover what must stay out of an event, and this skill does not leave that open. Record ids, not personal data. Keep passwords, tokens, auth headers, payment details and request or response bodies out of events. Prefer a user id to an email address. Where a field is useful but sensitive, leave it out and note the decision in the conventions doc.

This rule is the skill owner's, not Boris Tane's. His examples log an email address and an article title to show the pattern; they are not a list of fields to copy. Where an example logs something this rule excludes, this rule wins. For a field that is neither clearly safe nor clearly personal, such as a user agent, keep what his middleware keeps and list it in the final report for the owner to decide.

## Sampling

**Default: keep everything.** Most projects do not have the volume to need sampling, and sampling has costs: a stateful Collector tier, and counts that stop being exact. Write the rules below into the conventions doc anyway, so they are ready, and turn sampling on when volume or the bill calls for it.

**When sampling is needed, sample by outcome** (tail sampling), with the rules from [Logging Sucks](https://loggingsucks.com/):

1. **Always keep errors:** every 5xx, exception and failed job.
2. **Always keep slow ones:** above the p99 latency, as a fixed threshold the conventions doc records.
3. **Always keep specific users:** VIP or enterprise customers, internal test accounts, flagged sessions. His example code also keeps requests with a feature flag that is being rolled out; keep those too.
4. **Randomly keep a share of the rest:** he suggests 1–5% of healthy, fast traffic.

**One decider.** The service that owns the unit of work's root span decides, once, at the end: a `shouldSample(event)` function in the shape of his example in Logging Sucks (the embedded skill has none). It sets `sampling.keep` (true or false) and `sample_rate` (1 for always-kept, the share otherwise) on the root span, and on the logged event when there is one. An OpenTelemetry Collector's `tail_sampling` processor in front of the backend then keeps every trace whose root has `sampling.keep = true` (a `boolean_attribute` policy), plus any trace with an error status from another service (a `status_code` policy), and drops the rest. With a logged event, write it only when `sampling.keep` is true, so a kept event always has its trace and a dropped one has neither.

Do not let the app and the Collector each make a random choice: they use different hash functions, so at a 5% share almost none of the randomly kept events would have their trace.

What the Collector tier needs, from its README:

- **All spans of a trace reach the same Collector instance.** With more than one instance, put a `loadbalancing` exporter keyed on trace id in front.
- **It waits before deciding** (`decision_wait`, 30 seconds by default) and holds the trace in memory meanwhile. Size it for that.
- **Services export every span** (`OTEL_TRACES_SAMPLER=parentbased_always_on`), so the Collector sees whole traces. SDK samplers decide when a request starts, before its outcome is known, so they drop errors at the same rate as successes.

**Counts once sampling is on.** Kept events are no longer a full count. Take request and error rates from OpenTelemetry metrics, which sampling does not touch (the HTTP server duration histogram the instrumentation already records), or from a backend that weights by `sample_rate`. Say which in the conventions doc.

Backend specifics, such as Azure Monitor's own sampler, are in [backend.md](backend.md#azure-monitor-and-application-insights).

## Prove it works

Tests show that:

- A successful unit of work produces exactly one event.
- One that throws still produces exactly one event, with its error recorded.
- A field added through `enrich()` appears on the root span, and on the logged event when there is one.
- A logged event carries the trace id of the root span.
- Once sampling is on: `shouldSample` keeps an error, a slow request and a VIP request, drops a healthy one when the share is 0, and the root span carries `sampling.keep` and `sample_rate`.

Then run lint, typecheck and the test suite.

## Done when

- [ ] The lint rule runs in CI and reports nothing.
- [ ] The tests above pass.
- [ ] The conventions doc holds where the event lives and why, its schema, the sampling rules (on or not yet), how counts are taken, and the fields left out for privacy.
- [ ] The PR description says whether it is stacked on PR 1.
