# Transparent ListBoxes in Metro-style WPF Apps

The following is an excerpt from an internal email at EPS/CODE: 

FYI: Since this question came up, here is a thought on how to create styles for Metro listboxes that have a transparent background.

```
<Style TargetType="ListBox" x:Key="bla" BasedOn="{StaticResource Metro-Control-ListBox-Tiles}">
```

Basically, whenever you create a new style for a listbox, that style starts out as a standard Windows listbox. Which has a white background. You do not want that in Metro of course. For that reason, our framework defines a special Metro listbox style that has a transparent background (or no background at all, to be exact). However, if you create a new style for a listbox you want to use in Metro, you want to preserve that behavior of being transparent, but add your own things to it. The way you do that is the following:

```
<Style TargetType="ListBox" x:Key="bla" BasedOn="{StaticResource Metro-Control-ListBox-Tiles}">
```

So our default style is called “Metro-Control-ListBox-Tiles”. This creates one of those transparent listboxes and it also sets the layout type to be tiles (this is a good way to not have to worry about the layout panel… see the last email I sent… so this is much easier). If you just want a regular transparent listbox, do this:

```
<Style TargetType="ListBox" x:Key="bla" BasedOn="{StaticResource Metro-Control-ListBox}">
```

So same name, basically, but without “Tiles” at the end.

Anyway: With this style definition, you can then go ahead and set whatever other settings you feel like. Here is an example:

```
<Style TargetType="ListBox" x:Key="CustomerList" BasedOn="{StaticResource Metro-Control-ListBox-Tiles}">
    <Setter Property="Margin" Value="125,25,0,0" />
    <Setter Property="ItemTemplate">
        <Setter.Value>
            <DataTemplate>
                <Grid Background="{DynamicResource CODE.Framework-Application-ThemeBrush1}" Width="120" Height="120">
                    <Label Content="{Binding LastName}" Foreground="White" HorizontalAlignment="Left" VerticalAlignment="Top" FontSize="14.667" FontWeight="Bold" Margin="5,0" />
                    <Label Content="{Binding FirstName}" Foreground="White" HorizontalAlignment="Left" VerticalAlignment="Top" Margin="5,25,0,0" />
                    <Label Content="{Binding Company}" Foreground="White" VerticalAlignment="Bottom" HorizontalAlignment="Right" Margin="0,0,5,5" />
                </Grid>
            </DataTemplate>
        </Setter.Value>
    </Setter>
</Style>
```