# CODE Framework .NET Core Change Log

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
