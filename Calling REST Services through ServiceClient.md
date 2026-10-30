# Calling REST Services through ServiceClient

`ServiceClient`, in the `CODE.Framework.Services.Client` namespace, lets C# code call a service through its contract interface. Configuration selects the REST endpoint or an in-process implementation, so ordinary calling code works with request and response objects rather than HTTP details. For the service architecture and hosting setup, see [Understanding Services in .NET Core](Understanding-Services-Core).

For new synchronous calling code, use the overload that accepts `ServiceCallOptions`. For asynchronous calls to `Task<TResponse>` contract operations, use `CallAsync`. Both default to propagating failures with no retries. The older `Call` overload remains available with its legacy exception and callback-retry behavior.

> These APIs are described for the pending Services update. The new options and async APIs require a package version containing those additions; older packages continue to support the legacy calls described below.

## Calling CODE Framework REST Services

Reference the service contracts and the CODE Framework client package. The client reads the interface's REST attributes to determine the HTTP verb, operation name, and request mapping. See [Creating REST Services](Creating-REST-Services) for how those attributes define the endpoint.

Configure the protocol and base endpoint through the [CODE Framework configuration system](Configuration-System). See [ServiceClient Configuration and Caching](ServiceClient-Configuration-and-Caching) for source precedence, JSON integration, and cache refresh. For example, an application using `app.config` can provide:

```xml
<appSettings>
  <add key="ServiceProtocol:ICustomerService" value="Rest" />
  <add key="RestServiceUrl:ICustomerService" value="https://localhost:5001/api/customers" />
</appSettings>
```

Use the actual address of your host. The base URL identifies the service; operation names and URL parameters are supplied by the contract. `Rest`, `RestJson`, and `RestHttpJson` select REST/JSON. A global `ServiceProtocol` setting can supply the default, while `ServiceProtocol:ICustomerService` overrides it for that contract.

With an `ICustomerService` contract exposing `GetCustomers(GetCustomersRequest)`, a synchronous call looks like this:

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

The example uses the customer contract from the Services samples. A response reporting `Success = false` is an application result and should be handled by the caller. Transport and serialization failures propagate from this overload and can be handled with a `try`/`catch` around the call. See [Service Call Options, Errors, and Retries](Service-Call-Options-Errors-and-Retries) for the distinction and retry configuration.

The synchronous overload blocks until the callback completes. Use [Calling Services Asynchronously](Calling-Services-Asynchronously) for nonblocking HTTP calls:

```csharp
var response = await ServiceClient.CallAsync<ICustomerService, GetCustomersResponse>(
    service => service.GetCustomersAsync(new GetCustomersRequest()));
```

The same interface-based calling model supports [in-process hosting](Hosting-In-Process-Services). Register the implementation with `ServiceGardenLocal`, then configure that contract's protocol as `InProcess`. The options-based APIs support REST/JSON and in-process execution; older WCF and other transport examples elsewhere in these docs describe historical hosting environments and do not establish support in the current client.

A call can also supply the optional `restServiceUrl` argument for a specific endpoint. Configuration remains the usual approach, and the configured protocol still determines the transport. CODE Framework's [transparent proxy generator](TransparentProxyGenerator) creates the object implementing the interface and dispatches operations to the appropriate handler.

### Existing Call Code

Existing applications may use the original overload:

```csharp
ServiceClient.Call<ICustomerService>(service =>
{
    var response = service.GetCustomers(new GetCustomersRequest());
    if (response.Success)
        Console.WriteLine($"Retrieved {response.CustomerList.Count} customers.");
});
```

This overload catches exceptions raised by the callback and suppresses final callback failures. If global legacy retries are enabled, it can execute the entire callback again, including previously successful operations. Adding `ServiceCallOptions` opts that call site into different defaults: propagation and no retries. Plan exception handling when migrating. Do not pass an async lambda to the original Action-based `Call`; use `CallAsync` and await it.

## Calling Arbitrary REST Services

> Historical API: The CallREST examples in this section describe older CODE Framework versions. The current Services client does not expose these methods. They are retained here for existing readers and links; the options and async APIs above use service contract interfaces.

Here's a basic example to call a REST service through the `ServiceClient`:

```c#
var response = ServiceClient.CallREST("http://url.com/...", "{\"x\":1}");
```

This calls an arbitrary REST service at the specified URL and posts the second parameter as JSON data to that service. This is the simplest way of calling a REST service through the `ServiceClient` class. Additional parameters can be used to change the verb (`POST`, `GET`, `DELETE`,...) and also the data format (XML or JSON). The response in this example is a string containing the data returned from the service.

Fundamentally, this is all you need to call REST services. However, there are other convenience overloads as well as more strongly typed versions of this method. (Note: Strong typing for REST services may or may not be desirable. However, if the format sent back from the service is always the same, and thus acts as if it was strongly typed, being able to use strong typing on the client is very convenient and increases code quality). Consider this example:

```c#
var request = new MyRequest();
var response = ServiceClient.CallREST<MyRequest, MyResponse>("http://url.com/...", request);
```

In this example, the second parameter is a strongly typed input parameter, and the return value will be a strongly typed response (of type `MyResponse`) or `null`, if the call failed (in that case, the `ServiceClient` class fires the usual exception events).

This begs the question: "How does one get strongly typed client-side objects for REST service?". If the REST service is built with .NET (on the server), then it may be possible to obtain the data contracts and simply use them directly. Another option is to hand-craft these contracts on the client. As long as their structure matches the response from the server, the ServiceClient class will be able to serialize and deserialize to and from these hand-crafted objects.

Sometimes, the server-side services may also be created using CODE Framework. In that case, and even more convenient approach can be used. For one, this means that the data contracts are automatically available and do not have to be hand-crafted. In addition, the operation contract is available as well. Thus, the above example could potentially be written like this:

```c#
var request = new MyRequest(); 
var response = ServiceClient.CallREST<IMyService, MyRequest, MyResponse>("MyMethod", request);
```

In this case, the service is identified by its contract passed as a generic parameter, which is less error prone and more self-explanatory than specifying a service URL or a service name as a string parameter.

Also, in this example, rather than specifying the complete URL, the URL is determined by the ServiceClient class based on the standard service configuration (see also: [WCF Service Configuration](WCF%20Service%20Configuration)). This provides more flexibility than hardcoding the URL (note that this approach is also available for arbitrary REST service calls using different overloads of the same method).
