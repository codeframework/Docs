# Type and Null Checking Convenience Methods

_Excerpt from an internal email at EPS/CODE:_

FYI: You guys have probably all seen and written code like that:  

```cs
var element = FindElement(elementName);
if (element == null) return false;
var frameworkElement = element as FrameworkElement;
if (frameworkElement == null) return false;
ApplyStyleToObject(style, frameworkElement);
```

So this code gets some object somewhere, then checks if it is null, then checks if it is a certain type, and if that is all “for real”, then it can do something with it.

I have written this code hundreds of times. It is annoying. And it basically performs a single line operation but requires several additional lines of checking to make sure everything is cool. Lots of “noise” and little actual stuff happening.

I have now grown so tired of this, I created a convenience object in the CODE Framework to help me with this. If you find it useful, go ahead and use it. Basically, it is defined on a static class called “If” and that has a method called “Real”. You can tell Real() what type you expect and if that is all good, then it executes code you give it. So the above code can now be re-written like so:

```
var element = FindElement(elementName);
If.Real<FrameworkElement>(element, el2 => ApplyStyleToObject(style, el2));
```

So what happens here is that we are passing the object in question to the Real() method (“element” in this example). If.Real() then checks to make sure element is not null, and element is in fact a FrameworkElement, and if that is all good, it executes the code passed as the second parameter and passes element on, but this time types as FrameworkElement. So that becomes the first parameter, which I then use as a lambda and then run the same code we had in the original version.

This also works with 2 objects, btw:

```
var element = FindElement(elementName);
var style = FindStyle(styleName);
If.Real<FrameworkElement, Style>(element, style, (el2, s2) => ApplyStyleToObject(s2, el2));
```

So in that case, element and style must not be null, and they must be of type FrameworkElement and Style respectively, for the ApplyStyleToObject(…) code to execute.

There you have it! Just a simple little thing that can make your life easier.
