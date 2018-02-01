# Using ObjectHelper and Complex Paths

_This is an excerpt from an internal email at EPS/CODE:_

FYI: Here is a detailed but complex and nifty enhancement in ObjectHelper’s GetPropertyValue() and SetPropertyValue() methods: They now both support complex paths, rather than simple property names. So you can now do this:

```c#
var invoice = new Invoice();

var firstName = invoice.GetPropertyValue<string>("FirstName");
Console.WriteLine(firstName);
var addressLine1 = invoice.GetPropertyValue<string>("Address.Line1");
Console.WriteLine(addressLine1);
var description2 = invoice.GetPropertyValue<string>("LineItems[1].Description");
Console.WriteLine(description2);

invoice.SetPropertyValue("FirstName", "John");
invoice.SetPropertyValue("Address.Line1", "Cypresswood Dr.");
invoice.SetPropertyValue("LineItems[1].Description", "Test description");

firstName = invoice.GetPropertyValue<string>("FirstName");
Console.WriteLine(firstName);
addressLine1 = invoice.GetPropertyValue<string>("Address.Line1");
Console.WriteLine(addressLine1);
description2 = invoice.GetPropertyValue<string>("LineItems[1].Description");
Console.WriteLine(description2);
```

In other words: The actual “property name” is now a full path that can be passed in. The main reason I added this is that I had a need to use reflection to retrieve an object’s member values based on a WPF binding path. I use this in custom controls where I did away with some binding operations in favor of my own code that retrieves the property values (in an optimized way), to improve performance. In that, I wanted to use the WPF binding path expressions to retrieve these values. ObjectHelper can now do that.
