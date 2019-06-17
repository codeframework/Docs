# Accessing the Shell object

CODE Framework WPF applications (as well as other variations, such as WinRT and Windows Phone apps) launch a Shell object as the main application "frame". You can think of this as "the root object that ties all the UI stuff together". A Shell object gets created whenever a controller does a "return Shell()". Typically there is only one.

Sometimes it is beneficial to access the Shell object programmatically. For instance, accessing the Shell object provides access to its properties, such as NormalViews and TopLevelViews collections (to see which views are open or selected) as well as other features.

The current shell object instance can be accessed using the Shell.Current static property. For instance, the following code snippet looks at the number of currently opened normal views:

```cs
Console.WriteLine(Shell.Current.NormalViews.Count); 
```