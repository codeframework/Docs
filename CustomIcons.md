# Creating Custom Icons and Overriding Standard Icons

In addition to the set of standard icons, it is possible to create custom icons, or override the standard ones, and use them in place of CODE Framework standard icons.

Icons in CODE Framework are defined as brush resources. Ideally, drawing brushes, since they are vector images that scale and perform well. However, it is theoretically possible to define other brush resources, such as image brushes, or even video brushes, and us them as "icons".

Consider the following brush that defines a save icon:

```xml
<DrawingBrush x:Key="MyGreatSaveIcon" Stretch="Uniform">
    <DrawingBrush.Drawing>
        <DrawingGroup>
            <DrawingGroup.Children>
                <GeometryDrawing Brush="Black" 
                                 Geometry="F1 M 16.2,3.05176e-005C 16.3375,3.05176e-005 16.4646,0.0245056 16.5812,0.0734558C 16.6979,0.122406 16.8021,0.190643 16.8937,0.278168C 16.9854,0.365662 17.0573,0.468262 17.1094,0.585968C 17.1615,0.703705 17.1875,0.830231 17.1875,0.965668L 17.1875,16.2594C 17.1875,16.3969 17.1615,16.5255 17.1094,16.6454C 17.0573,16.7651 16.9854,16.8693 16.8937,16.9579C 16.8021,17.0464 16.6979,17.1146 16.5812,17.1625C 16.4646,17.2104 16.3375,17.2344 16.2,17.2344L 13.3688,17.2344L 13.3688,12C 13.3688,11.8584 13.3182,11.7386 13.2172,11.6407C 13.1161,11.5428 13.001,11.4938 12.8719,11.4938L 4.28749,11.4938C 4.14375,11.4938 4.02292,11.5428 3.925,11.6407C 3.82709,11.7386 3.77812,11.8584 3.77812,12L 3.77812,17.2344L 0.965622,17.2344C 0.830208,17.2344 0.703644,17.2104 0.585938,17.1625C 0.468231,17.1146 0.365616,17.0464 0.278122,16.9579C 0.19062,16.8693 0.122391,16.7651 0.0734329,16.6454C 0.0244751,16.5255 0,16.3969 0,16.2594L 0,0.965668C 0,0.692719 0.0927124,0.463593 0.278122,0.278168C 0.463539,0.0927429 0.692703,3.05176e-005 0.965622,3.05176e-005L 16.2,3.05176e-005 Z M 14.325,2.41254C 14.325,2.27505 14.276,2.15891 14.1781,2.06412C 14.0802,1.96933 13.9604,1.92191 13.8187,1.92191L 3.32813,1.92191C 3.18645,1.92191 3.06822,1.96933 2.97343,2.06412C 2.87864,2.15891 2.83125,2.27505 2.83125,2.41254L 2.83125,6.20941C 2.83125,6.34689 2.87864,6.46304 2.97343,6.55783C 3.06822,6.65262 3.18645,6.70004 3.32813,6.70004L 13.8187,6.70004C 13.9604,6.70004 14.0802,6.65262 14.1781,6.55783C 14.276,6.46304 14.325,6.34689 14.325,6.20941L 14.325,2.41254 Z M 8.575,17.2344L 5.70313,17.2344L 5.70313,14.3657L 8.575,14.3657L 8.575,17.2344 Z "/>
            </DrawingGroup.Children>
        </DrawingGroup>
    </DrawingBrush.Drawing>
</DrawingBrush>
```

There are several aspects of interest here. Note that the whole icon is defined as a ```DrawingBrush``` resource with a set key. 

> The easiest way to create XAML based icons is to create them with Microsoft's Expression Design graphics program. Yes, it is a bit dated, but it is a great tool for this job. You can create vector graphics and export them as XAML brushes. You can even import from other formats (most importantly perhaps Adobe Illustrator).

> Another option is to download existing art in XAML Brush format. One site that offers such art for free is our own http://www.xamalot.com.

Such an icon can be defined in any resource dictionary or resources element in XAML. However, we recommend putting it into the Themes folder and the sub-folder named after the theme you are using. For instance, if you are using the Workplace theme, put it into the Themes\Workplace folder. Typically, this is done in a resource dictionary specific to icons (such as Icons.xaml). Note that that dictionary needs to be loaded by linking it into another resource dictionary. Most commonly, this is done in the "theme root" XAML file of the same name as the theme. Therefore, in the Workplace theme there is a ```Workplace.xaml``` file where you can add the link to the existing ones like so:

```
<ResourceDictionary xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
    
    <ResourceDictionary.MergedDictionaries>
        <ResourceDictionary Source="Icons.xaml"/>
        <ResourceDictionary Source="Workplace-Colors.xaml"/>
        <ResourceDictionary Source="Workplace-Fonts.xaml"/>
        <ResourceDictionary Source="Workplace-Shell.xaml"/>
    </ResourceDictionary.MergedDictionaries>

</ResourceDictionary>
```

Now that this icon is defined, it can be used in various places. For instance, if you like using the [ThemeIcon](Icons-Theme-Icons) control, you can use your new icon like this:

```
<mvvm:ThemeIcon IconResourceKey="MyGreatSaveIcon" />
```

Similarly, the new icon can be used in View-Actions:

```
new ViewAction("Save",
               execute: (a, o) => Save(), 
               brushResourceKey: "MyGreatSaveIcon");
```

Note that a Save icon is perhaps not the best example for us to use here, since CODE Framework comes with its own Save icon that is appropriate for each theme (see also: [Standard Icons](Theme-Standard-Icon-Resources)). Therefore, if you want to use a Save icon, it is perhaps better to use this syntax:

```
<mvvm:ThemeIcon IconResourceKey="CODE.Framework-Icon-Save" />
```

Or, the more intuitive short-hand using the ```StandardIcons``` enumeration:

```
<mvvm:ThemeIcon StandardIcon="Save" />
```

This second version is really identical to the first version, because setting the ```StandardIcon``` property really just causes the ```IconResourceKey``` property to be set internally.

But what if you want a different Save icon rather than the one defined by CODE Framework? Easy: You simply define a local icon of the same name, which then overrules the standard one. Therefore, instead of creating a new icon with a different name/key, simply create the same icon but give it the standard key:

```
<DrawingBrush x:Key="CODE.Framework-Icon-Save" Stretch="Uniform">
  <!-- more definition here -->
</DrawingBrush>
```

Now, wherever ```CODE.Framework-Icon-Save``` is referenced, your newly redefined icon will be used. And, since setting a StandardIcon property really only sets the internal resource key, it will just work in those cases as well. Like magic! ;-)

The advantage of overriding existing icons is that if you were to switch to a different theme that you don't have a custom icon for, then that theme would still work since it would default to the usual icon again. Also, this applies your icon resource more consistently across your application. Even CODE Framework features that pop up icons intrinsically would now use your overridden icon.

For this reason, you should only create icons of new names when they really serve a different purpose. 