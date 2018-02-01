# Notifications

The WPF MVVM/MVC sub-set of CODE Framework features a standardized notification system. This allows to issue simple notifications to the user that may be displayed using “notification toasts” or similar visual paradigms. Notification messages are typically displayed for a short duration (10-12 seconds) and then disappear without any user interaction.

The CODE Framework notification system follows the same patterns as many sub-systems in the framework. In particular, the there is a simple way to issue notifications through the controller class like this:

```cs
Controller.Notification("Hello World!");
```

Notifications are then displayed in a matter that is specific to the chosen theme. The following screen shot shows notifications in a Metro-themed WPF application:

![](WPF%20MVVM/MVC%20Notifications_image_2.png)

The exact visual style of the notification within Metro applications depends on standardized color settings. Here are the settings one can override:

```
<Color x:Key="CODE.Framework-Metro-NotificationForegroundColor">White</Color>
<Color x:Key="CODE.Framework-Metro-NotificationBackgroundColor">DarkRed</Color>
```

Of course it is also possible to completely restyle notifications.

The following screen shot shows the default appearance of notifications using the Battleship theme (Windows 95):

![](WPF%20MVVM/MVC%20Notifications_image_4.png)

Notifications can be issued with a a few simple standard parameters, typically at least one or two lines of text, and sometimes an icon resource (stored as some sort of brush, often a drawing brush or an image brush). The following code snippet shows the code used to display the first of the notifications in the above screen shots:

```cs
Controller.Notification("Fox & Hound", "The quick brown fox jumps over the lazy dog.", imageResource: "Notification-Message"));
```

Note that it is up to each theme to determine how long each notification is displayed and how many notifications to display at once. Most themes display notifications for about 10 seconds and show up to 5 notifications. In most themes these settings can be changed by setting the MaximumNotificationCount and NotificationTimeout properties on the Shell (which can be easily done in the Shell’s style definition).

## Custom Notifications

Following a standard CODE Framework pattern, notifications messages use standardized view and view models under the hood to make the notification system tick. When a user calls Controller.Notification() as shown in the examples above, the system creates a standard notification view model and view, populates appropriate data in the model, and passes the result on for processing. This is just the default. Developers can take advantage of this setup and pass custom views and view models to the system to achieve complete freedom in what is being displayed in notification messages.

Consider the following example:

```cs
Controller.Notification(model: new ExampleNotificationViewModel(), viewName: "ExampleNotification", controllerType: typeof(CustomerController));
```

This displays a custom notification that even includes an interactive element (button). It also uses a custom view. Here is the code for the view model:

```cs
public class ExampleNotificationViewModel : NotificationViewModel 
{ 
    public ExampleNotificationViewModel()
    {
        Text1 = "Click Now!"; 
        Text2 = "This is an important..."; 
        Logo1 = Application.Current.FindResource("Notification-Message") as Brush; 
        ExampleAction = new ViewAction("Example", execute: (o, a) => Controller.Message("Well done!")); 
    } 

    public ViewAction ExampleAction { get; set; } 
}
```

And here is the actual view definition:

```
<Grid xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation">
  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="Auto" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
 
  <Rectangle Height="60" Width="60" Margin="0,0,10,0" Fill="{Binding Logo1}" />
  <StackPanel Grid.Column="1">
    <Button Content="{Binding Text1}" Command="{Binding ExampleAction}" />
    <TextBlock TextWrapping="Wrap" Text="{Binding Text2}"
               FontSize="{DynamicResource FontSize-Normal}"
               FontFamily="{DynamicResource DefaultFont}"/>
  </StackPanel>
</Grid>
```

Note that this view has to be defined in one of the standard view locations. If that location is not the shared location (typically it isn’t), then the system needs to know which controller the view goes with in order to find the view, which is why the controller type has to be passed as a parameter in the example above. This information provides overall execution context, so view engines can find their way around.

This results in the following UI:

![](WPF%20MVVM/MVC%20Notifications_image_6.png)

Note: Be careful with the amount of interactivity you put into notifications. Notifications are messages that are displayed for short periods of time. They are not suited for interaction that goes beyond a single click or something comparable since they are simply not on screen long enough. (Some themes may choose to only show a single notification at a time and thus scroll notifications through very quickly, or they may choose to show notifications for only 3 or 4 seconds or less.)

Note that it is also possible to add explicitly coded actions to a controller that returns a Notification Message. In most cases, this doesn’t provide much of a benefit though and is rarely done.
