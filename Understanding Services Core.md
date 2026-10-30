# Understanding Services in .NET Core

CODE Framework services separate a contract, its implementation, and its host. An operation accepts one request object and returns one response object. The same implementation can be hosted as REST/JSON in ASP.NET Core or registered for in-process execution; client code uses the contract interface and configuration to select the destination.

This article covers the current Services source. Its shared libraries, including contracts and the client, target .NET Standard 2.0; the ASP.NET Core host targets .NET 10. An implementation may target .NET Standard 2.0 for reuse or a compatible runtime when its dependencies require one. The host's target does not require every shared library to target .NET 10.

The article title and public URL retain their historical .NET Core names for existing links. Older WCF, WebApi, and development-host articles describe historical environments; their presence does not imply those transports are supported by the current client or ASP.NET Core package.

A typical solution contains:

1. A contracts library containing the service interface and request/response messages.
2. An implementation library referencing those contracts and the dependencies needed for its work.
3. An ASP.NET Core web application referencing the implementation and `CODE.Framework.Services.Server.AspNetCore` for REST hosting.
4. Client applications referencing the contracts and `CODE.Framework.Services.Client`.

For a deployment without HTTP, the calling application also references the implementation and registers it with `ServiceGardenLocal`. See [Client-Side In-Process Service Hosting](Hosting-In-Process-Services).

The pending Services update adds options-based client calls and `CallAsync`. Those examples require a package version containing the additions. See [Calling REST Services through ServiceClient](Calling-REST-Services-through-ServiceClient) for migration from legacy calls.

## Creating Service Contracts

Create a class library for the contracts. The framework's own shared contracts target .NET Standard 2.0, allowing compatible .NET applications to share the same message definitions without depending on the ASP.NET Core host. Target compatibility still depends on the packages and APIs used by your own library.

Reference `CODE.Framework.Services.Contracts` for REST attributes, response helpers, and file messages. The examples also use `System.ServiceModel.Primitives` for `[ServiceContract]` and `[OperationContract]`, and `System.Runtime.Serialization` for `[DataContract]` and `[DataMember]`. Those contract attributes do not require the service to be hosted in WCF.

Typical using directives are:

```csharp
using System;
using System.Runtime.Serialization;
using System.ServiceModel;
using CODE.Framework.Services.Contracts;
```

Create an interface for the service operations, as described in [Understanding CODE Framework Services](Understanding-Services). The following code snippet is a service contract example:

```cs
[ServiceContract]
public interface ICustomerService
{
    [OperationContract]
    GetAllCustomersResponse GetAllCustomers(GetAllCustomersRequest request);  

    [OperationContract]
    SearchCustomersResponse SearchCustomers(SearchCustomersRequest request);  

    [OperationContract]
    GetCustomerResponse GetCustomer(GetCustomerRequest request);  

    [OperationContract]
    SaveCustomerResponse SaveCustomer(CustomerInformation request);
}
```

Note that there is nothing special about this contract. It is exactly the same code as would have been used in the classic/full .NET Framework version of service contracts. In fact, older-style code can simply be reused and re-targetted for .NET Standard 2.0 in all but the most specialized cases.

The input parameters and return values are always based on full classes in CODE Framework (as opposed to simple value types, such as `string` or `int`). Here is an example of an input parameter (also often referred to as a "message"):

```cs
[DataContract]
public class GetCustomerRequest
{
    [DataMember(IsRequired = true)]
    public Guid CustomerId { get; set; } = Guid.Empty;
}
```

Note that these contracts create a most basic service contract. If a service was to be implemented based on this contract/interface it can be hosted in various ways, including REST/JSON. If REST/JSON was chosen as the standard, this service would default to the safest and most generic REST approach: It would be accessible through HTTP `POST` operations and it would require the request object to be posted as the payload. 

Example: If this service is hosted on somedomain.com, it could be exposed as a https://somedomain.com/customers/getcustomer endpoint, and the request would have to be posted as the following JSON payload as part of the post operation:

```json
{
  "CustomerId": "8BFA1EBC-C366-4A0E-B0FE-86B12F3A017D"
}
```

> Note: The domain of the endpoint is a result of where the service is hosted (such as `localhost` during development, or a real domain in a production deployment). The `/customers/` part of the URL can be used to map to a specific service class. This is explained further below.

It is also possible to specifically craft the endpoint to be different. In this case, it would make sense to access the GetCustomer method using a different verb and a different name. Consider this version of the GetCustomer() definition in the contract:

```cs
[OperationContract]
[Rest(Method = RestMethods.Get, Name = "")]
GetCustomerResponse GetCustomer(GetCustomerRequest request);  
```

In this case, the contract specifies that the method is to be accessible through a `GET` operation and the exposed method name is to be an empty string. This turns the potential URL into this: https://somedomain.com/customers. However, we probably also want to pass the customer ID on the URL. We can therefore also specify that the CustomerId property of the input parameter class can be passed on the URL as well:

```cs
[DataContract]
public class GetCustomerRequest
{
    [DataMember(IsRequired = true)]
    [RestUrlParameter(Mode = UrlParameterMode.Inline)]
    public Guid CustomerId { get; set; } = Guid.Empty;
}
```

> `Rest` and `RestUrlParameter` describe the HTTP mapping. In-process execution uses the same request and response objects without HTTP routing or serialization. Historical WCF hosting used different transport metadata; it is not part of the current ASP.NET Core host.

This indicates to a REST hosting environment that the ID is passed on the URL, therefore, our endpoint can now be accessed through this URL: https://somedomain.com/customers/8BFA1EBC-C366-4A0E-B0FE-86B12F3A017D. Note that since this is accessible as a GET operation, one could simply type this URL into a browser's address bar and get the expected result.

In this example, the actual method that is being invoked is exposed without a name (an empty string for a name). Remember that the `https://somedomain.com/customers` part of the endpoint URL is simply a root configuration that exposes the entire service object (see both above and below for more information). Therefore, no further name is exposed on the URL and only an input parameter is passed along, which then maps to an input object with CODE Framework will create on the fly and pass along. It is also possible to specify other names of course. For instance, one could do this:

```cs
[OperationContract]
[Rest(Method = RestMethods.Get, Name = "cust")]
GetCustomerResponse GetCustomer(GetCustomerRequest request);  
```

In this case, a potential full URL to access a customer record could be something like this: https://somedomain.com/customers/cust/8BFA1EBC-C366-4A0E-B0FE-86B12F3A017D

It is also possible to specify a manual full route for special cases, like this:

```cs
[OperationContract]
[Rest(Method = RestMethods.Get, Route = "load/customer/{CustomerId}")]
GetCustomerResponse GetCustomer(GetCustomerRequest request);  
```

This would result in an endpoint URL like this: https://somedomain.com/customers/load/customer/8BFA1EBC-C366-4A0E-B0FE-86B12F3A017D

> Note: It is not recommended to use the manually specified `Route`, unless there is a specific reason to do so, because not all CODE Framework REST hosting environments support route definitions like this. However, ASP.NET Core hosting environments *do* support it. Note also that when a manual route is specified, it is also up to the developer to specify the parameters and where they appear. `RestUrlParameter` attributes on contracts will be ignored in those cases.

Many scenarios also require multiple parameters to be passed. Consider this input parameter object/contract:

```cs
[DataContract]
public class GetCustomerRequest
{
    [DataMember]
    [RestUrlParameter(Mode = UrlParameterMode.Inline, Sequence = 1)]
    public bool IsActive { get; set; } = true;
  
    [DataMember(IsRequired = true)]
    [RestUrlParameter(Mode = UrlParameterMode.Inline)]
    public Guid CustomerId { get; set; } = Guid.Empty;
}
```

In this example, presumably, two parameters are passed to the service. One indicating the customer ID and the other indicating some sort of active flag. Both have been flagged as URL parameters for REST operations. The IsActive flag (which default to `true`) is not flagged as required, and it is set to be the second parameter through the `Sequence` setting (the default for the sequence is 0, therefore, sequence 1 is after the other property which defaults to 0). Therefore, our endpoint URL is now the following: https://somedomain.com/customers/8BFA1EBC-C366-4A0E-B0FE-86B12F3A017D/false. However, since the value is not flagged as required, we can also access this endpoint, in which case the `IsActive` flag would be set to `true` per its default: https://somedomain.com/customers/8BFA1EBC-C366-4A0E-B0FE-86B12F3A017D.

All these examples we have used so far used the HTTP `GET` verb/operation. However, URL parameter mapping also works on other verbs, such as `POST`. Consider this input contract, which still has the `IsActive` flag, but has no URL parameter mapping:

```cs
[DataContract]
public class GetCustomerRequest
{
    [DataMember]
    public bool IsActive { get; set; } = true;
  
    [DataMember(IsRequired = true)]
    [RestUrlParameter(Mode = UrlParameterMode.Inline)]
    public Guid CustomerId { get; set; } = Guid.Empty;
}
```

Now let's also assume that for some (strange) reason, we want to us a `POST` verb to access this service:

```cs
[OperationContract]
[Rest(Method = RestMethods.Post, Name = "")]
GetCustomerResponse GetCustomer(GetCustomerRequest request);  
```

We could thus now post to this endpoint yet still pass the `CustomerId` paramater on the URL: https://somedomain.com/customers/8BFA1EBC-C366-4A0E-B0FE-86B12F3A017D. However, the IsActive flag would either default to `true`, or we would have to post the following payload:

```json
{
  "IsActive": false
}
```

It is even valid to post a fully formed request object like this:

```json
{
  "CustomerId": "8BFA1EBC-C366-4A0E-B0FE-86B12F3A017D",
  "IsActive": false
}
```

However, in this case, if the `CustomerId` of the payload does not match the customer ID passed on the URL, the URL version will win out and override the one in the payload.

> Note: See the discussion about creating an ASP.NET Core hosting environment below for further details about URL patterns.

For a more complete discussion on the REST related attributes supported by CODE Framework, see [Creating REST Services](Creating-REST-Services).

## Creating a Service Implementation

Create an implementation library referencing the contracts. Use a target compatible with the host and with the APIs your service needs. Keeping the implementation independent of ASP.NET Core makes it easier to host the same service in-process.

The implementation is an ordinary class implementing the contract. Each operation receives a request message and returns a response message. For example, a response can derive from `BaseServiceResponse` to carry `Success` and `FailureInformation` alongside its operation-specific data.

A task-returning operation can perform asynchronous work in the implementation. The HTTP response contains its resolved response message, not the task. See [Calling Services Asynchronously](Calling-Services-Asynchronously) for contract examples and the distinction between asynchronous client I/O and server implementation choices.

### Standardized Exception Handling

Apply `[StandardExceptionHandling]` to an operation, contract interface, or implementation class to ask the framework's host/proxy to convert eligible exceptions into a standard failure response. For example:

```csharp
[ServiceContract]
[StandardExceptionHandling]
public interface ICustomerService
{
    [OperationContract]
    GetAllCustomersResponse GetAllCustomers(GetAllCustomersRequest request);
}
```

The response needs the standard failure properties, typically supplied by `BaseServiceResponse`, and must be constructible by the failure helper. Standard handling sets `Success` to `false` and populates `FailureInformation`. An implementation can also call `ServiceHelper.GetPopulatedFailureResponse<TResponse>(exception)` explicitly from its own catch block when it needs to control that boundary.

`ServiceHelper.ShowExtendedFailureInformation` controls whether detailed exception information appears in the response; it defaults to `false`. In an ASP.NET Core host, `app.ShowExtendedFailureInformation()` enables it. Enable it deliberately for development rather than relying on Debug versus Release compilation to choose the behavior.

In-process task handling covers both exceptions thrown before a task is returned and faults after an await. Local cancellation is not converted into a failure response. For callers, a valid failure response remains an application result, while transport errors and invalid payloads are separate failures. See [Service Call Options, Errors, and Retries](Service-Call-Options-Errors-and-Retries).

### Exposing a Ping Service

One other standard feature provided by CODE Framework that is often useful in services is to provide a `Ping` operation that allows pinging the service to see if it is running, and getting some details about the service. We could add such an operation to our `ICustomerService` like ths:

```cs
[ServiceContract]
public interface ICustomerService
{
    [OperationContract, Rest(Method = RestMethods.Get)]
    PingResponse Ping(PingRequest request);
}
```

The `PingResponse` and `PingRequest` types are provided by the standard `CODE.Framework.Services.Contracts` package, which also provides things like the `Rest` attribute, and thus is already included in the project.

The implementation of the `Ping` operation can be done easily through a helper class:

```cs
public class CustomerService : ICustomerService
{
    public PingResponse Ping(PingRequest request) => this.GetPopulatedPingResponse();
}
```

`GetPopulatedPingResponse` is a helper extension-method made available through a `ServiceHelper` static class. It collects information such as server time, server OS version, server Framework version, and version of the current assembly. This is useful in a variety of scenarios. Often, it can be useful to just be able to ping a REST service through a browser operation.

> Note: There also is a down-side to exposing this operation, as it provides additional information about the service and the environment the service runs in. This information could potentially be used by hackers to understand what your service is like. There is no inherent technical weakness in this, but one always has to decide how much information to give away to potential hackers.

### Special Case: Returning Files from a Service

Sometimes, it is desirable to return the contents of a file from a service. This could be a simple file, such as an image, or it could be a PDF or Word document, or anything else, for that matter. (Note: Files that are very large and need to be streamed - like videos - are probably not ideal for this feature). 

When returning a file, there are usually three elements that are included: 

1) The actual file bytes as a byte-array
2) The file name
3) The content-type of the file (such as `image/png`)

CODE Framework defines a special response type that can be used for such a scenario. This is provided for convenience, but also to enable some special features in some scenarios. (Specifically: RESTful services can return files as pure file responses, rather than encoding the file in JSON and base64). To take advantage of this feature, define a service operation that uses `FileResponse` as the response contract on one of the methods of your interface:

```cs
[OperationContract]
[Rest(Method = RestMethods.Get, Route = "photo/{CustomerId}")]
[RestContentType("image/png")]
FileResponse GetCustomerPhoto(GetCustomerPhotoRequest request);  
```

While the request contract still needs to be defined by the developer (and can include anything desired), the response is the pre-defined `FileResponse` type. This response type defines the three expected members, and can be used like so:


```cs
public FileResponse GetCustomerPhoto(GetCustomerPhotoRequest request)
{
    /// Real code goes here
    return new FileResponse
    {
        FileName = "ExampleImage.png", // File name as it appears to the caller, regardless of actual file name
        FileBytes = Resources.RocketMan, // Load your file bytes here
        ContentType = "image/png" // Could be changed for each returned photo if needed
    };
}
```

As you can see, this looks much like any other CODE Framework service method/operation. And in many ways it is. However, if this operation is called through a RESTful operation, the file (an image, in this example) is returned as a raw file. This is especially useful, if the service is callable using a `GET` verb. In this example, one could simply use a browser and navigate to a URL such as `http://localhost:5000/photo/123` and see the image in the browser, rather than the JSON-encoded version of the image data. In fact, one could use this response directly in an HTML `<img>` tag, like this: `src="http://localhost:5000/photo/123"`.

Note that if the same service is called from a non-REST client, or even if the REST service is called from C# using `ServiceClient.Call<>()`, the service will work as always:

```cs
ServiceClient.Call<ICustomerService>(s => {
    var response = s.GetCustomerPhoto(new GetCustomerPhotoRequest { Id = "123" });
    Console.WriteLine(response.FileName);
    Console.WriteLine($"File length: {response.FileBytes.Length}");
});
```

> Note: `FileResponse` is a sealed class that cannot be extended. This is due to the fact that the file data is used as the complete body, and the file name and content-type are encoded in the HTTP header. It is not possible to add additional properties (members) to the response contract, as there is nowhere to put that information in the REST scenario, without creating a non-standard response. If additional data elements are needed, a completely different resposne type should be created.

> Note: The definition of the interface method makes use of the `RestContentType` attribute. This is for documentation purposes only, and is used mainly by OpenAPI/Swagger (see below). It has no impact on the actual behavior. It is also possible for the response to return a different content-type (such as if photos were loaded from the database and varied in file format). This may be confusing for the person using OpenAPI or Swagger UI, but it may make sense in some scenarios. However, we recommend that the documentation attribute indicates a content-type consistent with what the service actually returns. (Note: Since the content-type property is set during runtime, and can be changed at will, it is not possible for the OpenAPI features to determine the content-type, thus requiring the need of this documentation attribute. If the attribute is not specified, a content-type of `application/x-binary` is assumed, but it will likely be different at runtime.)

## Hosting REST/JSON Services in ASP.NET Core

Create an ASP.NET Core web application targeting .NET 10 for the current hosting package. Reference `CODE.Framework.Services.Server.AspNetCore` and the service implementation library. In a web project using the standard ASP.NET Core implicit usings, `Program.cs` can configure a service like this (replace `CustomerService` with your implementation type):

```csharp
using CODE.Framework.Services.Server.AspNetCore;
using CODE.Framework.Services.Server.AspNetCore.Configuration;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddHostedServices(config =>
{
    config.Services.Clear();
    config.Services.Add(new ServiceHandlerConfigurationInstance
    {
        ServiceType = typeof(CustomerService),
        RouteBasePath = "/customers"
    });
});

var app = builder.Build();
if (app.Environment.IsDevelopment())
    app.ShowExtendedFailureInformation();

app.UseServiceHandler();
app.Run();
```

`AddHostedServices` configures hosting and registers implementation types with ASP.NET Core dependency injection. `UseServiceHandler` activates the REST handler. `AddServiceHandler` is an obsolete alias; use `AddHostedServices` in new code. Add the namespace containing your implementation and register its constructor dependencies before building the app.

Run the host with `dotnet run` or your IDE's debugger. The listening address comes from the application's hosting configuration; `/customers` is the service's base path, and the contract supplies each operation's REST mapping.

### Specifying the Hosted Services

Services can be configured in code, as above, or under `ServiceHandler` in `appsettings.json`. For configuration-based registration, use `builder.Services.AddHostedServices()` without replacing the configured services:

```json
{
  "ServiceHandler": {
    "Services": [
      {
        "ServiceTypeName": "MyCustomerService.Implementation.CustomerService",
        "AssemblyName": "MyCustomerService.Implementation.dll",
        "RouteBasePath": "/customers"
      }
    ]
  }
}
```

Substitute your implementation's full type name and assembly filename and make that assembly available to the host. Add more entries to the `Services` array to host additional contracts at their own base paths. Keep existing base paths and REST names when updating an established service: clients may already depend on those URLs.

Programmatic registration additionally allows delegates such as `OnAuthorize`. Use a delegate tied to your application's real authorization rules, for example:

```csharp
config.Services.Add(new ServiceHandlerConfigurationInstance
{
    ServiceType = typeof(CustomerService),
    RouteBasePath = "/customers",
    OnAuthorize = context => Task.FromResult(
        context.HttpContext.User.Identity?.IsAuthenticated == true)
});
```

This fragment belongs inside the `AddHostedServices` configuration callback. Configure the host's authentication and authorization pipeline for your application's requirements. The callback does not authenticate a user by itself. `JsonFormatMode` and `config.Cors` provide further JSON-format and CORS configuration; set these according to your existing wire format and deployment.

## In-Process Hosted Services

Register the same implementation locally to avoid a network round trip and serialization:

```csharp
using CODE.Framework.Fundamentals.Configuration;
using CODE.Framework.Services.Client;

ServiceGardenLocal.AddServiceHost(typeof(CustomerService), typeof(ICustomerService));
ConfigurationSettings.Sources["Memory"].Settings["ServiceProtocol:ICustomerService"] = "InProcess";
ServiceClient.ClearSettingsCache("ServiceProtocol:ICustomerService");
```

Register once during startup, before concurrent calls begin. The application needs the implementation assembly and its dependencies. Subsequent calls use the same contract-based client APIs, but the registered service and its proxy are retained in-process rather than created for each call. For async operations, the implementation must implement the task-returning contract.

See [Client-Side In-Process Service Hosting](Hosting-In-Process-Services) for lifetime, cancellation, dependency injection, and failure handling. Switching between REST/JSON and in-process execution is a supported configuration choice; this does not imply support for arbitrary additional transports.

## OpenAPI Support ("Swagger")

The ASP.NET Core hosting package can generate an OpenAPI description from the registered service contracts. Enable it before the REST handler in the host's `Program.cs`:

```csharp
app.UseOpenApiHandler();
app.UseServiceHandler();
app.Run();
```

This is the final part of the earlier host example; do not add a second `Run` call. The description is available at `/openapi.json` relative to the host's listening address. A UI or client-generation tool can consume it. Decide which environments should expose the description based on your application's deployment requirements.

### Fine-Tuning the OpenAPI definition

Supply service-level metadata when enabling the OpenAPI handler:

```csharp
app.UseOpenApiHandler(info: new OpenApiInfo
{
    Title = "Customer Service",
    Description = "Customer lookup and maintenance API",
    TermsOfService = "https://example.com/terms",
    License = "MIT",
    Contact = "support@example.com"
});
```

`OpenApiInfo` is defined in `CODE.Framework.Services.Server.AspNetCore`. Use your own service metadata. This replaces the parameterless OpenAPI registration in the previous example.

CODE Framework does an extensive job at inspecting the service contract defined in C# and exposing as much information as possible in the OpenAPI definition. This includes a deep inspection of all hosted services and service interfaces, all methods, all parameters and return values, and all properties (and sub-properties, and sub-sub-properties, and...). CODE Framework will inspect attributes put on types, methods, and properties (see below). CODE Framework also inspects potential XML documentation files, if they are used and present (typically, XML docs are only available during development time). In addition, CODE Framework takes the liberty to expose additional information it deems intersting (such as adding descriptive information about enum values, which would otherwise just be numbers).

Here is an example of a contract that takes advantage of these features:

```cs
[ServiceContract]
[Summary("Services/API related to user management")]
[Description("This service includes all kinds of operations related to user management, such as login/logout, user CRUD operations, and so on.")]
[ExternalDocumentation("For more information, please refer to the external documentation.", "https://docs.codeframework.io")]
public interface IUserService
{     
    [Summary("Signout/logout user")]
    [Description("This method logs the specified user out of the system and returns information indicating `success` or `failure`.")]
    SignoutResponse Signout(SignoutRequest request);
}

[DataContract]
public class SignoutRequest : BaseServiceRequest 
{
    [DataMember(IsRequired = true)]
    [Description("User name, typically an email address.")]
    public string UserName { get; set; } = string.Empty;
}
```

In this example, the service's interface, the single defined method, as well as the request message and its properties make use of CODE Framework attributes to document the service. (The same is true for the return type, but it is ommited for brevity in this example). `Summary`, `Description`, and `External Documentation` attributes are specifically designed to document the API's external interface.

The following attributes are supported to fine-tune the service/API definition:

* `[CODE.Framework.Services.Contracts.SummaryAttribute]` - used to display a (typically short) summary of an API element
* `[CODE.Framework.Services.Contracts.DescriptionAttribute]` - used to display a more detailed description of an API element
* `[System.ComponentModel.DescriptionAttribute]` - this is the default description attribute defined by the .NET Framework. It can be used interchangably with the CODE Framework `DescriptionAttribute`
* `[CODE.Framework.Services.Contracts.ExternalDocumentationAttribute]` - used to provide a link to external documentation. Typically used on an interface
* `[CODE.Framework.Services.Contracts.RestContentTypeAttribute]` - used to indicate a non-standard content-type as the return value of a method. Typically, REST services always return `application/json` as their content-type. However, it is possible to return other content from CODE Framework services, such as `image/png` (usually by defining a `FileResponse` return type on the method). In that case, this attribute can be used to statically document such a content-type. Note that it is not necessary to use this attribute when the method returns JSON. Also, note that this attribute is only used for documentation purposes and does not change the behavior of the method. It is also only applicable when the service is hosted in a REST environment. All other hosting options and protocols will ignore this attribute completely.
* `[CODE.Framework.Services.Contracts.FileContentAttribute]` - this attribute can be applied to a property of type `byte[]` on a data contract. It can be used to indicate that a byte-array is specifically used to transfer the contents of a file. (This information can be used by tools such as Swagger UI to change the way one can interact with the services, such as by displaying a file picker dialog)
* `[System.ObsoleteAttribute]` - an operation/method flagged as obsolete will be shown as `deprecated` in OpenAPI

> Note: If you are unfamiliar with the use of attributes in C#/.NET, here is a crash-course: Attributes are bits of meta-information that can be applied to code elements, such as interfaces, classes, method, properties, and so on. They are applied with syntax as shown in the example above. Attributes typically have formal, long names, and are defined within a namespace. However, they are applied using short-hand syntax, therefore, the `System.ObsolteAttribute` is applied to an element simply as `[Obsolete]` (assuming there is a `using System` somewhere in hour code file or in your global using statements). The same is true for CODE Framework specific attributes. Therefore, `CODE.Framework.Services.Contracts.SymmaryAttribute` is applied like this: `[Summary("This is my summary")]`. Note that attributes in different namespaces can have identical names. For this reason, the above list uses the formal name and can thus differentiate between `System.ComponentModel.DescriptionAttribute` and `CODE.Framework.Services.Contracts.DescriptionAttribute`. Depending on your using statements, this could lead to a conflict, but you can always use the long name of an attribute (although you never use the "Attribute" suffix... this is just how the C# compiler works :-) ): `[System.ComponentModel.Description("Hello!")]`.

### Swagger UI

Swagger UI can display and exercise the generated definition. If your host includes the `Swashbuckle.AspNetCore` package used by the Services sample, configure its UI to consume CODE Framework's document:

```csharp
app.UseOpenApiHandler();
app.UseSwaggerUI(options =>
{
    options.SwaggerEndpoint("/openapi.json", "Service Description");
});
app.UseServiceHandler();
app.Run();
```

Use this sequence in place of the final middleware setup in the host example. Browse to `/swagger` on the running host. CODE Framework supplies `/openapi.json`; this example uses Swashbuckle for the UI rather than a second document generator.

![](Understanding%20Services/SwaggerUI.png)

## Hosting Services in the Development Service Host

The classic development service host is a historical full .NET Framework hosting option described in [Understanding CODE Framework Services](Understanding-Services) and [Using the Service Test Host/Harness](Using-the-Service-Test-HostHarness). It is not the host used by the current ASP.NET Core example. Do not assume that the .NET 10 hosting package or runtime-specific implementation dependencies can be loaded into that older environment.

For current development, run the ASP.NET Core sample under the debugger, use its OpenAPI UI, or register the implementation in-process as appropriate.

# Calling Services from .NET Core

Reference `CODE.Framework.Services.Client` and your contracts library. Configure the endpoint through CODE Framework's configuration system; this example uses its memory source:

```csharp
using CODE.Framework.Fundamentals.Configuration;
using CODE.Framework.Services.Client;

ConfigurationSettings.Sources["Memory"].Settings["ServiceProtocol:ICustomerService"] = "Rest";
ConfigurationSettings.Sources["Memory"].Settings["RestServiceUrl:ICustomerService"] = "http://localhost:5008/api/customers";
```

Use the address and service base path configured by your host. The sample address above is illustrative. A synchronous call with explicit default options looks like this:

```csharp
GetCustomersResponse response = null;
ServiceClient.Call<ICustomerService>(new ServiceCallOptions(), service =>
{
    response = service.GetCustomers(new GetCustomersRequest());
});

if (response.Success)
    Console.WriteLine($"Retrieved {response.CustomerList.Count} customers.");
```

For a `Task<TResponse>` operation, await the async API:

```csharp
var response = await ServiceClient.CallAsync<ICustomerService, GetCustomersResponse>(
    service => service.GetCustomersAsync(new GetCustomersRequest()));
```

Options-based calls and `CallAsync` default to propagating failures with no retries. Existing `Call` code without options keeps its legacy callback exception and retry behavior. Do not pass async lambdas to the Action-based API.

See [Calling REST Services through ServiceClient](Calling-REST-Services-through-ServiceClient), [Calling Services Asynchronously](Calling-Services-Asynchronously), and [Service Call Options, Errors, and Retries](Service-Call-Options-Errors-and-Retries). [ServiceClient Configuration and Caching](ServiceClient-Configuration-and-Caching) covers configuration sources, ASP.NET Core JSON integration, and refreshing settings when destinations change.

# Services and Dependency Injection

An implementation can receive application dependencies through constructor parameters. For example, the Services sample's `UserService` implements `IUserService` and accepts an `IUserProvider`. The contract remains independent of the provider; a host decides which implementation supplies it.

## Dependency Injection and ASP.NET Core Hosts

Register constructor dependencies before building the host:

```csharp
builder.Services.AddScoped<IUserProvider, FakeUserProvider>();
builder.Services.AddHostedServices();
```

This is a fragment for the `Program.cs` setup above, using the sample provider types and services configured in `appsettings.json`. `AddHostedServices` registers service implementation types with ASP.NET Core's container. Choose dependency lifetimes appropriate to your host and application; they are separate from the lifetime of a client's generated proxy.

## Dependency Injection in In-Process Service Hosts

`ServiceGardenLocal` supplies its own constructor dependency mappings. Set them before registering services:

```csharp
ServiceGardenLocal.InjectDependency<IUserProvider, FakeUserProvider>();
ServiceGardenLocal.AddServiceHost(typeof(UserService), typeof(IUserService));
```

`InjectDependency` is an alias for `InjectTransientDependency`. Transient mappings create dependencies when the garden constructs objects; they do not create a new dependency on every service call because registered services are retained. `InjectSingletonDependency` reuses a dependency instance across consumers. These are garden-specific mappings, not ASP.NET Core request scopes. See [Client-Side In-Process Service Hosting](Hosting-In-Process-Services) for the registration and lifetime details.
