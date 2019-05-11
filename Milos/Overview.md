# Milos Solution Platform

The Milos Solution Platform is the spiritual predecessor of CODE Framework. It is a business application development framework that was originally created for .NET 1.0 and has been maintained ever since. It originally contained components for data access, WinForms development, service development, and more. Some of those components have eventually been used as the basis for CODE Framework's services components. Other elements, such as the WinForms components, are still available in their original form. Data access and business-objects components are also still available in their original form. However, they have also been converted into .NET Standard components (so they can be used in all flavors of .NET) and are maintained as a legacy support feature as part of CODE Framework.

## NuGet Support

Milos Components that are maintained as part of CODE Framework can be added to your project from NuGet. The following link provides a quick way to search for Milos NuGet components (although this search may also return other packages): https://www.nuget.org/packages?q=Milos.

> Typical uses of Milos require adding the Milos.BusinessObjects and Milos.Data.SqlServer packages. (This also brings along other dependencies and thus provide what is usually needed in most business applications).
