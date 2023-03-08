# Graceful shutdown of ASP.NET-based applications

Kubernetes-hosted applications should shut down smoothly and quickly when asked to do so. When they do, time necessary to upgrade deployments, to scale deployments down, and to migrate pods from overloaded/misbehaving nodes are all reduced. Even more importantly, being aware of the fact that Kubernetes requested a pod to shut down, gives the pod an opportunity to gracefully end all work in progress, improving system reliability.

## How Kubernetes requests application shutdown

Kubernetes will send `SIGTERM` signal to the main container process as means of requesting shutdown. By default the container has 30 seconds to exit gracefully before it gets killed, but this can be changed via [container lifecycle spec](https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/pod-v1/#lifecycle).

The default ASP.NET host [handles SIGTERM signal](https://learn.microsoft.com/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-7.0#ihostlifetime) and will raise the `ApplicationStopping` event when that happens. You should leverage that event to implement graceful application shutdown. How you handle the event, varies depending on what type of code is performing a long-running operation. There are two broad categories of such code: long-running requests and background processing.

## Long-running network requests

In general, it is better to avoid situations where a network requests takes a long time to process (several seconds or more). These kinds of requests tend to limit application scalability and are prone to failing due to network connection errors and client timeouts. It is better, if possible, to replace a long-running requests with a pair of related requests: the first request starts the operation, and the second request allows the client to ask for the result. If this is not feasible, you can make long-running requests more robust by judicious use of `CancellationTokens`.

Any ASP.NET request handler [can take a `CancellationToken` as a parameter](https://learn.microsoft.com/aspnet/core/fundamentals/minimal-apis/parameter-binding?view=aspnetcore-7.0#special-types). This `CancellationToken` is automatically created by ASP.NET and will be activated when the request is cancelled by the client. By default, the same `CancellationToken` will NOT be activated when `ApplicationStopping` event is raised, but we can create a "linked" `CancellationToken` to cover both cases. Here is how to do it with ASP.NET minimal API framework:

```csharp
app.Map("/longop/{value}", async Task<Results<StatusCodeHttpResult, Ok<String>>> (int value, CancellationToken requestCt, [FromServices] IHostApplicationLifetime hostLifetime) =>
{
    var effectiveCt = CancellationTokenSource.CreateLinkedTokenSource(requestCt, hostLifetime.ApplicationStopping).Token;

    try
    {
        // Simulates long processing...
        await Task.Delay(value * 1000, effectiveCt);
    } 
    catch (OperationCanceledException)
    {
        // Will happen if client cancels the request OR when the app is shutting down.
        return TypedResults.StatusCode(StatusCodes.Status503ServiceUnavailable);
    }
    
    return TypedResults.Ok($"Worked {value} seconds, looks good");
});
```

The same technique can be applied to traditional (controller-based) ASP.NET code, you just need to add `IHostApplicationLifetime` parameter to controller method(s) that might take long time to execute, construct a linked `CancellationToken`, and then use it inside the request method.

You can also change ASP.NET behavior so that the default `CancellationToken` passed to your request handlers *does get activated* when `ApplicationStopping` event occurs. This code snipped (a custom middleware) will do it:

```csharp
app.Use((httpContext, next) =>
{
    var hostLifetime = httpContext.RequestServices.GetRequiredService<IHostApplicationLifetime>();
    var originalCt = httpContext.RequestAborted;
    var combinedCt = CancellationTokenSource.CreateLinkedTokenSource(originalCt, hostLifetime.ApplicationStopping).Token;
    httpContext.RequestAborted = combinedCt;
    return next(httpContext);
});
```


## Background services

For background services ASP.NET provides [`IHostedService` interface and a `BackgroundService` base class](https://learn.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-7.0) that allow for easy handling of `ApplicationStopping` event:

- If you are using `BackgroundService` base class, the `CancellationToken` passed to [`ExecuteAsync` method](https://learn.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-7.0#backgroundservice-base-class) will be activated if `ApplicationStopping` event occurs. So as long as you are using this token, or a token derived from it, for all asynchronous calls, they will be automatically cancelled upon application shutdown.

- If you are implementing `IHostedService` interface directly, make sure you cancel all background processing when the framework calls [`StopAsync` method](https://learn.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-7.0#stopasync) on your service. This usually means you need to keep a reference, or a `CancellationTokenSource`, for all background tasks in progress, so that you can cancel them when `StopAsync` is called.

## Additional resources

(TBD)






