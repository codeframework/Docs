# Creating Special Data Loading Scenarios

This document deals with special data loading mechanisms within business entities. These mechanisms are meant to be used by advanced users. If you are not sure what all of this means and why you would use it: Don't worry about it! You probably do not need these things...

## On Demand Loading a.k.a. "Lazy Loading"

Business entities are generally compositions of multiple records from different tables. Each business entity always has one base record representing the entity, but often there are secondary tables that may have more than one record. In typical scenarios, all that data is loaded when the entity is initially loaded into memory (see HowTo_CreateChildItemsInEntities for an example). However, there are some scenario where loading all data initially is not desired (typically for performance reasons). For instance, a name entity may have binary information (such as pictures of the person) attached, which takes a lot of resources and time to load. However, this binary information may not be needed in all instances. In such as scenario, the binary information (and the collection representing that data in the business entity) can be loaded on demand only.

This example modifies the example created in HowTo_CreateChildItemsInEntities in a way that loads the product images collection on demand. To do so, we can keep the same collection and product image items objects. However, we have to modify the ProductBusinessEntity? object, so the collection does not get loaded right away and instead gets only loaded when needed. Here is the modified class:

```cs
public class ProductBusinessEntity : Milos.BusinessObjects.BusinessEntity
{
    ProductImagesCollection imagesCollection; 

    public ProductImagesCollection ProductImages
    {
        get
        {
            if (imagesCollection == null)
            {
                // If this is the first time the collection is accessed, it will not exist, so we have to load it.
                imagesCollection = new ProductImagesCollection(this);
                if (!InternalDataSet.Tables.Contains("productImages"))
                {
                    var products = (BusinessObject)AssociatedBusinessObject;
                    products.LoadSecondaryTablesOnDemand("productimages", PK, InternalDataSet);
                }
                imagesCollection.SetTable(InternalDataSet.Tables["productimages"]);
            }
            return imagesCollection;
        }
    }

    // This is now not needed anymore!
    // public void override LoadSubItemCollections()
    // {
    //    imagesCollection = new ProductImagesCollection(this);
    //    imagesCollection.SetTable(InternalDataset.Tables["productimages"]);
    // }}

    // Rest of class continues as in the previous example
```

When doing on-demand loading, the collection is not loaded initially, but only when accessed the first time. Therefore, the accessor (get) of the collection's property checks for the existance of the collection. If the collection is not there yet, it gets instantiated, and subsequentuially, the table with the underlying data is set as the collection's data source. However, since that table is not yet loaded, we have to give the business object the chance to load the required data. We do so by calling the '''LoadSecondaryTablesOnDemand()'''' method and pass along the name of the table and the primary key of the parent table, as well as the data set the data it to be filled into. It is important however to add a check that verifies that the table is not yet loaded, since there are some scenarios (such as new entity scenarios) where the table may already exist.

> Note that ```LoadSecondaryTablesOnDemand()``` is not a member of the ```IBusinessObject``` interface, since other business objects that customers or other third parties may create will probably not support on demand loading. Therefore, this method is a member of the default ```BusinessObject``` implementation. To use that functionality, we have to retrieve the default business object using the ```GetBusinessObject()``` method of the entity and then cast it to a ```BusinessObject``` type. Off course, this will only work for business objects derrived from Milos business objects.

In addition to changes to the entity, we also have to code the business object slightly different, so it does not load the product images table right away, but only when needed. This has a few side effects, since the table may never be loaded, therefore, other operations (such as save) have to be aware of that possibility. Here is the modified version of the business object:

```cs
public class ItemBusinessObject : Milos.BusinessObjects.BusinessObject
{
   // Previously existing code goes here...

    // This method has changed slightly from before
    public override bool SaveSecondaryTable(Guid parentPk, DataSet existingDataSet)
    {
        var retVal = true;
        if (existingDataSet.Tables.Contains("productimages"))
            retVal = SaveTable(existingDataSet.Tables["productimages"]), "image_pk");
        return retVal
    }

    // This method remains unchanged, since an empty new table does not hurt us
    public override void AddNewSecondaryTables(Guid parentPk, DataSet existingDataSet)
    {
        NewSecondaryEntity("productimages", existingDataSet);
    }

    // This method is now not required anymore since we moved the functinality into the LoadSecondaryTablesOnDemand() method. Note that if more than one table is loaded here, and the other tables are NOT loaded on-demand, this method may still be required.
    // protected override void LoadSecondaryTables?(ByVal parentPk As Guid, DataSet existingDataSet)
    // {
    //     using (var select = GetSingleRecordCommand("ProductImages", "*", "product_fk", parentPk))
    //     {
    //         ExecuteQuery(select, "productimages", existingDataSet);
    //     }
    // }

    // This method is called by the entity to load the tables when needed
    protected override void LoadSecondaryTablesOnDemand(string tableName, Guid parentPk, DataSet existingDataSet)
    {
        if (tableName == "productimages")
            QueryMultipleRecordsByKey("ProductImages", "*", "product_fk", parentPk, existingDataSet)
    }
}
```

The most important change in this method is in the loading code, since we moved the loading operation for the product images table to the ```LoadSecondaryTablesOnDemand()``` method, which we call from the business entity (see above). Note that it is important to check for the passed table name, and only load the required table (especially in cases where more than one table could be loaded on demand).

Since the product images table may or may not exist, we also have to slightly modify the ```SaveSecondaryTables()``` method, so we only attempt to save the table when it is actually there.

An interesting aspect of this example is that the ```AddnewSecondaryTables()``` has not changed. (This method gets called whenever a new entity is created). The reason is that there is no downside of creating an empty data structure for on-demand tables. Not creating this empty table however would create very odd side effects which we avoid by creating the empty structure in all scenarios.

## Background Loading a.k.a "Async Loading"

In many scenarios, data may be needed as soon as possible, at the same time, the developer may not want the application to "wait" and appear frozen while the data loads. For instance, a customer's complete order history may be very important, but at the same time, it may be on a different page in the customer edit screen and it is more important to load that screen quickly than it is to have 100% of the data available right away. Order history can be populated as soon as it has been loaded in the background. Until then, the user can still manipulate other customer data.

Milos supports these scenarios on business entities through a technique known as "background loading". Milos can background-load secondary tables on business entities. For the purpose of this document, we can continue with the example from above and assume we want to load the product images data in the background when the item business entity loads.

The main idea of background loading is to load collections immediately when a business entity loads. Roughly at the same time, we also want to initialize data loading. However, the data that is being loaded won't be available for a little while. Therefore, the collection will remain in unloaded state until the data loading operation has completed in the background, which will be indicated to the business entity when the BackgroundLoadComplete?() method is called. At that point, we can use the new data and assign it to the collection, which is now fully functional.

To implement this, we have to change our business entity to the following:

```cs
public class ProductBusinessEntity : Milos.BusinessObjects.BusinessEntity
{
    ProductImagesCollection imagesCollection;

    // Back to returning a simple collection
    public ProductImagesCollection ProductImages
    {
        get => imagesCollectionl;
    }
    
    // Back to loading the collection right away, although without data
    protected override void LoadSubItemCollections()
    {
       imagesCollection = new ProductImagesCollection(this);
       imagesCollection.SetTable(InternalDataSet.Tables["productimages"]);
    }

    // New method fired whenever Milos decides background loading should be initiated
    protected override void InitiateBackgroundLoading()
    {
        var items = (ItemBusinessObject)AssociatedBusinessObject;
        var callback = new AsyncCallback(BackgroundLoadResultHandler);
        items = LoadSecondaryTablesAsync("productimages", PK, InternalDataSet, callback);
    }

    // This method fires whenever background loading has been completed
    protected override void BackgroundLoadComplete(DataSet resultDataSet, string tableName)
    {
       if (tableName == "productimages")
           imagesCollection.SetTable(resultDataSet.Tables[tableName]);
    }
    
    // Rest of class continues as in the previous example
```

Most of this code is the same every time. Note that the actual process of background loading is somewhat sophisticated. However, almost all the complexity of this task is abstracted away. The only small glimps of the underlying functionality is the need to instantiate a Callback object in ```InitiateBackgroundLoading()```. However, the task is a simple one, since the code remains the same in every implementation. The only thing that changes is the table name that gets passed to ```LoadSecondaryTablesAsync()```.

Talking about ```LoadSecondaryTablesAsync()```: This is the only missing piece to the puzzle at this point. We have to add that method to our business object:

```cs
public class ItemBusinessObject : Milos.BusinessObjects.BusinessObject
{
    // Previously existing code goes here...

    // This method is called by the entity to load the tables in the background
    // This method replaces LoadSecondaryTablesOnDemand?() from the previous example
    protected override void LoadSecondaryTablesAsync(string tableName, Guid parentPk, DataSet existingDataSet, AsyncCallback callback)
    {
        if (tableName == "productimages")
        {
            var command = GetMultipleRecordsByKeyCommand("ProductImages", "*", "product_fk", parentPk);
            ExecuteQueryAsync(command, "productimages", existingDataSet, callback);
        }
    }
}
```

As you can see, the method used to load the tables is still very straightforward. The main difference is that we use an async query method on the business object. When we do that, we have to pass a command object (which we can generate easily in a number of ways... the above example is only one possibility) and the callback object which we receive as a parameter and simply pass along.

Voila, we have background loading implemented! Note that users of our business entity should now be aware of the fact that the ProductImages? collection may not be available right away. It is easy to respect these settings, as there are properties that indicate when background loading is complete. Here is an example:

```cs
var product = new ProductBusinessEntity(pk);
if (product.ProductImages.LoadState == EntityLoadState.LoadComplete)
{
    // The collection is ready for use...
}
```

> Note: All async loading is implemented on the ```BusinessObject``` and ```BusinessEntity``` classes. This is NOT done on an ```IBusinessEntity``` or ```IBusinessObject``` interface level. Therefore, non-Milos implementations of ```IBusinessObject``` and ```IBusinessEntity``` may not support this functionality.

## Semi Advanced Topic: Async Loading with Business Objects

> Note: **All async loading is currently in the process of being re-engineered.** Original Milos async loading was done on the thread-pool with manual operations. We have temporarily switched all operations to .NET async (using ```await``` and ```async``` syntax). However, we are currently evaluating what the most efficient way is to handle this going forward, because as it turns out, .NET async is not really the best option for all these scenarios. Current documentation here is still based on original Milos async operations.

As implied int he above example, business objects and data services have the ability to execute queries asynchronously. This functionality is implemented on the abstract DataService? class. All data services inheriting from this class (which means all default Milos data services) therefore support asynchronous queries.

Executing an query asynchronously is very similar to executing a regular query. Consider the following standard query for instance:

```cs
var command = NewDbCommand("SELECT * FROM Customers");
ExecuteQuery(command,"customers");
```

We can execute this query asynchronously in a very similar fashion. However, there are two important points due to the fact that asynchronous queries can not return data directly (since waiting for the return value would defeat the purpose): 1) We always have to pass an existing dataset. If we do not have one, we can simply create a new one. If we are loading secondary tables, it is of course OK to pass along a dataset that already contains other data. 2) We have to somehow receive notification when the query is completed. Otherwise, we would never be able to access the data returned from the query. We can do this by creating a method the business object can call whenever loading is complete.

Creating the dataset it trivial. Creating a method that can be called whenever the load operation is complete is also not very difficult, but it requires some additional explanation. The basic idea is to create a method that accepts a parameter that is an AsyncResult? object, which can then be used to retrieve the result dataset. This method can reside anywhere, such as on the user interface or the business object. Here is what needs to go into the handler method:

```cs
protected void YeahItIsDone(IAsyncResult result)
{
    if (result is AsyncResult)
    {
        AsyncResult result2 = (AsyncResult)result;
        if (result2.AsyncDelegate is ExecuteQueryDelegate)
       {
            var del = (ExecuteQueryDelegate)result2.AsyncDelegate;
            var dsResult = del.EndInvoke(result);
        }
    }
}
```

The name of the method may change, but the actual code is always the same.

Now that we have that method, we can finally call our async query like so:

```cs
var command = NewDbCommand("SELECT * FROM Customers");
var resultDataSet = new DataSet();
var callback = new AsyncCallback(whatever.YeahItIsDone);
ExecuteQuery(command, "customers", resultDataSet,callback);
```

The query will now execute on a secondary thread and whenever the query is done, the ```YeahItIsDone()``` method will be invoked, which retrieves the resulting dataset.

> Note: The ```YeahItIsDone()``` method will be executed on the secondary thread as well. This means that this method should only interact with objects that are thread-safe. Windows Forms UI controls are NOT thread-safe. Therefore, you should not take the resulting dataset and assign it as the control source of a control (or similar operations). Instead, you will have to use the ```Invoke()``` method to funnel the dataset back to the UI thread. Some of the Milos controls also have thread-safe methods that can be used safely. These methods can be spotted easily by their ```_TS suffix```. For instance, ```SetDataSet()``` is NOT thread-safe, but ```SetDataSet_TS()``` is. 
