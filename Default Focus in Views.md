# Default Focus in Views

CODE Framework allows setting where the default focus in views goes when views load. This is simply done by means of an attached property. For instance, if you want the focus to go to a textbox within a view by default, you can use the following XAML definition:

```
<TextBox Text="{Binding UserName}" c:View.HasDefaultFocus="True" />
```

You should only set one control within a view to receive the default focus. If you were to set this property to True on multiple controls however, the last one wins.
