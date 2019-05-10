# Creating a Simple Business Entity

1) Add a new class to your project called ```ProductBusinessEntity```
   1) Change the class so it inherits from ```Milos.BusinessObjects.BusinessEntity```. Note that this class must overwrite the ```GetBusinessObject``` method if this code isn't added automatically, add it yourself.
2) In the ```GetBusinessObject()``` method, create an instance of ```ProductBusinessObject``` and return it. Note that the return type of this object needs to be typed as an ```IBusinessObject```.
3) At this point, you also need to override the object's constructors.
   1) Simply override  two overloaded constructors with calls to their base methods only.
4) At this point, you can add properties that reference data in the internal dataset
   1) To access field values within the internal dataset, use the ```ReadFieldValue<T>()``` method.
   2) To set vield values, use the ```WriteFieldValue()``` method. Note that the properties can be significantly different from the actual values in the database. (See below)
  
Here is an example of what your business entity may look like at this point:

```cs
public class ProductBusinessEntity : Milos.BusinessObjects.BusinessEntity 
{
    public ProductBusinessEntity() : base() {}
    public ProductBusinessEntity(Guid id) : base(id) {}

    public override Milos.BusinessObjects.IBusinessObject GetBusinessObject() => new ProductBusinessObject();

    public string Name
    {
        get => ReadFieldValue<string>("ProductName");
        set => WriteFieldValue("ProductName", value);
    }

    public string QuantityPerUnit
    {
        get => ReadFieldValue<string>("QuantityPerUnit");
        set => WriteFieldValue("QuantityPerUnit", value);
    }

    public int TheoreticalUnits
    {
        get
        {
            var one = ReadFieldValue<int>("UnitsInStock");
            var two = ReadFieldValue<int>("UnitsOnOrder");
            return one + two;
        }
    }
}
```
 
## Adding more information when a new entity gets created

Applications are usually required to add new default information whenever a new entity gets added. This can be accomplished easily by overriding the NewEntity() method. Make sure however, that you do call the default behavior!

```cs
protected override void NewEntity(IBusinessObject biz)
{
    base.NewEntity (biz);
    this.Name = "Another new product" ;
}
```

> Note: Similar operations can be performed when the object needs to be saved. Simply overwrite the Save() method.

## How do you use this object

Create a brand new entity:

```cs
var entity = new ProductBusinessEntity();
```

Load an existing entity:

```cs
var entity = new ProductBusinessEntity(key);
```

Get field values:

```cs
Console.WriteLine(entity.ProductName);
Console.WriteLine(entity.Description);
```

Updating field values:

```cs
entity.ProductName = "My Product";
entity.Description = "This is a great product!";
```

Save a new or modified entity:

```cs
entity.Save()
```

Figure out whether an entity needs saving:

```cs
if (entity.IsDirty) 
{
   Console.WriteLine("This entity has unsaved data.");
}
```

Get the primary key of the entity:

```cs
var guidKey = entity.PK;
```

Delete the current business object:

```cs
entity.Remove();
```

> Note that after removing an entity, the Save() method needs to be called to actually cause the operation on the server. Note also that after calling the Remove() object, the business entity is no longer valid and one should not interact with it in any way, other than calling the Save() method.

## Adding Static Instantiation Methods

In many cases, it can be benefitial to load entities through static methods. These methods can be added like this for Guid-based entities:

```cs
public class ProductBusinessEntity : BusinessEntity 
{
   public static ProductBusinessEntity NewEntity() => new ProductBusinessEntity();
   public static ProductBusinessEntity LoadEntity(Guid pressReleaseId) => new ProductBusinessEntity(pressReleaseId);
```

Entities can then be instantiated like this:

```cs
var newEntity = ProductBusinessEntity.NewEntity();
var existingEntity = ProductBusinessEntity.LoadEntity(key);
```
 
## Creating Business Entities with non-Guid Primary Keys

Whenever business objects are based on non-guid keys (see HowTo_CreateBusinessObject), the corresponding entities need to support those scenarios as well. Luckily, very little work is required to enable entities to work with integer or string keys. The main difference is that instead of supporting a constructor that takes a Guid, we need to support constructors that support integers and strings instead. This makes it possible to instantiate the entity objects and pass along the appropriate keys, so the objects can load themselves.

The first example shows the integer version:

```cs
public class ProductBusinessEntity : BusinessEntity 
{
   public ProductBusinessEntity() : base() {}
   public ProductBusinessEntity(int id) : base(id) {}
   // ... the rest of the class continues on as usual.
```

A string-based entity is created in a very similar fashion:

```cs
public class ProductBusinessEntity : BusinessEntity 
{
   public ProductBusinessEntity() : base() {}
   public ProductBusinessEntity(string id) : base(id) {}
   // ... the rest of the class continues on as usual.
```

Also, entities that are based on string or integer primary keys, we need to use different properties to query the keys. Here's the integer version:

```cs
int intKey = entity.PKInteger;
```

And the string version:

```cs
string strKey = entity.PKString;
```

Asides from these differences, simple entities behave exactly the same way, no matter what keys the are based on. Most of the differences are absorbed by the business object (see also: HowTo_CreateBusinessObject). However, there are additional considerations for entities that manage related child records. For more information, see HowTo_CreateChildItemsInEntities).
