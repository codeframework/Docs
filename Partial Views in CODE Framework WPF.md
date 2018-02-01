# Partial Views in CODE Framework WPF MVVM/MVC

_Excerpt from an internal email at CODE/EPS:_

FYI: The CODE Framework WPF stuff now supports partial views. This is a feature that was requested so sub-areas of UIs can be created in reusable ways. For instance, if you had the need to display invoice information in various UIs, then you could use a partial view for that.

The easiest way to use a partial view is to use the PartialView object and set the View property:

```
<Mvvm:PartialView Controller="Customer" View="CustomerName" />
```

This loads a view called “CustomerName.xaml” within the context of the customer controller. In other words: It will assume that this view is either in the Views\Customer folder or in Views\Shared. So basically the same exact rules as full views. (Views can also be XAML with code behind or our preferred stand-alone XAML files).

So this is just a simple way to break XAML out into reusable pieces. This is very similar to ASP.NET MVC’s @Html.RenderPartial() feature. Note that in Html.RenderPartial(), you can pass a model. You can do the same in our setup:

```
<Mvvm:PartialView Controller="Customer" View="CustomerName" Model="{Binding NameSubObject}" />
```

However, this is not needed if the elements used in the sub-view bind to the current view model level. In other words: the partial view, by default, shares the DataContext of the current view, and in most cases, there is no need to mess with that. But you can if you want.

Note that we can also take things a step further and instead of just rendering a partial view as a simple view file, you can actually use a controller and a method on that controller to set up a new partial view even with its own view model:

```
<Mvvm:PartialView Controller="Customer" Action="CustomerName" />
```

In this example, rather than just loading a view file, the system actually runs CustomerController.CustomerName(). This method should be something like this:

```c#
public ActionResult CustomerName()
{
    return PartialView();
}
```

This method could be a lot more complex of course, and load its own view model and all that.

It is also possible to pass parameters to the method in the controller. To do so, use the Parameter property on the PartialView object:

```
<Mvvm:PartialView Controller="Customer" Action="CustomerName" Parameter="{Binding .}" />
```

In this particular example, the Parameter property is bound to the current data context. Of course this can be set to anything, including manually set methods as well as more complex binding scenarios.

This allows calling the following method in the controller:

```c#
public ActionResult CustomerName(ParameterType parameter) 
{ 
   // Do whatever with the parameter
   return PartialView();  
}
```

Note that the parameter doesn’t have to be called "parameter". CODE Framework matches single-parameter action methods by parameter type if the name doesn’t match, hence this parameter can be called anything you would like it to be, as long as the type matches:

```c#
public ActionResult CustomerName(ParameterType parentModel) 
{ 
   // Do whatever with the parameter
   return PartialView();  
}
```

As a side-note: I now added a new method to the static Controller class. In addition to Controller.Action(controller, method), which calls the specified method on the specified controller and returns a view and possibly a model, you can now take a shortcut for controller methods that only return a view using Controller.ViewOnly(view). So you can do this:

```c#
Controller.ViewOnly("CustomerName");
```

This is very similar to calling the Action() method, and having a method of that name on the controller. However, in this case, you do not actually need to have a CustomerName() method. In fact, ViewOnly() just pretends that method exists and acts as if that method only had one line of code (return View()). (And if all of this just made no sense to you, then don’t worry about it… you won’t need it :-)).
