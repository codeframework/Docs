# Caps-Lock Warning

CODE Framework has a control called the CapsLockWarning control. It can be used, as you probably guessed, to warn the user that caps-lock is on. It looks different in different themes, but here is an example:

![](Caps-Lock%20Warning%20and%20Global%20Key%20Handlers/Caps-Lock%20Warning%20and%20Global%20Key%20Handlers.jpg)

Here is the code that goes with that view:

```
<mvvm:View xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" 
           xmlns:mvvm="clr-namespace:CODE.Framework.Wpf.Mvvm;assembly=CODE.Framework.Wpf.Mvvm"
           xmlns:c="clr-namespace:CODE.Framework.Wpf.Controls;assembly=CODE.Framework.Wpf"
           Title="Login to NuTyme"
           Style="{DynamicResource CODE.Framework-Layout-SimpleFormLayout}">
  <Label>User Name</Label>
  <TextBox Text="{Binding UserName}" mvvm:View.WidthEx="30" />
  <Label>Password</Label>
  <PasswordBox c:PasswordBoxEx.Value="{Binding Password}" mvvm:View.WidthEx="30" />
  <c:CapsLockWarning />
</mvvm:View>
```

We decided to add this control, because we saw this implemented in problematic ways a number of times. One of the issues is that the warning needs to work right, even if the user switches to a different app and hits CAPSLOCK there, the indicator can’t get confused. The other reason we added this was that we simply wanted to make this easy and be able to remove the often complex code that was required for this.

The control itself is simple. It only has one meaningful property called CapsLockOn. When that is true, the CAPSLOCK key is pressed. The control has a template that shows a warning whenever that property is true. The control looks a bit different in different themes, but what you see above is representative. It is of course possible (and easy) to simply create your own control template that shows whatever you want displayed when CAPSLOCK is on.

## Global Key Handlers

BTW: The way we make sure we don’t miss that CAPSLOCK is turned on while the focus is elsewhere, we use a “global keyboard hook”, which is a lower-level feature in Windows, that allows us to listed to keyboard events, no matter where they happen, and also provides access to certain keystrokes you would otherwise not have access to. This requires interop and knowledge of the Windows SDK, so we thought we’d also make this easier. We thus introduced a GlobalKeyboardHookHelper class. Using this class, you can do this:

```cs
_keyHelper = new GlobalKeyboardHookHelper();
_keyHelper.KeyDown += (s, e) =>
{
    if (e.Key == Key.Capital || e.Key == Key.CapsLock)
        CapsLockOn = Keyboard.GetKeyStates(Key.CapsLock) != KeyStates.Toggled;
};
 
Unloaded += (s2, e2) =>
{
   _keyHelper.Dispose();
};
```

So this fires a KeyDown event, just like other keyboard events. The difference is that this fires, even if your app doesn’t have focus (so this may or may not be something you want). You also are notified of keys you would otherwise miss, such as WINDOWS keys.

Note that we are making sure we are calling Dispose() 