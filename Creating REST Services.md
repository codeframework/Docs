# Creating REST Services with CODE Framework

CODE Framework allows for the creation of various types of services, including REST services with JSON data structures.

Note that in the CODE Framework paradigm, services are created in a very generic fashion. The same service may then be hosted in various ways, such as WCF, ASP.NET, WebApi, or .NET Core. However, the way the service is created doesn't change.

One of the unique features of CODE Framework is that services can be created that are hosted as REST as well as binary (and other) standards. This is unusual, because most services in .NET are either created for WCF, or for another technology such as WebApi. The approaches in doing so are drastically different. Not so if you use CODE Framework.

Let's consider a basic service definition:

```c#
public interface ICustomerService
{
    [OperationContract]
    GetCustomerListResponse GetCustomerList(GetCustomerListRequest request);

    [OperationContract]
    GetCustomerResponse GetCustomer(GetCustomerRequest request);

    [OperationContract]
    SaveCustomerResponse SaveCustomer(SaveCustomerRequest request);
}
```

This service definition looks very much like a WCF service. And it is true that this service *can* be implemented as a WCF service and hosted as a SOAP, TCP/IP, or other service. However, there is nothing in this service that forces it to be WCF hosted. In CODE Framework, this very service can be hosted in WebApi as a REST service (see also: [WebApi Service Hosting](WebApi%20Service%20Hosting)) or a [.NET Core Service](Understanding-Services-Core) (even deployed to Docker and all kinds of other things).

The question is: What would a service like this look like if accessed using REST? 

Well, that's debatable. There is no automatic answer that leads to a really satisfying result from a REST point-of-view. However, the service could be hosted in a way where every method becomes accessible using an HTTP `POST` operation, posting the input message ("parameter") as the payload in JSON format. In fact, that is the default CODE Framework applies. Hence if this service is hosted as REST, either in one of the CODE Framework hosts (development or runtime), in WebApi, or in .Net Core, all these operations will be REST callable. Not the most elegant of solutions (and we will improve on this if you keep reading), but at least it works right out of the box.

## Designing for REST

We can improve on the above scenario by giving CODE Framework some hints as to how to better expose the service. Consider the following operation for instance:

```c#
[OperationContract]
GetCustomerResponse GetCustomer(GetCustomerRequest request);
```

The GetCustomer() operation is designed to return a customer by some kind of identifier (such as the name or the customer ID which is part of the request parameter/object/message). By default, it would be exposed on a URL like this (which you could post the input message/parameter/object to):

```
http://mydomain.com/Customer/GetCustomer
```

This URL has to be accessed using an HTTP `POST` operation and the "request" parameter needs to be posted to the URL as the HTTP payload. (This can be done programmatically, or - to try it out - using a tool such as Postman or Fiddler). 

For completeness, here is the structure of the request parameter (the "input message"):

```c#
[DataContract]
public class GetCustomerRequest
{
    [DataMember](DataMember)
    public string CustomerId { get; set; }
}
```

> Note: The DataContract and DataMember attributes are often considered to be "WCF Attributes". However, they are really just serialization attributes and are perfectly fine to use, even if the service has nothing to do with WCF.

Here is the JSON version of the same contract (hint: the CODE Framework development service host tool can show all contracts in different formats, including JSON and XML):

```json
{
  "CustomerId": ""
}
```

In a typical REST world, we would probably like to call this service using a `GET` operation (as is even hinted at by the name of the method... although CODE Framework does not map to verbs based on the method name for security and architectural reasons). Which means we could simply type the URL into a browser, like this:

```
http://mydomain.com/Customer/1234
```

There are several things that have changed compared to the previous example. For one, we are now using a `GET` HTTP verb. Secondly, the "/GetCustomer/" part of the URL (which identifies the method/operation) is not necessarily desirable in REST, where the URI is simply supposed to identify a certain customer. Furthermore, the filter information is now not posted to the URL anymore as the HTTP payload, but it instead makes up for the last part of the URL.

How can we adorn our service so CODE Framework knows that this is what we want to do? Well, there are two small steps to take. First, we have to tell the system how we want to expose the name of the method on the URL. We can do this by adding a small bit of information to our operation definition:

```c#
[OperationContract]
[Rest(Method = RestMethods.Get, Name = "")]
GetCustomerResponse GetCustomer(GetCustomerRequest request);
```

This uses a custom attribute provided by CODE Framework, called "Rest" (which can also be applied to non-REST services, but it would be completely ignored there). The Rest attribute has several parameters. For one, we are setting the "Method" (also known as the "HTTP Verb") to `GET`. Secondly, we are setting the REST-exposed name of the operation to an empty string. This tells the system that we do not want the /GetCustomer/ part of the URL and completely omit the method name.

> Note that these attributes are supported both on the full .NET Framework version as well as in .NET Standard and .NET Core versions. The later also expose a `Route` setting on the attribute. However, it is not generally recommended to set a custom route and instead use the approach described here. For more information see also: [.NET Core Service Hosting](Understanding-Services-Core).

Note that you can set the REST-exposed name of the operation to anything you want. If this attribute is not set, the default method name will be used. If it is set to an empty string, the name will be completely omitted from the URL (this can only done once per HTTP Method, otherwise, the system will fail to identify the correct method). Or, you can of course choose to expose the method as anything you want using this attribute:

```c#
[Rest(Method = RestMethods.Get, Name = "Hello")]
GetCustomerResponse GetCustomer(GetCustomerRequest request);
```

Will result in the following URL: http://mydomain.com/Customer/Hello/1234

The second piece of the puzzle is that we need to tell the system that part of the URL (the `/1234` part) is meant to be part of the input object/message. We can do so by changing the data contract in the following fashion:

```c#
[DataContract]
public class GetCustomerRequest
{
    [DataMember]
    [RestUrlParameter(Mode = UrlParameterMode.Inline)]
    public string CustomerId { get; set; }
}
```

Again, a custom CODE Framework attribute is used to provide the additional information needed. In this case, the `CustomerId` property is decorated with the `RestUrlParameter` attribute, and it's Mode parameter is set to `Inline`. This tells that system that the `CustomerId` property is to be populated from a piece of information that is provided as an inline URL parameter.

Voila! We have designed our first REST service!

> Note: Both the Rest and RestUrlParameter attributes are completely ignored in non-REST scenarios and do in no way interfere or impact other service standards. For instance, an operation that has its REST-exposed name set to "Hello" will not in any way change the name of the operation as it is accessed using SOAP or other standards.

This is a relatively simple (although very common) scenario. What if things get more complex? Let's say we have more than one property on the input message. Perhaps something like this:

```c#
[DataContract]
public class GetCustomerRequest
{
    [DataMember]
    [RestUrlParameter(Mode = UrlParameterMode.Inline)]
    public string CustomerId { get; set; }

    [DataMember]
    [RestUrlParameter(Mode = UrlParameterMode.Inline, Sequence = 1)]
    public bool IncludeInactive { get; set; }
}
```

In this case, we have a second property on the input message called "IncludeInactive". This property is also set to be an inline parameter on the URL. It also has its sequence set to 1 (the previous property didn't specify the Sequence, hence the default - 0 - applied), meaning that the CustomerId parameter is expected before the IncludeInactive parameter. Therefore, our URL now follows this pattern: http://mydomain.com/Customer/1234/true

Note that if the second parameter is not provided on the URL, the default value for the parameter (false in this case) is assumed.

> Note: When hosting this service in WebApi, you need to make sure that the routing configuration supports as many parameters as you want to pass this way. For more information, see [WebApi Service Hosting](WebApi%20Service%20Hosting)).

A slightly different approach would be to use named parameters rather than inline parameters. To do so, we change the contract to the following:

```c#
[DataContract]
public class GetCustomerRequest
{
    [DataMember]
    [RestUrlParameter(Mode = UrlParameterMode.Inline)]
    public string CustomerId { get; set; }

    [DataMember]
    [RestUrlParameter(Mode = UrlParameterMode.Named)]
    public bool IncludeInactive { get; set; }
}
```

The RestUrlParameter attribute now has it's mode set to "Named". Actually, that is the default, so we could change the definition of that parameter to the following:

```c#
[RestUrlParameter]
public bool IncludeInactive { get; set; }
```

Either way, this now ends up with the following URL pattern: http://mydomain.com/Customer/1234?IncludeInactive=true 

Our service has more than just a `GetCustomer()` method. Another method of interest is the following:

```c#
[OperationContract]
GetCustomerListResponse GetCustomerList(GetCustomerListRequest request);
```

Just like in the case of GetCustomer, this operation should probably be exposed as a GET operation. Perhaps the URL should be something like this: http://mydomain.com/Customer/List

We can easily do so by decorating that operation appropriately:

```c#
[OperationContract]
[Rest(Method = RestMethods.Get, Name = "List")]
GetCustomerListResponse GetCustomerList(GetCustomerListRequest request);
```

Not all operations on our contract are suitable to be called a `GET` operations. For instance, the SaveCustomer operation should perhaps be exposed using a `PUT` Verb, yet on the same URL pattern as the GetCustomer operation:

```c#
// Called with a PUT HTTP Verb!!!
http://mydomain.com/Customer/1234
```

Since we are using a different HTTP Verb, it is fine and indeed desired to use the same URL. This is in fact one of the very core ideas of REST. We can do this by decorating our operation definition like so:

```c#
[OperationContract]
[Rest(Method = RestMethods.Put, Name = "")]
SaveCustomerResponse SaveCustomer(SaveCustomerRequest request);
```

There are some interesting details here. To explore those, let's first take a look at the input message's structure (omitting serialization attributes for brevity):

```c#
public class SaveCustomerRequest
{
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Id { get; set; }
    public string Address { get; set; }
    public string Phone { get; set; }
    public decimal CreditLimit { get; set; }
    public DateTime CustomerSince { get; set; }
}
```

To achieve the desires URL pattern, what want to do is to map the Id property to a URL parameter, which we can do like so:

```c#
[RestUrlParameter(Mode = UrlParameterMode.Inline)]
public string Id { get; set; }
```

And that's it! We can now call the service on the desired URL with a `PUT` Verb (you will have to use a tool like Fiddler, if you want to try this out... this URL can NOT be called by just typing it into a browser... which is a desired side-effect!).

A typical example would be to call this from a JavaScript client. The actual customer data has to be passed along as the HTTP payload in JSON format. You may wonder how you would know the correct JSON format. Well, you can probably deduce it from the C# contract. A simpler version is to use the CODE Framework Service Test Harness (see also: [Using the Service Test Harness](Using%20the%20Service%20Test%20Harness)), which always gives you access to the contract in various formats, including JSON. Here's an example of the input message in JSON format:

```json
{
    "Address": "6605 Cypresswood Dr.",
    "CreditLimit": 10000,
    "CustomerSince": "\/Date(946706400000-0600)\/",
    "FirstName": "Markus",
    "Id": "1234",
    "LastName": "Egger",
    "Phone": "555-555-5555"
}
```

The eagle-eyed observer may notice an interesting detail here: The Id is actually passed both as the payload as well as the URL parameter. This is technically unnecessary. In fact, we could simply go for the following URL pattern (without ID parameter): http://mydomain.com/Customer

This would work. However, this would not follow the REST paradigm of "the URL identifies the resource, and the verb defines the action". In other words: The URL should uniquely identify the customer we want to "put" into the system. It is thus probably advisable to include the URL parameter. (However, there is no real true or false answer as to how to design this. CODE Framework allows for full freedom in this matter).

Another option would be to simply remove the Id information from the JSON payload to avoid duplication. In real-world scenarios however, that may not be practical, since the JSON may often simply result from data binding in the user interface or it may be provided in some other way. Removing the Id would this be a pain. 

> Note: There is no actual JSON/REST standard that defines how this is supposed to work. Technically, all approaches are fine. You can pass the Id on the URL. You can pass it as part as the payload. Or you can pass it in both places.

The way CODE Framework handles this is as follows: First, the payload is considered and re-hydrated back into a real object. Then, the URL is inspected. All Parameter passed on the URL are then used to update the in-memory object. Therefore, if no Id is passed on the URL, the Id from the JSON payload is used, but if there is an Id in the URL, the JSON will be overridden with the URL information. If no Id information is passed in the payload, the system simply picks up the URL Id. This way, all scenarios work, allowing the developer to pick the approach that suits them best. Simply be aware that URL parameters always win. Sometimes, developers will pass different values for the parameter in the URL and in the payload (a highly discouraged scenario that is probably indicative of a bug). In that case, the URL parameter always wins out.
