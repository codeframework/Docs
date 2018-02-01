# Font Sizes and Font Families in WPF Apps

_Excerpt from an internal email at CODE/EPS:_

When creating WPF applications (as well as other XAML-based UIs), one should never hardcode font sizes or font families. Instead, one should use one of the default resources defined for fonts. All Themes have the following font resources defined (actual settings may vary, but the names of the resources remain the same):

```
<FontFamily x:Key="DefaultFont">Segoe UI</FontFamily>

<System:Double x:Key="FontSize-Smallest">8</System:Double>
<System:Double x:Key="FontSize-Smaller">12</System:Double>
<System:Double x:Key="FontSize-Normal">13.333</System:Double>
<System:Double x:Key="FontSize-Large">16</System:Double>
<System:Double x:Key="FontSize-Larger">20</System:Double>
```

To use these, simply refer to them as resources as in the following example:

```
<Label FontSize="{DynamicResource FontSize-Large}" FontFamily="{DynamicResource DefaultFont}" />
```

Of course, you are free to change these settings on a theme-by-theme basis (using the default templates, the Themes folder has a font resource dictionary that can be used to change these settings).

If additional sizes or font faces are needed, they can also be added in the same resource dictionary. Doing this is recommended over hard-coding any of these settings. But make sure you define the same resource for every theme in case you are switching between themes.
