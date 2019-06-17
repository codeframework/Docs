# ServiceClient Events

The ServiceClient class (which can be used to call services in an easy and generic fashion), fires a number of static events that can be used to add generic functionality to service calls.

One set of events has to do with "opening a channel" to a service. For instance, consider the following service operation:

```c#
ServiceClient.Call<ICustomerService>(s => {
    // Service calling code goes here...
};
```

This opens up a channel to an instance of a customer service (which implements/supports the ICustomerService interface. When this happens, the ServiceClient class fires the following events:

* BeforeChannelOpens - Fires before the channel is opened. All parameters and configuration options (typically WCF objects) are passed along with the event as parameters and can be manipulated to further configure the channel.
* BeforeEndpointAdded - Fires before a new endpoint is established on the client. Can be used to manipulate all parameters associated with creating endpoints.
* ChannelException - Fires when a channel triggers an error.

As an example (and among other things), BeforeEndpointAdded can be used to configure endpoint parameters, such as the settings on a specific binding. This example looks for TCP/IP (NetTcp) connections to be opened, and whenever that happens, the security model is set to "Transport" security:

```c#
ServiceClient.BeforeEndpointAdded += (s, e2) =>
{
    var binding = e2.Binding as NetTcpBinding;
    if (binding != null)
        binding.Security.Mode = SecurityMode.Transport;
};
```

Note that this setting needs to be matched on the server, otherwise, the server will reject the request. There is a server-side equivalent event on the ServiceGarden hosting environment object that can be used for this purpose:

```c#
ServiceGarden.BeforeEndpointAdded += (s, e) =>
{
    var binding = e.Binding as NetTcpBinding;
    if (binding != null)
        binding.Security.Mode = SecurityMode.Transport;
};
```

ServiceClient also fires events every time one calls a service. Consider this example:

```c#
ServiceClient.Call<ICustomerService>(s => {
    s.SearchCustomers(criteriaA);
    s.SearchCustomers(criteriaB);
};
```

In this example, after opening the channel (see above), two separate calls are made to the service. For each of these calls, ServiceClient fires a pair of before/after events called BeforeServiceOperationCall and AfterServiceOperationCall. These events are very useful, since they can be used to intercept and remove ALL service calls.

One example that demonstrates the power of this is that of a multi-tenant system (in which the data for multiple companies is kept in a single database and accessed through the same set of services, as is often the case in cloud-based systems). In such a scenario, most of the service calls will have to include a Company ID to make sure the user only sees the records for his or her company. This can be achieved by adding the following code to the startup code of the application:

```c#
ServiceClient.BeforeServiceOperationCall += (s, e) =>
{
    var principal = Thread.CurrentPrincipal as MyPrincipal;
    if (principal == null) return;

    foreach (var inputContract in e.InputDataContracts)
    {
        var companyIdContract = inputContract as ICompanyIdentifier;
        if (companyIdContract == null) continue;
        if (companyIdContract.CompanyId == Guid.Empty)
            companyIdContract.CompanyId = principal.CompanyId;
    }
};
```

In this example, absolutely every service call made through ServiceClient has its parameters inspected. If the parameter implements a certain interface, the event handler code checks to see if a company ID was passed along, and if not, it sets it based on the currently logged in user. This is extremely powerful, since it ensures that no developer can make the mistake of forgetting to set the right company ID on the call and potentially retrieve the wrong data and show it to the wrong user.

This is just one of many examples of where these events are useful.

Note: Handler code for the Before/After events need to be set before the ServiceClient.Call() line of code executes. This is due to the overall channel being monitored for calls. If no event handlers are set up when the channel is established, events are never fired for performance reasons.
