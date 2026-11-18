# PR 1: tracing

Branch: `observability/tracing`, from the default branch.

## What to build

### 1. SDK setup in one module

It loads before anything else and sets the service name, version, commit, environment and region as resource attributes. Start from [assets/sdk-setup.md](../assets/sdk-setup.md) ([C#](../assets/dotnet.md), [Java](../assets/java.md)), which also shows how to keep the app running, and still recording spans, with no endpoint set.

### 2. One tracing helper, applied where functions are defined

A function is traced by wrapping its definition, not by opening a span inside its body or at its call sites. Start from [assets/tracing-helper.md](../assets/tracing-helper.md), or the [C#](../assets/dotnet.md) and [Java](../assets/java.md) equivalents:

```ts
// at the definition
export const getReceipt = traced("receipts.getReceipt", async (id: string) => {
  // ...
});
```

The helper's module is the only file that touches the tracer.

Why definitions and not inline spans: every caller gets the span for free, nobody can forget to end one, and a linter can check coverage mechanically. Inline spans drift, and no tool can tell you which ones are missing.

### 3. Coverage

Trace what crosses a boundary: every route handler and other entry point, every middleware, and every function that makes a network, database, queue, cache or file call. Pure computation is not traced; a span per helper costs money and buries the spans that matter.

**Don't trace an I/O call twice.** Where an instrumentation library already spans an I/O library (the Java agent and .NET's instrumentation packages cover JDBC, EF Core, SqlClient, HTTP clients, gRPC and most messaging clients; Node has equivalents), those calls are traced already. Wrapping the repository around them adds a second span with nothing new on it. Trace the business operation that calls them instead (a service method, use case, job or consumer), and keep manual spans for I/O that no library covers.

Make the boundary mechanical so a linter can check it. Name the **traced layers** in the conventions doc: I/O layers where nothing traces the I/O for you, and business-operation layers where something does. Use folders (`src/db/`, `src/services/`, `handlers/`), naming patterns (`*Repository`, `*Service`, `*Client`), or both. The rule the linter enforces is: every exported function in a traced layer, and every entry point, is traced. An I/O call that lives outside those layers is a sign the layer list or the code needs moving.

Frameworks often take handlers as inline functions, which escape a rule about exported functions. Hoist each one into a named, exported, traced function and register that. The middleware that opens the root span is the exception: the root span is its span, so it needs no second one.

### 4. Span names

Stable and low-cardinality, such as `module.function` or the route pattern. Ids go in attributes, never in names. Use OpenTelemetry semantic-convention attribute names where one exists.

### 5. Request context on every span

Keep the request's identity in request-scoped context (AsyncLocalStorage, or the framework's equivalent) and have it copied onto each span as it starts: by a span processor you register, or by configuration where the SDK offers it (the Java agent copies baggage to spans; see [java.md](../assets/java.md#request-context)). The fields are:

- request id
- user id
- tenant id
- route
- the ids of the domain entities the request is about

Set them as early in the request as they are known. A span processor only sees a span when it starts, so the function that sets a field must also write it to the root span and to the span that is active at that moment; otherwise the root span and the auth span never carry the user id. The root span gets all of them, plus the final outcome and status code when the request ends.

### 6. Errors the framework swallows

Find out what the framework does with an error a handler throws. Many catch it and turn it into a 500 response, so the middleware's `catch` never runs and the root span looks healthy. Hono does this: it keeps the error on `c.error` and calls its error handler. Mark the root span as an error from the response status as well as from a thrown error, and add a test for it.

### 7. Propagation

- **In.** Read the incoming W3C `traceparent` (and `tracestate`) in the server middleware so the request joins the caller's trace.
- **Out.** Inject it into every outgoing request by instrumenting the HTTP client or the one fetch wrapper the project uses.
- **Async work.** Queue messages and job payloads carry it as well.
- **Baggage.** Send it only to the project's own services, since it can hold user data.
- **Clients.** Browser, mobile and edge code inject `traceparent` into calls to the project's own APIs, so a user action and the server work it causes are one trace. [backend.md](backend.md#browser-mobile-and-edge) has what that needs.

### 8. A lint rule that fails the build

In whatever linter the project uses (a custom ESLint rule, a Biome plugin, or a small script wired into the lint command and CI), enforce two things:

- An exported function in a traced layer, or an entry point, that is not wrapped by the tracing helper is an error.
- Opening a span anywhere but the tracing module is an error (`startSpan`, `startActiveSpan`, `ActivitySource`, `Tracer`, whatever the language uses).

For a project that already has many violations, see [adopt.md](adopt.md#lint-rules-on-an-existing-codebase) for landing the rule with a baseline.

If the project has a lint command but no working linter configuration, set up the smallest one that runs the rule, and say so in the PR.

Allow an opt-out comment that must give a reason, for the rare function that should not be traced. Run the rule over the whole repo and fix everything it finds. The PR is not done while the rule reports anything.

### 9. Sampling

Decide it in PR 1 and write it in the conventions doc. The default is tail sampling with the rules in [wide-events.md](wide-events.md#sampling): keep every error, every slow request and every VIP or flagged one, plus a random share of the rest (1–5% at scale, 100% at low volume).

- **Where it runs:** an OpenTelemetry Collector with the `tail_sampling` processor, or a backend that samples by outcome. SDK samplers decide when a request starts, before its outcome is known, so they drop errors at the same rate as successes; use them only where no collector can run.
- **Services keep every span** (`OTEL_TRACES_SAMPLER=parentbased_always_on`), so the collector sees whole traces and a trace is kept or dropped whole. Set it through env vars, not code.
- **Clients** usually send a smaller share of healthy sessions. The server follows the client's decision through `traceparent`, so set the client's share deliberately.
- **On the event:** `trace_sampled` next to `trace_id`, and `sample_rate`, so an event whose trace is missing says why and counts can be scaled back up.

## Prove it works

Do not report tracing as working on the strength of a clean typecheck. Add tests with an in-memory span exporter that show:

- A request produces a root span and child spans, and the children carry the request context.
- A request arriving with a `traceparent` continues that trace id.
- An outgoing call carries a `traceparent` header.
- A request that ends in a server error marks the root span as an error.

Then run lint, typecheck and the test suite. If you can start the app, send it one request and look at the spans that come out.

## Done when

- [ ] The lint rule runs in CI and reports nothing.
- [ ] The four tests above pass.
- [ ] The conventions doc exists and covers the backend, local viewing, the helper, the traced layers, the opt-out, the request-context fields and the sampling decision.
- [ ] The PR description names the env vars to set and anything that could not be traced.

## References

- [OpenTelemetry semantic conventions](https://opentelemetry.io/docs/specs/semconv/) for attribute names.
- [W3C Trace Context](https://www.w3.org/TR/trace-context/) for `traceparent` and `tracestate`.
- [Context propagation](https://opentelemetry.io/docs/concepts/context-propagation/) and [baggage](https://opentelemetry.io/docs/concepts/signals/baggage/) in the OpenTelemetry docs.
