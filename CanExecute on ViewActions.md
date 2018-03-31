# Using CanExecute on `ViewAction`s

_Excerpt from an internal email at CODE/EPS:_

`ViewAction`s in the framework are just commands (objects that implement the `ICommand` interface). Commands have a `CanExecute()` method than can be overridden/implemented. When an object is bound to a command, it automatically evaluates the `CanExecute()` method. If it returns true, the control is enabled, otherwise is disabled. This is standard WPF functionality, and it of course also works as such in CODE Framework.

Question is: How can you change this after the initial assignment? In other words: How can you indicate to WPF that the state may have changed and thus the `CanExecute()` method needs to be executed again to determine the latest correct state? As it turns out, `ICommand` defines an event called `CanExecuteChanged` and whenever a command is bound to a control, that control listens for that event and when it fires, it knows to call `CanExecute()` again. So you can fire that event manually and everything should work. (The only slight trouble is that in order to do that, you have to usually subclass the command or implement the entire interface, which is something I am trying to protect you from. Nevertheless, this is how it works and it isn’t super-difficult).

So with the CODE Framework, we use these `ViewAction` objects which are really just fancy commands (and they implement `ICommand`), so everything I said above is valid. Of course we are trying to make it as simple to use these actions/commands as possible, so you generally do not have to implement those yourself, but you can simply instantiate a `ViewAction` object and pass it a few parameters (such as delegates for the Execute() method, a caption, and a few other things). As it turns out, you can (and always have been able to) pass a second delegate for `CanExecute()`, which is supposed to be a piece of code that returns true or false depending on whether you want the command to be executable.

Example: Let’s say you have a view model with a `SearchText` property and a Search command, allowing people to search based on text they specify. And you only want the command to be enabled when `SearchText` is not empty. You can define such a command like so:

```c#
Search = new `ViewAction`("Search", 
    execute: (a, o) => MessageBox.Show("Searching for " + SearchText), 
    canExecute: (a, o) => !string.IsNullOrEmpty(SearchText)); 
```

As you can see, this executes a single line of code, which is a message box that shows what we want to search for (which would be a more sophisticated piece of code that actually performed some search in the real world). The `canExecute` delegate on the other hand returns true only when `SearchText` is not empty.

If you were to just create this action and then bind it to a control such as a button, chances are the `SearchText` property is going to be empty by default, thus `CanExecute` returns false when initially evaluated, and the button will automatically be disabled. (There is nothing you need to do further to cause disabling. You do NOT need to bind the enabled property or anything like that. Enabling and disabling is simply an automatic behavior of controls that use commands).

So now the question is: How would you cause this command to be re-evaluated when someone puts some text into `SearchText`? For this, you actually have to raise that `CanExecuteChanged` event, which up to now, you could only do by subclassing the `ViewAction` class. But here is the good news: I just made this simpler by adding an `InvalidateCanExecute()` method to the `ViewAction` class. You can call this method to indicate to the command/action that the prior can-execute state is now not valid anymore and needs to be re-evaluated. This could be done from the setter of the `SearchText` property for instance. (The `InvalidateXxx` naming convention goes along with quite a view other things in Windows that can be invalidated).

So a complete view model for the scenario described above could be this:

```c#
public class CanExecuteChangedViewModel : ViewModel
{
    public CanExecuteChangedViewModel()
    {
        Search = new `ViewAction`("Search", 
           execute: (a, o) => MessageBox.Show("Searching for " + SearchText), 
           canExecute: (a, o) => !string.IsNullOrEmpty(SearchText));
    }

    public `ViewAction` Search { get; set; }

    private string _searchText;
    public string SearchText
    {
        get { return _searchText; }
        set
        {
            _searchText = value;
            NotifyChanged("SearchText");
            Search.Invalidate`CanExecute()`;
        }
    }
}
```

You could now use this view model to bind to the following XAML, and it would behave exactly like you would expect it to, with the search button being enabled and disabled depending on whether or not the textbox is empty:

```
<Window x:Class="CODE.Framework.Wpf.TestBench.CanExecuteChangedTest"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:Controls="clr-namespace:CODE.Framework.Wpf.Controls;assembly=CODE.Framework.Wpf"
        Title="CanExecuteChangedTest" Height="300" Width="300">
  <Grid Controls:GridEx.RowHeights="Auto,*" Controls:GridEx.ColumnWidths="*,Auto">
    <TextBox Text="{Binding SearchText, UpdateSourceTrigger=PropertyChanged}" />
    <Button Command="{Binding Search}" Grid.Column="1" Content="{Binding Search.Caption}" />
    <ListBox Grid.ColumnSpan="2" Grid.Row="1" />
  </Grid>
</Window> 
```
