# Business Object Introduction

In the Milos Solution Platform Framework, there are two different entities that are combined to provide the functionality that is normally referred to as "Business Object". Most of the time, developers will use a type of object known as a "Business Entity". Entities are stateful objects that deal with - well - entities. For instance, an entity could be an invoice (a single invoice that is). This would mean that the business entity contains information about a single record for the invoice, but also additional records for things such as line items, recipients, addresses, and so forth.

## Using Business Entities

Using business entities that already exist is very straightforward. For instance if there was an ```InvoiceBusinessEntity```, the developer could create a new invoice like so (C#):

```
var invoice = new InvoiceBusinessEntity();
```

Or, one could load an existing invoice like so:

```
var invoice = new InvoiceBusinessEntity("123456");
```

Note: In Milos, it is also common to follow a pattern where a static method is used to instantiate an entity. This is functionally similar to the examples above, but it allows for additional functionality to be added to entity loading/creating, if needed. In that case, entity instantiation works like this:

```
var newInvoie = InvoiceBusinessEntity.NewEntity();
var existingInvoice = InvoiceBusinessEntity.LoadEntity(key);
```

Once an invoice is loaded, one can access and assign information about the invoice like so:

```
console.WriteLine(invoice.FooterText);
console.WriteLine(invoice.Terms);
invoice.InvoiceDate = DateTime.Now; 
```

Also, additional invoice information stored in separate records of a related table can be accessed through the invoice entity object:

```
invoice.LineItems.Add(100, "Great Products");
invoice.LineItems[0].Description = "This is some cool descriptive text";
invoice.LineItems[0].ProductImage = Bitmap.FromFile("c:\\prodimg.jpg");
invoice.Addresses.Add(AddressTypes.Shipping, "6605 Cypresswood Dr.", "Houston");
```

Saving an invoice is also trivial:

```
invoice.Save();
```

Note that this saves the complete business entity, including data that may be stored in related tables. In this example, invoice information that could be stored in an invoice table would be updated as well as line item information stored in the line item table, and address information stored in an addresses table. The developer does not have to call a save method on those child items, nor is it necessary to verify information separately.

### Advanced Business Entity Features

Business entities have a number of advanced features. For instance, data used by the entity can be loaded async or on demand. This means that even a very complex business entity that contains a large number of different items can be used without having to worry about performance, because some of the data will only be loaded if needed. Example: The CODE Magazine web site uses a ArticleEntity to load article information (this entity object is subsequently hosted in a CODE Framework Micro-Service). This includes all types of things, such as attached images and other binary attachments, such as download files and the original article in Word format. Needless to say that this could be a lot of data. But since most of this data is only loaded when needed (entire collections, such as the BinaryAttachments collection are loaded when they are accessed the first time), the site can simply instantiate one of these entities and not worry about the overhead of loading megabytes of data for every hit.

Entities also have undo buffers. This means that everything in an entity can be undone.

Entities can be serialized.

Business entities allow access to potentially very complex data in very simple ways. A good example is accessing, adding, and manipulating child item collections. Another example is accessing encrypted data. Yet another example would be accessing complex data types, such as images. All of those things can be exposed in a very friendly fashion to the developer using the entity.

> Note: Not all features of the original Milos Solution Platform, business objects and entities, are being maintained in the CODE Framework version of Milos. We focus on those features that are important in modern scenarios, especially service-based scenarios. However, if there are features you are missing, and need for backwards compatibility, please don't hesitate to contact us and we will see what we can do to add support.

## Business Objects

Asides from entities, there are also business objects. While business entities provide simple yet powerful access to business information, they are also very dumb. All the heavy lifting is done behind the scenes by business objects. Business objects have the ability to run queries against the database and retrieve and update information. Business objects also verify input. All business entities use business objects to do the heavy lifting. Every time someone calls the "Save" method on a business entity, the business entity really uses its assigned business object to first verify the information, and secondly, save the modified or new information.

No business entity can function without a business object. One could, however, use business objects on their own. While business objects could be used to update information, they are mostly used to retrieve lists of items stored in data sets.

Note that business objects are generally stateless. They may have methods such as ```GetTop100Customers()```, which returns a dataset filled with customer information, but they will not store any of that information in their own memory space. Once the dataset is retrieved, it is entirely disconnected from the business object. Want a stateful object? Use the associated entity.

An easy way to think of this is that business objects really are part of business entities by composition. Each business entity automatically has a business object contained inside itself. Think of the combination of business entity and business object as a "business unit". Mostly, developers will work with that "unit".

Note, however, that there are reasons to work with the business object directly. Retrieving lists for instance. While we could create a collection of business entities to display their information in a grid, this would not be very performant. Instead, one can simply retrieve a dataset with a number of records directly from the business object and bind it to a datagrid. Once the user wants to take a closer look at a single record, and perhaps edit the information, it's time to use the business entity instead.

Another use for a business object would be in web services that are accessed by non-VS.NET clients. It is very easy to expose methods from a business object. Exposing the complex functionality provided by business entities to non-VS.NET clients, however, could be challenging (especially for the consumer of the data ;-) )

## Comparing Milos Business Object Features with Entity Framework

.NET's Entity Framework is an Object/Relational Mapping Framework. As such, the Milos setup can be seen as a spiritual predecessor (this framework has been available since .NET 1.0 in 2000, after all). However, there are some differences. Entity Framework is more powerful in mapping a web of relations through a large relational database, while Milos usually looks at just a single entity and it's immediate relations. For instance, Milos may map an invoice and line items to an entity. It will also map the customer that is linked to the invoice. However, it will stop at that. Entity Framework on the other hand, allows looking at the relations beyond that, such as a customer's other orders, and so on. Everything can be explored further that way. Milos on the other hand would create a separate entity for a customer's orders. 

Milos also has separate business objects, which (in addition to supporting the entities and doing all the heavy lifting for them) provide a simple way to query data and return DataSet objects, or individual values. This is often seen as a simpler way of accessing data, especially in non-update scenarios. (And it also provides a very generic way that can be moved between different database technologies without code changes). The business object layer is also suitable to express sophisticated business logic, while Entity Framework concerns itself more with the data aspects.
