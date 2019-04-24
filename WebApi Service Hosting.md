# WebApi Service Hosting

CODE Framework Services can be hosted in a variety of ways, including WebApi. Using WebApi, one typically creates an "ASP.NET App" that contains API Controllers that have methods that are invoked according to certain naming conventions. These methods take JSON and URL parameters as input and create JSON as output. Using CODE Framework extensions, any service can be hosted using the same mechanism.

Assuming a service implementation called CustomerService (perhaps based on an ICustomerService contract/interface), one can host the service in a new controller like this:

```c#
public class CustomerController : ServiceHostController<CustomerService>
{
}
```

Note: The ServiceHostController class is defined in the CODE.Framework.Service.Server.WebApi namespace/dll, which needs to be included in your project/file.

Fundamentally, that's all that's needed. The service will now be hosted in the overall environment according to general system configuration. In particular important is the routing setup. By default, ASP.NET creates a routing setup for WebApi (typically found in the WebApiConfig.cs file in the App_Start folder) similar to this:

```c#
config.Routes.MapHttpRoute("DefaultApi", "api/{controller}/{id}", new {id = RouteParameter.Optional});
```

Using this default setup, the CustomerService can now be called using a URL like this:

```c#
http://mydomain.com/api/Customer/...
```

You can easily change the overall URL pattern in the route configuration. For instance, if you do not want the /api/ part of the URL, you can define the route like this:

```c#
config.Routes.MapHttpRoute("DefaultApi", "{controller}/{id}", new {id = RouteParameter.Optional});
```

This results in the following URL:

```c#
http://mydomain.com/Customer/...
```

Note that the default route configuration doesn't allow for many unnamed URL parameters. In fact, it only allows for one ("id"). In many REST scenarios, you may design your services to have additional unnamed URL parameters. You can add as many as you need. Here is an example:

```c#
config.Routes.MapHttpRoute("DefaultApi", "{controller}/{id}/{id2}/{id3}/{id4}/{id5}", new {id = RouteParameter.Optional, id2 = RouteParameter.Optional, id3 = RouteParameter.Optional, id4 = RouteParameter.Optional, id5 = RouteParameter.Optional});
```

Note that the full URL each service operation is available at is defined by the service itself. Any REST hosting version of the same service will result in the same URL patterns (not just WebApi). For more information on how REST services are exposed, see: [Creating REST Services](Creating%20REST%20Services)

## Camel-Case JSON Serialization

When creating WebApi services using REST and JSON, all return values from services get serialized to JSON (essentially using JSON.NET). By default, property names appear exactly like they are defined in your .NET contracts (which usually means they use ProperCase property names). However, when used from clients such as JavaScript (and others), developers often prefer camelCase property names. Using our infrastructure, it is possible to force camelCase naming, regardless of the actual property names.

One way of forcing camelCase is to specify as much in the controller:

```c#
public class CustomerController : ServiceHostController<CustomerService>
{
    public CustomerController()
    {
        JsonPropertyCaseMode = JsonPropertyCaseMode.ForceCamelCase;
    }
}
```

This allows turning this behavior on for each controller individually.

It is also possible to turn this on globally in the web.config file, but adding the following setting:

```xml
<configuration>
  <appSettings>
    <add key="JsonPropertyCaseMode" value="ForceCamelCase" />
  </appSettings>
</configuration>
```

All controllers automatically pick that up, as long as the JsonPropertyCaseMode property has not been explicitly set to anything other than "Default" (which is the default).