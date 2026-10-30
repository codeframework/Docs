# Client-Side In-Process Service Hosting

In-process hosting lets an application call a CODE Framework service as a local .NET object. The application uses the same service contract and request/response messages as a REST caller, but no HTTP connection or serialization is involved. This is useful for a local deployment or for service logic that runs within another application.

The application references `CODE.Framework.Services.Client`, the contracts library, the implementation library, and the implementation's dependencies. Keep the implementation independent of the web host so it can be reused here. See [Understanding Services in .NET Core](Understanding-Services-Core) for the overall architecture.

## Register and Configure the Service

Register services once during application startup, before concurrent calls begin:

```csharp
using CODE.Framework.Fundamentals.Configuration;
using CODE.Framework.Services.Client;

ServiceGardenLocal.AddServiceHost(typeof(CustomerService), typeof(ICustomerService));
ConfigurationSettings.Sources["Memory"].Settings["ServiceProtocol:ICustomerService"] = "InProcess";
ServiceClient.ClearSettingsCache("ServiceProtocol:ICustomerService");
```

The explicit contract argument avoids ambiguity if an implementation supports multiple interfaces. `AddServiceHost(typeof(CustomerService))` can infer the contract when the type implements one interface, or a contract plus `IServiceEvents`. It cannot infer an arbitrary choice among multiple contracts.

Registration constructs the service and stores a proxy for that contract. If no per-contract protocol setting is supported by the current configuration sources and a Memory source is present, registration supplies an `InProcess` setting there. An existing setting is not replaced. Configure the protocol explicitly when deployment selection matters, and clear a previously resolved protocol after changing it. An `app.config` source can supply the equivalent setting:

```xml
<appSettings>
  <add key="ServiceProtocol:ICustomerService" value="InProcess" />
</appSettings>
```

See [ServiceClient Configuration and Caching](ServiceClient-Configuration-and-Caching) for source precedence and cache refresh. Registering a local implementation does not force callers away from REST if their effective protocol remains `Rest`.

## Make Synchronous Calls

For the customer contract used by the Services samples:

```csharp
GetCustomersResponse response = null;
ServiceClient.Call<ICustomerService>(new ServiceCallOptions(), service =>
{
    response = service.GetCustomers(new GetCustomersRequest());
});

if (response.Success)
    Console.WriteLine($"Retrieved {response.CustomerList.Count} customers.");
else
    Console.WriteLine(response.FailureInformation);
```

The options-first call propagates failures by default and does not retry local exceptions. A missing registration is reported as a failure. This overload is part of the pending Services update and requires a package version containing it. Existing `Call<ICustomerService>(action)` code remains available with its legacy exception suppression and optional callback-wide retries; migrate with explicit exception handling. See [Service Call Options, Errors, and Retries](Service-Call-Options-Errors-and-Retries).

## Await Task-Returning Operations

If the implementation supports the task-returning operation in its contract, the pending async API can call it locally:

```csharp
var response = await ServiceClient.CallAsync<ICustomerService, GetCustomersResponse>(
    new ServiceCallOptions { CancellationToken = cancellationToken },
    service => service.GetCustomersAsync(new GetCustomersRequest()));
```

The caller supplies `cancellationToken`. `CallAsync` awaits a real `Task<TResponse>` from the registered service; it does not move synchronous service work onto a worker thread. Any synchronous work before the service returns its task still runs during invocation. Use [Calling Services Asynchronously](Calling-Services-Asynchronously) for supported operation shapes and callback patterns.

Cancellation can stop waiting for the local task, but it does not forcibly stop service execution. Synchronous local calls check cancellation before invocation and cannot interrupt running synchronous code. The token also does not interrupt arbitrary application code in the callback. Local cancellation remains observable even when exception suppression is selected.

## Service Lifetime and Shared State

`ServiceGardenLocal` constructs the implementation during registration and retains its proxy for subsequent calls. A fresh options-based calling proxy does not create a fresh underlying service. Design the implementation and its dependencies for the sharing and concurrency your application permits.

Registration state is process-wide. There is no public unregister API, and registering the same contract again does not replace its existing registration. Complete registration before normal application activity; do not use repeated registration as a per-call lifetime strategy.

In-process calls pass object references directly. There is no serialization boundary to copy a request, response, or nested data. Avoid relying on HTTP serialization to isolate mutable state when the same implementation also runs locally.

## Standard Failure Responses

The registered proxy supports `[StandardExceptionHandling]` on service interfaces, classes, and operations. For eligible exceptions, it creates the standard failure response, such as a response with `Success = false` and `FailureInformation` populated. The caller still needs to inspect that response.

Task-returning operations receive the same conversion for exceptions raised before a task is returned and for faults after an await. Without standard conversion, local exceptions preserve the underlying exception rather than exposing reflection invocation wrappers. Local cancellation remains cancellation. These task-handling corrections are part of the pending update; the synchronous local path retains its existing behavior.

A local exception is not an HTTP transport failure and is never retried by the options-based retry policy. Choosing `ExceptionBehavior.Suppress` can suppress a final non-cancellation failure but does not manufacture a standard failure response.

## Constructor Dependencies

Configure the garden's dependency mappings before registering a service. For example, using the sample user provider types:

```csharp
ServiceGardenLocal.InjectDependency<IUserProvider, FakeUserProvider>();
ServiceGardenLocal.AddServiceHost(typeof(UserService), typeof(IUserService));
```

The garden uses interface-typed constructor parameters and chooses the public constructor with the most parameters. Register mappings for the interfaces required by that constructor and any nested dependencies. Concrete dependency types used with these registration methods must satisfy their `new()` constraint.

`InjectDependency<TInterface, TConcrete>()` is an alias for `InjectTransientDependency<TInterface, TConcrete>()`: a dependency is created when an object needing it is constructed. Since registered services are retained, this does not create a dependency on every service operation. Different consuming services can receive separate transient instances.

`InjectSingletonDependency<TInterface, TConcrete>()` reuses a dependency instance across consumers. These garden mappings are separate from ASP.NET Core dependency injection and do not create request scopes. The garden does not provide the same scope/disposal lifecycle as an ASP.NET Core request.

If the implementation also implements `CODE.Framework.Services.Contracts.IServiceEvents`, the garden calls its `OnInProcessHostLaunched()` method after registration. This is a registration notification, not a per-operation client event.
