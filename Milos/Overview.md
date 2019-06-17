# Milos Solution Platform

The Milos Solution Platform is the spiritual predecessor of CODE Framework. It is a business application development framework that was originally created for .NET 1.0 and has been maintained ever since. It originally contained components for data access, WinForms development, service development, and more. Some of those components have eventually been used as the basis for CODE Framework's services components. Other elements, such as the WinForms components, are still available in their original form. Data access and business-objects components are also still available in their original form. However, they have also been converted into .NET Standard components (so they can be used in all flavors of .NET) and are maintained as a legacy support feature as part of CODE Framework.

## Data Access and Business Objects

The most commonly used components of the Milos Solution Platform within modern CODE Framework applications are the business object and data access components. 

### Business Objects

Business objects allow for data access in an abstract and straightforward fashion. For instance, using a hypothetical customer business object, one could access customer data like this:

```cs
using (var biz = new CustomerBusinessObject())
using (var dataSet = biz.GetCustomersByName("Smith"))
{
    Console.WriteLine(dataSet.Tables[0].Rows[0]["Key"]);
    Console.WriteLine(dataSet.Tables[0].Rows[0]["Name"]);
}
```

> Note that Milos data components are largely based on the ```DataSet``` infrastructure by .NET Standard.

### Business Entities

Business entities abstract data access one step further by providing entity objects that can be seen as individual "records" of data with added functionality, such as business logic, data manipulation, and so on. Consider this hypothetical example:

```cs
using (var customer = CustomerBusinessEntity.LoadEntity(key))
{
    window.ImageControl.Source = customer.Photo;
    customer.Note = "This is a new note";
    customer.Save();
}
```

As you can see, this not just provides access to customer information, but it can express advanced features, such as loading binary photo data and returning it as a usable ```Image``` object.

## NuGet Support

Milos Components that are maintained as part of CODE Framework can be added to your project from NuGet. The following link provides a quick way to search for Milos NuGet components (although this search may also return other packages): https://www.nuget.org/packages?q=Milos.

> Typical uses of Milos require adding the Milos.BusinessObjects and Milos.Data.SqlServer packages. (This also brings along other dependencies and thus provide what is usually needed in most business applications).
