# CORS Support in ServiceHostController (WebApi)

ServiceHostController support CORS for WebApi hosted services, enabling cross domain calls to services. 

Let's say you have a service like this:

```c#
public class UserController : ServiceHostController<UserService> {}
```

This service simply supports CORS out of the box for all origins. In other words: It supports cross-domain calls from clients such as Angular apps (including pre-flight options checks). There really shouldn’t be anything else you have to do. 

You can however control it further, if you want. For instance, you can restrict the origins like this:

```c#
public class UserController : ServiceHostController<UserService> 
{
    public UserController()
    {
        AllowableCorsOrigin = “codemag.com”;
    }
}
```

Now it can only be called from codemag.com. Or, if you want to turn it off, you can do this:

```c#
public class UserController : ServiceHostController<UserService> 
{
    public UserController()
    {
        EnableCors = false;
    }
}
```

So this can be turned on and off individually for each controller. In most cases, you won’t have to do anything though. It should just work the way you expect it.

The only thing you may need to be aware of is that in some ASP.NET project setups, settings in web.config can interfere with this by high jacking some calls we need to handle. You should have the following in your web.config:

```xml
<system.webServer>
  <handlers>
    <remove name="ExtensionlessUrlHandler-Integrated-4.0" />
    <remove name="OPTIONSVerbHandler" />
    <remove name="TRACEVerbHandler" />
    <add name="ExtensionlessUrlHandler-Integrated-4.0" path="*."
      type="System.Web.Handlers.TransferRequestHandler" 
      preCondition="integratedMode,runtimeVersionv4.0" />
  </handlers>
</system.webServer>
```

Most likely, this isn’t something to worry about though. I think the newer ASP.NET project templates put this in automatically.
