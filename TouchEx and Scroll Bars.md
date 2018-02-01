# TouchEx and Scroll Bars

_Excerpt from an internal email at EPS/CODE:_

A relatively minor, but still very interesting feature is the latest style for scroll bars in Metro. For one, the style has changed to more closely resemble the scroll bars WinRT uses. Here is an example of a screen with a horizontal scroll bar: 

![](TouchEx%20and%20Scroll%20Bars/TouchEx%20and%20Scroll%20Bars_clip_image001_2.jpg)

As you can see, the scroll bar is now a bit wider (a feature some of our customers explicitly asked for) and it is very square. The arrows on either end of the scroll bar have also changed. 

What is more interesting is that if the user performs any kind of touch interaction with the app, the scroll bar changes to this: 


![](TouchEx%20and%20Scroll%20Bars/TouchEx%20and%20Scroll%20Bars_clip_image002_2.jpg)


Side-note: The scroll bar actually just appears to be smaller. The interactive area is still the same size. So if you were to grab it with the finger, it would actually be relatively easy to touch because it is still technically as tall as in the previous screen shot. It just has a lot of transparent areas. 

This works back and forth. Touch the app with the finger, and all scroll bars change to the thin mode. Move the mouse over and – snap – it is back to the wide mode. 

One of the reasons this is so interesting is how this was implemented. There now is a class in CODE Framework called TouchEx. This helper class has an attached property called UpdatePointingDeviceInputMode. If that property is set to true on any element, it auto-updates another attached property on itself called PointingDeviceInputMode, which is either set to Mouse or Touch. In the Metro theme, the default Shell style uses this feature, which means that the Shell window always knows what the current input mode is. And you can simply bind to that. The default Metro ScrollViewer does just that. Here’s the binding expression it uses: 

```
{Binding Path=(controls:TouchEx.PointingDeviceInputMode), RelativeSource={RelativeSource Mode=FindAncestor, AncestorType=Window}} 
```

To be more specific, the scroll bar template uses this binding expression to bind the margin of the visual items (and it uses a binding converter to create a different margin for touch mode, which makes the scroll bar appear thin). 

Bottom line: This is a very useful feature to be aware of, because one can always bind to the Shell window’s PointingDeviceInputMode property to provide features specific to touch. 
