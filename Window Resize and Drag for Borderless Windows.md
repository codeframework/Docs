# Window Resize and Drag for Borderless Windows

CODE Framework now has a feature that makes it easy to make borderless windows that can still be resized and moved around on the screen. For instance, let’s say you make a window like the Word 2013 start screen:

![](Window%20Resize%20and%20Drag%20for%20Borderless%20Windows/Window%20Resize%20and%20Drag%20for%20Borderless%20Windows_clip_image002_2.jpg)

This window has no standard border, but you can actually click anywhere in the window and drag it around with your mouse (although you cannot change the size).

Another example is the main Word app, which also doesn’t use a standard window border or title. But you can still move the window around by grabbing it in the title area, and you can resize it like you would expect:

![](Window%20Resize%20and%20Drag%20for%20Borderless%20Windows/Window%20Resize%20and%20Drag%20for%20Borderless%20Windows_clip_image004_2.jpg)

You can create the same sort of window in our WPF framework very easily. To do so, create a XAML window declaration like this:

```xml
<Window x:Class="CODE.Framework.Wpf.TestBench.CustomBehaviorWindow" 
        xmlns:Controls="clr-namespace:CODE.Framework.Wpf.Controls;assembly=CODE.Framework.Wpf" 
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" 
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" 
        Title="CustomBehaviorWindow"
        Height="300" Width="300"
        MinHeight="50" MinWidth="100"
        WindowStyle="None" ResizeMode="NoResize" 
        Controls:WindowEx.HeaderHeight="26" 
        Controls:WindowEx.AutomaticWindowDragEnabled="True" 
        Controls:WindowEx.AutoWindowMaximizeEnabled="True" 
        Controls:WindowEx.AutoWindowResizingEnabled="True"> 
```

> Note that the ```WindowStyle``` is set to ```None```, and ```ResizeMode``` (somewhat confusingly) is set to ```NoResize```. This is standard WPF stuff to create a window without a border, which gives you the right look but not the right behavior.

Now we add the new behavior. See the attached properties? Those can be used to turn on various custom behaviors:

* ```AutomaticWindowDragEnabled``` enables – wait for it – the drag behavior. So with this set to True, you can drag the window around. Whether this works in the whole window area or just in what you consider the part of the window that is the header is dependent on whether you declare what your supposed header is. If the HeaderHeight property is set to anything but 0, the drag operation only works in the header area (in the example above, everything within 26 pixels of the top edge of the window is considered the header area). If you leave this value at 0 (or don’t touch it at all), the entire window is drag-enabled and thus movable by clicking anywhere in the window. 

* ```AutoWindowMaximizeEnabled``` allows to double-click in the header (or whole window depending on whether you declare a header area... this behaves just like the prior property) to maximize the window (or return it to normal state if it is already maximized). 

* ```AutoWindowResizingEnabled``` defines on whether the window supports standard resize behavior by dragging on the sides or the corners of the window. 

> Note that CODE Framework has custom behavior to keep borderless windows from overlapping the Windows taskbar. Since borderless windows are used for many themes that employ a custom window look (chrome), maximized windows should usually not overlap the taskbar, hence this custom behavior makes sense. However, by default, WPF windows that are set to borderless _do_ overlap the taskbar. If that is the desired behavior, set the ```WindowEx.HandleCustomMinMax``` to ```false```.
