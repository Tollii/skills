# Java / Spring Boot

The TypeScript assets translated to Java: SDK setup, tracing at the definition, request context on every span, the wide-event filter with `enrich()`, and the lint rules. Same rules, Java idiom.

Written for Java 17+, Spring Boot 3, the OpenTelemetry Java agent 2.x and SLF4J 2, and **not compiled**. Confirm every type, property name and package against the versions the project installs before relying on it.

Two things differ from TypeScript:

- **Setup is not code.** Pick one of three paths; the rest of this file is the same for all of them except where noted:
  - **The Java agent (the default).** The OpenTelemetry docs recommend it for Spring Boot. It instruments servlets, HTTP clients, JDBC and messaging without code changes and is configured only through `OTEL_*` env vars, so the application has no telemetry setup at all.
  - **The community `opentelemetry-spring-boot-starter`.** For GraalVM native images, where the agent doesn't work, when agent startup time matters, or when the team wants configuration in `application.yml`.
  - **Spring Boot 4's own `spring-boot-starter-opentelemetry`.** The Spring-native path, built on Micrometer Observation. Two catches: it also pushes OTLP metrics to localhost by default, which fails with no collector unless `management.otlp.metrics.export.enabled=false`, and its property names changed from Spring Boot 3. Check the reference docs for the exact keys of the version in use.
- **Tracing at the definition is an annotation:** `@WithSpan` from `opentelemetry-instrumentation-annotations`. It names the span `Class.method`, ends it on every path and records exceptions.

## SDK setup

Start the JVM with the agent and configure it through env vars only:

```sh
java -javaagent:opentelemetry-javaagent.jar -jar app.jar
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
@Service
public class ReceiptService {

    @WithSpan
    public Receipt approve(@SpanAttribute("receipt.id") String id) {
        // ...
    }
}
```

The agent already gives every request a server span and every JDBC, HTTP-client and messaging call its own span, so controllers and repositories need no annotation. Put `@WithSpan` on business operations (services, use cases), scheduled jobs and listeners, and on I/O no instrumentation covers.

With either starter, `@WithSpan` also needs `spring-boot-starter-aop`, and it works only on Spring beans: a method that calls another method in the same class gets no span for the second one. Under the agent, annotations work on any class. `otel.instrumentation.annotations.enabled=false` turns them off without a code change.

## Request context

Keep the request's state in an OpenTelemetry `Context` key rather than a `ThreadLocal`: the agent carries `Context` across executors and reactive operators, so it reaches async code.

```java
public final class RequestContext {
    public static final ContextKey<RequestState> KEY = ContextKey.named("request-state");

    public static Optional<RequestState> current() {
        return Optional.ofNullable(Context.current().get(KEY));
    }

    /** Writes the field to the root span and to the span that is current right now. */
    public static void set(String key, String value) {
        if (value == null) return;   // ConcurrentHashMap rejects nulls; an absent field says the same
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

`set()` reaches the root span and the span current at that moment. To put the fields on every span that starts later as well, use baggage and the baggage span processor, which the agent and the community starter both include and switch on through configuration:

```sh
OTEL_JAVA_EXPERIMENTAL_SPAN_ATTRIBUTES_COPY_FROM_BAGGAGE_INCLUDE=request.id,user.id,tenant.id
OTEL_PROPAGATORS=tracecontext
```

The second line matters: with only `tracecontext` propagated, baggage never leaves the process, so user and tenant ids are not sent to other services. Baggage is immutable, so the fields go in with a scope that wraps the rest of the request. Do it in a filter placed after authentication, where the identity is known:

```java
@Component
@Order(SecurityProperties.DEFAULT_FILTER_ORDER + 1)   // after Spring Security
public class RequestContextFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal(HttpServletRequest req, HttpServletResponse res, FilterChain chain)
            throws ServletException, IOException {
        var baggage = Baggage.current().toBuilder();
        put(baggage, "request.id", req.getRequestId());
        put(baggage, "user.id", currentUserId());     // from the security context
        put(baggage, "tenant.id", currentTenantId());
        try (Scope ignored = baggage.build().makeCurrent()) {
            chain.doFilter(req, res);
        }
    }

    private static void put(BaggageBuilder b, String key, String value) {
        if (value == null) return;
        b.put(key, value);
        RequestContext.set(key, value);   // the root span started before this filter
    }
}
```

A field that only becomes known deeper in the request (an entity id) goes through `RequestContext.set()` alone; it reaches the root span and the current span, which is usually enough. With Spring Boot 4's own starter, check its docs for the baggage-to-span setting before relying on it; if there is none, use `set()` alone.

Do not write a span processor in an agent extension jar for this: the extension is loaded by the agent's class loader, where a `ContextKey` created in application code is not visible, so it would copy nothing and report no error.

## Wide event and `enrich()`

The event's contents follow the embedded `logging-best-practices` rules; these are only the lines that tie it to the trace. Give the filter the highest precedence, so its `finally` sees the final status. With a starter, the server span is opened by the starter's own filter, so place this one after that.

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
        boolean escaped = false;

        try (Scope ignored = Context.current().with(RequestContext.KEY, state).makeCurrent()) {
            chain.doFilter(req, res);
        } catch (Exception e) {
            escaped = true;
            state.event().put("error.type", e.getClass().getSimpleName());
            state.event().put("error.message", String.valueOf(e.getMessage()));
            throw e;
        } finally {
            // An exception that escapes leaves the status at 200 here; the container sets 500 afterwards.
            int status = escaped && !res.isCommitted() ? 500 : res.getStatus();
            state.event().put("status_code", status);
            state.event().put("outcome", status >= 500 || state.event().containsKey("error.type") ? "error" : "success");
            state.event().put("duration_ms", (System.nanoTime() - started) / 1_000_000.0);
            if (status >= 500) root.setStatus(StatusCode.ERROR);

            // SLF4J 2 key-values; a JSON encoder that writes them as fields
            // (logstash-logback-encoder 7+, or Logback's JsonEncoder) is required.
            // With a logged event, apply the tail-sampling rule here (references/wide-events.md#sampling).
            // When the root span is the event, skip the log line: the root span already holds every field.
            var event = log.atInfo();
            state.event().forEach(event::addKeyValue);
            event.log("request");
        }
    }
}

public final class WideEvent {
    /** The one call handlers use to add business context: to the event, and to the root span. */
    public static void enrich(String key, Object value) {
        if (value == null) return;   // ConcurrentHashMap rejects nulls; an absent field says the same
        RequestContext.current().ifPresent(s -> {
            s.event().put(key, value);
            s.root().setAttribute(key, String.valueOf(value));   // flat keys, primitive values: "user.id", not a map
        });
    }
}
```

**Swallowed errors.** Spring resolves handler exceptions into responses inside `DispatcherServlet`, so the filter's `catch` rarely runs. Record the exception where it is handled: in the project's `@ControllerAdvice`, call `WideEvent.enrich("error.type", ...)` and `WideEvent.enrich("error.message", ...)`. The status-code check catches anything left.

**Async requests.** For `DeferredResult`, `Callable`, SSE and other async dispatches, the filter's `finally` runs when the first thread returns, before the response is finished, and `OncePerRequestFilter` skips the async dispatch by default. Status and duration are then wrong. If the project uses async requests, emit the event from an `AsyncListener.onComplete` registered when `req.isAsyncStarted()` is true, and only emit in `finally` otherwise.

## Lint rules

ArchUnit tests, run with the normal test suite:

```java
@AnalyzeClasses(packages = "com.example")
class ObservabilityRules {

    // Traced layers come from the conventions doc: business operations, plus I/O no instrumentation covers.
    @ArchTest
    static final ArchRule business_operations_are_traced =
        methods().that().arePublic()
            .and().areDeclaredInClassesThat().resideInAnyPackage("..service..", "..usecase..")
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
