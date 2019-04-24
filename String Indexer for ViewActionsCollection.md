# String Indexer for ViewActionsCollection

Any ViewActionsCollection in any object that implements IHaveActions (such as all view models) have a string-based indexer that makes it easy to access view actions.

The following is an example of how to add actions and then accessing them by means of various indexers in C# code:

```cs
Actions.Add(new ViewAction("First Action"));
Actions.Add(new ViewAction("Second Action"));
Actions[0]; // First Action
Actions[1]; // Second Action
Actions["First Action"]; // First Action
Actions["Second Action"]; // Second Action
Actions["FirstAction"]; // First Action
Actions["SecondAction"]; // Second Action
Actions["0"]; // First Action
Actions["1"]; // Second Action
```

As you can see, the string indexer is flexible and accepts action names with and without spaces. It also accepts numeric indices passed in as strings.

Note that internally, view actions have an Id property, which by default is set to the caption of the action. However, it is also possible to explicitly set the Id:

```cs
Actions.Add(new ViewAction("First Action") {Id = "First"});
Actions.Add(new ViewAction("Second Action") {Id = "Second"});
Actions["First"]; // First Action
Actions["Second"]; // Second Action
```

The string indexer always refers to the Id, NOT the caption. This means that the caption can be localized, but the Id remains unchanged, making it more reliable for identification that the caption.

Having the string-based indexer makes it more convenient to bind to actions in the generic collection of Actions. For instance, a button could be bound to one of the actions like this:

```xml
<Button Command="{Binding Actions[First]}" Content="{Binding Actions[First].Caption}" />
```

Note that XAML binding expressions support string-based indexers (without setting quotes around the index expression).

It is recommended that for all generic references to view-actions in the Actions collection, a string-indexer is used over a numeric one, since that approach is less fragile and does not break if a new action was added to the collection or the order of actions changed.
