# Activating Existing Views

There is a feature to CODE Framework that makes it easier to activate a view/model that is already open. For instance, if you have an item edit form, and fire it up to edit item #1, and then, while that form is still open, fire up another edit form for item #1, then you may or may not want to prevent that from happening and instead bring the first instance of that view to the foreground. This has been possible in the past, but it was harder than people wanted it to be. I made this easier, by allowing developers to run this code in the controller:

```Cs
public ActionResult Edit(Guid key)
{
    var alreadyOpen = GetOpenModel<EditViewModel>(m => m.Id == key);
    if (alreadyOpen != null) return ActivateView(alreadyOpen);
    return View(new EditViewModel(key));
}
```

What’s happening here is that in the first line, we use the GetOpenModel() method of the controller to check whether an instance of the EditViewModel is already loaded, and if so, whether there is one that has the same Id as the key we are about to launch again. If such a model is found (not null), we return an ActivateView() result, rather than a new View(). The framework handles the rest and it will bring the view that is already open to the foreground.
