# WPF ImageHelper

_Excerpt from an internal email at EPS/CODE:_

CODE Framework includes a CODE.Framework.Wpf.Utilities.ImageHelper class that has a BitmapToImageSource() method. This method takes a GDI+ bitmap/image and converts it something you can use in WPF image scenarios.

Example: You have a PNG file you want to load from somewhere (disk, URL,…):

```c#
var bitmap = new Bitmap(“Test.png”);
```

But how could you use that in a view model in WPF? Like this:

```c#
public ImageSource Bitmap
{
    get
    {
        var bitmap = new Bitmap("Test.png");
        return ImageHelper.BitmapToImageSource(bitmap);
    }
}
```
This is also useful when loading images from databases, btw.
