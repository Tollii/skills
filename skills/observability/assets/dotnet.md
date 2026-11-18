# C# / ASP.NET Core

The TypeScript assets translated to .NET: SDK setup, tracing at the definition, request context on every span, the wide-event middleware with `enrich()`, and the lint rules. Same rules, .NET idiom.

Written for .NET 8+ and the `OpenTelemetry.*` 1.x packages, and **not compiled**. Confirm every type and overload against the versions the project installs before relying on it.

Two things differ from TypeScript:

- **.NET's tracing API is `System.Diagnostics.Activity`.** OpenTelemetry .NET builds on it: an `Activity` is a span, an `ActivitySource` is a tracer. Only the telemetry module touches `ActivitySource`.
- **There are no function wrappers at the definition**, so the method body *is* the wrapper: an expression-bodied member that hands its work to `Traced.Run` (or `Traced.RunSync` for a synchronous method). That keeps the span at the definition, ends it on every path and records errors.

Packages: `OpenTelemetry.Extensions.Hosting`, `OpenTelemetry.Instrumentation.AspNetCore`, `OpenTelemetry.Instrumentation.Http`, `OpenTelemetry.Exporter.OpenTelemetryProtocol`, `OpenTelemetry.Exporter.Console`, plus the instrumentation package for each I/O library the project uses (`OpenTelemetry.Instrumentation.SqlClient`, `OpenTelemetry.Instrumentation.EntityFrameworkCore`, gRPC, Redis and so on).

## SDK setup: one call in `Program.cs`

Keep every line of setup in one extension method in its own project, so a service's `Program.cs` has a single line and daily work never touches telemetry. This is the pattern .NET Aspire uses: its `ServiceDefaults` project has `AddServiceDefaults()`, which calls `ConfigureOpenTelemetry()`.

- **The project already uses Aspire:** don't add a second setup. Add `Traced.SourceName`, the request-context processor and any extra instrumentation inside its `ConfigureOpenTelemetry()`, and keep the rest of this file.
- **It doesn't:** create a small shared project (`<App>.ServiceDefaults` or `<App>.Telemetry`) with the method below, and reference it from every service.

```csharp
// Program.cs
builder.AddServiceDefaults();
```

```csharp
// ServiceDefaults/Extensions.cs
public static class Extensions
{
    public static TBuilder AddServiceDefaults<TBuilder>(this TBuilder builder)
        where TBuilder : IHostApplicationBuilder
    {
        builder.ConfigureOpenTelemetry();
        return builder;
    }

    public static TBuilder ConfigureOpenTelemetry<TBuilder>(this TBuilder builder)
        where TBuilder : IHostApplicationBuilder
    {
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
                 .AddSqlClientInstrumentation()    // or EF Core, gRPC, Redis: whatever the project uses
                 .AddProcessor(new RequestContextProcessor());

                // The OTLP exporter defaults to localhost when no endpoint is set, so add it
                // only when one is. Without it, spans still record and propagate.
                if (!string.IsNullOrEmpty(otlpEndpoint)) t.AddOtlpExporter();
                else if (builder.Environment.IsDevelopment()) t.AddConsoleExporter();
            });

        return builder;
    }
}
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
        using var activity = Start(file, member);
        try
        {
            return await work();
        }
        catch (Exception ex)
        {
            Fail(activity, ex);
            throw;
        }
    }

    public static async Task Run(
        Func<Task> work,
        [CallerFilePath] string file = "",
        [CallerMemberName] string member = "")
        => await Run(async () => { await work(); return true; }, file, member);

    /// For synchronous I/O methods. A separate name, so a lambda never has to
    /// choose between the sync and async overloads.
    public static T RunSync<T>(
        Func<T> work,
        [CallerFilePath] string file = "",
        [CallerMemberName] string member = "")
    {
        using var activity = Start(file, member);
        try
        {
            return work();
        }
        catch (Exception ex)
        {
            Fail(activity, ex);
            throw;
        }
    }

    // "ReceiptService.GetReceiptAsync": file name is the class name by C# convention.
    private static Activity? Start(string file, string member)
        => Source.StartActivity($"{Path.GetFileNameWithoutExtension(file)}.{member}");

    private static void Fail(Activity? activity, Exception ex)
    {
        activity?.SetStatus(ActivityStatusCode.Error, ex.Message);
        // AddException comes with System.Diagnostics.DiagnosticSource 9: built into .NET 9,
        // and pulled in on .NET 8 by OpenTelemetry 1.10+. On older versions use
        // OpenTelemetry's RecordException extension instead.
        activity?.AddException(ex);
    }
}
```

`Run` covers `Task` and `Task<T>`, and `RunSync` covers synchronous methods. A method that returns `ValueTask` or `ValueTask<T>` needs its own overload with the same body; add it when the project has such methods on its traced layers.

At the definition:

```csharp
public Task<Receipt> GetReceiptAsync(string id) => Traced.Run(async () =>
{
    // ...
});
```

Put `Traced.Run` on business operations (services, use cases, jobs, consumers) and on I/O that no instrumentation package covers. A repository over EF Core or SqlClient needs none: the instrumentation already spans each query, and the service method that calls it gives the context.

**Attributes instead of `Traced.Run`.** Community source generators let a method carry an attribute and generate the span code at compile time: [X39.Roslyn.OpenTelemetry](https://www.nuget.org/packages/X39.Roslyn.OpenTelemetry), [Purview.Telemetry.SourceGenerator](https://www.nuget.org/packages/Purview.Telemetry.SourceGenerator), and [AutoInstrument](https://www.nuget.org/packages/AutoInstrument) (which uses interceptors). They read better, but they are small projects. Use one only if the team accepts the dependency after checking its maintenance; `Traced.Run` needs nothing.

## Request context (`RequestContext.cs`)

The middleware creates one `RequestState` per unit of work and puts it in an `AsyncLocal`. Code deeper in the request mutates that same object, so changes are visible everywhere; an `AsyncLocal` *value* set in a child method would not flow back up.

The two maps are `ConcurrentDictionary`, because one request can run branches in parallel (`Task.WhenAll`): one branch starts a span, which reads `Fields`, while another sets a field. A plain `Dictionary` throws "Collection was modified" there.

```csharp
using System.Collections.Concurrent;

public sealed class RequestState(Activity? root)
{
    public Activity? Root { get; } = root;
    public ConcurrentDictionary<string, object?> Fields { get; } = new();   // request.id, user.id, tenant.id, ...
    public ConcurrentDictionary<string, object?> Event { get; } = new();    // the wide event
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
        var escaped = false;

        try
        {
            await next(http);
        }
        catch (Exception ex)
        {
            escaped = true;
            state.Event["error.type"] = ex.GetType().Name;
            state.Event["error.message"] = ex.Message;
            throw;
        }
        finally
        {
            // An exception that escapes every handler leaves the status at 200 here;
            // the server sets 500 after this middleware has returned.
            var status = escaped && !http.Response.HasStarted ? 500 : http.Response.StatusCode;
            state.Event["status_code"] = status;
            state.Event["outcome"] = status >= 500 || state.Event.ContainsKey("error.type") ? "error" : "success";
            state.Event["duration_ms"] = Stopwatch.GetElapsedTime(started).TotalMilliseconds;
            if (status >= 500) root?.SetStatus(ActivityStatusCode.Error);

            // A list of key-value pairs as the state is written as fields by the JSON
            // console formatter and by Serilog; check the project's provider does too.
            // ToArray() on a ConcurrentDictionary is a consistent snapshot; LINQ's ToList() is not.
            logger.Log(LogLevel.Information, default, state.Event.ToArray(), null, (_, _) => "request");
        }
    }
}

public static class WideEvent
{
    /// The one function handlers call to add business context: to the event
    /// and to the root span. Values are primitives under dotted keys.
    public static void Enrich(string key, object? value)
    {
        if (RequestContext.State is not { } s) return;
        s.Event[key] = value;
        s.Root?.SetTag(key, value);
    }
}
```

**Flat keys, primitive values.** Write nested data as dotted keys (`user.id`, `error.type`), never as an object. The JSON console formatter writes an object value as its `ToString()` text, and span tags drop or stringify it. If the project's log provider does serialize objects, the event may nest, but the root span still needs the flat form.

**Swallowed errors.** Registered first, the middleware's `catch` only runs for exceptions nothing handled. `UseExceptionHandler`, `IExceptionHandler` and MVC exception filters turn the rest into responses before it sees them, so record the exception where it is handled: in the project's `IExceptionHandler`, call `WideEvent.Enrich("error.type", ...)` and `WideEvent.Enrich("error.message", ...)`. The status-code check catches anything left.

## Lint rules

- **No spans or console outside the telemetry module.** Add `Microsoft.CodeAnalysis.BannedApiAnalyzers` to every project except the telemetry one (a `Directory.Build.props` with a condition works), with a `BannedSymbols.txt`:

  ```text
  T:System.Diagnostics.ActivitySource; Trace through Traced.Run
  T:System.Console; Log through the wide event
  ```

  Treat `RS0030` as an error.
- **Traced-layer methods are traced.** No off-the-shelf analyzer checks that a method body is `Traced.Run(...)` or `Traced.RunSync(...)`. Write a small Roslyn analyzer, or a test that parses the traced layers' source with `Microsoft.CodeAnalysis.CSharp` and fails on any public method in a traced layer whose body is not a `Traced.Run` or `Traced.RunSync` call. The layers come from the conventions doc.

## Tests

Use `OpenTelemetry.Exporter.InMemory` with `WebApplicationFactory` to assert the four tracing and four wide-event cases in the references, and a fake `ILoggerProvider` that captures the event.
