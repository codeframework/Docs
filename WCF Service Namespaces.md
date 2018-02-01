# WCF Service Namespaces

By default, WCF services are tied to the (not very useful)  http://tempuri.org namespace. Microsoft recommends to replace that namespace with a more meaningful namespace for each service. This is usually done through a Namespace setting on the ServiceContract attribute, a Namespace setting on the ServiceBehavior attribute, and a config file setting. CODE Framework aims to make this as simple as possible.

By default, CODE Framework service contract templates put the Namespace setting into the ServiceContract attribute. By default, it sets it to “http://www.mydomain.com/service”, which is really no more useful than the tempuri one, but it is only meant to serve as a reminder that the setting needs to be set (the tempuri setting is done invisibly and is thus often forgotten). Please replace this setting to something more sensible for your needs. Here is an example:

```cs
[ServiceContract(Namespace = "http://www.epsservices.net/customer")]
public interface ICustomerService { }
```

The service implementation then also needs to define a ServiceBehavior attribute with a matching namespace setting. Unfortunately, there is no reliable way to get around the need to have this hardcoded that works in all cases, but the framework tools aim to make this step as easy as possible. If you add a new service implementation based on the service implementation template provided, the chosen service interface is automatically inspected for namespace settings and a ServiceBehavior attribute is automatically added to the new implementation, like so:

```cs
[ServiceContract(Namespace = "http://www.epsservices.net/customer")]
public class CustomerService : ICustomerService { }
```

Note: If there is no Namespace setting on the service contract, the ServiceBehavior attribute on the implementation is omitted. 

No other settings are needed in config files or elsewhere. When this service is hosted by any of the CODE Framework hosts (which are all based on the ServiceGarden class), then the hosting environment will make all further settings automatically for the service to be hosted under the specified namespace.
