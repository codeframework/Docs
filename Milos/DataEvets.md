# Using Data Events

Whenever data base commands are executed against a database back end, events are fired for a number of reasons. Most of these events are triggered by the data service. However, most developers never come in direct contact with data service objects, since data services are instantiated and used behind the scenes. For this reason data events are exposed as static events on the data service factory object. The factory automatically hooks events on all data service objects it creates and passes them on. In addition, the data service factory also fires a few events on its own. In sum, the factory object is a single mediator object that makes all data events available in a single place, which makes it the prime object of interest for all data events.

> Note: In special cases, developers may write low-level code that hooks data service events directly. The signature of those events is the same on data services and on the data service factory.

## Detecting New Data Connections

Whenever Milos needs to talk to data, it initializes a data service (either by creating a new service object, or by recycling an existing one). Whenever a data service gets initialized successfully, the factory object raises a ```DataServiceInitialization``` event. This event (like all data service factory events) is a static event. This means that the event can be hooked on the ```DataServiceFactory``` class, without first instantiating that object.

Before the event can be used, we need to create an event handler method, which will be executed whenever the event fires. The following code shows such a method:

```cs
public void DataServiceInitializationHandler(object sender, DataServiceInitiationEventArgs e)
{
    MessageBox.Show(e.ConnectionStatus.ToString());
}
```

Once we have such a method, we can hook it up the the data service factory object's ```DataServiceInitialization``` event:

```cs
DataServiceFactory.DataServiceInitialization += new DataServiceInitiationEventHandler(DataServiceInitializationHandler);
```

Note that it is not required (or desired) to instantiate the ```DataServiceFactory``` before executing this line of code, since this event is static.

> Note: This assumes that the above method is a member of the same object, as the object that runs the line of code. 

The result of this is that whenever a new service is initialized, we see a message box that shows the connection status. Possible values include ```Offline```, ```OnlineInternet```, and ```OnlineLAN```.

If is of course also possible (and common) to use lambdas eather than point at another method:

```cs
DataServiceFactory.DataServiceInitialization += new DataServiceInitiationEventHandler((s, e) => { /* do something */ };
```

## Reacting to Completed Queries

Whenever data is queried and returned in a DataSet, the data service factory raises a ```QueryComplete``` event (this is true for synchronous and async queries, no matter whether they were executed by simple commands or stored procedure calls) . ```QueryComplete``` event handlers have access to the data set and all its data, as well as other information, such as the command executed to generate the data as well as the name of the table within the data set that was created by the executed command. Note that the data set may contain other data that may have been queried previously or that has been generated manually.

This event requires an event handler similar to the following:

```cs
private void DataServiceFactory_QueryComplete(object sender, QueryEventArgs e)
{
    Console.WriteLine(e.ResultDataSet.Tables[e.QueriedEntity].Rows.Count);
    Console.WriteLine(e.GetSerializedCommandXml());
}
```

This event handler can be hooked to the event with the following code (C#):

```cs
DataServiceFactory.QueryComplete +=
    new QueryEventHandler(DataServiceFactory_QueryComplete);
```

## Reacting to Completed Scalar Queries

Scalar queries raise a ```ScalarQueryComplete``` event, which is very similar to the ```QueryComplete``` event. The main difference is that these queries do not return data sets, but single values. The resulting value is accessible through the Result property.

The following event handling code is required to hook scalar query complete events:

```cs
private void DataServiceFactory_ScalarQueryComplete(object sender, ScalarEventArgs e)
{
    Console.WriteLine(e.Result);
    Console.WriteLine(e.GetSerializedCommandXml());
}
```

This event handler can be hooked ot the event with the following code:

```cs
DataServiceFactory.ScalarQueryComplete += new ScalarEventHandler(DataServiceFactory_ScalarQueryComplete);
```

## Reacting to Completed Non-Query Commands

Database commands that do not return a query result (such as UPDATE commands) raise a ```NonQueryComplete``` event. This event is similar to the last two events, but instead of returning a result or result set, this event provides access to the number of records affected by the command.

The following event handling code is required to hook a non-query event:

```cs
private void DataServiceFactory_NonQueryComplete(object sender, NonQueryEventArgs e)
{
    Console.WriteLine(e.AffectedRecords);
    Console.WriteLine(e.GetSerializedCommandXml());
}
```

This event handler can be hooked ot the event with the following code:

```cs
DataServiceFactory.NonQueryComplete += new NonQueryEventHandler(DataServiceFactory_NonQueryComplete);
```
