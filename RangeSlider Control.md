# RangeSlider Control

CODE Framework has a RangeSlider control. 

Here’s what that looks like: 

![](Range%20Slider/RangeSlider.jpg)

The control is fully stylable (although currently, all the themes use the same style… consider the visuals a work in progress, as we will likely create more styles in the future). 

The control offers pretty much what you would expect: Minimum, Maximum, LowValue, HighValue. It also offers LowValueFinal and HighValueFinal properties (which are only updated when the user lets go of the slider, while regular LowValue and HighValue properties are updated continuously as the user interacts). The RangeSlider also has an IsInverted property (if you look closely, the first slider goes left-to-right, while the second one goes right-to-left… same for the vertical version). Oh, and of course there is an Orientation property. 
