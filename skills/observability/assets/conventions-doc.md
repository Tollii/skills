# Observability

<!--
Template for the project's conventions doc. Fill every section from what was
actually built, delete these comments, and keep it short. PR 1 writes the
tracing sections; PR 2 adds the wide-event sections.
-->

Last swept: <YYYY-MM-DD>

<!-- The day this skill last checked the code: the day of the setup PR, then of each maintenance pass. -->

## Where telemetry goes

<!-- Backend, how it was chosen (found, chosen, defaulted), the env vars that configure it, and what happens with none set. -->

| Variable | Purpose |
| --- | --- |
| `OTEL_EXPORTER_OTLP_ENDPOINT` | |
| `OTEL_EXPORTER_OTLP_HEADERS` | |
| `OTEL_SERVICE_NAME` | |
| `OTEL_RESOURCE_ATTRIBUTES` | |

### Viewing traces locally

<!-- The one command that starts a local OTLP viewer, and the URL to open. -->

## Tracing

<!-- Where the helper lives, one example of using it, and the lint rule that enforces it. -->

### I/O layers

<!-- The folders and naming patterns whose exported functions must be traced. The lint rule reads the same list. -->

### Opting a function out

<!-- The exact comment, and that it must give a reason. -->

### Request context

| Field | Set where |
| --- | --- |
| request id | |
| user id | |
| tenant id | |
| route | |
| <domain entity id> | |

### Sampling

<!-- The sampler and ratio for servers and for clients, where it is configured (env vars or collector), and why. "Every trace is kept" is a valid answer. -->

### Clients

<!-- Browser, mobile and edge apps: SDK, which origins get `traceparent`, where they export to, how they flush, and the consent rule. Delete if there are none. -->

## Wide events

<!-- Where the middleware and `enrich()` live, and which units of work emit an event. -->

### Schema

| Field | Meaning | Always present |
| --- | --- | --- |
| | | |

### Sampling

<!-- "Every event is kept" unless sampling was added. If it was: what is always kept, the thresholds, the rate, and the config value that changes it. -->

## Lint baselines

<!-- Only while adopting an existing codebase: each baselined rule, where its baseline lives, and its size at each sweep. Delete when empty. -->

## Gaps

<!-- Only while adopting: each difference from the target and the plan for it. Delete when empty. -->

## Deliberately left out

<!-- Runtimes that cannot be traced, and fields excluded for privacy, each with its reason. -->

## Background

- The `logging-best-practices` agent skill by Boris Tane, which sets this project's logging rules
- [Logging Sucks](https://loggingsucks.com/) by Boris Tane
- [Observability wide events 101](https://boristane.com/blog/observability-wide-events-101/) by Boris Tane
- [Canonical log lines](https://stripe.com/blog/canonical-log-lines) on the Stripe blog
