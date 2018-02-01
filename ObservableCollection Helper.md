# Observable Collection Helper

_CODE Framework features an ObservableCollectionHelper class in the WPF MVVM namespace. This class provides useful extension methods that are added to ObservableCollection<T> whenever the namespace is in focus. The following is an excerpt from an internal email at EPS/CODE discussing the advantages of using this class:_

Take a look at the following code (Drivers is an ObservableCollection<EmployeeViewModel> in this case):

```cs
if (serviceResponse.DriverResponse.Success)
{
    foreach (var driver in serviceResponse.DriversResponse.Drivers)
    {
        Drivers.Add(new EmployeeViewModel
        {
            EmployeeId = driver.EmployeeId,
            FullName = driver.FullName,
            Reviewed = driver.Reviewed
        });
    }
}
```

This is code that runs when driver data has been retrieved from the server. So it looks over all the data returned and creates client-side models for each of the “records” for each driver. There could be a few hundred perhaps. So each of those EmployeeViewModel drivers gets added to the Drivers collection, which is an observable collection. That collection then fires appropriate events so the UI can issue a refresh notice and re-bind the new data. With a few hundred drivers, that results in a few-hundred refreshes. It actually gets worse: Since more than one screen element may be bound do the Drivers observable collection, each of those will receive a few hundred refreshes. So let’s say we have a list of 500 deliveries, each of which has a drop-down of drivers. Now we have hundreds of binding refresh operations going on for each of the 500 deliveries. So if we had 250 drivers, let’s say, that is 125,000 binding refresh operations. Not good. And then we are still loading checkers and loaders and all kinds of other stuff, which also has the same problem.

When we first went through the app to performance optimize it, we added some manual code to improve this, but we then decided it would probably be better to add something to CODE Framework to make this easier for people. As a result, we now have an ObservableCollection helper in the MVVM namespace. This gives you an AddRange() extension method on the ObservableCollection<T> class. So you can now do this:

```cs
if (driversResponse.Success)
{
    var localList = new List<EmployeeViewModel>();
    foreach (var driver in driversResponse.Drivers)
    {
        localList.Add(new EmployeeViewModel 
        {
            EmployeeId = driver.EmployeeId,
            FullName = driver.FullName,
            Reviewed = driver.Reviewed
        });
    }
    Drivers.AddRange(localList);
}
```

In this example, we are first creating a local list that has nothing to do with the observable collection. I then stuff the data from the service into that local list. Once that list is complete, I then call Drivers.AddRange() and pass that local list. This means that all the new items are now stuffed into the observable collection at once and only one binding refresh is triggered. So that is quite a nice improvement.

Note that you can also do the same thing using LINQ syntax:

```cs
if (driversResponse.Success)
{
    Drivers.AddRange(
        driversResponse.Drivers.Select(driver => new EmployeeViewModel
        {
            EmployeeId = driver.EmployeeId,
            FullName = driver.FullName,
            Reviewed = driver.Reviewed
        }));
}
```

It’s probably a matter of personal preference, but LINQ seems to be a bit nicer here, since you do not manually have to create a local list. The result of the Select() method already is that list. So you can simply perform an AddRange() on that very result.

This also has a few desirable side effects. In particular, this means that you can also create the local list on a background thread, so you do not have to have the noticeable slowdown on the foreground thread whenever the data gets mapped from the service result into the local view models. In the example so far, I have created myself a simple method that calls the service and returns its data. Here’s the one for the Drivers:

```cs
private GetDriversResponse GetDriversFromService()
{
    GetDriversResponse driverResponse = null;
    ServiceClient.Call<IRouteService>(s => {
        driverResponse = s.GetDrivers(new GetDriversRequest { Inactive = false });
    });
    return driversResponse;
}
```

So this returns the raw data from the service and I need to process that data on the foreground thread as discussed above. However, I could actually change this to the following:

```cs
private static EmployeeResult GetDriversFromService()
{
    GetDriversResponse driverResponse = null;
    ServiceClient.Call<IRouteService>(s => {
        driverResponse = s.GetDrivers(new GetDriversRequest { Inactive = false });
    });
    
    return new EmployeeResult
    {
        Success = driverResponse.Success,
        FailureInformation = driverResponse.FailureInformation,
        Employees = droverResponse.Drivers.Select(driver => new EmployeeViewModel
        {
            EmployeeId = driver.EmployeeId,
            FullName = driver.FullName,
            Reviewed = driver.Reviewed
        })
    };
}

public class EmployeeResult
{
    public bool Success { get; set; }
    public string FailureInformation { get; set; }
    public IEnumerable<EmployeeViewModel> Employees { get; set; }
}
```

So now, rather than just taking the raw data from the service, I create the client-side data structure right in the background thread. So this will never block the UI thread, and the user will not see a hanging UI while this is going on. And it is perfectly fine to create this list of EmployeeViewModel classes on the background thread, because they are not user interface controls, nor do they directly interact with a user interface control at this point. Hence using a background thread is perfectly fine. I am then stuffing all this data into a small helper object I created (I suppose I could have just passed the list of Employees/Drivers as the result, but I wanted to preserve the Success and FailureInformation properties as well) and I return that from the background thread method. This then ends up in the complete method on the foreground thread, where I can use it like so:

```cs
AsyncWorker.Execute(GetDriversFromService, GetLoadersFromService, GetCheckersFromService, 
    (driversResponse, loadersResponse, checkersResponse) =>
    {
        if (driversResponse.Success)
            Drivers.AddRange(driversResponse.Employees);
        else
            Controller.Notification("Unable to load drivers: " + driversResponse.FailureInformation);
            
        /// ... more code here ...
    }
);
```

All I have to do is take the collection that was generated on the background thread and use it with an AddRange(). This now happens on the foreground thread, but it should be quite fast. Then a single binding refresh happens, and bam! we are done.