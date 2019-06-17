# CODE Framework Theme Icons

CODE Framework WPF has a special ```ThemeIcon``` control, which is kind of cool. In its simplest fashion, it just makes it easy to add an icon into a view and bind it to one of the default icons we support, by means of setting an enum. Like this:

```xml
<mvvm:ThemeIcon StandardIcon="Save" />
```

So that is kind of cool, because it makes it easier to use the default icons we provide. So instead of always looking the information up in the documentation (see also: [Standard Icons](Themes---Standard-Icon-Resources) ), the enum guides you to the icons that are available (with a simplified name... so ```CODE.Framework-Icon-Save``` is just called ```Save``` in the enum).

The ThemeIcon does a few things for you. For one, it loads the icon in question, and, as the name suggest, it does so in a theme-specific way. So it will always load the right icon for the current theme. But the thing that is really neat about it is that it is aware of themes changing on the fly. So if the user changes the theme, the ```ThemeIcon``` control realizes that the theme was switched, and it will unload the old icon, and reload the one for the newly selected theme. So that makes for a nice user experience.

## Using Other Icons

Note that this also works for non-standard theme. The ```StandardIcon``` property is a convenient way to get at icons, but you can also do this:

```xml
<mvvm:ThemeIcon IconResourceKey="MySpecialIcon" />
```

This simply loads a brush resource called “MySpecialIcon” (the ```StandardIcon``` property is just a simplified way of setting the resource key). Even in this case, the theme icon control still listens to theme switching events (which are raised on the ```ApplicationEx``` object... so ```Application.Current``` has those events) and thus re-evaluates the resource whenever a new theme is loaded. So if you have created theme-specific icons using your own keys, then they will load correctly. (If the icon isn’t theme specific, it will just end up re-loading the same one… wastes a bit of processing power, but doesn’t have other ill effects). 

> Side-note: Icon resources should be vector-based drawing brushes. Ours certainly all are. But you could use any kind of brush resource, such as image brushes (yuk!) or video brushes, and so forth. But vector brushes are best, because they are high quality and can be displayed at any size. (Really, you could use this control to display “icons” that are huge in size and perhaps will an entire 4K display or more).

Another cool feature of this control is that it has fallback icons. In other words: If the icon you reference doesn’t exist, it will load a different icon. By default, it will load an icon resource called ```CODE.Framework-Icon-IconMissing```, which is provided in every theme. But you can change that by means of a fallback resource property. Just make sure you point to a resource that is always there ;-). Also, you can turn the whole fallback behavior off through the ```UseFallbackIcon``` property. Oh, and of course the Fallback icon is also re-evaluated when themes switch.

A really nice side-effect of all this is that CODE Framework now properly switches all the standard icons it uses whenever the theme changes. For instance, the Ribbon in the Workplace theme, or the Metro Tiles, or the Universe Hamburger Menu, or all drop-down menus, toolbars, and even the standard view-model templates in lists, now switch icons properly when the theme switches. It is probably not a surprise that this was one of the main motivations to create this control in the first place :-).

## Related Feature: Replacing Colors in Icons

A related question that keeps popping up is how to create an icon with embedded color resources that can then be replaced as needed. For instance, one could create an icon like this:

```xml
<DrawingBrush x:Key="Dynamic-Color-Icon" Stretch="Uniform">
  <DrawingBrush.Drawing>
    <DrawingBrush>
      <DrawingGroup.Children>
        <GeometryDrawing Brush="{DynamicResource Brush1}" Geometry="..." />
        <GeometryDrawing Brush="{DynamicResource Brush2}" Geometry="..." />
      </DrawingGroup.Children>
    </DrawingBrush>
  </DrawingBrush.Drawing>
</DrawingBrush>
```

*Note: Geometry data is omitted for brevity.*

Using this approach, it is not (at least theoretically) possible to replace the resources whenever the icon is displayed, and thus display the same icon but in different colors. However, the question is where to define such a resource for WPF to find it correctly and thus replace the color/brush in the icon instance.

The answer to this question revolves around where resources are defined and where WPF searches for them. This is also referred to as the "resource lookup chain". Essentially, when you have something that refers to a resource (like a DynamicResource reference), WPF looks in the current object to see if it has that resource. So this would work:

```xml
<Rectangle Fill="{DynamicResource Bla}">
 <Rectangle.Resources>
   <SolidColorBrush x:Key="Bla" Color="Red" />
 </Rectangle.Resources>
</Rectangle>
```

Doesn't make a whole lot of sense to do it like that, but it would work. It generally makes more sense to define the resource "further up". For instance, if the Rectangle is in a Grid, you could put the resource into the Grid, and then all objects inside the Grid have access to it. Or, you can move it up even further, perhaps to the root of the Window of Page or View or something like that.

The reason these resources are found is that WPF looks at the current object and if it isn't found there, it goes up to the object's Parent and then its Parent, and so on. It essentially walks the "visual tree" upwards to find resources. When it isn't found there, it goes to global app resources. Global resource dictionaries are usually merged into app-global resources. So all these places can have resources that will be found.

> On a side-note: Resources can be re-defined. For instance, you can put a resource called "Bla" into Application.Resources. However, if you then put a resource of the same name into a Grid, then within the grid, that resource (of the same name) will be find before the global one. In fact, you can even load a resource of the same name. This often happens when you merge resource dictionaries. So if you add two resource dictionaries that both define a resource of the same name, then the last one wins. This mechanism allows you to override existing CODE Framework resources, so this all works out rather well. (On a technical level, this basically means that resources are added as ```Resources.Add("Bla", ...)``` but if it already exists it simply does a ```Resources["Bla"] = ...``` )

Anyway: For the icon example, the question is: Where in the lookup chain is the icon and where will it find stuff. The drawing brush that makes the icon is NOT added to the visual tree. It is simply used to draw the icon. But from the brush's point of view, what would ```this.Parent``` be? Answer: nothing. It's not part of the visual tree and thus it doesn't have a parent. Therefore, whatever resources are defined in the rest of the XAML, are not found for this purpose. Only global ones are.

This is all rather inconvenient. However, with CODE Framework you are in luck. The ```ThemeIcon``` control has a ReplacementBrushes collection. So simply put your changed color brushes into that collection. The ```ThemeIcon``` object/element then takes a look at the assigned icon brush, and if it is a ```DrawingBrush``` object, it walks through the entire drawing composition, trying to look for the brush in question, and it will replace it manually if need be. Long story short: It was a pain to build, but it just works now :-)

Here's an example of how to use this feature:

```xml
<mvvm:ThemeIcon IconResourceKey="Dynamic-Color-Icon">
  <mvvm:ThemeIcon.ReplacementBrushes>
    <utilities:ObservableResourceDictionary>
      <SolidColorBrush x:Key="Brush1" Color="Green"/>
    </utilities:ObservableResourceDictionary>
  </mvvm:ThemeIcon.ReplacementBrushes>
</mvvm:ThemeIcon>
```
