# WCF Self-Hosted Service Concurrency

WCF Services allow calling a single hosted service concurrently, assuming certain settings are set correctly. This is especially critical for self-hosted services (such as standard CODE Framework SOA services hosted in a Windows Service) since those usually default to single-use (this is the WCF default in this case). Note that CODE Framework services default to allow multiple callers at the same time (default is a maximum of 10 concurrent callers). Here is a bit more information about this feature:

When CODE Framework service hosts are launched (this is generally done through a class called ServiceGarden) then the new host is automatically configured to allow multiple callers by setting the Host object’s ServiceBehaviorAttribute (found in hist.Description.Behaviors[]) to ConcurrencyMode = Multiple (and InstanceContextMode = Single). In addition, MaxConcurrentSessions and MaxConcurrentCalls on the ServiceThrottlingBehavior definition is set as well (to 10 by default). So this is the out-of-the-box behavior one gets for all services hosted by the CODE Framework infrastructure. (Note: Of course CODE Framework services are just standard WCF services that can be hosted in other environments, such as IIS, as well. In that case, the defaults of those environments apply and CODE Framework does not interfere).

There are a few additional things to know:

## Implementation Classes with ServiceBehavior Attributes

Whenever a ServiceBehavior attribute is added to a service implementation class, that attribute includes a setting (that may be set explicitly, but it is also there by default of course) for ConcurrencyMode. If that attribute is attached, CODE Framework will NOT apply the concurrency settings described above UNLESS additional settings are made in the configuration of the host application (see below).

## CODE Framework Service Concurrency Configuration Settings

CODE Framework concurrency settings can be applied in the configuration of the host application. These settings then override both the CODE Framework defaults as well as settings provided by a ServiceBehavior attribute applied to a service implementation class (see above). Note that settings can be made for all services hosted in a host environment, or on a service-by-service basis.

The following settings are available:

* **ServiceConcurrencyMode**: Can be set to “single”, “multiple”, or “reentrant”. This defines the concurrency mode of a hosted service (or all hosted services, as described below). 
* **ServiceMaxConcurrentCalls**: Defines how many maximum concurrent callers per service are allowed. The default is 10. Note that this is per service. If you host 5 different services and the max concurrent calls setting is set to 20, there could be up to 100 callers processed simultaneously. 
* **ServiceMaxConcurrentSessions**: Defines how many sessions an individual host can launch. The default is 10. Note that this is per service. If you host 5 different services and the max concurrent sessions setting is set to 20, there could be up to 100 total sessions. 

Note that each of these settings can be made for all services or per service. For instance, a ServiceMaxConcurrentCalls setting can be set to 5 to generally configure all services to allow for 5 concurrent callers. However, if you need more concurrent callers for the ICustomerService, an additional setting for ServiceMaxConcurrentCalls:ICustomerService can be added and set to a higher number, which then only applies to ICustomerService.

Settings for services are set through the standard CODE Framework settings system, which means that these settings could be specified in a number of places. A typical place however is an app.config file and it’s appSettings section, as in the following example:

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <appSettings>
    <add key="ServiceMaxConcurrentCalls" value="5"/>
    <add key="ServiceMaxConcurrentCalls:ICustomerService" value="15"/>
  </appSettings>
</configuration>
```

For more information on service configuration, see also [WCF Service Configuration](WCF%20Service%20Configuration).

 
