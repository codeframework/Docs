# Stored Procedure Facade

> Note: Support for Stored Procedure Facades is mainly provides for legacy reasons. This feature is not very commonly used for new development anymore, as most people seem to have moved away from using stored procedures for all data access.

The stored procedure facade concept allows for the simulation of stored procedures for databases that do not support stored procedures. The facade takes advantage of the fact that stored procedures are very simple in concept. Stored procedures simply are applets that exist between a client and a database. In normal cases, we think of stored procedures as "living inside the database". However, this is not a requirement. In fact, on a more abstract level, we can think of stored procedures as "in between" applets that have a name and a certain number of parameters with certain names and types. It could exist anywhere, even inside the calling application. This concept enables us to support "stored procedures" (simulated or real) on any given database environment.

## A Simple Stored Procedure Example

Consider the following example: An application has been built based on the assumption that it runs on SQL Server. The application queries from a names table based on a first name and last name parameter. A business object is used for this purpose, and it in turn uses a GetDBNames? stored procedure in the following fashion:

```cs
// Business object code
public DataSet GetNames(string firstName, string lastName)
{
    using (var cmd = NewDbCommand())
    {
        cmd.CommandText = "GetDBNames";
        cmd.CommandType = CommandType.StoredProcedure;
        AddDbCommandParameter(cmd, "@FirstName", "Markus%");
        AddDbCommandParameter(cmd, "@LastName", "Egger%");
        ExecuteStoredProcedureQuery(cmd, "Names");
    }
}
```

This of course assumes that there is a stored procedure like the following in SQL Server:

```sql
CREATE PROCEDURE GetDBNames 
    @FirstName nvarchar(100),
    @LastName nvarchar(100)
AS
BEGIN
    SET NOCOUNT ON;
    SELECT * FROM Names WHERE cLastName LIKE @LastName AND cFirstName LIKE @FirstName
END
```

This works fine with SQL Server, and it is a good architectural approach, because the stored procedure can be re-created on a different database in a fashion that is ideal for that database. However, when the system is to be converted to a different database that does not support stored procedures, it is not possible to reproduce the desired procedure in the database. This could happen with a database such as SQL Server Everywhere Edition.

## The Facade Solution

To move our application to SQL Server Everywhere Edition, we can reproduce the equivalent of the above stored procedure in .NET code. The first step in doing so is to subclass the default stored procedure facade object. Subsequently, we add the equivalent of the above stored procedure in .NET code:

```cs
public class TestFacade : Milos.Data.StoredProcedureFacade
{
    public DataSet GetDbNames(string firstName, string lastName)
    {
        using (var cmd = DataService?.NewDbCommand())
        {
            cmd.CommandText = "SELECT * FROM Names WHERE cLastName LIKE @LName AND cFirstName LIKE @FName";
            cmd.Parameters.AddWithValue("@LName", firstName);
            cmd.Parameters.AddWithValue("@FName", lastName);
            return DataService.ExecuteQuery(cmd, "Name");
        }
    }
}
```

As you can see, the method is conceptually very similar to the stored procedure above, although it is written in pure .NET code. The SQL Everywhere dataservice can now use this facade instead of real stored procedures, allowing the business object to remain unchanged. All that is left to do is configure the system to use this facade, which can be done in a configuration file, such as app.config:

```xml
<xml version="1.0" encoding="utf-8" ?>
<configuration>
   <appSettings>
      <!-- other settings go here -->
      <add key="database:StoredProcedureFacade" value="MyAssembly/MyNamespace.TestFacade"/>
   </appSettings>
</configuration>
```

This means that the data service automatically loads this facade when it is instantiated, thus enabling simulated stored procedure execution. (Note: If the system/service is configured to use a database configuration prefix other than "database", then the setting also needs to use that specific prefix.

Note: It is also conceivable to create an instance of facade manually and attach it to the service in code. This can be done in a number of places. However, the best approach for this might be to hook the data service factorie's ```DataServiceInitialization``` (static) event. For more information on data events, see HowTo_UseDataEvents.

## Some Points of Interest

Note that there are a few small differences between the stored procedure and the method on the facade (as well as a few other points of interest):

The name of the method is not exactly identical to the name of the stored procedure. The name match is not case sensitive.

The same is true for the parameters. They are also not case sensitive.
Parameters on the stored procedure and in the command object's parameters collection have names that start with "@". The names of the parameter on the facade object do not start with an "@". This is OK. The facade feature handles that difference.

The facade uses parameters based on their names, not sequence. It therefore does not matter in which sequence parameters get added to the command object's parameters collection and in which sequence they are in the method. In essence, this simulates named parameters.

Whenever a method creates a DataSet as the return value, the table within the DataSet has a certain name ("Name" in the above example). However, the name will be changed based by the facade, based on the desired result name indicated by the caller/ business object ("Names" in this example).

When the method returns a ```DataSet```, it always is a stand-alone ```DataSet```. However, the business object may specify that it expects the result set as part of a previously existing DataSet. In that case, the facade will merge the new result set into the existing one, this achieving the behavior expected by the caller.
Note that the facade object has a reference to the data service that is using the facade called ```DataService```. This is a handy way for the facade to use features that are available on the data service, such as the ability to execute a standard query. (Note: This may work with some services and not at all with others, depending on the nature of the service. It is up to the implementor of each facade to know what is appropriate).
 
## ExecuteQuery, ExecuteScalar, ExecuteNonQuery

In general, database commands can be executed in 3 different ways: Comamnds can be queries, non-queries, and scalar queries. A query returns a result set (DataSet), a scalar query a single value, and a non-query the number of affected records.

Whenever a Facade is called by the caller (such as a business object) using a query (```ExecuteQuery``` or ```ExecuteStoredProcedureQuery```), then only methods that return a DataSet can be called on the facade. For scalar queries, methods that return a single result or a ```DataSet``` can be used (for ```DataSets```, only the first field in the first row of the first table is returned). For non-queries, the method on the facade must return an integer value.

> Note: Methods on the facade that are executed through ```ExecuteScalar()``` can theoretically return any .NET type. However, since this simulates stored procedures, it only makes logical sense to return types that have ```System.Data.DbType``` equivalent types. (The only exception is a ```DataSet```).

## Entirely Different Facades

The above example is a very simple example where the method in the facade is very similar to the stored procedure. Note that the facade could be vastly different from the stored procedure. For instance, we could write a facade that allows access to XML. In that case, an XML document could be loaded using an XmlDocument object. Then, data could be parsed out of the XML document using XPath, and finally, a compatible ```DataSet``` could be constructed from that information, resulting in a simulated stored procedure that is capable of executing against XML.

Here is a simple example for such a facade:

```cs
public class CustomerStoredProcedures : StoredProcedureFacade
{
    public DataSet GetDbNames(string firstName, string lastName)
    {
        // Load the XML source as a document
        var customerXml = new XmlDocument?();
        customerXml.Load(@"c:\Customers.xml");

        // Create an xpath search expression that can be used for the provided parameter
        // (Note: This simple example demonstrated this for one parameter only)
        string searchExpression;
        if (firstName.EndsWith("%"))
            searchExpression = "RootNode/Customers?[starts-with(CompanyName,'"+ firstName.Replace("%",string.Empty) +"')]";
        else
            searchExpression = "RootNode/Customers[CompanyName = '"+ firstName +"']";

        // Query all the matches from the XML document
        var customersToReturn = customerXml.SelectNodes(searchExpression);

        // Create a new in-memory XML stream that is DataSet compatible
        var sb = new StringBuilder?();
        sb.Append("<?xml version=\"1.0\"?><NewDataSet>");
        foreach(var customer in customersToReturn)
            sb.Append("<Customers>"+ customer.InnerXml +"</Customers>");
        sb.Append("</NewDataSet>");

        // Create a new DataSet and load it from the XML string
        var dsResult = new DataSet();
        dsResult.ReadXml(new System.IO.StringReader(sb.ToString()));
        return dsResult;
    }
}
```

> Note: There are more efficient ways of implementing this functionality (especially with LINQ), but this only serves as a simple example. Note also that the exact expressions used vary with the schema (structure) of the XML source document.