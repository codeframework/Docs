# Standard CODE Framework Icons

## Standard Icon Resources

All the CODE Framework WPF themes support a list of standard icons in the form of Brush resources that can be used to fill any WPF object (typically, this is used as the fill color for a rectangle of the desired size).

Note that all icons are defined as vector graphics expressed as Brushes, so they can be used as “fill colors” wherever brushes are used. They can also be used by resource name in elements that support brush resources directly. Since icons are based on vector graphics, they can be used at any size without loss of quality.

Here’s an example where an icon is used as a “fill color”:

```
<Rectangle Height="36" Width="36" Fill="{DynamicResource CODE.Framework.Canvas-Icon-Paste}" />
```

The following is an example for an object that supports resources directly:

```cs
new ViewAction("Full Screen",
               execute: (a, o) => FullScreen(), 
               brushResourceKey: "CODE.Framework-Icon-ZoomIn");
```

It is also possible to use these kinds of icons programmatically. For instance, if you want to assign a brush to an object, you can do the following:

```cs
var rect = new Rectangle();
rect.Fill = Application.Current.FindResource("CODE.Framework-Icon-ZoomIn") as Brush;
```

Or, a view model could have a property that retrieves a brush (which one could later use for binding):

```cs
public Brush SomeLogo
{
    get
    {
        return Application.Current.FindResource("CODE.Framework-Icon-ZoomIn") as Brush;
    }
}
```

Metro icons are all monochrome and use a single defined color/brush to draw themselves. That color can be changed, simply by redefining those color resources. Here is the default definition for those colors:

```
<Color x:Key="CODE.Framework-Metro-IconForegroundColor">Black</Color>
<SolidColorBrush x:Key="CODE.Framework-Metro-IconForegroundBrush" Color="{DynamicResource CODE.Framework-Metro-IconForegroundColor}" />
```

When using standard view models, the model class provides methods to load brush resources. The simplest way to load a brush resource in a standard view model is through the GetBrushFromResource() method:

```cs
public class CustomerQuickInformation : StandardViewModel
{
     public CustomerQuickInformation()
     {
         Image1 = GetBrushFromResource("CODE.Framework-Icon-Home");
     }
}
```

This loads the resource and performs a few additional tasks such as making sure resource uses appropriately themed colors. Note that this can at times be a performance intensive task and should thus be used with care. If you have to load hundreds of brush resources (as is often the case in large lists) and many or all of those items share the same icon, there are more specific methods such as LoadSharedImage1FromBrushResource() that perform the same task but in a highly optimized way that works particularly well for numerous instances of the same view model:

```cs
public class CustomerQuickInformation : StandardViewModel
{
     public CustomerQuickInformation()
     {
         LoadSharedImage1FromBrushResource("CODE.Framework-Icon-Home");
     }
}
```

Note there this loads the brush and assigns it internally to Image1. There are equivalent methods for all image and logo properties.

> **Side-Note: Other Icons**<br/>For a very large selection of additional icon resources (specifically designed for XAML use, but also available as SVG and various bitmap formats such as JPG, PNG,…) check out www.Xamalot.com!

## List of Supported Icons

The following is a list of supported icons:

_Note: Whenever no icon is specified for a specific theme, the Metro icon is used as a default._

<table>
<thead>
<tr>
<td>Resource Name</td>
<td style="text-align:center; width:60px;">Battleship</td>
<td style="text-align:center; width:60px;">Geek</td>
<td style="text-align:center; width:60px;">Metro</td>
<td style="text-align:center; width:60px;">Vapor</td>
<td style="text-align:center; width:60px;">Wildcat</td>
<td style="text-align:center; width:60px;">Workplace</td>
</tr>
</thead>
<tbody>
<tr>
<td>CODE.Framework-Icon-Accounts</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px 0px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px 0px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px 0px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px 0px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px 0px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px 0px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Add</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -32px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -32px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -32px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -32px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -32px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -32px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Admin</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -64px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -64px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -64px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -64px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -64px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -64px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-AlignCenter</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -96px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -96px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -96px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -96px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -96px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -96px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-AlignLeft</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -128px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -128px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -128px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -128px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -128px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -128px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-AlignRight</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -160px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -160px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -160px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -160px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -160px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -160px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-ArrowDown</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -192px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -192px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -192px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -192px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -192px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -192px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-ArrowDownLeft</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -224px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -224px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -224px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -224px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -224px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -224px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-ArrowDownRight</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -256px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -256px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -256px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -256px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -256px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -256px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-ArrowLeft</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -288px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -288px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -288px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -288px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -288px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -288px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-ArrowRight</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -320px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -320px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -320px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -320px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -320px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -320px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-ArrowUp</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -352px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -352px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -352px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -352px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -352px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -352px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-ArrowUpLeft</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -384px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -384px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -384px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -384px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -384px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -384px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-ArrowUpRight</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -416px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -416px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -416px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -416px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -416px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -416px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Attach</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -448px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -448px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -448px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -448px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -448px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -448px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-AttachCamera</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -480px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -480px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -480px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -480px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -480px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -480px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Audio</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -512px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -512px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -512px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -512px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -512px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -512px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Bold</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -544px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -544px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -544px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -544px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -544px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -544px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Bookmarks</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -576px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -576px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -576px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -576px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -576px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -576px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-BrowsePhotos</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -608px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -608px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -608px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -608px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -608px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -608px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Bullets</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -640px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -640px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -640px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -640px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -640px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -640px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Calendar</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -672px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -672px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -672px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -672px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -672px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -672px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Caption</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -704px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -704px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -704px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -704px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -704px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -704px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Cc</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -736px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -736px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -736px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -736px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -736px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -736px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Characters</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -768px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -768px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -768px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -768px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -768px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -768px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Clock</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -800px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -800px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -800px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -800px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -800px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -800px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-ClosePane</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -832px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -832px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -832px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -832px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -832px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -832px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Collapsed</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -864px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -864px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -864px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -864px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -864px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -864px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Comment</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -896px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -896px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -896px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -896px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -896px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -896px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Contact</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -928px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -928px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -928px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -928px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -928px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -928px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Contact2</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -960px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -960px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -960px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -960px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -960px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -960px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-ContactInfo</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -992px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -992px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -992px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -992px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -992px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -992px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Copy</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1024px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1024px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1024px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1024px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1024px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1024px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Crop</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1056px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1056px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1056px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1056px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1056px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1056px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Cut</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1088px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1088px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1088px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1088px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1088px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1088px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Data</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1120px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1120px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1120px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1120px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1120px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1120px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Data2</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1152px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1152px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1152px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1152px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1152px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1152px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Data3</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1184px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1184px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1184px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1184px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1184px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1184px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Day</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1216px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1216px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1216px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1216px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1216px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1216px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-DisableUpdates</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1248px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1248px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1248px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1248px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1248px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1248px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Discard</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1280px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1280px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1280px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1280px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1280px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1280px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Dislike</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1312px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1312px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1312px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1312px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1312px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1312px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-DockBottom</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1344px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1344px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1344px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1344px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1344px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1344px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-DockLeft</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1376px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1376px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1376px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1376px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1376px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1376px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-DockRight</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1408px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1408px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1408px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1408px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1408px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1408px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Document</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1440px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1440px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1440px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1440px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1440px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1440px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Download</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1472px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1472px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1472px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1472px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1472px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1472px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Edit</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1504px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1504px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1504px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1504px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1504px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1504px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Emoji</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1536px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1536px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1536px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1536px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1536px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1536px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Emoji2</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1568px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1568px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1568px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1568px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1568px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1568px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Expanded</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1600px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1600px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1600px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1600px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1600px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1600px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Favorite</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1632px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1632px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1632px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1632px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1632px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1632px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Filter</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1664px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1664px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1664px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1664px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1664px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1664px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Flag</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1696px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1696px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1696px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1696px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1696px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1696px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Folder</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1728px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1728px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1728px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1728px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1728px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1728px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Font</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1760px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1760px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1760px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1760px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1760px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1760px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-FontColor</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1792px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1792px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1792px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1792px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1792px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1792px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Globe</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1824px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1824px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1824px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1824px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1824px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1824px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Go</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1856px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1856px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1856px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1856px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1856px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1856px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-HangUp</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1888px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1888px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1888px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1888px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1888px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1888px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Help</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1920px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1920px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1920px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1920px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1920px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1920px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-HideBcc</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1952px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1952px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1952px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1952px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1952px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1952px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Highlight</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -1984px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -1984px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -1984px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -1984px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -1984px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -1984px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Home</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2016px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2016px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2016px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2016px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2016px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2016px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Import</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2048px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2048px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2048px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2048px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2048px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2048px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-ImportAll</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2080px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2080px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2080px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2080px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2080px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2080px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Important</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2112px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2112px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2112px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2112px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2112px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2112px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Italic</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2144px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2144px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2144px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2144px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2144px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2144px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Keyboard</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2176px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2176px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2176px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2176px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2176px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2176px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Like</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2208px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2208px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2208px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2208px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2208px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2208px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-LikeDislike</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2240px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2240px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2240px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2240px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2240px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2240px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Link</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2272px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2272px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2272px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2272px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2272px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2272px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-List</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2304px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2304px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2304px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2304px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2304px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2304px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Login</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2336px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2336px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2336px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2336px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2336px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2336px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Mail</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2368px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2368px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2368px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2368px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2368px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2368px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Mail2</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2400px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2400px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2400px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2400px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2400px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2400px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-MailForward</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2432px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2432px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2432px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2432px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2432px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2432px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-MailReply</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2464px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2464px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2464px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2464px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2464px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2464px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-MailReplyAll</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2496px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2496px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2496px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2496px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2496px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2496px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-MapPin</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2528px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2528px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2528px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2528px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2528px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2528px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Menu</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2560px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2560px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2560px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2560px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2560px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2560px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Message</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2592px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2592px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2592px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2592px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2592px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2592px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-MissingIcon</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2624px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2624px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2624px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2624px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2624px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2624px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Money</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2656px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2656px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2656px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2656px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2656px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2656px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-More</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2688px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2688px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2688px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2688px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2688px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2688px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-MoveToFolder</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2720px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2720px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2720px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2720px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2720px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2720px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-MusicInfo</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2752px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2752px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2752px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2752px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2752px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2752px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Mute</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2784px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2784px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2784px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2784px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2784px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2784px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Next</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2816px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2816px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2816px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2816px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2816px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2816px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-No</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2848px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2848px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2848px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2848px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2848px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2848px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-OpenFile</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2880px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2880px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2880px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2880px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2880px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2880px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-OpenLocal</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2912px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2912px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2912px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2912px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2912px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2912px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Orientation</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2944px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2944px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2944px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2944px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2944px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2944px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-OtherUser</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -2976px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -2976px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -2976px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -2976px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -2976px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -2976px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Out</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3008px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3008px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3008px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3008px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3008px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3008px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Page</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3040px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3040px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3040px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3040px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3040px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3040px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Page2</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3072px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3072px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3072px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3072px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3072px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3072px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Paste</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3104px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3104px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3104px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3104px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3104px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3104px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Pause</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3136px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3136px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3136px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3136px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3136px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3136px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-People</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3168px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3168px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3168px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3168px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3168px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3168px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Permissions</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3200px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3200px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3200px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3200px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3200px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3200px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Phone</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3232px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3232px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3232px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3232px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3232px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3232px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Photo</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3264px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3264px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3264px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3264px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3264px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3264px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Pictures</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3296px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3296px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3296px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3296px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3296px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3296px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Pin</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3328px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3328px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3328px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3328px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3328px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3328px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Placeholder</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3360px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3360px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3360px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3360px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3360px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3360px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Play</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3392px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3392px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3392px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3392px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3392px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3392px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Presence</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3424px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3424px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3424px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3424px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3424px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3424px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Preview</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3456px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3456px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3456px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3456px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3456px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3456px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-PreviewLink</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3488px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3488px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3488px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3488px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3488px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3488px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Previous</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3520px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3520px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3520px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3520px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3520px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3520px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Print</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3552px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3552px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3552px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3552px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3552px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3552px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Priority</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3584px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3584px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3584px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3584px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3584px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3584px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-ProtectedDocument</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3616px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3616px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3616px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3616px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3616px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3616px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Read</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3648px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3648px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3648px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3648px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3648px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3648px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Redo</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3680px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3680px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3680px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3680px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3680px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3680px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Refresh</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3712px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3712px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3712px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3712px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3712px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3712px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Remote</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3744px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3744px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3744px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3744px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3744px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3744px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Remove</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3776px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3776px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3776px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3776px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3776px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3776px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Rename</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3808px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3808px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3808px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3808px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3808px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3808px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Repair</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3840px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3840px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3840px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3840px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3840px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3840px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-RotateCamera</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3872px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3872px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3872px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3872px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3872px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3872px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Save</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3904px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3904px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3904px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3904px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3904px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3904px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-SaveLocal</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3936px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3936px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3936px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3936px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3936px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3936px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Search</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -3968px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -3968px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -3968px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -3968px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -3968px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -3968px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-SelectAll</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4000px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4000px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4000px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4000px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4000px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4000px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Send</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4032px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4032px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4032px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4032px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4032px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4032px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-SetLockscreen</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4064px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4064px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4064px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4064px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4064px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4064px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Settings</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4096px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4096px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4096px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4096px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4096px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4096px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-SetTitle</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4128px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4128px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4128px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4128px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4128px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4128px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Shop</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4160px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4160px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4160px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4160px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4160px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4160px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-ShowBcc</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4192px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4192px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4192px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4192px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4192px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4192px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-ShowResults</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4224px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4224px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4224px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4224px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4224px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4224px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Shuffle</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4256px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4256px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4256px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4256px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4256px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4256px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-SkipAhead</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4288px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4288px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4288px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4288px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4288px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4288px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-SkipBack</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4320px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4320px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4320px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4320px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4320px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4320px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Skydrive</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4352px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4352px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4352px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4352px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4352px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4352px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Slideshow</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4384px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4384px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4384px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4384px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4384px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4384px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Sort</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4416px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4416px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4416px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4416px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4416px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4416px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-SortAscending</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4448px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4448px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4448px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4448px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4448px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4448px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-SortDescending</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4480px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4480px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4480px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4480px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4480px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4480px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Stop</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4512px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4512px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4512px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4512px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4512px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4512px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-StopSlideshow</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4544px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4544px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4544px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4544px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4544px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4544px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Switch</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4576px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4576px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4576px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4576px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4576px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4576px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Sync</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4608px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4608px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4608px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4608px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4608px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4608px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Today</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4640px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4640px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4640px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4640px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4640px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4640px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Trim</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4672px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4672px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4672px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4672px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4672px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4672px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-TwoPage</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4704px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4704px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4704px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4704px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4704px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4704px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Underline</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4736px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4736px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4736px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4736px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4736px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4736px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Undo</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4768px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4768px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4768px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4768px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4768px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4768px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Unfavorite</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4800px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4800px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4800px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4800px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4800px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4800px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-UnPin</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4832px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4832px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4832px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4832px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4832px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4832px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Upload</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4864px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4864px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4864px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4864px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4864px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4864px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-User</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4896px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4896px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4896px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4896px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4896px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4896px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-UserRights</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4928px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4928px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4928px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4928px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4928px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4928px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-UserRoles</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4960px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4960px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4960px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4960px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4960px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4960px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Users</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -4992px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -4992px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -4992px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -4992px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -4992px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -4992px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Video</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -5024px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -5024px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -5024px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -5024px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -5024px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -5024px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-VideoChat</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -5056px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -5056px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -5056px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -5056px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -5056px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -5056px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-View</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -5088px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -5088px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -5088px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -5088px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -5088px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -5088px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-ViewAll</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -5120px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -5120px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -5120px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -5120px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -5120px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -5120px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Volume</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -5152px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -5152px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -5152px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -5152px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -5152px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -5152px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Webcam</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -5184px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -5184px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -5184px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -5184px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -5184px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -5184px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Week</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -5216px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -5216px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -5216px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -5216px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -5216px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -5216px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-World</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -5248px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -5248px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -5248px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -5248px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -5248px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -5248px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Yes</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -5280px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -5280px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -5280px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -5280px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -5280px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -5280px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-Zoom</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -5312px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -5312px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -5312px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -5312px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -5312px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -5312px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-ZoomIn</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -5344px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -5344px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -5344px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -5344px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -5344px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -5344px;"></td>
</tr>
<tr>
<td>CODE.Framework-Icon-ZoomOut</td>
<td style="background: lightgray; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/BattleshipIcons.png') 0px -5376px;"></td>
<td style="background: #BCC6D6; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/GeekIcons.png') 0px -5376px;"></td>
<td style="background: navy; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/MetroIcons.png') 0px -5376px;"></td>
<td style="background: black; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/VaporIcons.png') 0px -5376px;"></td>
<td style="background: lightblue; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WildcatIcons.png') 0px -5376px;"></td>
<td style="background: whitesmoke; text-align:center;"><img src="AllIcons/1x1.png" style="margin: 8px; height: 32px; width: 32px; background: url('AllIcons/WorkplaceIcons.png') 0px -5376px;"></td>
</tr>
</tbody>
</table>