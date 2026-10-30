# Calling Services Asynchronously

`ServiceClient.CallAsync` calls service operations that return `Task<TResponse>` and awaits the callback using the service. It supports REST/JSON and in-process hosting. REST calls use asynchronous HTTP I/O, so waiting for the response does not require a blocked worker thread. The client API belongs to `CODE.Framework.Services.Client`.

> This article describes the pending Services update. Use a package version containing `CallAsync`; it is not available in older releases.

Configure the service as described in [Calling REST Services through ServiceClient](Calling-REST-Services-through-ServiceClient). Protocol and endpoint configuration work the same way for synchronous and asynchronous callers.

## Define an Async Contract Operation

The service still receives one request message and returns one response message. On the .NET interface, the asynchronous operation wraps its response in a task:

```csharp
using System.ServiceModel;
using System.Threading.Tasks;
using CODE.Framework.Services.Contracts;

[ServiceContract]
public interface ICustomerService
{
    [OperationContract]
    [Rest(Method = RestMethods.Get, Name = "Customers2")]
    Task<GetCustomersResponse> GetCustomersAsync(GetCustomersRequest request);
}
```

`GetCustomersRequest` and `GetCustomersResponse` are ordinary message classes. This operation matches the async customer operation in the Services samples. Keep existing REST names and routes when adapting a contract: the C# async method name does not require changing the public endpoint.

Only the response message is serialized over HTTP. The task is a .NET execution mechanism and never becomes part of the JSON payload. A client contract returning `Task<TResponse>` can call a synchronous REST implementation when the REST mapping and request/response formats agree; asynchronous client I/O does not require asynchronous server code.

For in-process hosting, the implementation must implement the task-returning contract. It can use `async`/`await` or return an existing task directly. ASP.NET Core hosting also recognizes task-returning implementations that forward a task without the `async` keyword.

## Await a Callback

Use the callback overload when you want to perform application work after receiving a response:

```csharp
await ServiceClient.CallAsync<ICustomerService>(async service =>
{
    var response = await service.GetCustomersAsync(new GetCustomersRequest());
    if (response.Success)
        Console.WriteLine($"Retrieved {response.CustomerList.Count} customers.");
    else
        Console.WriteLine(response.FailureInformation);
});
```

`CallAsync` completes after the callback completes, including application work that the callback awaits. Await every service task whose completion matters to the call. Fire-and-forget work is not tracked.

> Do not pass an async lambda to `ServiceClient.Call`, whose callback is an `Action`. That produces an async-void callback, which the synchronous call cannot await or reliably observe for failures.

The callback starts on the calling context. Its own awaits capture that context normally; library-internal awaits avoid capturing it. This allows UI callers to use their usual async event-handler patterns without wrapping service calls in `Task.Run`.

## Return a Result

The second generic type identifies the callback's result:

```csharp
var response = await ServiceClient.CallAsync<ICustomerService, GetCustomersResponse>(
    service => service.GetCustomersAsync(new GetCustomersRequest()));

if (response.Success)
    Console.WriteLine($"Retrieved {response.CustomerList.Count} customers.");
```

The callback can also await a response and return a derived application result, such as a count. In all cases, the service operation itself must return `Task<TResponse>`.

## Cancellation and Call Options

Both callback forms have an options-first overload. The following assumes the caller supplies `cancellationToken`:

```csharp
var options = new ServiceCallOptions
{
    CancellationToken = cancellationToken
};

try
{
    var response = await ServiceClient.CallAsync<ICustomerService, GetCustomersResponse>(
        options,
        service => service.GetCustomersAsync(new GetCustomersRequest()));
    Console.WriteLine(response.Success);
}
catch (OperationCanceledException)
{
    Console.WriteLine("The call was canceled.");
}
```

The token can cancel HTTP I/O, retry delays, and waiting for an in-process service task. It does not cancel arbitrary application code in the callback. In-process cancellation stops waiting without forcibly stopping the underlying service. Canceling an HTTP call likewise does not guarantee that server-side work stops or that a write was not performed.

Options are copied when the call starts. Changing the options object afterward does not reconfigure an ongoing call. See [Service Call Options, Errors, and Retries](Service-Call-Options-Errors-and-Retries) for operation-specific retries and failure handling.

## Errors and Standard Failure Responses

Without explicit suppression, callback exceptions and call failures propagate to the code awaiting `CallAsync`. Legacy global retry settings do not affect this API, and no retries occur by default.

A normal response with `Success = false` remains a normal response. When standard exception handling is configured, in-process task operations convert eligible exceptions both during invocation and after an await into the configured failure response. Without that conversion, local faults preserve their original exceptions. Cancellation remains cancellation in the in-process path. REST failures follow the host's response and HTTP status behavior; client cancellation is observed independently.

With `ExceptionBehavior = ServiceExceptionBehavior.Suppress`, final failures other than cancellation are suppressed. The nonresult overload completes normally; the result overload returns `default(TResult)`. For response classes that means `null`, so callers selecting suppression must handle a missing result. Invalid options still propagate. Suppression can also hide exceptions from application callback code, so propagation is usually the clearer choice.

## Supported Operation Shapes and File Responses

Operations invoked through `CallAsync` must return `Task<TResponse>`. Bare `Task`, `ValueTask`, other awaitables, and synchronous operations are outside this API's supported operation shapes. An interface may contain both synchronous and task-returning methods, but invoking a synchronous member through the async channel is rejected. Use an options-based synchronous `Call` for synchronous members.

A contract operation returning `Task<FileResponse>` uses the binary response path. Over REST, the client returns the file bytes, content type, and filename from the HTTP response rather than trying to deserialize file bytes as JSON. `FileResponse` belongs to `CODE.Framework.Services.Contracts`.

The current implementation buffers HTTP responses, including files. Asynchronous execution does not imply streaming file downloads.
