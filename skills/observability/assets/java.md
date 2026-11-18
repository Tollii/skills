# Java / Spring Boot

The TypeScript assets translated to Java: SDK setup, tracing at the definition, request context on every span, the wide-event filter with `enrich()`, and the lint rules. Same rules, Java idiom.

Written for Java 17+, Spring Boot 3, the OpenTelemetry Java agent 2.x and SLF4J 2, and **not compiled**. Confirm every type, property name and package against the versions the project installs before relying on it.

Two things differ from TypeScript:

- **The Java agent does the SDK setup.** It instruments servlets, HTTP clients, JDBC and messaging without code changes, and reads the standard `OTEL_*` env vars. Where an agent is not allowed (some native-image or locked-down runtimes), use the `opentelemetry-spring-boot-starter`, which covers the common libraries from inside the app.
- **Tracing at the definition is an annotation:** `@WithSpan` from `opentelemetry-instrumentation-annotations`. It names the span `Class.method`, ends it on every path and records exceptions.

## SDK setup

Start the JVM with the agent and configure it through env vars only:

```sh
java -javaagent:opentelemetry-javaagent.jar \
     -Dotel.javaagent.extensions=telemetry-extension.jar \
     -jar app.jar
```

| Variable | Value |
| --- | --- |
| `OTEL_SERVICE_NAME` | the service |
| `OTEL_RESOURCE_ATTRIBUTES` | `service.version=...,vcs.ref.head.revision=...,deployment.environment.name=...` |
| `OTEL_EXPORTER_OTLP_ENDPOINT` | the backend, when there is one |
| `OTEL_TRACES_EXPORTER` | `none` when no endpoint is set (the agent otherwise exports to localhost), or the console exporter in development; its name differs between agent versions, so check |
| `OTEL_PROPAGATORS` | `tracecontext` only. Baggage can hold user data. |
| `OTEL_TRACES_SAMPLER`, `OTEL_TRACES_SAMPLER_ARG` | the sampling decision |

Spans still record and propagate with no exporter, so trace ids keep working.

## Tracing at the definition

Dependency: `io.opentelemetry.instrumentation:opentelemetry-instrumentation-annotations`.

```java
@Repository
public class ReceiptRepository {

    @WithSpan
    public Receipt getReceipt(@SpanAttribute("receipt.id") String id) {
        // ...
    }
}
```

The agent already gives every request a server span, so controllers need no annotation; put `@WithSpan` on the I/O layers and other entry points (scheduled jobs, listeners) the agent does not cover.

## Request context

Keep the request's state in an OpenTelemetry `Context` key rather than a `ThreadLocal`: the agent carries `Context` across executors and reactive operators, so it reaches async code.

```java
public final class RequestContext {
    public static final ContextKey<RequestState> KEY = ContextKey.named("request-state");

    public static Optional<RequestState> current() {
        return Optional.ofNullable(Context.current().get(KEY));
    }

    /** A processor only sees spans as they start, so also write to the root and current span. */
    public static void set(String key, String value) {
        current().ifPresent(s -> {
            s.fields().put(key, value);
            s.root().setAttribute(key, value);
            Span.current().setAttribute(key, value);
        });
    }
}

public record RequestState(Span root, Map<String, String> fields, Map<String, Object> event) {
    public static RequestState begin(Span root) {
        return new RequestState(root, new ConcurrentHashMap<>(), new ConcurrentHashMap<>());
    }
}
```

The processor copies the fields onto every span as it starts. With the agent, register it in a small extension jar (the `-Dotel.javaagent.extensions` above) through `AutoConfigurationCustomizerProvider`:

```java
public final class RequestContextProcessor implements SpanProcessor {
    @Override public void onStart(Context parent, ReadWriteSpan span) {
        RequestState state = parent.get(RequestContext.KEY);
        if (state != null) state.fields().forEach(span::setAttribute);
    }
    @Override public boolean isStartRequired() { return true; }
    @Override public void onEnd(ReadableSpan span) {}
    @Override public boolean isEndRequired() { return false; }
}

@AutoService(AutoConfigurationCustomizerProvider.class)
public final class TelemetryCustomizer implements AutoConfigurationCustomizerProvider {
    @Override public void customize(AutoConfigurationCustomizer c) {
        c.addTracerProviderCustomizer((builder, config) ->
            builder.addSpanProcessor(new RequestContextProcessor()));
    }
}
```

## Wide event and `enrich()`

The event's contents follow the embedded `logging-best-practices` rules; these are only the lines that tie it to the trace. Give the filter the highest precedence, so its `finally` sees the final status.

```java
@Component
@Order(Ordered.HIGHEST_PRECEDENCE)
public class WideEventFilter extends OncePerRequestFilter {
    private static final Logger log = LoggerFactory.getLogger("wide-event");

    @Override
    protected void doFilterInternal(HttpServletRequest req, HttpServletResponse res, FilterChain chain)
            throws ServletException, IOException {
        Span root = Span.current();   // the agent's server span
        RequestState state = RequestState.begin(root);
        SpanContext sc = root.getSpanContext();
        state.event().put("trace_id", sc.getTraceId());
        state.event().put("span_id", sc.getSpanId());
        state.event().put("trace_sampled", sc.isSampled());
        long started = System.nanoTime();

        try (Scope ignored = Context.current().with(RequestContext.KEY, state).makeCurrent()) {
            chain.doFilter(req, res);
        } catch (Exception e) {
            state.event().put("error", Map.of("type", e.getClass().getSimpleName(), "message", String.valueOf(e.getMessage())));
            throw e;
        } finally {
            int status = res.getStatus();
            state.event().put("status_code", status);
            state.event().put("outcome", status >= 500 || state.event().containsKey("error") ? "error" : "success");
            state.event().put("duration_ms", (System.nanoTime() - started) / 1_000_000.0);
            if (status >= 500) root.setStatus(StatusCode.ERROR);

            // SLF4J 2 key-values; a JSON encoder that writes them as fields
            // (logstash-logback-encoder 7+, or Logback's JsonEncoder) is required.
            var event = log.atInfo();
            state.event().forEach(event::addKeyValue);
            event.log("request");
        }
    }
}

public final class WideEvent {
    /** The one call handlers use to add business context: to the event, and to the root span. */
    public static void enrich(String key, Object value) {
        RequestContext.current().ifPresent(s -> {
            s.event().put(key, value);
            s.root().setAttribute(key, String.valueOf(value));   // flatten nested values to dotted keys first
        });
    }
}
```

**Swallowed errors.** Spring resolves handler exceptions into responses inside `DispatcherServlet`, so the filter's `catch` rarely runs. Record the exception where it is handled: in the project's `@ControllerAdvice`, call `WideEvent.enrich("error", ...)`. The status-code check catches anything left.

## Lint rules

ArchUnit tests, run with the normal test suite:

```java
@AnalyzeClasses(packages = "com.example")
class ObservabilityRules {

    // I/O layers come from the conventions doc.
    @ArchTest
    static final ArchRule io_methods_are_traced =
        methods().that().arePublic()
            .and().areDeclaredInClassesThat().resideInAnyPackage("..repository..", "..client..")
            .should().beAnnotatedWith(WithSpan.class);

    @ArchTest
    static final ArchRule only_telemetry_opens_spans =
        noClasses().that().resideOutsideOfPackage("..telemetry..")
            .should().dependOnClassesThat().haveFullyQualifiedName("io.opentelemetry.api.trace.Tracer");

    @ArchTest
    static final ArchRule no_standard_streams = GeneralCodingRules.NO_CLASSES_SHOULD_ACCESS_STANDARD_STREAMS;
}
```

ArchUnit's `FreezingArchRule` is the baseline mechanism for an existing codebase: it records current violations and fails only on new ones.

## Tests

Use `opentelemetry-sdk-testing` (`InMemorySpanExporter`, or `OpenTelemetryExtension` for JUnit 5) and a Logback `ListAppender` to assert the four tracing and four wide-event cases in the references. Tests that rely on the agent need it on the test JVM; otherwise wire an SDK with the in-memory exporter for the test.
