# Where telemetry goes

## The code stays vendor-neutral

Whatever the backend is, application code uses the OpenTelemetry SDK with the OTLP exporter, configured through the standard env vars:

- `OTEL_EXPORTER_OTLP_ENDPOINT`
- `OTEL_EXPORTER_OTLP_HEADERS`
- `OTEL_SERVICE_NAME`
- `OTEL_RESOURCE_ATTRIBUTES`

Keep vendor SDKs and agents out of application code, so that changing backend is a config change. With no endpoint set, the app must still run: print spans to the console in development and export nothing in production.

## Choosing the backend

Take the first case that applies:

1. **The project already sends telemetry somewhere, or its config names a backend.** Use that. Check its current docs for the OTLP endpoint and auth header.
2. **Nothing is chosen and someone is there to answer.** Ask one question, with your recommendation first.
3. **Nothing is chosen and nobody answers.** Go ahead with plain OTLP and no backend configured, and say so at the top of PR 1.

## What to recommend when nothing is chosen

In this order:

1. A tool the project already pays for that accepts OTLP, such as its error tracker or APM.
2. The hosting platform's own tracing, if it has one.
3. Otherwise a backend built for high-cardinality events, where any field can be queried without declaring it first. Wide events are only useful if you can filter and group by fields like user id. Honeycomb, Axiom, and ClickHouse-based tools such as SigNoz are examples; the Grafana stack is the common self-hosted choice.

Pricing, free tiers and OTLP support change, so check current docs before naming one, and give the reason for the pick in a sentence.

## Azure Monitor and Application Insights

Application Insights fits the default design (root span as the event): server and consumer root spans become rows in `requests`, their attributes land in `customDimensions`, and KQL filters and groups by any of them. Things to know:

- **Span kind decides the table.** Only `SERVER` and `CONSUMER` spans become `requests`; `INTERNAL`, `CLIENT` and `PRODUCER` spans become `dependencies`. Give the root span of a job, scheduled task or CLI command kind `SERVER` or `CONSUMER`, or its event lands among the dependencies.
- **Limits.** Property keys up to 150 characters, values up to 8,192. Keep values short; the content rule (ids, not bodies) already does.
- **Check, don't assume:** whether `customDimensions` values arrive as strings (then numeric fields need `todouble()` or `toint()` in KQL), and which tables native OTLP ingestion writes to, if the project uses it.
- **The distro samples by default.** Microsoft's Azure Monitor OpenTelemetry distro samples at five traces per second in .NET, Node.js and Python, and the Java agent is rate-limited by default since 3.4.0. That drops errors as readily as successes. Set the sampler explicitly.
- **Trace-based log sampling** drops log records whose trace was sampled out. It is on by default in .NET and Java, off in Node.js and Python. With a logged event, know which way it is set.

Sampling in Application Insights, when it is needed, has two paths, and neither does everything:

| | (a) The distro's sampler | (b) Collector tail sampling |
| --- | --- | --- |
| **How** | Distro in the app, fixed-percentage or rate-limited sampling | Vendor-neutral SDK with `parentbased_always_on`, OTLP to a Collector with `tail_sampling`, exported to Azure Monitor |
| **Keeps every error** | No; it decides at the start of a request, and ignores upstream decisions | Yes, with the rules in [wide-events.md](wide-events.md#sampling) |
| **Portal counts** | Correct: the distro tells Application Insights the rate, and `itemCount` scales the views | Overstated for errors: the exporter writes no rate, so `itemCount` stays 1; take counts from metrics |
| **Support** | Microsoft's | Community: Microsoft documents outcome-based sampling as needing a downstream Collector and does not support it |

Path (a) breaks this skill's vendor-neutral rule; path (b) keeps it. Without sampling, neither matters: keep every trace and the portal's counts are right. Record the path in the conventions doc.

## Local viewing

Give developers a way to see traces locally from day one: a single-container OTLP viewer (Jaeger, or Grafana's `otel-lgtm` image), documented in the conventions doc.

## Browser, mobile and edge

Clients and constrained runtimes are in scope, and they follow the same rules as servers: one setup module, spans at the definition, request context, propagation, no stray logging. What differs is the same for all three: the code runs somewhere you don't control, it can't hold secrets, and it may stop at any moment.

- **SDK.** Use the platform's OpenTelemetry SDK: `@opentelemetry/sdk-trace-web` in the browser, the Swift and Android SDKs on mobile, and the platform's supported path, or a small OTLP-over-fetch exporter, on edge workers and managed functions.
- **Units of work.** A page load, a navigation or a user action (submit, purchase, upload) gets a root span. Its children are the calls it makes. On a client, that root span is the wide event: put the fields there through the same `enrich()` idea rather than shipping a separate log stream, unless the project already has one.
- **Propagation to your own APIs.** Inject `traceparent` into calls to the project's own origins only, never third parties (in the browser: `propagateTraceHeaderCorsUrls` on the fetch and XHR instrumentations). Cross-origin APIs must allow the `traceparent` and `tracestate` request headers in CORS. The server then continues the client's trace.
- **No secrets in the client.** A backend API key in a bundle or an app is public. Export to an endpoint the project controls: an OpenTelemetry Collector or a small proxy route that adds auth, with rate limits.
- **Flushing.** The runtime can end before a batch is sent. In the browser, flush on `visibilitychange` to hidden (with `fetch` `keepalive` or `sendBeacon`); on edge workers, hand the flush to `waitUntil` or the platform's equivalent; on mobile, flush when the app goes to the background.
- **Privacy and consent.** Strip query strings and fragments from URLs, never record input values, and use the user id only where the product's consent rules allow client telemetry. Where consent is required, the SDK starts only after it is given; record that in the conventions doc.
- **Sampling.** See [tracing.md](tracing.md#9-sampling); clients usually start lower than servers.
- **Proof.** The same in-memory-exporter tests run in the client's test runner, and one test shows an outgoing call to the API carries `traceparent` while a third-party call does not.

Clients can go in PR 1 with the servers. When they are large enough to review on their own, give them their own branch, `observability/client-tracing`, and say so in PR 1.

If part of the system cannot be traced, or cannot export, say so in the PR and in the conventions doc. Do not leave a setup that looks complete and silently emits nothing.

## References

- [OTLP exporter configuration](https://opentelemetry.io/docs/languages/sdk-configuration/otlp-exporter/) for the endpoint and header env vars.
- [General SDK configuration](https://opentelemetry.io/docs/languages/sdk-configuration/general/) for the service name and resource attributes.
