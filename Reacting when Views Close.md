# Reacting when Views Close

It is a common need to have to react to views closing. For instance, one may have a customer list, then double-click on of the customers in the list to open an edit form. When the user then edits the customer and closes it, and returns to the list, the list likely shows outdated data. Therefore, the customer list view/view-model needs to react to the edit view closing.

Here's an example of how this can be done:

```c#
var context = Controller.Action("Customer", "Edit", new {id = x}).Result as ViewResult;
if (context != null)
    context.ViewClosed += (s, e) => RefreshList();
```

This example presumably runs in the view-model associated with the customer list (probably in a view action hat triggers when the customer is double-clicked in the list). It then calls the Controller.Action() method to launch another view. Usually, the return value of that method is ignored, but in this case, the result is retrieved and used as a ViewResult. (Note: The Action method always returns ActionResult objects, but depending on the exact response, subclasses of ActionResult may be returned... in most cases - such as then the method does a return View(...) - it is actually a ViewResult).

ViewResult objects have a number of interesting members. For instance, that object has properties that grant access to the view itself, the view-model, details such as the view title, and more. Of particular interest for us is the ViewClosed event. That event fires - you guessed it - when the view has been closed. We can thus simply react to this event and do things like refresh the list of customers. We could also grab the view-model and pick out individual data elements (perhaps it would be enough to grab the customer ID and the display name and go through the existing list of customers in memory and replace those 2 data elements... which would probably be faster).

Another option is to explicitly pass an "event sink" object to a controller when a view gets created. This object can then respond to events. Here is an example:

```c#
var sink = new ViewResultEventSinks();
sink.ViewClosed += (s, args) =>
{
    // This is where one likely wants to read values from the model
    var myModel = args.ViewResult.Model as WhateverViewModel;
    var selectedCustomer = myModel.SelectedCustomer;
};
Controller.Action("Something", "Whatever", null, sink);
```
