# Setting a Default Page on the Ribbon

Using CODE Framework, some styles use a Ribbon that is automatically populated from view actions. This creates a default Ribbon, with the first page within the Ribbon selected when a view is opened. However, this is often not desired. To indicate that a default page should be selected by default, one can yse the IsDefaultSelection property on view actions. (Note: If that property is set to true, it can be used by themes in various ways. In our the case of the Ribbon (such as in the Workplace theme), if this property is true on a view action, the page of the ribbon that view action is in will be shown by default.

Example:

```c#
Actions.Add(new ViewAction("New Customer", 
                           execute: (a, p) => Controller.Action("Customer", "New"), 
                           category: "View") { IsDefaultSelection = true });
```

In this example, whenever the user navigates to the view that uses this view action, the “View” tab in the ribbon will be shown, because that is the category this action is in, and it has its IsDefaultSelection set to true. (Note: If more than one view action sets this property to true – which is not what one should do, really – then the last one would win).