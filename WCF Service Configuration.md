# WCF Service Configuration

CODE Framework does its best to apply smart default settings to services (both server-side and client-side) for the typical type of application build with CODE Framework (business applications). Nevertheless, it is often desirable and necessary to change settings for services. This topic provides an overview of settings applicable to services.

Note that nearly all service settings (which are applied through the standard [CODE Framework Configuration System](Configuration System)) can be made globally for all services, or individual on a per-service basis. For instance, it is possible to set the allowable message size for all services through the ServiceMessageSize setting, and it is possible to apply that setting for a single service (example: ICustomerService) by adding a ServiceMessageSize:ICustomerService setting.

It is also possible to add settings programmatically in various ways. The most common approach is to use static events fired by the ServiceGarden (server-side) and ServiceClient (client-side) classes. These events grant access to all the low level WCF objects, so any conceivable WCF setting or manipulation can be made. A very common use of this feature is to arbitrarily change the URL a service is hosted at.

## Server-Side Settings

These settings can be applied in server-side projects (these settings are used by the ServiceGarden class, which in turn is used by all CODE Framework WCF hosting environments):

| Setting                        | Default   | Description                              |
|--------------------------------|-----------|------------------------------------------|
| ServiceBaseUrl                 | localhost | Root URL services are hosted at.<br />The default only applies for development environments.<br />In a production environment, this should be set to something like "www.mydomain.com". |
| ServiceBasePort                | 50000     | Base port used for TCP/IP (net.tcp) services. Ignored for all other service types.<br />This setting defines the first utilized port number. The first TCP/IP service hosted will receive this port number. The next host is incremented by 1, and so forth.<br />Note that this setting can only be set globally and not for individual services (although when launching services, a parameter on the host method allows defining the port explicitly) |
| ServiceBasePath                | dev       | Services are usually not just hosted at the root of a domain. For instance, a service project created to deal with payments is probably not hosted at<br><a href="http://www.mydomain.com">www.mydomain.com</a>, but at <a href="http://www.mydomain.com/payments"><br>www.mydomain.com/payments</a>. In that case, ServiceBasePath would be set to "payments". |
| ServiceMessageSize             | medium    | Size of messages the service can process. This has an impact on the maximum buffer size, maximum buffer pool size, maximum message size, maximum array length, and maximum string content length.<br />CODE Framework supports 3 settings:<br />Default: WCF defaults apply<br />Medium: 10MB<br />Large: 100MB<br />VeryLarge: 1GB (this setting is not recommended!)<br />Max: int.MaxValue, about 2GB (this setting is def. not recommended!) |
| ServiceBasicHTTPExtension      | basic     | To differentiate between the same services hosted different ways, a suffix is usually added for Basic HTTP services URL unless this setting is an empty string in which case no suffix is added. |
| ServiceWsHttpExtension         | ws        | To differentiate between the same services hosted different ways, a suffix is usually added for WS HTTP services URL unless this setting is an empty string in which case no suffix is added. |
| ServiceRestExtension           | rest      | To differentiate between the same services hosted different ways, a suffix is usually added for REST services URL unless this setting is an empty string in which case no suffix is added. |
| ServiceRestXmlFormatExtension  | xml       | To differentiate between the same services hosted different ways, a suffix is usually added for REST XML services URL unless this setting is an empty string in which case no suffix is added. |
| ServiceRestJsonFormatExtension | json      | To differentiate between the same services hosted different ways, a suffix is usually added for REST JSON services URL unless this setting is an empty string in which case no suffix is added. |
| ServiceConcurrencyMode         | multiple  | Defines whether a single hosted service supports multiple simultaneous callers (see also: [WCF Self-Hosted Service Concurrency]) |
| ServiceMaxConcurrentSessions   | 10        | Defines how many concurrent sessions a single hosted service supports (if multiple simultaneous callers are allowed) (see also: [WCF Self-Hosted Service Concurrency]) |
| ServiceMaxConcurrentCalls      | 10        | Defines how many concurrent callers a single hosted service supports (if multiple simultaneous callers are allowed) (see also: [WCF Self-Hosted Service Concurrency]) |
| ServiceSecurityMode            | None      | Defines the security mode for TCP/IP (NetTcp) services. Allowed settings are None, Message, Transport, and&nbsp;TransportWithMessageCredential. |

## Client-Side Settings

These settings can be applied in client-side projects (these settings are used by the ServiceClient class)

Defines the security mode for TCP/IP (NetTcp) services. Allowed settings are None, Message, Transport, and TransportWithMessageCredentials.

| Setting                        | Default   | Description                              |
|--------------------------------|-----------|------------------------------------------|
| ServiceBaseUrl                 | localhost | Root URL services are hosted at. <br />The default only applies for development environments.<br />In a production environment, this should be set to something like "www.mydomain.com&rdquo; |
| ServiceBasePath                | dev       | Services are usually not just hosted at the root of a domain. For instance, a service project created to deal with payments is probably not hosted at<br><a href="http://www.mydomain.com">www.mydomain.com</a>, but at <a href="http://www.mydomain.com/payments"><br>www.mydomain.com/payments</a>. In that case, ServiceBasePath would be set to "payments&rdquo;. |
| ServiceMessageSize             | medium    | Size of messages the service can process. This has an impact on the maximum buffer size, maximum buffer pool size, maximum message size, maximum array length, and maximum string content length.<br />CODE Framework supports 3 settings:<br />Default: WCF defaults apply<br />Medium: 10MB<br />Large: 100MB<br />VeryLarge: 1GB (this setting is not recommended!)<br />Max: int.MaxValue, about 2GB (this setting is def. not recommended!) |
| ServiceBasicHTTPExtension      | basic     | To differentiate between the same services hosted different ways, a suffix is usually added for Basic HTTP services URL unless this setting is an empty string in which case no suffix is added. |
| ServiceWsHttpExtension         | ws        | To differentiate between the same services hosted different ways, a suffix is usually added for WS HTTP services URL unless this setting is an empty string in which case no suffix is added. |
| ServiceProtocol                | nettcp    | Defines the protocol that is to be used to call the service. Possible values are BasicHTTP, WsHTTP, NetTCP, REST, and InProcess.<br />Note: For more information on calling REST Services (such as those hosted in WebAPI), see [Calling REST Services through Service Client](Calling%20REST%20Services%20through%20ServiceClient). |
| ServicePort                    | n/a       | Defines the port to be used to call a service. This only applies to TCP/IP (net.tcp) services. Note that it makes no sense to set this setting globally. It needs to be made for each service (example: ServicePort:ICustomerService = 45000) |
| ServiceRestExtension           | rest      | To differentiate between the same services hosted different ways, a suffix is usually added for REST services URL unless this setting is an empty string in which case no suffix is added. |
| ServiceRestXmlFormatExtension  | xml       | To differentiate between the same services hosted different ways, a suffix is usually added for REST XML services URL unless this setting is an empty string in which case no suffix is added. |
| ServiceRestJsonFormatExtension | json      | To differentiate between the same services hosted different ways, a suffix is usually added for REST JSON services URL unless this setting is an empty string in which case no suffix is added. |
| ServiceUrl                     | &nbsp;    | This setting is used to define explicit service URLs. This is very commonly used for REST service calls. For SOAP-based services (BasicHttp and WsHttp), it can also be used when services are to be called from an arbitrary URL (such as when<br> the service is hosted by IIS). Otherwise, it is more common to set individual service settings (like the base URL and extension and such). (Note: This setting is always set as ServiceUrl:IXxxService for each service&hellip;) |
| ServiceSecurityMode            | None      | Defines the security mode for TCP/IP (NetTcp) services. Allowed settings are None, Message, Transport, and&nbsp;TransportWithMessageCredential. |

