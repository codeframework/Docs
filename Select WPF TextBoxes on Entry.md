# Selecting Textbox Content on Entry

WPF TextBoxes do not automatically select their content when the cursor moves into them. CODE Framework supports a feature that remedies the situation. Using the Ex object, one can set select-on-entry behavior. Consider the following 3 TextBoxes:

```xml
<TextBox Text="Test" c:Ex.SelectOnEntry="True" />
<TextBox Text="Test" c:Ex.SelectOnEntry="False" />
<TextBox Text="Test" c:Ex.SelectOnEntry="True" />
```

The first and the last are automatically selected when the cursor moves into the textbox. For instance, if the user tabs into the textbox, this behavior kicks in. On the other hand, if the user clicks into a specific part of the textbox, that is where the cursor will be, rather than selecting all the text.

The second textbox in this example does not select all text on entry, because the property is set to false. Note that that’s the default and setting it explicitly as in the example above does nothing. In fact, it is recommended that you do not explicitly set this property to false, is it doesn’t do anything useful and just causes overhead.

The example above requires that the namespace for the “Ex” object is declared. In this example, the namespace is called “c”. If you use tools like Resharper, this can be done automatically. Here is the declaration:

```xml
xmlns:c="clr-namespace:CODE.Framework.Wpf.Controls;assembly=CODE.Framework.Wpf"
```

Note that the attached SelectOnEntry setting can be set on all controls that derive from TextBoxBase.
