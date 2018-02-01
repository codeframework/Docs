# Fast Loading of Standard View-Models with Image Resources

_Excerpt from an internal email at EPS/CODE:_

We have done quite a bit of work on improving the performance when loading standard views and view models with associated icons. This was quite slow at times, because the task is quite complex. When we load lists with hundreds of items, each of which has an icon, the system actually has to handle that icon and do a few things such as make sure the icon uses the right colors. This is particularly true in Metro, where the typical black Metro standard icon is turned into whatever other color the theme calls for.

Anyway: We have improved the performance on that in general, so it should just be faster now. We have also added a bunch of new methods for loading icons faster in specific scenarios, mainly having tons of standard view models in a list with the same (or mostly the same) icons.

Here’s how we always loaded icons before:

```cs
public class CustomerQuickInformation : StandardViewModel 
{ 
    public CustomerQuickInformation() 
    { 
        Image1 = GetBrushFromResource("CODE.Framework-Icon-Home"); 
    } 
}
```

This ran for every instance of this view model and loaded a new icon for each of them. So this could potentially run hundreds of times. While this still works, we now also have this approach:

```cs
public class CustomerQuickInformation : StandardViewModel 
{ 
    public CustomerQuickInformation() 
    { 
        LoadSharedImage1FromBrushResource("CODE.Framework-Icon-Home"); 
    } 
} 
```

This does essentially the same, but it shares the icon resource across all instances of this view model, which is drastically faster.

You can still load different icons into each model. But all the ones that will use the same icon will share it. So if you had 2 icons, one to indicate let’s say an active state and another to indicate an inactive state of the data element you are showing, you could load two different icons in those view models, but all the ones that are active and all the ones that are inactive would share their icon.

The performance improvement of this is considerable and this is what we should use as a standard from now on.

Note that there are several versions of this method, one for each of the 5 image properties, and one for each of the 2 logo properties.

The only downside of this method (and loss of flexibility) is that the icons in the views have to appear exactly idendical. So if you loaded a tile with a blue background and white foreground, the icon would now appear white. If you then had another tile that had a white background and blue foreground that shared the same icon, the icon would now still be white. Now this is a somewhat unlikely scenario, but still, it is something to be aware of.

Note: The method actually has a second parameter that allows for context separation which would solve exactly this problem. You probably will not need that, but here’s how you’d use that:

```cs
public class CustomerQuickInformation : StandardViewModel 
{ 
    public CustomerQuickInformation() 
    { 
        LoadSharedImage1FromBrushResource("CODE.Framework-Icon-Home", MyBruses); 
    } 

    private static readonly Dictionary<string, Brush> MyBrushes = new Dictionary<string, Brush>(); 
} 
```

In this case, the sharing would now be local to the customer quick information view model and not all standard view models.
