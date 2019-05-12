# Using Local Transactions

Local transactions are transactions that occur within a single data source, such as a database in SQL Server. Many of the transactions needed by Milos applications fall under this category. The exception are transactions that span across multiple databases, such as transactions that involve a table in SQL Server, and another table in an Oracle database. That would be considered a "distributed transaction" which is not covered in this topic.

## Using Transactions within a Business Object

Often transactions within a single method of a business object must either all succeed or all be undone. For instance, the following example represents to insert operations triggered from within a method in a business object:

```cs
var cmd1 = NewDbCommand("INSERT INTO Customers (cName) values ('New Customer;)");
ExecuteNonQuery(cmd1);
var cmd2 = NewDbCommand("INSERT INTO Orders (cCustomerName) values ('New Customer')");
ExecuteNonQuery(cmd2);
```

If one of these operations fail, both of them need to be abandoned and should never reach the database. This can be accomplished by wrapping a transaction around the two operations. This can be accomplished easily in the following fashion:

```cs
BeginTransaction();
try
{
    var cmd1 = this.NewDbCommand("INSERT INTO Customers (cName) values ('New Customer;)");
    ExecuteNonQuery(cmd1);
    var cmd2 = this.NewDbCommand("INSERT INTO Orders (cCustomerName) values ('New Customer')");
    ExecuteNonQuery(cmd2);
}
catch
{
    AbortTransaction();
}
finally
{
    CommitTransaction();
}
```

Note that transactions are supported natively by business objects and are not database specific. The above code can run against any database. However, it is important that all tables involved in the transaction reside in the same database (whatever that database might be).

Note that both commands could exist in separate methods as in the following example:

```cs
public void UpdateCustomers()
{
    BeginTransaction();
    try
    {
        var cmd1 = this.NewDbCommand("INSERT INTO Customers (cName) values ('New Customer')");
        ExecuteNonQuery(cmd1);
        UpdateOrders();
    }
    catch
    {
        AbortTransaction();
    }
}

public void UpdateOrders()
{
    try
    {
        var cmd2 = this.NewDbCommand("INSERT INTO Orders (cCustomerName) values ('New Order')");
        ExecuteNonQuery(cmd2);
        CommitTransaction();
    }
    catch
    {
        AbortTransaction();
    }
}
```

Therefore: A transaction can end in a different method than it starts. However, this tends to be very confusing and it is not recommended to do this. Instead, consider creating a third template method:

```cs
public void UpdateCustomersAndOrders()
{
    BeginTransaction();
    try
    {
        UpdateCustomers();
        UpdateOrders();
        CommitTransaction();
    }
    catch
    {
        AbortTransaction();
    }
}

public void UpdateCustomers()
{
    var command = this.NewDbCommand("INSERT INTO Customers (cName) values ('New Customer')");
    ExecuteNonQuery(command);
}

public void UpdateOrders()
{
    var command = this.NewDbCommand("INSERT INTO Orders (cCustomerName) values ('New Order')");
    ExecuteNonQuery(command);
}
```

## Using Transactions with Multiple Business Objects

Transactions can also span multiple business objects. Note that for transactions to be shared by multiple business objects, all involved business objects must use the same underlying database. If this isn't the case, distributed transactions have to be used instead!

To share transactions across multiple business objects, first instantiate the objects, then call the ```ShareDataContext()``` method on one business object, and pass the second business object as a parameter. Then, use the ```SharedDataContext``` property/reference to initiate transactions:

```cs
using (var customers = CustomerBusinessObject.NewInstance())
using (var orders = OrderBusinessObject.NewInstance())
{
    customers.ShareDataContext(orders);

    customers.SharedDataContext.BeginTransaction();
    try
    {
        customers.UpdateCustomers();
        orders.UpdateOrders();
        customers.SharedDataContext.CommitTransaction();
    }
    catch
    {
        customers.SharedDataContext.AbortTransaction();
    }
}
```

Note: It does not matter which business object the ```ShareDataContext()``` method is called on, and which business object is passed as the parameter. Similarly, it does not matter, which business object's ```SharedDataContext``` property is used, since it references the same context internally.

This can be done with as many business objects as desired. For instance, the following example is perfectly valid:

```cs
var customers = CustomerBusinessObject.NewInstance();
var orders = OrderBusinessObject.NewInstance();
var invoices = InvoiceBusinessObject.NewInstance();
var payments = PaymentsBusinessObject.NewInstance();
customers.ShareDataContext(orders);
customers.ShareDataContext(invoices);
customers.ShareDataContext(payments);
```

It is also interesting to note that the individual methods being called within the shared transactions do not have to be designed to be transactional. Even if the methods themselves do not have any transactional logic, they will be wrapped inside the shared transaction and thus be handled in a transactional fashion. The two methods used in the example above could thus be implemented like so:

```cs
public void UpdateCustomers()
{
    var command = NewDbCommand("INSERT INTO Customers (cName) values ('New Customer;)");
    ExecuteNonQuery(command);
}

public void UpdateOrders()
{
    var command - NewDbCommand("INSERT INTO Orders (cCustomerName) values ('New Customer')");
    ExecuteNonQuery(command);
}
```

However, the methods can themselves be transactional. The methods would also work perfectly if they were constructed like this:

```cs
public void UpdateCustomers()
{
    BeginTransaction();
    try
    {
        var command - NewDbCommand("INSERT INTO Customers (cName) values ('New Customer;)");
        ExecuteNonQuery(command);
        CommitTransaction();
    }
    catch
    {
        AbortTransaction();
    }
}

public void UpdateOrders()
{
    BeginTransaction();
    try
    {
        var command = NewDbCommand("INSERT INTO Orders (cCustomerName) values ('New Customer')");
        ExecuteNonQuery(command);
    }
    catch
    {
        AbortTransaction();
    }
}
```

What is interesting in this scenario is that when each method is called independently from the shared transaction, they are themselves transactioned (and could contain multiple calls to the database that are wrapped in a local transaction). However, if they are wrapped in a larger shared transaction, they work in the larger context. That means that the call to ```CommitTransaction()``` in the first method will commit the transaction if the transaction is local, but it will only indicate fundumental success if it runs in a shared transaction. Overall, the shared transaction will only be commited to the database when the shared transaction ends. If one of the involved objects calls ```AborTransaction()``` in a shared scenario, the system raises an ```AbortedSharedTransactionException```, which is cought by the try/catch in the calling code, allowing the caller to abort the shared transaction (as in the example above).

## Using Transactions with Business Entities

Business entities are always saved in a transacted manner. This means that if business objects contain multiple tables (as in parent-child scenarios), all updates are contained within a transaction. Therefore, developers do not have to manually initiate transactions when saving single business entities.

In scenarios where multiple business entities need to be saved in a single atomic transaction, the static ```AtomicSave()``` method on the ```BusinessEntity``` class can be used. This method takes an array of any number of business entities and wraps all saves into an atomic transaction:

```cs
var customer = CustomerBusinessEntity.LoadEntity(...);
var order1 = OrderBusinessEntity.LoadEntity(...);
var order2 = OrderBusinessEntity.LoadEntity(...);

// Change entities here

var entities = {customer, order1, order2};
try 
{
    BusinessEntity.AtomicSave(entities);
}
catch
{
    MessageBox.Show("Save failed!");
}
```

> Note: It is also possible to simply share data contexts of the business objects associated with the business entities and then initiate transactions manually, but it is easier to use the ```AtomicSave()``` method.

## Using Transactions with Business Objects and Business Entities

It is possible for business objects and business entities to share the same data context. The following example spans a transaction around a business object and a business entity:

```cs
using (var customer = CustomerBusinessEntity.LoadEntity(...))
using (var orders = OrderBusinessObject.NewInstance())
{
    customer.Name = "Smith";
    orders.ShareDataContext(customer);
    orders.SharedDataContext.BeginTransaction();
    try
    {
        customer.Save();
        orders.UpdateSomething();
        orders.SharedDataContext.CommitTransaction();
    }
    catch
    {
        orders.SharedDataContext.AbortTransaction();
    }
}
```

Note that the ```ShareDataContext()``` method of the business object can be called with an entity as the parameter, rather than another business object. This can be done with any combination and any number of business objects and entities.