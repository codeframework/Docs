# CODE Framework .NET Core Change Log

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