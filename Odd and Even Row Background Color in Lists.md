# Odd and Even Row Background Colors in Lists

_The following is an excerpt from an internal email at EPS/CODE related to creating listbox themes with odd and even row background colors:_

Here’s the main code snippet that is responsible for the background colors:

```
<Style TargetType="ListBox" x:Key="Metro-Control-ListBox-Columns" BasedOn="{StaticResource Metro-Control-ListBox-Stack}">
    <Style.Resources>
        <Style TargetType="ListBoxItem">
            <Setter Property="Template">
                <Setter.Value>
                    <ControlTemplate TargetType="ListBoxItem">
                        <c:GridEx BackgroundBrush="{DynamicResource CODE.Framework-Application-ThemeBrush1}"
                                  BackgroundBrushLightFactor=".85" 
                                  UseItemIndex="True" x:Name="bg"
                                  Margin="0,0,0,2">
                            <ContentPresenter HorizontalAlignment="{TemplateBinding HorizontalContentAlignment}" 
                                              SnapsToDevicePixels="{TemplateBinding SnapsToDevicePixels}" 
                                              VerticalAlignment="{TemplateBinding VerticalContentAlignment}"
                                              Margin="5,2"/>
                        </c:GridEx>
                        <ControlTemplate.Triggers>
                            <Trigger Property="IsOddRow" SourceName="bg" Value="True">
                                <Setter Property="BackgroundBrushLightFactor" TargetName="bg" Value=".75" />
                            </Trigger>
                        </ControlTemplate.Triggers>
                    </ControlTemplate>
                </Setter.Value>
            </Setter>
        </Style>
    </Style.Resources>
</Style>
```

The basic idea is actually pretty simple: This defines a style for a ListBox. Then within that style’s resources, I define another style for ListBoxItem. So this basically means “use this listboxitem style automatically/implicitly for all items within this listbox”. It’s WPF’s equivalent of a CSS rule that applies for a certain element within another element… so in CSS this would probably be written as: .Metro-Control-ListBox-Columns ListBoxItem {}

Anyway: Within this listboxitem style, I define the control template with a grid that has a background color of whatever. And then I define a trigger that says “if IsOddRow is on the grid I use as the background then…” and I use that trigger to change the background color.

Now the trick is that I use GridEx as the object to set the background color on. That is where CODE Framework comes in (everything else is standard WPF). GridEx has the capability to know whether it is in an odd or even row within a listbox IF UseItemIndex is set to true. So that is important to turn on! (On a side-node: This isn’t the fastest thing ever, so you wouldn’t want to do it on a very large list).

