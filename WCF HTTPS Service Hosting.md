# WCF HTTPS Service Hosting

Using CODE Framework, you can easily host and call services using the HTTPS protocol. All methods on the service host objects (server-side) as well as the ServiceClient class (client-side) related to services that use the HTTP protocol now feature an optional "useHttps" parameter. 

The following is an example of how that parameter is used to host services:

```cs
host.AddServiceHostBasicHttp(typeof(TestService)); // HTTP 
host.AddServiceHostBasicHttp(typeof(TestService), useHttps: true); // HTTPS 
host.AddServiceHostWsHttp(typeof(TestService)); // HTTP 
host.AddServiceHostWsHttp(typeof(TestService), useHttps: true); // HTTPS 
host.AddServiceHostRestJson(typeof(TestService)); // HTTP 
host.AddServiceHostRestJson(typeof(TestService), useHttps: true); // HTTPS 
host.AddServiceHostRestXml(typeof(TestService)); // HTTP 
host.AddServiceHostRestXml(typeof(TestService), useHttps: true); // HTTPS 
```

The equivalent works for calling services using the ServiceClient class. 

Note that you still need to make sure you have HTTPS certs configured properly on your server, which is an admin task and has as such nothing to do with CODE Framework. But if the cert is not configured properly, you will get an error message stating the certificate error.
