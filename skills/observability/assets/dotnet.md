# C# / ASP.NET Core

The TypeScript assets translated to .NET: SDK setup, tracing at the definition, request context on every span, the wide-event middleware with `enrich()`, and the lint rules. Same rules, .NET idiom.

Written for .NET 8+ and the `OpenTelemetry.*` 1.x packages, and **not compiled**. Confirm every type and overload against the versions the project installs before relying on it.

Two things differ from TypeScript:

- **.NET's tracing API is `System.Diagnostics.Activity`.** OpenTelemetry .NET builds on it: an `Activity` is a span, an `ActivitySource` is a tracer. Only the telemetry module touches `ActivitySource`.
- **There are no function wrappers at the definition**, so the method body *is* the wrapper: an expression-bodied member that hands its work to `Traced.Run`. That keeps the span at the definition, ends it on every path and records errors.

Packages: `OpenTelemetry.Extensions.Hosting`, `OpenTelemetry.Instrumentation.AspNetCore`, `OpenTelemetry.Instrumentation.Http`, `OpenTelemetry.Exporter.OpenTelemetryProtocol`, `OpenTelemetry.Exporter.Console`.

## SDK setup (`Program.cs`)

```csharp
var otlpEndpoint = builder.Configuration["OTEL_EXPORTER_OTLP_ENDPOINT"];

builder.Services.AddOpenTelemetry()
    // OTEL_SERVICE_NAME and OTEL_RESOURCE_ATTRIBUTES are read by the default resource.
    .ConfigureResource(r => r.AddAttributes(new Dictionary<string, object>
    {
        ["service.version"] = Environment.GetEnvironmentVariable("SERVICE_VERSION") ?? "unknown",
        ["vcs.ref.head.revision"] = Environment.GetEnvironmentVariable("COMMIT_SHA") ?? "unknown",
        ["deployment.environment.name"] = builder.Environment.EnvironmentName,
    }))
    .WithTracing(t =>
    {
        t.AddSource(Traced.SourceName)
         .AddAspNetCoreInstrumentation(o => o.RecordException = true)
         .AddHttpClientInstrumentation()   // injects traceparent into outgoing calls
         .AddProcessor(new RequestContextProcessor());

        // The OTLP exporter defaults to localhost when no endpoint is set, so add it
        // only when one is. Without it, spans still record and propagate.
        if (!string.IsNullOrEmpty(otlpEndpoint)) t.AddOtlpExporter();
        else if (builder.Environment.IsDevelopment()) t.AddConsoleExporter();
    });
```

Sampling comes from `OTEL_TRACES_SAMPLER` and `OTEL_TRACES_SAMPLER_ARG`; check that the installed SDK version reads them, or set `t.SetSampler(...)` from the same config values.

## The tracing module (`Traced.cs`)

```csharp
using System.Diagnostics;
using System.Runtime.CompilerServices;

/// The only file that touches ActivitySource. Everything else traces by
/// making its body a call to Traced.Run.
public static class Traced
{
    public const string SourceName = "App";
    private static readonly ActivitySource Source = new(SourceName);

    public static async Task<T> Run<T>(
        Func<Task<T>> work,
        [CallerFilePath] string file = "",
        [CallerMemberName] string member = "")
    {
        // "ReceiptService.GetReceiptAsync": file name is the class name by C# convention.
        using var activity = Source.StartActivity($"{Path.GetFileNameWithoutExtension(file)}.{member}");
        try
        {
            return await work();
        }
        catch (Exception ex)
        {
            activity?.SetStatus(ActivityStatusCode.Error, ex.Message);
            activity?.AddException(ex);   // .NET 9+; on .NET 8 use OpenTelemetry's RecordException
            throw;
        }
    }

    public static async Task Run(
        Func<Task> work,
        [CallerFilePath] string file = "",
        [CallerMemberName] string member = "")
        => await Run(async () => { await work(); return true; }, file, member);
}
```

At the definition:

```csharp
public Task<Receipt> GetReceiptAsync(string id) => Traced.Run(async () =>
{
    // ...
});
```

## Request context (`RequestContext.cs`)

The middleware creates one `RequestState` per unit of work and puts it in an `AsyncLocal`. Code deeper in the request mutates that same object, so changes are visible everywhere; an `AsyncLocal` *value* set in a child method would not flow back up.

```csharp
public sealed class RequestState(Activity? root)
{
    public Activity? Root { get; } = root;
    public Dictionary<string, object?> Fields { get; } = new();   // request.id, user.id, tenant.id, ...
    public Dictionary<string, object?> Event { get; } = new();    // the wide event
}

public static class RequestContext
{
    private static readonly AsyncLocal<RequestState?> Current = new();
    public static RequestState? State => Current.Value;
    public static RequestState Begin(Activity? root) => Current.Value = new RequestState(root);

    /// Sets a request-context field. A processor only sees spans as they start,
    /// so also write it to the root span and the span active right now.
    public static void Set(string key, object? value)
    {
        if (State is not { } s) return;
        s.Fields[key] = value;
        s.Root?.SetTag(key, value);
        Activity.Current?.SetTag(key, value);
    }
}

public sealed class RequestContextProcessor : BaseProcessor<Activity>
{
    public override void OnStart(Activity activity)
    {
        if (RequestContext.State is not { } s) return;
        foreach (var (key, value) in s.Fields) activity.SetTag(key, value);
    }
}
```

## Wide event and `enrich()` (`WideEventMiddleware.cs`)

The event's contents follow the embedded `logging-best-practices` rules; these are only the lines that tie it to the trace. Register it first, before `UseExceptionHandler`, so its `finally` sees the status code the client actually got. The ASP.NET Core server activity already exists then; hosting starts it before any middleware.

```csharp
public sealed class WideEventMiddleware(RequestDelegate next, ILogger<WideEventMiddleware> logger)
{
    public async Task InvokeAsync(HttpContext http)
    {
        var root = Activity.Current;   // the server span from AspNetCore instrumentation
        var state = RequestContext.Begin(root);
        state.Event["trace_id"] = root?.TraceId.ToString();
        state.Event["span_id"] = root?.SpanId.ToString();
        state.Event["trace_sampled"] = root?.Recorded ?? false;
        RequestContext.Set("request.id", http.TraceIdentifier);
        var started = Stopwatch.GetTimestamp();

        try
        {
            await next(http);
        }
        catch (Exception ex)
        {
            state.Event["error"] = new { type = ex.GetType().Name, message = ex.Message };
            throw;
        }
        finally
        {
            var status = http.Response.StatusCode;
            state.Event["status_code"] = status;
            state.Event["outcome"] = status >= 500 || state.Event.ContainsKey("error") ? "error" : "success";
            state.Event["duration_ms"] = Stopwatch.GetElapsedTime(started).TotalMilliseconds;
            if (status >= 500) root?.SetStatus(ActivityStatusCode.Error);

            // A list of key-value pairs as the state is written as fields by the JSON
            // console formatter and by Serilog; check the project's provider does too.
            logger.Log(LogLevel.Information, default, state.Event.ToList(), null, (_, _) => "request");
        }
    }
}

public static class WideEvent
{
    /// The one function handlers call to add business context: to the event,
    /// and flattened to dotted keys on the root span.
    public static void Enrich(string key, object? value)
    {
        if (RequestContext.State is not { } s) return;
        s.Event[key] = value;
        s.Root?.SetTag(key, value);   // flatten nested objects to "user.id" etc. before this
    }
}
```

**Swallowed errors.** Registered first, the middleware's `catch` only runs for exceptions nothing handled. `UseExceptionHandler`, `IExceptionHandler` and MVC exception filters turn the rest into responses before it sees them, so record the exception where it is handled: in the project's `IExceptionHandler`, call `WideEvent.Enrich("error", new { type = ..., message = ... })`. The status-code check catches anything left.

## Lint rules

- **No spans or console outside the telemetry module.** Add `Microsoft.CodeAnalysis.BannedApiAnalyzers` to every project except the telemetry one (a `Directory.Build.props` with a condition works), with a `BannedSymbols.txt`:

  ```text
  T:System.Diagnostics.ActivitySource; Trace through Traced.Run
  T:System.Console; Log through the wide event
  ```

  Treat `RS0030` as an error.
- **I/O methods are traced.** No off-the-shelf analyzer checks that a method body is `Traced.Run(...)`. Write a small Roslyn analyzer, or a test that parses the I/O layers' source with `Microsoft.CodeAnalysis.CSharp` and fails on any public method whose body is not a `Traced.Run` call. The layers come from the conventions doc.

## Tests

Use `OpenTelemetry.Exporter.InMemory` with `WebApplicationFactory` to assert the four tracing and four wide-event cases in the references, and a fake `ILoggerProvider` that captures the event.
