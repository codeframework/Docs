# Client-Side In-Process Service Hosting

One of the truly unique features of CODE Framework is that services can be "hosted" in a mode we call "in-process". This is a way of saying that while an application can be completely built and architected with services, the end result can be compiled into a single EXE. This is useful for apps that are truly client-only applications, or at least have a mode where that may be an interesting additional deployment choice. An example could be an application that may usually connect to services in the Cloud that use a standard database server on the back-end, but there could be a second version of that app that is installed on laptops and uses a local database and nobody wants to run a separate service process. Nevertheless, the architecture of those two versions can be completely the same, down to calling the services.

The fundamental setup of such as project is the same as all other service projects. (For more info see also: [Understanding Services in CODE Framework](Understanding-Services)). However, there now is the additional option of registering the services for "hosting" in the client app. Typically, this is a WPF app (although you can use the same approach in any kind of application), in which case the setup code can be added to ```startup.cs```. Hosting itself works very similar to other self-hosting options in CODE Framework. The key is the use of the ```ServiceGardenLocal``` object:

```cs
ServiceGardenLocal.AddServiceHost(typeof(CustomerService));
ServiceGardenLocal.AddServiceHost(typeof(InvoiceService));
```

Note that this requires references to the assemblies that define the services (```CustomerService``` and ```InvoiceService``` in this example) as well as all their dependencies as well as potential configuration, such as database access settings.

> Note: This registers the service objects as local object that is to be used as a service. It doesn't truly launch the objects as services. Instead, it tells the system that these services are to be made available as highly efficient local objects that are instantiated as needed. This also means that this creates a highly efficient environment with no serialization our out-of-process invokation overhead.

Now that these objects are registered locally, the system needs to still be configured to tell all calls to the service to use the local object. This can be done using the [CODE Framework configuration system](Configuration-System). Often this means adding the following setting to your app.config file:

```xml
<xml version="1.0" encoding="utf-8" ?>
<configuration>
  <appSettings>
    <add key="ServiceProtocol:ICustomerService" value="InProcess"/>
    <add key="ServiceProtocol:IInvoiceService" value="InProcess"/>
  </appSettings>
</configuration>
```

Now, whenever a call is made to either of these services (based on their defined service contracts), the call is *not* calling to an external service, but the local object is used in-process. Here is an example call to one of these services:

```cs
ServiceClient.Call<ICustomerService>(s => {
  var response = s.GetCustomers(new GetCustomersRequest()
});
```

As you can see, this code does not change at all. Therefore, all it takes to switch from local, in-process operation to calls across the Internet, is a simple change in the config settings. 