# WPF MVVM Shell Styling Tips

## General Styling Tips

There is a complete CODE Magazine article on creating styles and themes. Take a look at it here: [http://www.codemag.com/article/1211091](http://www.codemag.com/article/1211091)

## Showing the Selected View

There are SelectedNormalViewResult and SelectedTopLevelViewResult properties on the Shell object so one can easily bind a content control to whatever view is currently open, rather than always binding to all open views. 

This can now be done like so in the Shell style:

```xml
<ContentControl Content="{Binding SelectedNormalViewResult.View}" />
```

That one shows the selected normal view. If you want to show the current top-level (popup) view, it can be done like so:

```xml
<ContentControl Content="{Binding SelectedTopLevelViewResult.View}" />
```

 
