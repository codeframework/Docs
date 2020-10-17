# AsyncWorker 

The CODE Framework is an extensive set of utilities that can make your life easier, one of which is `AsyncWorker`. This little gem can be found in the `CODE.Framework.Wpf.Mvvm` name space and provides a convenient way to execute code on a background thread without having to recall the syntax required to go through the application dispatcher to invoke and control a background thread.

The typical syntax for using `AsyncWorker` is:

```C#
AsyncWorker.Execute(() =>
{
    var response = null;
    //code to execute on the background thread
    return response;
},
response =>
{
    // do something on your UI thread with the response
});
```

The concept is simple, any code you want to run on the background thread is passed into the first parameter (block of code), which will then be run on a background thread and returned to the second parameter (block of code) which runs on the UI thread. Clean and simple background thread execution.

## The Status Object

But this is just the beginning of the feature set of the AsyncWorker. There are three optional parameters that provide much more functionality. The most common one is a reference to the current ViewModel. So adding to the code above:

```C#
AsyncWorker.Execute(() =>
{
    var response = null;
    //code to execute on the background thread
    return response;
},
response =>
{
    //do something on your UI thread with the response
}, 
this);
```

Offically called the `statusObject` parameter, the `this` keyword will pass a reference to the current view model. `The AsyncWorker` will take this parameter and set the CODE Framework `ViewModel` property called `ModelStatus`. Here is where it gets interesting, the standard CODE Framework Metro theme has a progress indicator who’s visibility bound to the `ModelStatus` property. This means that the CODE Framework will display the progress indicator while the AsycWorker is waiting on the background thread to run and automatically hide it when the background thread has completed. So easy background threading with automatic wait indicator, pretty sweet.

Related to the `statusObject` parameter is the `ModelStatus` parameter. This optional parameter accepts an enumeration of the type `ModelStatus`, with your choices being: `Saving`, `Loading`, `Ready`, and `Unknown`. Most themes will use the same wait indicator for Saving and Loading, but using this parameter and a custom `ModelStatus` visibility converter it is possible to create a theme that will display different wait indicators for each of the choices. Looking at our code we now have:

```C#
AsyncWorker.Execute(() =>
{
    var response = null;
    //code to execute on the background thread
    return response;
},
response =>
{
    //do something on your UI thread with the response
},   
this, 
ModelStatus.Loading);
```

## Running at an Interval

Now there are times when you want to run a process on a scheduled time, say to periodically update a dashboard. Here is where our next `AsyncWorker` parameter, `interval`, comes into play.

```C#
AsyncWorker.Execute(() =>
{
    var response = null;
    //code to execute on the background thread
    return response;
},
response =>
{
    //do something on your UI thread with the response
},   
TimeSpan.FromMinutes(5)
this, 
ModelStatus.Loading);
```

As we can see here the `nterval`parameter accepts a `System.TimeSpan`. The code above would cause our code in the first parameter to be executed every 5 minutes. Of course you may be thinking that this is nice, but how do I stop it? Not to worry there is one last parameter worth discussing that gives us full control of the scheduled execution.

## Controlling Individual Processees 

Called `processId`, this little known gem is a `Guid` that you create and pass into the `AsyncWorker`. The `AsyncWorker` will use this `Guid` to reference your scheduled block of code. Keep in mind that you will want to define this as a global variable so that you can access this Guid from other parts of your view model, so it would appear something like this:

```C#
private Guid _myProcessId = Guid.NewGuid();

void DoMyStuff()
{
    AsyncWorker.Execute(() =>
    {
        var response = null;
	//code to execute on the background thread
	return response;
    },
    response =>
    {
        //do something on your UI thread with the response
    }, 
    TimeSpan.FromMinutes(5),
    this, 
    _myProcessId);
}
```

Now that we have fired off the `AsyncWorker`, given it an interval to run on, and provided a process id to control it we can now use the other `AsyncWorker` functions, `PauseContinuousProcess`, `ResumeContinuousProcess`, and `StopContinuousProcess`. Each of these methods accepts a Guid used to identify the process id to control. So if we wanted to have a button to stop the `AsyncWorker` created above we would have:

```C#
void StopDoingMyStuff()
{
    AsyncWorker.StopContinuousProcess(_myProcessId);
}
```

## Firing Code on the Foreground Thread

`AsyncWorker` also supports firing code that runs on the foreground thread, while running background operations. Here's an example:

```C#
AsyncWorker.Execute(() =>
{
    var response = null;
    //code to execute on the background thread
    
    AsyncWorker.OnForegroundThread(() => 
    {
        ItemsProgressed = 200; // View model property bound to the UI
    });
    
    return response;
},
response =>
{
   //do something on your UI thread with the response
});
```

This makes it easy to interact with object that have UI thread affinity (anything bound to the UI, or any WPF control object) from a background thread.

## Blocking View-Action Execution

There also is a `viewAction` parameter on `AsyncWorker.Execute()`. This parameter behaves somewhat similar to the model status parameter. However, this time, it's a view-action that has it's status set. Or, to be more accurate, the `IsBusy` flag. When a view-action is passed to `AsyncWorker` in this fashion, the busy flag is set until the process has completed. This automatically disables the action until the process has been completed. This, in turn, usually disables the action visually in the user interface. Note that it is possible to run multiple processes in parallel. `AsyncWorker` is smart enough to keep the busy flag until app running workers associated with that view-action have finished.

Here is an example:

```c#
actions.Add(new ViewAction("IsBusy Example Action",
                           execute: (a, o) =>
			   {
			       AsyncWorker.Execute(() =>
			       {
			           System.Threading.Thread.Sleep(10000); // Simulating something slow
				   return true;
			       }, i =>
			       {
			           Controller.Message("Done!");
			       }, viewAction: a);
			   }));
```

In this example, when someone triggers this action (perhaps because it is bound to a button, or a menu item, or something like that), the execute method immediately uses `AsyncWorker.Execute()` to run some example code. Note that the `viewAction` parameter is passed to `AsyncWorker`. In this case, the action passes itself, by passing along the `a` parameter, which was passed ot the `execute` delegate. This will result in the action being disabled for 10 seconds until the process finishes.

## More Information

Update: Also see [AsyncWorker Part 2](AsyncWorker%20Part%202) for more advanced uses of AsyncWorker.

