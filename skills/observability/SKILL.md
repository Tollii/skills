---
name: observability
description: "Tracing and logging for any codebase, new or existing: OpenTelemetry tracing plus wide-event logging (one event per unit of work) across servers, browsers, mobile apps and edge workers. Use to set observability up, to bring existing tracing or logging up to standard, for a periodic maintenance pass, and for single changes such as tracing a function, fixing a log call or reviewing a diff's telemetry."
license: MIT
---

# Observability

Bring a codebase to the point where any request can be explained after the fact. Two things do that: distributed tracing through OpenTelemetry, and one wide event per unit of work in place of scattered log lines.

Tracing is this skill's own subject. The logging rules are Boris Tane's `logging-best-practices` skill, embedded verbatim in [vendor/logging-best-practices](vendor/logging-best-practices/SKILL.md); this skill applies it and ties its events to the traces.

There are no separate metrics. Counts, rates and latencies come from querying wide events and spans in the backend. Add OpenTelemetry metrics only for what no unit of work carries, such as queue depth, connection-pool use or runtime memory, or where the project already has metrics and dashboards that depend on them.

## Files in this skill

Read only what the current step needs.

| File | Read it when |
| --- | --- |
| [references/backend.md](references/backend.md) | Deciding where telemetry goes, and for browser, mobile and edge code |
| [references/tracing.md](references/tracing.md) | Building or fixing tracing (PR 1), including sampling |
| [references/wide-events.md](references/wide-events.md) | Building or fixing wide events (PR 2): applying `logging-best-practices` and tying events to traces |
| [vendor/logging-best-practices](vendor/logging-best-practices/SKILL.md) | Writing or judging any logging code. Boris Tane's skill, verbatim; read all of it |
| [references/adopt.md](references/adopt.md) | The project already has tracing or logging that this skill did not build |
| [references/maintenance.md](references/maintenance.md) | Both halves exist and follow this skill |
| [references/sources.md](references/sources.md) | A rule needs its reasoning, or someone asks where it comes from |
| [assets/sdk-setup.md](assets/sdk-setup.md), [assets/tracing-helper.md](assets/tracing-helper.md), [assets/wide-event-middleware.md](assets/wide-event-middleware.md) | Writing the TypeScript pieces |
| [assets/dotnet.md](assets/dotnet.md) | Writing the same pieces in C# / ASP.NET Core |
| [assets/java.md](assets/java.md) | Writing the same pieces in Java / Spring Boot |
| [assets/conventions-doc.md](assets/conventions-doc.md) | Creating or extending the project's conventions doc |
| [scripts/survey.sh](scripts/survey.sh) | Step 1, to collect the mechanical facts quickly |

The assets are starting points to adapt to the project's framework and naming, not files to copy unchanged.

### Other languages

The rules are the same everywhere; only the idiom changes.

| Language | Setup | Traced at the definition | Request context | Lint rules |
| --- | --- | --- | --- | --- |
| TypeScript | One module loaded first | `traced()` wrapper | `AsyncLocalStorage` | ESLint or Biome rule, or a script |
| C# | `builder.AddServiceDefaults()` (Aspire's, or the same pattern) | `Traced.Run(...)` as the method body | `AsyncLocal` holding a shared object | BannedApiAnalyzers, plus a Roslyn check |
| Java | The Java agent and `OTEL_*` env vars; no code | `@WithSpan` | Baggage, copied to spans by configuration | ArchUnit tests |
| Python | `opentelemetry-instrument` or one module | `@tracer.start_as_current_span(...)` as a decorator | `contextvars` | ruff (`T20`, banned-api) |
| Go | One package called from `main` | `ctx, span := tracer.Start(ctx, ...)` and `defer span.End()` as the first lines | `context.Context` | forbidigo, or a `go/analysis` pass |

## Step 1: find out where the project stands

Read before deciding anything. Run `bash <this skill's folder>/scripts/survey.sh` from the repository root for the facts a search can find (telemetry dependencies, `console.*` counts, inline spans, earlier runs), then read the code for the rest:

- **Stack.** Languages, runtimes (Node, Bun, Deno, .NET, JVM, edge workers, serverless, a managed backend), web framework, monorepo layout, and which packages are deployed services, browser or mobile clients, or libraries.
- **Units of work.** HTTP routes, and everything else that starts work: queue consumers, scheduled jobs, webhooks, CLI commands, and on clients page loads, navigations and user actions.
- **Existing telemetry.** OpenTelemetry packages, vendor SDKs or agents (APM, error tracker, analytics, RUM), a logger library, how much `console.*` there is, and any env vars, infra config, CI secrets or docs that name a backend.
- **Identity.** What identifies the user and the tenant (org, workspace, business, account), where in a request they become known, and what the domain's main entities are.
- **Conventions.** Linter, test runner, branch and commit style, PR template, and any PR-filing skill or contributing guide in the repo.
- **Earlier runs of this skill.** A conventions doc (`docs/observability.md` by default), and branches or PRs named `observability/*`, open or merged. The conventions doc holds the decisions already made for this repository, and they stand.

For a narrow change, the conventions doc and the code around the change are enough; skip the survey.

## Step 2: pick the mode

| What you find, or what was asked | Mode |
| --- | --- |
| A single change: trace a function, fix a log call, add a field, review a diff's telemetry, answer a question | **Narrow change.** Apply the rules below and the relevant reference to that change only. Follow the conventions doc if there is one. Open no branches or PRs beyond what was asked. If the project has no setup to build on, say so and offer a full setup or adoption instead of inventing one piece. |
| No tracing, no logging worth keeping | **Full setup:** steps 3, 4, 5, 6 |
| Tracing or logging that this skill did not build: a vendor agent, inline spans, a logger with scattered lines | **Adopt:** [references/adopt.md](references/adopt.md) |
| Tracing exists and follows this skill (merged, or PR 1 open), no wide events | Resume at step 5 |
| Both halves exist and follow this skill | **Maintenance pass:** [references/maintenance.md](references/maintenance.md) |

Judge by the code, not only by the doc. A conventions doc with no tracing helper behind it means setup was never finished.

## Step 3: decide where telemetry goes

Follow [references/backend.md](references/backend.md). In short: the code stays vendor-neutral (OpenTelemetry SDK, OTLP exporter, standard `OTEL_*` env vars), an existing backend wins, otherwise ask one question with a recommendation, and if nobody answers go ahead with plain OTLP and say so at the top of PR 1. Browser, mobile and edge code follow the same rules, with the additions in its last section.

## Step 4: PR 1, tracing

Branch `observability/tracing` from the default branch. Build what [references/tracing.md](references/tracing.md) lists: SDK setup in one module, one tracing helper applied where functions are defined, request context on every span, W3C propagation in and out (clients included), a sampling decision, and a lint rule that fails the build on an untraced I/O function or a span opened anywhere else. Prove it with in-memory-exporter tests before calling it done.

## Step 5: PR 2, wide events

Branch `observability/wide-events`; the reference says what to branch from while PR 1 is still open.

Follow [references/wide-events.md](references/wide-events.md). First decide where the event lives: the root span, when the backend queries span attributes well, or a log record beside it, for log-first stacks. The logging itself is built from the embedded `logging-best-practices` skill, read in full. This skill adds only what that one cannot know: trace ids on the event, one `enrich()` function that writes to the event and the root span, the replacement of existing log calls, a lint rule against stray logging, and what must stay out of an event. Prove it with tests.

## Step 6: the conventions doc

PR 1 creates it from [assets/conventions-doc.md](assets/conventions-doc.md) and PR 2 extends it. It lives at `docs/observability.md`, or wherever the project keeps such docs. It is how the next person, and the next run of this skill, learns what was decided, so keep it short and keep it true.

## Rules that hold in every mode

- **Definitions, not call sites.** A function is traced by wrapping its definition, or by opening its span as the first statement of its own body where the language has no wrapper. Spans opened mid-function or around a call drift, and no tool can tell which ones are missing.
- **Trace what crosses a boundary.** Entry points and functions that do I/O get spans; pure computation does not. Spans cost money and attention.
- **Vendor-neutral code.** Changing backend is a config change. With no endpoint set, the app still runs.
- **Logging rules come from `logging-best-practices`, unmodified.** Do not paraphrase, trim or override them here, and do not edit the embedded copy.
- **One event per unit of work.** Handlers add fields; they do not write log lines.
- **Tests are the proof.** A clean typecheck does not show that telemetry works.
- **No setup that looks complete and emits nothing.** If part of the system cannot be instrumented, say so in the PR and in the conventions doc.
- **Earlier decisions stand** unless they no longer fit the code. The conventions doc records them.

## Pull requests and the final report

Follow the repository's own conventions for branches, commits and PR descriptions, including any PR skill or template it has. Never push to the default branch. If you cannot push or open PRs from where you are running, leave the branches committed locally and say exactly what is left to do. If an earlier PR from this skill is still open, add to it or report that it is waiting for review; do not open a competing one.

Each PR description covers what was added, how it was verified, what the reviewer needs to set (env vars, secrets), and anything skipped with the reason. PR 2 says that it is stacked on PR 1 when it is.

The final message to the user gives:

- The PR links.
- The backend decision, and whether it was found in the project, chosen by them, or defaulted.
- Where the event lives, and the tail-sampling rules.
- The env vars to set before telemetry flows.
- Anything that needs their judgment: opt-outs you were unsure about, fields you left out for privacy, parts of the system that could not be instrumented.

## Credit

The wide-event half of this skill is the work of Boris Tane: his `logging-best-practices` skill, from [Logging Sucks](https://loggingsucks.com/). The tracing half is general OpenTelemetry practice. The full list, and what each source is used for, is in [references/sources.md](references/sources.md). The skill is under the MIT licence in `LICENSE`, which also says how his files are included. When a user asks why a rule exists, point them to the source rather than guessing.
