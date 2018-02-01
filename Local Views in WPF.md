# Local Views

Some WPF themes in CODE Framework support the concept of “local” views or “child” views. Those are views that belong to another view in a hierarchical fashion. For instance, a customer search list may open a customer for editing. The edit form could be considered a view that is “local” to the list, or is a “child” of the list, or even to “belong” to the list. If the edit form is opened in a modal fashion, users could still move to other main windows (such as an invoice search form) with the “modal” customer edit list remaining open within the context of the customer list and has to be closed before the user can do anything in the customer list… although the user is free to do whatever they please with all other forms. In that sense, the edit form, although modal, is only considered modal within the local context of the customer list. This is a concept that was originally introduced with the Wildcat theme, where it was inspired by the popular StaffLynx demo application, which prominently features this concept.

Here is an example of what this might look like:

![](Local%20Views/Local%20Views%20in%20WPF.png)

To make a modal view local to a parent form, it can simply be launched from a controller like any other modal view, but with the scope set to Local, as in this controller action method:

```cs
public ActionResult Edit()
{
    return ViewModal(new EditViewModel(), ViewLevel.TopLevel, ViewScope.Local);
}
```

Note the last parameter.

Note: Not all themes support Local scope. The above line of code executes without error in all themes, but many themes may ignore the local scope and open the view in an application-modal fashion. So results may vary for each theme.
