# Understanding Services in .NET Core

CODE Framework supports service features both in the classic .NET Framework as well as .NET Core. Fundamentally, those two environments are very similar and the creation of services is syntax compatible in both worlds, allowing for reuse of services across all flavors of .NET. However, the Core version of CODE Framework Services are the most up-to-date version and we recommend using the approach described in this topic over classic .NET Framework services.

In .NET Core, one usually creates the following service components:

1. Service contracts, implemented as .NET Standard class libraries. This allows for the (re)use of these contracts in all .NET Standard compliant versions of .NET, including full/classic .NET Framework version v4.7+, .NET Core, and others. Since .NET Standard is also supported in the full framework, it is recommended to always create service contracts in .NET Standard class libraries.
2. Service implementation, implemented as .NET Standard class libraries. Targetting .NET Standard for service implementation allows for hosting of services in all kinds of environments, ranging from classic/full .NET Framework v4.7+, to .NET Core and others (including support for both Linux and Windows). Not that in rare cases, services may be asked to perform tasks that are not (yet?) supported in .NET Core. In that case, service implementation may target other platforms, such as classic .NET Framework only. However, those cases are rare and growing rarer by the day. If in doubt, always aim to target .NET Standard.
3. To host the service, create a .NET Core ASP.NET host application, which can then run on either Windows or Linux as desired.
4. It is also possible (and in many cases recommended) to create a Docker hosting environment.
5. It is also possible to host .NET Standard based services in the development host environment as well as all other previously supported hosting environments, such as WCF hosts and older-style WebApi hosting environments as long as these environments target at least the classic .NET Framework v4.7+.

> Note: To fully understand CODE Framework Services, see [Understanding CODE Framework Services](Understanding-Services)

## Creating Service Contracts

To create a service contract project, create a new class library project that targets .NET Standard 2.0. This can be done in various tools, such as Visual Studio Code or regular Visual Studio (among others). The following screen shot shows how this is done in Visual Studio 2019:

![](Understanding%20Services/CoreFigure1.png)

> Note: It is recommended to use .NET Standard version 2.0 as the target platform, even though v2.x is available at the time of this writing. However, many environments (such as the classic .NET Framework) do not support .NET Standard 2.x and therefore, 2.x is too limited. This will probably change in the .NET Standard 3.x timeframe, but in the 2.x timeframe, we do not recommend targetting a later version unless you have some very specific reasons to do so.

> Note: We are often asked whether one should target .NET Standard or .NET Core, and there is much confusion between these two terms. .NET Standard is simply a standardized feature-set of .NET that is supported on all kinds of .NET Platforms, including .NET Core. Therefore, targetting .NET Standard implicitly targets .NET Core as well. At the same time, it creates services and contracts that can also be used in other versions of .NET. Therefore, targetting .NET Standard is the most generic way to create services.

Once you create a new .NET Standard class library project, one needs to add a few NuGet references/packages to provide some relatively minor features used by CODE Framework services. The packages that have to be added are:

* `System.ServiceModel.Primitives`
* `CODE.Framework.Services.Contracts`

The first is a package published by Microsoft that provides attributed CODE Framework services use, such as the `OperationContract` attribute. The second is a CODE Famework package that provides CODE Framework's `Rest` and `RestUrlParameter` attributes. Both of these packages are very leight-weight and do not introduce significant external dependencies and are thus suitable to be added to service contracts.

Once these packages have been added, one can create a service contract as described in the [Understanding CODE Framework Services](Understanding-Services) topic. The following code snippet is a service contract example:

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

```js  
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

> Note: `Rest` and `RestUrlParameter` attributes are only considered when a service is hosted in CODE Framework as REST. All other hosting options that expose other protocols and standards will completely ignore REST-related attributes. It does not have any ill-affects when they are present however. For instance, if this service was to be hosted as a binary TCP/IP based service in WCF, the REST-attributes would simply be ignored. It is as if they were not even present in those scenarios.

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

```js
{
  "IsActive": false
}
```

It is even valid to post a fully formed request object like this:

```js
{
  "CustomerId": "8BFA1EBC-C366-4A0E-B0FE-86B12F3A017D",
  "IsActive": false
}
```

However, in this case, if the CustomerId of the payload does not match the customer ID passed on the URL, the URL version will win out and override the one in the payload.

> Note: See the discussion about creating an ASP.NET Core hosting environment below for further details about URL patterns.

## Creating a Service Implementation

To create a service implementation, create another .NET Standard project (as described above) and add a reference to the service contract project (see also above). Since .NET Standard projects understand the dependencies of other .NET Standard projects (the service contract project in this case), it is not necessary to add any other dependencies as far as CODE Framework is concerned.

> Note: It may often to be necessary to add other dependencies depending on what the service does. For instance, if the service accesses Azure features, then such dependencies may need to be added. Or if the service accesses SQL Server, then those dependencies and packages will have to be added. Another example of further dependencies are Milos Solution Platform business objects. 

It is generally recommended to implement services in .NET Standard 2.0, just like the same recommendation applies for contracts (see above). However, in some cases, it may not be possible to stick with .NET Standard. For instance, a service implementation may have to access Windows-speficic features, or it may have to use a third-party library that is not .NET Standard compliant. In those cases, other target platforms will have to be chosen. However, one should always strive to target .NET Standard and one should only deviate from that approach if absolutely required.

The actual implementation of the service is no different from any other CODE Framework services. For instance, the CustomerService.GetAllCustomers() implementation of the ICustomerService contract could look like this:

```cs
public GetAllCustomersResponse GetAllCustomers(GetAllCustomersRequest request)
{
    try
    {
        var response = new GetAllCustomersResponse();
 
        using (var biz = new CustomerBusinessObject)
        {
            var customers = biz.GetAllCustomers();
            Mapper.Map(customers.Tables[0], response.Customers);
        }
 
        response.Success = true;
        return response;
    }
    catch (Exception ex)
    {
        LoggingMediator.Log(ex);
        return new GetAllCustomersResponse { Success = false, FailureInformation = "Generic fault..." };
    }
}
```

There is nothing special about this code being .NET Standard or .NET Core. It is 100% identical to all other CODE Framework service implementations and is only shown here for completeness.

## Hosting REST/JSON Services in ASP.NET Core

The service that has been created so far can be hosted in many different ways. One of the most interesting approaches for .NET Core developers is to host it in an ASP.NET Core application.

To do so, create a new **empty** ASP.NET Core application by first selecting this:

![](Understanding%20Services/CoreFigure2.png)

Then, after specifying a name and location for the project, pick this:

![](Understanding%20Services/CoreFigure3.png)

This creates the most basic and leight-weight ASP.NET Core environment. All we need to do now is add what we need, which is:

1. A reference to the service implementation project (which will inherently bring along all dependencies that project may have).
2. The `CODE.Framework.Services.Servver.AspNetCore` NuGet package, which provides everything needed to host CODE Framework REST/JSON services in ASP.NET Core.

Now that we have everything available that is needed, we can tell the ASP.NET Core environment to use the CODE Framework service hosting infrastructure. This can be done in the `Startup.cs` file of the project. Here is the full version of what that file needs to contain:

```cs
using CODE.Framework.Services.Server.AspNetCore;
using CODE.Framework.Services.Server.AspNetCore.Configuration;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;

namespace PressReleaseCoreService
{
    public class Startup
    {
        public Startup(IConfiguration configuration) => Configuration = configuration;

        public IConfiguration Configuration { get; }

        public void ConfigureServices(IServiceCollection services) => services.AddServiceHandler(config => { });

        public void Configure(IApplicationBuilder app, IHostingEnvironment env, ServiceHandlerConfiguration config) => app.UseServiceHandler();
    }
}
```

Some of this will already be in your Startup.cs file as it was created by default. The most important parts of this - as far as CODE Framework is concerned - are the lines that call `AddServiceHandler()` and `app.UseServiceHandler()`, which wire up the CODE Framework infrastructure. For this to work, the two using lines at the top must also be present. Voila! You are ready to host services now!

Note that some ASP.NET Core fundamentals should already be in place based on the default project setup created for you. However, just in case, make sure that the `Program.cs` file includes the following code:

```cs
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;

namespace PressReleaseCoreService
{
    public class Program
    {
        public static void Main(string[] args) => BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                   .UseStartup<Startup>()
                   .Build();
    }
}
```

### Specifying the Hosted Services

All that remains now is to specify which services are to be hosted and what the exposed endpoints should be like. There are two fundamental ways to do so: 

1. By programmatically specifiying which services are to be hosted during startup.
2. By defining the hosted services in the `appsettings.json` config file.

The most common way is through the config file, so we will cover that option first. To host the customer service, your `appsettings.json` file should contain the following:

```js
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*",
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

The only part of interest here is the `ServiceHandler` section. It defines an array of services to be hosted (you can have as many as you want). In this example, the service we host is a class called 'MyCustomerService.Implementation.PressReleaseService' (substitute your full class name) which is stored in a DLL called `MyCustomerService.Implementation.dll` (again, substitute your DLL file name here). Note that this is the same file that we added a project reference to (see above). 

The final configuration setting is the "base path", which is set to `/customers`. This explains where the "/customers/" part of a URL such as https://somedomain.com/customers/8BFA1EBC-C366-4A0E-B0FE-86B12F3A017D comes from (see above). It links that part of the endpoint URL to the `CustomerService` class as a whole. This is important to have, as it allows multiple services to be hosted in the same ASP.NET Core app. Note that it is possible to set this to just about anything you want. "/api/customers/service" is just as much a valid setting (resulting in URLs such as https://somedomain.com/api/customers/service/8BFA1EBC-C366-4A0E-B0FE-86B12F3A017D) as an empty string (resulting in URLs such as https://somedomain.com/8BFA1EBC-C366-4A0E-B0FE-86B12F3A017D).

To host multiple services, the service handler configuration could look like this:

```js
"ServiceHandler": {
  "Services": [
    {
      "ServiceTypeName": "MyCustomerService.Implementation.CustomerService",
      "AssemblyName": "MyCustomerService.Implementation.dll",
      "RouteBasePath": "/customers"
    },
    {
      "ServiceTypeName": "MyCustomerService.Implementation.InvoiceService",
      "AssemblyName": "MyCustomerService.Implementation.dll",
      "RouteBasePath": "/invoices"
    }
  ]
}
```

As mentioned above, it is also possible to configure services in source code rather than in a config file. In that case, no `ServiceHandler` section is added to the JSON configuration file. Instead, code like the following is to be added in the `ConfigureServices()` method of `Startup.cs`:

```cs
public void ConfigureServices(IServiceCollection services)
{
    services.AddServiceHandler(config =>
    {
        config.Services.Clear();

        config.Services.AddRange(new List<ServiceHandlerConfigurationInstance>
        {
            new ServiceHandlerConfigurationInstance
            {
                ServiceType = typeof(CustomerService),
                RouteBasePath = "/customers",
                JsonFormatMode = JsonFormatModes.CamelCase
            }
        });

        config.Cors.UseCorsPolicy = true;
        config.Cors.AllowedOrigins = "*";
    });
}
```

This is functionally identical to the JSON configuration example above. Except that in this case, Cors (cross-domain) settings were added as well as an additional configuration setting that specifies specifics about how JSON objects are serialized (but these are also supported in the config file).

The advantage of using the programmatic approach is that some extra features are available. Most importantly, an `OnAuthorize` delegate can be used to perform a custom authorization task. Here is an example:

```cs
public void ConfigureServices(IServiceCollection services)
{
    services.AddServiceHandler(config =>
    {
        config.Services.Clear();

        config.Services.AddRange(new List<ServiceHandlerConfigurationInstance>
        {
            new ServiceHandlerConfigurationInstance
            {
                ServiceType = typeof(CustomerService),
                RouteBasePath = "/customers",
                JsonFormatMode = JsonFormatModes.CamelCase,
                OnAuthorize = context =>
                {
                    // fake a user context 
                    context.HttpContext.User = new ClaimsPrincipal(new ClaimsIdentity(new[]
                    {
                        new Claim("Permission", "CanViewPage"),
                        new Claim(ClaimTypes.Role, "Administrators"),
                        new Claim(ClaimTypes.NameIdentifier, "Rick S. Cust")
                    }, "Basic"));

                    return Task.FromResult(true);
                }
            }
        });

        config.Cors.UseCorsPolicy = true;
        config.Cors.AllowedOrigins = "*";
    });
}
```

You are now ready to launch your application. If you are using Visual Studio, you can simply hit `F5`. If you use Visual Studio Code, you can type `dotnet run` into your console window. You can then start hitting the hosted services. If you are hosting services with a `GET` verb, you can simply use a web browser to browse to the service URLs. If you are using more complex REST services, use a tool such as Fiddler or PostMan (among others) to give your services a spin.

> Note: When services are hosted in .NET Core as explained here, debugging is fully supported. It is possible to simply add a breakpoing to the service implementaiton code, even if the ASP.NET Core application is run as a Linux-based application.
 
## Hosting Services in the Development Service Host

Development host hosting is supported in exactly the same way it has always been in CODE Framework. The development service host if a full-framework WinForms/WPF application that hosts the services in a way that is convenient for development. For more information, see [Understanding CODE Framework Services](Understanding-Services).

Note that since the development host is a classic .NET Framework application, it does not understand dependencies .NET Standard and .NET Core projects do. Make sure you add all the NuGet packages and project references to this project manually. For instance, if you add a reference to the service implementation project, make sure to also add NuGet packages such as `System.ServiceModel.Primitives` and `CODE.Framework.Services.Contracts` manually.
