# Custom Views and View-Models in the Ribbon

_Excerpt from an internal email at EPS/CODE:_

FYI: The latest version of CODE Framework, which I am about to check in, supports custom views and view-models to be hosted inside the Ribbon. This means that the Ribbon is now fully programmable and you can put whatever you want into the Ribbon. (Note: Please use this sensibly though… a Ribbon isn’t a place for a complete UI, such as a customer edit form ;-)).

Here’s how this works: The Ribbon is of course populated from view actions. So if the UI you have open has a ViewActions collection, then all the actions in that collection will be used to populate the Ribbon. For instance, you can add the following action into the Ribbon:

```cs
Actions = new ViewActionsCollection
{
    new ViewAction("Font Controls", execute: (a, o) => { /* Launch an action UI */ }
};
```

So this will result in a button in the Ribbon labelled “Font Controls” and when you click it, it will run whatever is defined as an execute statement. (Just a comment in this case, but imagine it launching a dialog, or something like that).

So this would work well in a menu for instance. But in a Ribbon, it would be much nicer to have the font format controls right in place inside the Ribbon. You can now do this, by attaching a custom view and view-model. Perhaps like this:

```cs
Actions = new ViewActionsCollection
{
    new ViewAction("Font Controls", execute: (a, o) => { /* Launch an action UI */ }) 
    {
        ActionView = new FontControls(), ActionViewModel = this
    }
};
```

In this case, the “view” is simply an instantiated user control, and the current object (which is itself a view-model) is used as the view-model for the view. (So if the current object has properties like FontWeight,… they could just be bound to UI elements in the view).

The following example shows a larger Ribbon that uses this technique:

![](Custom%20Views%20and%20View-Models%20in%20Ribbons/Custom%20Views%20and%20View-Models%20in%20Ribbons%201.jpg)

Note: Only some themes (in particular the Metro and the Workplace theme) support these settings. All others, the custom view and view-model will be ignored and instead, a standard item will be created, and – when clicked – the execute metod will fire. IF the theme respects the custom view and view-model however, the custom view takes over and the default execute method is ignored entirely. (The custom view-model can of course have its own actions and UI elements that fire those, if that is needed).

This particular page in this Ribbon example has three associated view actions. One for the “Test” button (which isn’t a standard Ribbon button, but it is just a plain old WPF button), the whole set of controls in the “Format” group, and the list in the “List” group.

There are a few variations on this theme. One is that the view model can be anything. Passing in “this” is convenient in many cases, but it is also a special case. It is also possible (and useful) to pass in a new view-model that gets instantiated with a new Xxx() command.

Also, note that hardcoding a view as in the example above is not the most elegant approach. For anything beyond the simplest scenarios, it is probably better to create a standard CODE Framework view (a XAML file in the Views/xxx folders) and then use Controller.ViewOnly() to load that view. However, if all you need is a single list or a single control, it is probably OK to just load that hardcoded as in the example above.

On a side-note: The latest Ribbon also supports a number of other features. It looks better with disabled controls. It can more dynamically react to changes of element visibility and changes in captions of elements. It also behaves better with longer labels and breaks them more accurately across multiple lines. Here’s an example:

![](Custom%20Views%20and%20View-Models%20in%20Ribbons/Custom%20Views%20and%20View-Models%20in%20Ribbons%202.jpg)
