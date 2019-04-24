# Setting Generic and Abstract Control Dimensions in WPF

_Excerpt from an internal email at CODE/EPS:_

FYI: I have added a feature we were sort of sorely lacking in the CODE Framework (WPF components): The ability to define sizes of elements in a way that adjusts easily to different styles.

In the past, if you wanted to add a textbox that was 200 pixels wide and 40 pixels tall, you had to do it like this:

```xml
<Window x:Class="CODE.Framework.Wpf.TestBench.SizeCalcEx"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" 
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
  <Grid>
    <TextBox Width="200" Height="40" />
  </Grid>
</Window>
```

This results in a textbox of exactly these dimensions. In many cases, that is inconvenient. For instance, if a style gets applied that uses a larger font size, then this size is going to be completely wrong!

So this was the one problem we still had with our styling approach. Instead, we can now set WidthEx and HeightEx properties like so:

```xml
<Window x:Class="CODE.Framework.Wpf.TestBench.SizeCalcEx" 
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" 
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" 
        xmlns:m="clr-namespace:CODE.Framework.Wpf.Mvvm;assembly=CODE.Framework.Wpf.Mvvm">
  <Grid>
    <TextBox m:View.WidthEx="20" m:View.HeightEx="3" />
  </Grid>
</Window>
```

This defines a textbox of a size that can roughly accommodate 20 characters of text horizontal, and 3 lines vertical. Of course this is not an exact science, because different characters have different widths (I am calculating based on the upper-case letter “N”, so this should be able to accommodate roughly 20 Ns). Also, depending on the style, various controls use different margins and padding and such. (In most cases, controls accommodate slightly less than you’d think… maybe 19 Ns or so, in this case… but that is fine, since nobody actually types 20 Ns, but different characters).

So this is now our preferred way to set height and width in cases where we need it. Of course if you can get away without setting any dimensions, that is best. But Width is a somewhat common size to set in many data UIs.
