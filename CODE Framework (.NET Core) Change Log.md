# CODE Framework .NET Core Change Log

## 2.0.17 - 7/6/2023

* Added more control over the proxy cache and also the settings cache in `ServiceClient`.

## 2.0.16 - 6/29/2023

* Fixed a problem with caching proxies for `ServiceClient.Call<T>()` operations.
* Added more flexibility with property name casing in REST `POST` operations.

## 2.0.15 - 6/7/2923

* `PingResponse` now includes an optional `CodeFrameworkVersion` member. If used with `ServiceHelper.GetPopulatedPingResponse()` it is automatically populated with the current CODE Framework version.
* CODE Framework now correctly shows camelCase serialization in Swagger. (There used to be a bug where it always showed ProperCase, regardless of the actual configuration).
* URL parameters now show appropriate default values.

## 2.0.13 - 6/2/2023

* Added support for more URL parameters (inline and named parameters) for methods such as DELETE. (This used to only be supported for GET and POST).
* Updated external dependencies to the latest versions.

## 2.0.11 - 5/24/2022

* Added error handling around default value determination for members in in Open API documentation.

## 2.0.10 - 5/21/2022

* Operations that return `Task<T>` (async) are now handled properly in Swagger/OpenAPI features.
* Parameters on `GET` operations that are not configured with attributes are now still handled properly (based on the default values the attributes would provide).
* Default values (where available and applicable) are now indiated in the OpenAPI definition.

## 2.0.8 - 5/11/2022

* Minimum target version is now .NET 6
* Newtonsoft.json dependency has been removed.
* Services now support Open API ("Swagger") through `app.UseOpenApiHandler()`
  * Generic documentation is generated from the structure defined in the type system.
  * This includes an extended version of loading descriptions and other documentation from XML docs as well as a variety of attributes to fine-tune how the documentation is generated.
* There now is a new `FileResponse` default response type that can be used to return raw files from services. This is especially useful when returning things like images (or other large data files) from a service, which can be hanled as raw data in HTTP requests (although they are handled like all other contracts in other protocols/standards) and are therefore more efficient. Plus, such service can directly serve up things such as images that are referred to as the `src` in `<img>` tags.
  * `ServiceClient.Call<>()` has ben updated ot transparently support `FileResponse` if called from C#.
* `BaseServiceResponse.Success` now defaults to `true` again (as was the case in full framework versions). This means that this flag does not have to be set in most scenarios unless there is a problem. Since standard exception handling will automatically create responses with this flag (if present in the response) set to `false`, most developers may never have to set this manually.
* There now is a new `[StandardExceptionHandling]` attribute that can be attached to individual operations/methods, service implementation classes, and service interfaces. If present, CODE Framework will automatically perform a try/catch around the service method and populate a response message with `Success` set to `false` and `FailureInformation` populated with exception information (assuming these properties are present in the response message).
* `ServiceGardenLocal` now can act as a dependency injection container (IoC container) to support dependency injection in in-process hosted services.
* Services hosted in-process (in `ServiceGardenLocal`) now also support the `[StandardExceptionHandling]` attribute to provide behavior that is identical to that of services hosted in other environments.

## 1.5.15 - 12/15/2021

* ~~Minimum target version is now .NET 5~~ (pushed to the next version)
  * * ~~Removed dependency on Newtonsoft.Json~~ (pushed to the next version)
* Date handling for REST URL parameters in `ServiceClient` has been changed to conform with ISO 8601.
* Re-enabled support for passing a service URL into `ServiceClient.Call()`. This is only supported in REST scenarios.
* Removed dependency on Westwind.Utilities

## 1.5.13 - 12/20/2020

* Improved the `Mapper.Collections()` method to be able to handle collection items that require a back-link constructor parameter (back-link to the parent collection).
* The options object for `Mapper.Collections()` now supports an `AddTargetCollectionItem` delegate, which can be used to define custom code that adds elements to the target collection, which is useful when that collection doesn't just support a simple `target.Add(item)` or needs other custom handling.

## 1.5.7 - 5/15/2020

* All the `To...Safe()` methods of the `DataHelper` class have been improved to handle null values more efficiently.

## 1.5.6 - 5/14/2020

* Fixed a problem in `StringHelper` related to retrieving just the file name within a string (path) on Linux.
* Made `ServiceHelper` configurable so `ServiceHelper.GetPopulatedFailureResponse` will only return extended debug information when the static `ShowExtendedFailureInformation` is manually set to `true`. (Automatic detection of whether a service runs in a debug environment turned out to not be reliable enough and could sometimes return extended information in runtime environments).

## 1.5.4 - 2/24/2020

* Fixed CSV sub-system bugs that were introduced in the move from .NET Framework to .NET Core.

## 1.5.3 - 2/11/2020

* The `Mapper` class (mapping framework) has received an overhaul.
* The `Mapper` class now supposed a new `MappingOptions.LogMappedMembers` property, which, when set to true (which happens automatically in debug builds, but can be overridden) populates `MappedSourceProperties`, `MappedDestinationProperties`, `IgnoredSourceProperties`, and `IgnoredDestinationProperties`, which are useful in seeing which properties/members are actually included in a mapping operation. (This is especially useful in development/debug scenarios, which is why this feature is enabled in debug builds automatically).
* The `MappingOptions` class now has an `AddMap()` convenience method.

## 1.4.0 - 1/5/2020

* `ServiceHelper` now has a standard `GetPopulatedFailureResponse()` method. (See also: [Understanding Services in .NET Core - Standardized Exception Handling](http://docs.codeframework.io/Understanding-Services-Core#standardized-exception-handling)).

## 1.3.0 - 1/4/2020

* Added `MarkupHelper` static class to `CODE.Framework.Fundamentals.Utilities` namespace (`CODE.Framework.Fundamentals` package).
* Added `MarkdownHelper` static class to `CODE.Framework.Fundamentals.Utilities` namespace (`CODE.Framework.Fundamentals` package).

## 1.2.0 - 12/27/2019

* Switched all JSON Serialization/Deserialization from JSON.NET to System.Text.Json
* JSON operations are now async.

## 1.1.4

* Added AzureLogger to log message whenever an app or service runs in Azure.
* Handling some scenarios related to .NET Core 3 sync/async operations.

## 1.1.0

* Supporting .NET Core 3.0

## 1.0.7. - 10/10/2019

* Changes to service contract package to fix bugs.
* Supporting a new `ServiceClient:LogCommunicationErrors` configuration setting (and `LogCommunicationErrors` property on the `ServiceClient` class) to optionally log additional information on failed REST calls.

## 1.0.2 - 1.0.6 

* Internal builds with minor fixes.

## 1.0.1 - 6/24/2019

* Added DataHelper support to .NET Core version
* Added Mapper support to the .NET Core version

## 1.0.0 - 6/20/2019

* CODE Framework Core components aimed at hosting services in .NET Core and Docker have now been made available as preview versions of NuGet packages. (See also: [Understanding Services in .NET Core](Understanding-Services-Core))
* CODE Framework client-side components for calling services from .NET Core have been made available. (See also: [Understanding Services in .NET Core](Understanding-Services-Core))
