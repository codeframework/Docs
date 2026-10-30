# Service Call Options, Errors, and Retries

`ServiceCallOptions` makes exception handling, cancellation, and retries explicit for interface-based service calls. Use it as the first argument to synchronous `ServiceClient.Call` or either `ServiceClient.CallAsync` callback form. These types belong to `CODE.Framework.Services.Client`.

> This article describes the pending Services update. The options-based overloads require a package version containing these additions. Existing calls without options retain their legacy behavior.

For endpoint configuration, see [Calling REST Services through ServiceClient](Calling-REST-Services-through-ServiceClient). For task-returning operations, see [Calling Services Asynchronously](Calling-Services-Asynchronously).

## Defaults and Options

A default options object propagates failures and makes one attempt per REST operation:

```csharp
ServiceClient.Call<ICustomerService>(new ServiceCallOptions(), service =>
{
    var response = service.GetCustomers(new GetCustomersRequest());
    Console.WriteLine(response.Success);
});
```

This synchronous call blocks its caller. It accepts synchronous contract operations; use `CallAsync` for operations returning `Task<TResponse>`.

| Option | Default | Meaning |
| --- | --- | --- |
| `ExceptionBehavior` | `ServiceExceptionBehavior.Propagate` | Propagate failures; select `Suppress` explicitly to suppress final failures other than cancellation. |
| `MaxAttempts` | `1` | Total attempts per eligible REST operation, including the first attempt. |
| `RetryDelay` | 200 milliseconds | Fixed delay between eligible attempts. |
| `CanRetryOperation` | `null` | Predicate receiving the operation's `MethodInfo`; must explicitly permit replay before any retry can occur. |
| `CancellationToken` | No cancellation | Cancels REST I/O and retry waits, or waiting for in-process tasks. |

Options are snapshotted before the callback starts. Legacy global retry settings do not affect this path. `MaxAttempts` must be at least one; `RetryDelay` must be nonnegative and no greater than `int.MaxValue` milliseconds. Invalid options and null arguments are rejected even when suppression is selected.

The options-based client supports REST/JSON and in-process execution. A missing local registration or unsupported protocol is reported as a failure rather than silently skipping the callback.

## Distinguish Failure Types

An application failure response, an HTTP failure, and an invalid payload require different handling:

| Result | Client behavior with default options |
| --- | --- |
| Valid response with `Success = false` | Returns normally. Inspect `FailureInformation` or the contract's equivalent fields. No automatic retry. |
| HTTP error status or supported connection failure | Throws `ServiceTransportException` after any permitted retries. `StatusCode` contains the HTTP status when available; `InnerException` retains the underlying error. |
| Request serialization or response deserialization failure | Throws a Newtonsoft.Json exception. These failures are never retried. Empty or JSON-null response messages are rejected. |
| In-process service exception | Preserves registered standard failure-response handling; otherwise propagates the underlying exception. Local application exceptions are not retried. |
| Cancellation | Propagates as cancellation, including when suppression is selected. |
| Application callback exception | Propagates by default; the callback is not retried. |

`ServiceTransportException` derives from `CommunicationException`. `ServiceClient.LogCommunicationErrors` enables logging of final transport failures in the options-based REST client. An HTTP failure remains a transport failure even if its body contains JSON; it is not automatically converted into an application response.

Handle transport errors around the call, separately from response processing:

```csharp
try
{
    GetCustomersResponse response = null;
    ServiceClient.Call<ICustomerService>(new ServiceCallOptions(), service =>
    {
        response = service.GetCustomers(new GetCustomersRequest());
    });

    if (!response.Success)
        Console.WriteLine(response.FailureInformation);
}
catch (ServiceTransportException exception)
{
    Console.WriteLine($"Service transport failed. HTTP status: {exception.StatusCode}");
}
```

This example only handles transport errors. Serialization failures, cancellation, and other exceptions continue to the caller. Add application-appropriate handling where needed.

## Permit Retries for Repeatable Operations

Retries require all three conditions: `MaxAttempts` greater than one, explicit permission from `CanRetryOperation`, and a narrowly classified transient transport failure. For example:

```csharp
var options = new ServiceCallOptions
{
    MaxAttempts = 3,
    RetryDelay = TimeSpan.FromMilliseconds(200),
    CanRetryOperation = method =>
        method.Name == nameof(ICustomerService.GetCustomers),
    CancellationToken = cancellationToken
};

GetCustomersResponse response = null;
ServiceClient.Call<ICustomerService>(options, service =>
{
    response = service.GetCustomers(new GetCustomersRequest());
});

Console.WriteLine(response.Success);
```

The caller supplies `cancellationToken`. The predicate permits replay of this lookup operation; the framework does not assume that every GET is safe or that every write is unsafe. Decide based on what the operation actually does. A lost response can follow successful server execution, so explicit permission does not provide deduplication or exactly-once execution.

Only the failed REST operation is retried. If the callback calls two operations and the second fails, the first is not repeated. The callback itself runs once, and the retry sends the same serialized request bytes. The same policy applies to async operations:

```csharp
var response = await ServiceClient.CallAsync<ICustomerService, GetCustomersResponse>(
    new ServiceCallOptions
    {
        MaxAttempts = 3,
        CanRetryOperation = method =>
            method.Name == nameof(ICustomerService.GetCustomersAsync),
        CancellationToken = cancellationToken
    },
    service => service.GetCustomersAsync(new GetCustomersRequest()));
```

Eligible HTTP statuses are **408, 429, 502, 503, and 504**. Eligible connection errors are `HttpRequestException` chains containing a `SocketException` with connection reset, aborted, refused, timed-out, or try-again status. Other failures are not automatically retried.

The current policy uses a fixed delay. It does not implement `Retry-After`, exponential backoff, or jitter. In-process exceptions, application failure responses, serialization failures, and callback failures are not retry candidates.

## Cancellation and Suppression

Cancellation interrupts HTTP I/O and retry delays. For synchronous in-process calls it is checked before invocation and cannot interrupt running synchronous code. For task-returning in-process calls it can stop waiting for the task without stopping the service. It does not interrupt arbitrary application callback work or guarantee that server-side side effects were prevented.

Explicit suppression is available when the application deliberately wants to absorb final call failures:

```csharp
var response = await ServiceClient.CallAsync<ICustomerService, GetCustomersResponse>(
    new ServiceCallOptions
    {
        ExceptionBehavior = ServiceExceptionBehavior.Suppress,
        CancellationToken = cancellationToken
    },
    service => service.GetCustomersAsync(new GetCustomersRequest()));

if (response == null)
    Console.WriteLine("The call did not produce a response.");
```

With suppression, synchronous `Call` returns normally after a failure and the nonresult async overload completes normally. The result-returning async overload returns `default(TResult)`, which is `null` for response classes. Suppression also covers application callback exceptions; it does not create a failure response, replay the callback, or suppress cancellation or invalid options. Do not treat normal completion under suppression as proof that an operation succeeded.

## Migrating Legacy Calls

The original `Call<TService>(action, restServiceUrl)` overload retains its existing callback behavior. It catches callback exceptions and suppresses final callback failures. Channel setup occurs before that callback catch, so this should not be read as a promise that every possible setup failure is suppressed.

If `AutoRetryFailedCalls` is enabled, the legacy path can replay the **entire callback once**, repeating successful operations and application side effects. `AutoRetryFailedCallsForExceptions` matches exact exception types. A null or empty filter retries any callback exception; the configured `AutoRetryDelay` applies in either case. These global settings do not govern options-based calls or `CallAsync`.

Migrate individual call sites by adding options and exception handling together. Start with propagation and one attempt, distinguish business failures from transport failures, then permit retries only for operations whose replay is safe. For async operations, use and await `CallAsync`; adding an async lambda to the Action-based API is not a migration to asynchronous transport.
