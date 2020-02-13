# Creating a Simple Business Object

Follow these steps to create a new project with a new business object (manually).

In this example, I assume you are creating a simple business objects that handles a simple products table stored in SQL Server (such as the Northwind products table).

## Create a new class library project

Delete the default class that gets created automatically.

1) Add a Nuget Package Reference to ```Milos.BusinessObjects```.
   1) Add a new class to your business objects and give it a name such as ```ProductBusinessObject```.
   2) Change this class so it inherits from ```Milos.BusinessObjects.BusinessObject```.
2) Optional: Add a private constructor to prevent direct instantiation of an object. (Note: The static instantiation method is an extension point that allows for added functionality when the object is instantiated. However, this is not strictly required and not done in most scenarios).
   1) Add a static/shared ```NewInstance()``` method for the purpose of allowing controlled instantiation of the object.
3) Note that the editor automatically adds an overridden method named ```Configure```. (If not, override this method manually... otherwise the app won't compile)
   1) Set the ```MasterEntity``` and ```PrimaryKeyField``` properties in an overridden version of the ```Configure()``` methods which is provided by many framework objects.
4) Compile your new application

Here's the code we have created so far:

```cs
public class ProductBusinessObject : Milos.BusinessObjects.BusinessObject
{
    private ProductBusinessObject() {}

    public static ProductBusinessObject NewInstance() => new ProductBusinessObject();

    protected override void Configure()
    {
        MasterEntity = "products";
        PrimaryKeyField = "product_pk";
    }
}
```

Here is the same example without the static initialization helper:

```cs
public class ProductBusinessObject : Milos.BusinessObjects.BusinessObject
{
    protected override void Configure()
    {
        MasterEntity = "products";
        PrimaryKeyField = "product_pk";
    }
}
```

> Do not forget that whatever client application uses this business object needs to configure data access as defined in Documentation_ConfigurationOptions

> The private constructor and the static method is not strictly required, but it is a good idea. (For simplicity, this detail is ommited in subsequent examples).

## Retrieving Data

By default, the new business object has the ability to retrieve all fields and all records from the default entity using the GetList() method:

```cs
using (var biz = ProductBusinessObject.NewInstance())
using (var ds = biz.GetList())
{
    Consolw.WriteLine(ds.Tables[0].Rows[0][0]);
}
```

Retrieving all fields works well in simple scenarios, but it isn't usually the desired behavior. However, you can easily limit the list of fields you would like to retrieve by setting the ```DefaultFields``` property similar to:

```cs
DefaultFields = "Product_PK, ProductName";
```

In many cases however, we need the ability to query data in a more organized and filtered fashion. For instance, we may want to query all records with a certain product name. We could do this by adding a new method. That method could either have a brand new name, or it could overload ```GetList()``` with parameter. In this example, we will create a new method:

```cs
public DataSet GetListByName(string productName)
{
    using (var command = NewDbCommand("SELECT Product_PK, ProductName FROM Products WHERE ProductName LIKE @Name"))
    {
        AddDbCommandParameter(command, "@Name", productName + "%" );
        return ExecuteQuery(command);
    }
}
```

As you can see, the ```ExecuteQuery()``` method makes it very easy to fire data commands against the server back end. Note also that this is done in a very generic fashion. The generated command object is very generic and can be used with most databases. Much of this functionality is based on the ```NewDbCommand()``` method which returns a generic command object. 

> Note: During runtime, this will generate a command object specific to the used database. In SQL Server scenarios for instance, this would be an ```SqlCommand``` object.

> Note the user of the Parameters collection on the command object. This is mandatory for all operations! (Especially SQL Server.) Although some versions of the framework may let you get away with specifying where clause conditions directly (```...WHERE ProductName LIKE 'A%'```), we try to catch such operations whever possible (without too much of a performance hit... which sometimes is difficult). This is done for security reasons, as the parameters collection protects from SQL injection attacks.

Note also that there is a helper method to easily add parameters to the generic command object. One can also use the command object's ```Parameters``` collection, however, that is unnecessarily difficult for generic command objects (it would be much easier for more specific command objects, such as ```SqlCommand```).

## Saving Data

By default, the new business object also has the ability to save recordsets we modify, assuming they have a primary key field with the same name as the one we specified, and assuming there is a table in the dataset with the same name as our master entity (by default, this should be true).

To save a modified dataset, simply call the ```Save()``` method on the business object and pass along the dataset.

Note however, that saving data through the dataset is NOT the recommended way. Instead, editing and saving data is done through a business entity. See HowTo_CreateBusinessEntityObject.

## Instantiating the Business Object

A business object can be used by declaring a local variable of the type of the business object, and subsequently, creating an instance of the business object using the ```NewInstance()``` static/shared method (if you chose the static instantiation helper approach from above):

```cs
using (var biz = ProductBusinessObject.NewInstance())
{
    var dataset = biz.GetList();
}
```

If you chose to go with a standard constructor, the object can be create like this:

```cs
using (var biz = new ProductBusinessObject())
{
    var dataset = biz.GetList();
}
```

Note that whatever app is using this business object needs to be configured to know how to access the database. This is explained in detail in the data configuration how-to, but for now, you can create an app.config (or web.config) file with the following contents (replace this with settings that match your SQL Server setup):

```xml
<xml version="1.0" encoding="utf-8" ?>
<configuration>
   <appSettings>
      <add key="DataServices" value="SqlDataService"/>
      <add key="database:UserName" value="devuser"/>
      <add key="database:Password" value="devuser"/>
      <add key="database:Server" value="(local)"/>
      <add key="database:Catalog" value="Northwind"/>
   </appSettings>
</configuration>
```

## Creating Business Objects that use non-Guid based Primary Keys 

In general, Milos uses Guid-based primary keys to identify records in a database. However, we will not always have the luxury of specifying that. For instance, we may have to work with an existing database that uses other key types. Or perhaps, Milos needs to interact with a database that does not support guids. Therefore, Milos business objects also support integer (identity and non-identity) based keys as well as string-based keys.

### Using Integer (Identity) Keys

The basic concept used by business objects that use auto-increment (identity) integer fields as primary keys is identical to guid-based business objects. However, in addition to specifying the master entity and primary key field names, we also have to switch the business object into IndentityKey mode. The following example shows a simple C# exmaple for a business object that can modify the Employees table in the SQL Northwind database example:

```cs
public class EmployeeBusinessObject : BusinessObject
{
   protected override void Configure()
   {
      MasterEntity = "Employees" ;
      PrimaryKeyField = "EmployeeID" ;
      PrimaryKeyType = KeyType.IntegerAutoIncrement; 
   }
}
```

The important part is the very last line of this sample, which indicates to the business object that an auto-increment primary key is to be used.

Note that there are some far-reaching implication when using identity keys. Whenever an identity key is used, data that is created on the client machine needs to be assigned a temporary key until that data is saved to the database, which will trigger the actual creation of the real identity key. Milos automatically creates temporary keys on the client. All these temporary keys are negative (minus) values (and are therefore easy to spot). Once the data gets saved to the database, SQL Server (or whatever the database may be) will automatically create real identity keys. At that point, the data on the client is out of sync with the data on the server. While "real" keys are used on the server, the client still has the temporary keys. Milos will therefore query the database to retrieve the keys that were actually generated and replace the temporary key in the updated row, so the row can later be re-identified. There are a few important aspects to this however:

This requires that Milos has access to the database in some form, so the actual keys can be queried. This means that integer keys can not be used in offline scenarios.

> Important: Tables that are related to the table that was just saved were linked through the temporary keys. For instance, if we create a new employee, that employee will receive the key "-1" until it is saved the first time. Once the employee is saved, that id will be replaced with a real indentity key, such as "25". Milos handles all of that automatically. However, there might be related tables, such as EmployeeTerritories. Lets say we also created 2 new territories, which will receive the keys -1 and -2 respectively. Both of those records would be linked to employee -1. Once employee -1 is assigned the key 25 however, the two territory records become orphaned untill their foreign key is updated to 25 as well. This needs to be handled in the PrimaryKeyValueChanged() method of the business object. (See also: HowTo_CreateChildItemsInEntities).

### Using Non-Identity Integer Keys

Generally, integer keys will be identity keys, meaning that the database server will automatically assign unique (generally sequential) key values. However, integer keys could also be manually generated. This is supported by the Milos framework as well (although it is error prone and therefore not recommended).

The main difference between identity and manual integer keys is that the key type of the business object has to be set to ```KeyType.Integer```, and also, the ```GetNewIntegerKey()``` method has to be overridden as in the following example:

```
public class EmployeeBusinessObject : BusinessObject
{
   protected override void Configure()
   {
      MasterEntity = "Employees";
      PrimaryKeyField = "EmployeeID";
      PrimaryKeyType = KeyType.Integer;
   }

   public  override int GetNewIntegerKey(string entityName, DataSet dataSetWithNewRecord)
   {
      return 1;  // Return a real value here, rather than 1
   }
}
```

The tricky part is that the ```GetNewIntegerKey()``` method needs to return a unique key value (which is not the case in the example above). It is up to the the developer to ensure that the routine creates a key that is unique. This could be achieved through some kind of mechanism that generates unique integers, such as a "pseudo guid" algorithm. Another popular implementation would be to connect to the database and query some kind of key table.

### Using String Keys

Milos also supports the use of string keys in addition to Guids and Integers. The situation with string keys is similar to the situation with manual integer keys (see above) in that the developer has to make sure that whatever key gets generated is unique. However, string keys are somewhat easier to handle than manual integers, because strings could be converted guids. This is the default string-key implementation milos provides. String keys can be used in the following fashion:

```cs
public class CustomerBusinessObject : BusinessObject 
{
   protected override void Configure()
   {
      MasterEntity = "Customers";
      PrimaryKeyField = "CustID";
      PrimaryKeyType = KeyType.String;
   }
}
```

This example assumes that the CustID field is a string field that is big enough to hold the string representation of a Guid. This may not always be the case though. Often, a string key may be an abritrary alphanumeric key generated by some type of algorithm, as in the following example:

```cs
public class CustomerBusinessObject : BusinessObject
{
   protected override void Configure()
   {
      this.MasterEntity = "Customers";
      this.PrimaryKeyField = "CustID";
      this.PrimaryKeyType = KeyType.String;
   }

   public  override int GetNewStringKey(string entityName, DataSet dataSetWithNewRecord)
   {
      return "NEWKEY" ;  // Return a real value here, rather than "NEWKEY"
   }
}
```

As in the integer example above, this version would not work in a real-live application, since the value needs to be unique. Once again, a more sophisticated algorithm would be required. 
