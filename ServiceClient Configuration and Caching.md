# ServiceClient Configuration and Caching

`ServiceClient` selects a transport and endpoint using the [CODE Framework configuration system](Configuration-System). Configure destinations during startup and keep ordinary calling code focused on the service contract. Settings and resolved protocols are cached by default, so changing a configuration source after calls begin also requires invalidating the appropriate client cache.

This article describes the current Services source, including the pending cache-invalidation corrections. See [Calling REST Services through ServiceClient](Calling-REST-Services-through-ServiceClient) for calls and [Service Call Options, Errors, and Retries](Service-Call-Options-Errors-and-Retries) for call-specific options. `ServiceCallOptions` does not replace endpoint configuration.

## Protocol and Endpoint Selection

For a contract named `ICustomerService`, the relevant settings are:

| Setting | Purpose |
| --- | --- |
| `ServiceProtocol:ICustomerService` | Per-contract transport selection, taking precedence over the global transport setting when nonempty. |
| `ServiceProtocol` | Global transport fallback when the contract has no nonempty override. The current client defaults to REST/JSON when neither is supplied. |
| `RestServiceUrl:ICustomerService` | Base REST endpoint for the contract. The contract's REST attributes determine operation names and URL parameters. |

`Rest`, `RestJson`, and `RestHttpJson` select REST/JSON. `InProcess` selects a registered local implementation. The current options-based APIs support these two transports. Historical names represented in the protocol enum are not a promise of active client support; unsupported protocols are reported as failures by the options-based path.

Contract settings use the interface's simple name, not its namespace-qualified name. Use the exact key spelling and casing shown above, including `ServiceProtocol` and `RestServiceUrl`; cache keys and targeted invalidation retain exact-case semantics. Avoid naming distinct contracts identically within one configuration context.

An optional `restServiceUrl` argument on `Call`, `CallAsync`, or `GetChannelDedicated` overrides the endpoint for that channel. It does not override the selected protocol. When using it for HTTP, the effective protocol must still be REST/JSON.

## Configuration Sources

A simple startup configuration uses the built-in memory source:

```csharp
using CODE.Framework.Fundamentals.Configuration;
using CODE.Framework.Services.Client;

ConfigurationSettings.Sources["Memory"].Settings["ServiceProtocol:ICustomerService"] = "Rest";
ConfigurationSettings.Sources["Memory"].Settings["RestServiceUrl:ICustomerService"] = "http://localhost:5008/api/customers";
```

Substitute the address and base path of your actual host. The standard source collection places Memory first so it can override lower-priority sources. Applications can register other sources; their order determines which supported setting is selected. Complete source registration before concurrent application activity.

An application using the .NET configuration-file source can provide equivalent values in `app.config`:

```xml
<appSettings>
  <add key="ServiceProtocol:ICustomerService" value="Rest" />
  <add key="RestServiceUrl:ICustomerService" value="http://localhost:5008/api/customers" />
</appSettings>
```

Changing the source does not automatically invalidate `ServiceClient`'s cached values. See the refresh examples below.

## ASP.NET Core JSON Configuration

An ASP.NET Core application calling another service can bridge its configuration into CODE Framework's source collection. For example, `appsettings.json` can contain:

```json
{
  "ApplicationSettings": {
    "ServiceProtocol": {
      "ICustomerService": "Rest"
    },
    "RestServiceUrl": {
      "ICustomerService": "http://localhost:5008/api/customers"
    }
  }
}
```

In `Program.cs`, before making client calls:

```csharp
using CODE.Framework.Fundamentals.Configuration;
using CODE.Framework.Fundamentals.Utilities;

var builder = WebApplication.CreateBuilder(args);
var clientSettings = new DictionarySettings(
    ApplicationSettingsJsonHelper.GetDictionaryFromSection(builder.Configuration));
ConfigurationSettings.Sources.Insert(1, clientSettings);
```

This fragment inserts the dictionary after the standard Memory source. The helper flattens the illustrated keys to `ServiceProtocol:ICustomerService` and `RestServiceUrl:ICustomerService`. It reads `ApplicationSettings` by default. Register the source once; retain `clientSettings` if your application will later replace it.

This bridge is a snapshot, not a live subscription to ASP.NET Core configuration reloads. If the underlying JSON configuration changes, refresh or replace the CODE Framework source as appropriate, then clear the relevant `ServiceClient` caches. Clearing client caches alone does not reread external files or rebuild the dictionary snapshot.

Do not confuse `ApplicationSettings`, which supplies client settings through this bridge, with `ServiceHandler`, which configures the services exposed by an ASP.NET Core host.

## Refresh Cached Settings

`ServiceClient.ClearSettingsCache()` clears all cached raw settings, resolved protocols, service IDs, and message-size entries. Passing `null` or an empty string has the same effect.

Targeted clearing has a narrower scope:

| Call | Effect |
| --- | --- |
| `ClearSettingsCache("ServiceProtocol:ICustomerService")` | Removes that raw setting and that contract's resolved protocol. |
| `ClearSettingsCache("ServiceProtocol")` | Removes the global raw setting and all resolved protocols. Cached per-contract overrides still apply. |
| `ClearSettingsCache("RestServiceUrl:ICustomerService")` | Removes that raw endpoint setting; other contracts and protocol selections are unaffected. |
| `ClearSettingsCache(otherKey)` | Removes only that raw setting entry. |

After updating an endpoint in the source:

```csharp
ConfigurationSettings.Sources["Memory"].Settings["RestServiceUrl:ICustomerService"] = "https://services.example.com/api/customers";
ServiceClient.ClearSettingsCache("RestServiceUrl:ICustomerService");
```

A subsequently created REST channel reads the refreshed endpoint. An existing channel retains its original handler and endpoint. Finish or replace retained channels according to your application's own lifecycle; invalidation does not redirect a call already in flight.

To switch a registered contract to in-process execution:

```csharp
ServiceGardenLocal.AddServiceHost(typeof(CustomerService), typeof(ICustomerService));
ConfigurationSettings.Sources["Memory"].Settings["ServiceProtocol:ICustomerService"] = "InProcess";
ServiceClient.ClearSettingsCache("ServiceProtocol:ICustomerService");
```

The registration belongs in startup and should occur only once. See [Client-Side In-Process Service Hosting](Hosting-In-Process-Services). When changing several related settings, update the source coherently before clearing the caches; pause new channel creation if your application requires an atomic deployment switch.

Channel setup and invalidation are synchronized so overlapping setup cannot repopulate a cleared configuration cache with its old resolution after clearing completes. This does not provide a transaction across external source edits or cancel existing operations.

## Disable Settings Caching

`ServiceClient.CacheSettings` defaults to `true`. Setting it to `false` bypasses cached configuration reads and writes, but does not erase values already cached. Before re-enabling caching after changes, clear those stale entries:

```csharp
ServiceClient.CacheSettings = false;
// Calls created while disabled resolve configuration without the client caches.
// Update configuration sources as required by the application.
ServiceClient.ClearSettingsCache();
ServiceClient.CacheSettings = true;
```

This setting and the caches are process-wide. It is not a per-call refresh mechanism and does not make source changes thread-safe. One-time property initialization is separate: clearing settings does not rerun the static initializer that reads `ServiceClient:LogCommunicationErrors`. Set `ServiceClient.LogCommunicationErrors` explicitly to change that property after initialization.

## Configuration Caches and Generated Proxy Types

The [transparent proxy generator](TransparentProxyGenerator) caches emitted interface implementation types. These types do not contain a service endpoint or per-call options. Each new proxy instance binds its own handler.

`ClearSettingsCache` leaves generated proxy types cached. Do not clear the proxy type cache just to change an endpoint, handler, or protocol. `TransparentProxyGenerator.ClearProxyCache` removes generated-type cache entries without unloading emitted types, while `ServiceGardenLocal` retains registered service proxies independently. Configuration invalidation is not service unregistration or disposal.
