# Calling REST Services through ServiceClient

The ServiceClient class is used to call all kinds of services from the client. This includes REST based services (using both JSON and XML data formats). As such, it can call REST-based services hosted by CODE Framework, or completely arbitrary REST services.

## Calling CODE Framework REST Services

CODE Framework supports the creation of REST Services in various ways (for more information, see [Creating REST Services]). When calling a CODE Framework REST/JSON services through ServiceClient, there is a highly efficient and transparent mechanism to do so. (Note however that this mechanism requires the server to be built using CODE Framework. For arbitrary REST services, see below).

Using this mechanism, a call to a REST service is 100% identical to a call to any other service in CODE Framework. Here is an example:

```c#
ServiceClient.Call<ICustomerService>(s =>
{
    var response = s.GetCustomerList(new GetCustomerListRequest());
    if (response.Success)
        customerList.ItemsSource = response.Customers;
});
```

Not only does this syntax look exactly identical to any other CODE Framework service call, but it is in fact the same exact code. It simply relies on configuration to know that the service is REST-based, and it uses the contract (interface) to figure out all the details about the REST service (such as knowing the appropriate HTTP verb, or understanding URL patterns, and so on… see [Creating REST Services] for details on how things work on the server-side and thus what this needs to map to). The developer is completely shielded from such details. Furthermore, the code works entirely interchangeably with other services. For instance, the code above can be configured to use TCP/IP or in-process calls or any other supported mechanism, without requiring any code change.

To make the above call, the system simply needs the following configuration settings:

```c#
<appSettings>
  <add key="ServiceProtocol" value="Rest"/>
  <add key="RestServiceUrl:ICustomerService" value="http://localhost/dev/ICustomerService/rest/json"/>
</appSettings>
```

The ServiceProtocol has to be set to “Rest” (it can also be “RestJson” or “RestHttpJson”, which are just aliases for the same setting). Secondly, the REST Url has to be specified. In this case, it is going to a CODE Framework development host URL. But this could also be a WebApi hosted URL or something like that. If it was WebApi, a typical URL would be something like [http://www.myserver.com/api/Customer](http://www.myserver.com/api/Customer) or [http://www.myserver.com/Customer](http://www.myserver.com/Customer)

Note that this implementation is assuming that the REST service has strong structure. In other words: While REST is inherently capable of dynamic service formats, many REST services still return the same format on each call. In other words: Every time you retrieve a customer from the service, it will return the same structure, even though REST doesn’t require that. When you create a service using CODE Framework, this is automatically the case (the same is true for many business-app REST services in general). Due to this characteristic, the result from the service call is a strongly typed object. This has the great advantage that the developer does not have to work with dynamic objects in C# or perform any extra tasks to get a strongly typed, standard C# object. The result of a service call made in this manner is always a standard C# object structure that can be used easily and naturally. (See below for services that may be different in nature).

_For those interested in some technical details: We often get asked how this can even work in the first place. ServiceClient.Call() has a parameter (“s” in the example above) that is a concrete object that implements the desired interface (ICustomerService in this example), yet there is no such object/class anywhere. That is true. CODE Framework creates a proxy on the fly. In other words: CODE Framework quickly creates a class that implements the interface and contains all the appropriate infrastructure that enables strongly typed REST calls. This is often referred to as a “transparent proxy”. (In fact, WCF channels also make use of the same approach). CODE Framework has it’s own transparent proxy generator/factory to enable it to do so. In fact, this transparent proxy factory is not specific to REST. The TransparentProxyGenerator class lives in the CODE.Framework.Code.Utilities namespace and might come in handy for any other kind of transparent proxy needs._

## Calling Arbitrary REST Services

Here's a basic example to call a REST service through the ServiceClient:

```c#
var response = ServiceClient.CallREST("http://url.com/...", "{x:1}");
```

This calls an arbitrary REST service at the specified URL and posts the second parameter as JSON data to that service. This is the simplest way of calling a REST service through the ServiceClient class. Additional parameters can be used to change the verb (POST, GET, DELETE,...) and also the data format (XML or JSON). The response in this example is a string containing the data returned from the service.

Fundamentally, this is all you need to call REST services. However, there are other convenience overloads as well as more strongly typed versions of this method. (Note: Strong typing for REST services may or may not be desirable. However, if the format sent back from the service is always the same, and thus acts as if it was strongly typed, being able to use strong typing on the client is very convenient and increases code quality). Consider this example:

```c#
var request = new MyRequest();
var response = ServiceClient.CallREST<MyRequest, MyResponse>("http://url.com/...", request);
```

In this example, the second parameter is a strongly typed input parameter, and the return value will be a strongly typed response (of type MyResponse) or null, if the call failed (in that case, the ServiceClient class fires the usual exception events).

This begs the question: "How does one get strongly typed client-side objects for REST service?". If the REST service is built with .NET (on the server), then it may be possible to obtain the data contracts and simply use them directly. Another option is to hand-craft these contracts on the client. As long as their structure matches the response from the server, the ServiceClient class will be able to serialize and deserialize to and from these hand-crafted objects.

Sometimes, the server-side services may also be created using CODE Framework. In that case, and even more convenient approach can be used. For one, this means that the data contracts are automatically available and do not have to be hand-crafted. In addition, the operation contract is available as well. Thus, the above example could potentially be written like this:

```c#
var request = new MyRequest(); 
var response = ServiceClient.CallREST<IMyService, MyRequest, MyResponse>("MyMethod", request);
```

In this case, the service is identified by its contract passed as a generic parameter, which is less error prone and more self-explanatory than specifying a service URL or a service name as a string parameter.

Also, in this example, rather than specifying the complete URL, the URL is determined by the ServiceClient class based on the standard service configuration (see also: [WCF Service Configuration](WCF%20Service%20Configuration)). This provides more flexibility than hardcoding the URL (note that this approach is also available for arbitrary REST service calls using different overloads of the same method).
