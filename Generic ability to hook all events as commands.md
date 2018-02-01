# Binding Events to Commands in WPF

_Excerpt from an internal email at EPS/CODE:_

CODE Framework (probably released tomorrow or the day after) has the ability to hook any event on any object using a command. In other words: Rather than writing an event handler, you can now bind an event to a command like so:

```
<Window x:Class="CODE.Framework.Wpf.TestBench.EventCommandTest"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:c="clr-namespace:CODE.Framework.Wpf.Controls;assembly=CODE.Framework.Wpf" 
        Title="EventCommandTest" Height="300" Width="300">
    <Grid>
        <Button Content="Hello">
            <c:Ex.EventCommand>
                <c:EventCommand Command="{Binding DoubleClickCommand}" Event="MouseDoubleClick" />
            </c:Ex.EventCommand>
        </Button>
    </Grid>
</Window>
```

So you simply set the EventCommand property defined on the generic “Ex” object (which we will use for similar things in the future… “Ex” stands for “generic Extensions”). You then specify a command, the event you want to use as a string, and (optionally) a command parameter. 

Typically, you will use this to bind one of our ViewActions to any event you want. (However, this works not just with ViewActions, but with all WPF commands).

Note that the above version only allows hooking a single event that way. If you want to hook more than one vent, you need to use the following, slightly more verbose syntax: 

```
<Window x:Class="CODE.Framework.Wpf.TestBench.EventCommandTest"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:c="clr-namespace:CODE.Framework.Wpf.Controls;assembly=CODE.Framework.Wpf" 
        Title="EventCommandTest" Height="300" Width="300">
    <Grid>
        <Button Content="{Binding TestCaption}">
            <c:Ex.EventCommands>
                <c:EventCommandsCollection>
                    <c:EventCommand Command="{Binding TestCommand2}" Event="MouseEnter" />
                    <c:EventCommand Command="{Binding TestCommand3}" Event="MouseDoubleClick" />
                </c:EventCommandsCollection>
            </c:Ex.EventCommands>
        </Button>
    </Grid>
</Window>
```

In this example, you set the EventCommands property (with an S at the end!) and unlike in the first version, you set this to a collection first, and then populate the collection of commands. You can bind to any number of events, and you could even bind more than one command to an event like this:

```
<c:Ex.EventCommands>
  <c:EventCommandsCollection>
    <c:EventCommand Command="{Binding TestCommand2}" Event="MouseEnter" />
    <c:EventCommand Command="{Binding TestCommand3}" Event="MouseDoubleClick" />
    <c:EventCommand Command="{Binding TestCommand3b}" Event="MouseDoubleClick" />
  </c:EventCommandsCollection>
</c:Ex.EventCommands>
```

So this should be quite useful, as it eliminates the vast majority of scenarios that require code-behind files, or behaviors. It is of course still better to use explicit commands if the exist. There is no reason to use this for the click event of a button for instance, since a button has a native Command property that fires when the click event happens. Similarly, we have special commands when a selection on a listbox happens. And we will add more such commands in the future. In those cases, do NOT use the construct described here, but use the native way to raise commands.
