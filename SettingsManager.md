CODE Framework features a "settings manager", which allows for the preservation of various values between sessions on a user or workstation basis. Typical examples of this are things such as window positions, column customizations, remembering recently used files, remembering the most recent user name, and so on.

The fundamental idea is straightforward: Whenever settings (such as window positions, or user names, or ...) need to be serialized, a serializer object is invoked by the SettingsManager class to serialize the desired settings. This may be a single serializer, or multiple serializers. For instance, an item edit view/window may want to save the current window position and size (so the window can re-appear at the same position the next time the user opens it), and it may also want to save the ID of the most recently used item (so the most recently edited item can be loaded as the default whenever the UI is opened the next time). Window information and item ID can be handled as two completely different sets of settings that can be loaded and saved independently. If that is desired, two different serializers can be used on the same window.

Furthermore, it may be desired to save settings on a per-user basis, or it may be saved workstation specific. For instance, the window size and position may be saved for a specific workstation so the window appears at the same position no matter which user opens it, but on a different workstation, which may have completely different monitor sizes, the same settings are *not* applied. The memorized item ID on the other hand, may be user-tied and thus if the same user opens the same UI on a different workstation, the user would still see their last edited item. Another variation in this theme is the ability to save something user *and* workstation specific. For instance, window positions could be saved on specific workstations but for each user individually, so while user 1 sees the same window position they used previously (but with different window positions on different workstations), but user 2 would see different settings. All those are valid options supported by the framework. It is up to the developer to decide which approach to use.

In addition to serializers, there are also "handler" objects. Handler objects are responsible for saving the serialized state. For workstation specific settings (or user and workstation settings), the default handler saves away all settings in JSON format on the local harddrive. For user-specific settings, things are a bit more complicated, since settings usually need to be saved into a database or handed over to a service that can save the information and then retrieve it, regardless of what workstation the user is on.

## A Simple Example

As a first example, consider the ability to save window position and size.

> Note: There is a more automated way to handle this scenario (see below).

To enable saving of window information, do the following in the window's constructor:

```cs
SettingsManager.RegisterSerializer<WindowPositionSettingsSerializer>();
SettingsManager.LoadSettings(this);
```

This does two things: First, it makes sure that the <code>SettingsManager</code> is configured to use the <code>WindowPositionSettingsSerializer</code> class. (This line of code can be called repeatedly without ill affects. It simply makes sure that that serializer is known and in use). Secondly, it attempts to load settings for the current object (the window the constructor belongs to). 

> Note: It is also possible to remove the registration of the serializer from the constructor and move it into application startup, since the serializer is registered globally for the app as this calls a static method. However, this is an error-prone approach, because if anyone removes that registration, it may not be obvious in this class that that serializer is even required or desired. Therefore, it never hurts to make sure within each class that the desired serializers are registered.

The second line of code is interesting. It attempts to load all settings for the current object that are registered. In this example, the only serializer that is registered is the <code>WindowPositionSettingsSerializer</code>, which is therefore invoked. It is conceivable that there are other serializers registered as well, which would thus be called in this line of code. (Note: There are additional parameters that can be used to define in more detail which serializers are invoked, if that is desired).

The <code>WindowPositionSettingsSerializer</code> is invoked, it looks at the provided object, and, since that object is a Window, the serializer decides that it can handle the call. It then looks for existing settings for the window. The first time around, there aren't any. Therefore, nothing will happen. However, once settings are saved away, the serializer will find the previously saved settings and apply them to the current window.

For this to work, we also have to save settings. This can be done in a number of ways. The most common is to save settings when the window closes. Therefore, we can add the following code to our constructor:

```cs
window.Closing += (s2, e2) => 
{ 
    SettingsManager.SaveSettings(this); 
};
```

This does the opposite of the load operation. It invokes all currently registered serializers for the current object and causes them to save away the current window information.

Note: In save scenarios, one often wants to be more specific. For instance, if we want to make sure that we *only* invoke the window settings serializers, but no others, we could do it like this:

```cs
window.Closing += (s2, e2) => 
{ 
    SettingsManager.SaveSettings(
        this, 
        serializerTypeFilter: typeof (WindowPositionSettingsSerializer), 
        includeDerivedTypeFilterTypes: true); 
};
```

This is similar to the previous version, except this time, the save operation is filtering the serializers to the <code>WindowPositionSettingsSerializer</code>, or subclasses thereof. Whether one wants to get this specific or not depends on what one wants to save when the window closes.

> Note: By default window settings are considered to be "Workstation" settings shared by all users. (This can be changed by passing more parameters to the save and load methods). The window settings serializer serializes Top, Left, Height, and Width properties.

### Automatic Window Position Handling

The scenario described above is so common, we also make it available in a more automated fashion. In fact, if the need is to save window position and size on a workstation-scoped basis, it can be done by setting the following attached property on any window object:

```xml
<Window xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:controls="clr-namespace:CODE.Framework.Wpf.Controls;assembly=CODE.Framework.Wpf"
        Title="Save Window Settings Test" Height="300" Width="300"
        controls:WindowEx.AutoSaveWindowPosition="True">
```

When the <code>AutoSaveWindowPosition</code> property is set to <code>True</code>, it automatically handled window positions in pretty much exactly the same way as shown above.

## The WorkstationSettingsHandler class

By default, workstation settings of any kind are saved away by the <code>WorkstationSettingsHandler</code> class. This class takes the serialized state as provided by the serializer, and saves it in the <code>SpecialFolder.CommonApplicationData</code> folder configured on that machine (typically, this is a folder such as <code>c:\ProgramData</code>), plus a folder name. The sub-folder name is either the name of the current EXE, or an explicitly specified folder using the <code>AppDataSubFolder</code> static property on the <code>WorkstationSettingsHandler</code> class:

```cs
WorkstationSettingsHandler.AppDataSubFolder = "MyApp";
```

Each saved set of settings (such as the window positions) are stored in individual JSON files of the same name as the fully qualified class name. For instance, if the app is called Test.exe and the class is called <code>MyApp.Something.EditWindow</code>, the full path and file name is:

```txt
c:\ProgramData\Test.exe\MyApp.Something.EditWindow.WindowPosition.json
```

> All settings in the JSON file are stored in clear text and are therefore not secure. For this reason, no sensitive data should ever be serialized away in this fashion. Note however that it is possible to replace the WorkstationSettingsHandler class with a different handler object that saves information in a more secure fashion.

## The DefaultSettingSerializer

The <code>DefaultSettingSerializer</code> class is the "mother of all serializers". It simply takes all object state that is serializable and serializes it to JSON. This is useful for scenarios such as saving all fields of a data object to create state buffers or recovery information, among other scenarios.

Registering and using this serializer is practically identical to the window position serializer:

```cs
public class MyDataObject
{
    public MyDataObject()
    {
        SettingsManager.RegisterSerializer<DefaultSettingsSerializer>();
        SettingsManager.LoadSettings(this);
    }
    
    public void SaveLocalState()
    {
        SettingsManager.SaveSettings(this);
    }
}
```

Again, the first line in the constructor makes sure the serializer is registered (which can be called repeatedly without problems), and then the load and save operations automatically use it.

> Note that this is a very heavy-handed approach. This handler is now registered system-wide (since serializers are registered in a static fashion). Therefore, if some other unrelated object now calls <code>SettingsManager.SaveSettings(this)</code>, this generic serializer would also attempt to save that object as well. In fact, serializers can indicate whether or not they work in conjunction with other serializer. This serializer indicates (see below) that it can only work on its own. Therefore, this would be the *only* serializer used in a serialization run. For these reasons, it is suggested to only use this serializer in special cases.

## The AttributeSettingSerializer

The <code>AttributeSettingSerializer</code> class is one of the most powerful serializers in CODE Framework. It can be used on any object in conjunction with attributes. Consider this example:

```cs
public class MyDataObject
{
    public MyDataObject()
    {
        SettingsManager.RegisterSerializer<AttributeSettingsSerializer>();
        SettingsManager.LoadSettings(this);
    }
    
    public void SaveSettings()
    {
        SettingsManager.SaveSettings(this);
    }
    
    public string Name { get; set; }

    [UserAttribute]    
    public string Id { get; set; }
    
    [WorkstationAttribute]
    public DateTime LastOpenedOnThisWorkstation { get; set; }
}
```

In this example, we first configure the use of the <code>AttributeSettingSerializer</code> class and then call the <code>LoadSettings()</code> method (and the equivalent save operation in a separate method). This serializer then looks at all the members of the object it is asked to handle and inspects the attributes on all its members. It then saves all properties flagged with <code>WorkstationSetting</code> on the current workstation, and all the ones flagged as <code>[UserSetting]</code> in user settings. (There also is a <code>[WorskationAndUserSetting]</code> attribute). All other members are ignored. Therefore, this example saves the <code>Id</code> in user settings, the <code>LastOpenedOnThisWorkstation</code> property in workstation settings, and it completely ignores the <code>Name</code> property.

This serializer provides a great deal of control and can therefore be used for a wide range of scenarios.

## The ListColumnsSettingsSerializer

This is a specialized serializer that can serialize the CODE Framework <code>ListColumnsCollection</code> class, and is therefore great to save away customized column settings, allowing for a very simple approach to displaying a multi-column list and allowing the user to save column customizations (such as changing column widths, column orders, or sorting).

Here is how to use this serializer:

```cs
public class MyViewModel : ViewModel
{
    public MyViewModel()
    {
        SettingsManager.RegisterSerializer<ListColumnsSettingsSerializer>();
        SettingsManager.LoadSettings(Columns, scope: SettingScope.User);
    }
    
    public void SaveColumnSettings()
    {
        SettingsManager.SaveSettings(Columns, scope: SettingScope.User);
    }
    
    public ListColumnsCollection Columns { get; } = new ListColumnsCollection();
}
```

This code should by now be familiar, except in this case, I chose to save the column customization as user settings, so they appear the same way on each workstation the user logs in (the default for this is to be workstation specific). This is simply done here as an example, and whether or not this is appropriate depends on individual needs.

> Note that this serializes each Columns collection individually. If a single object defined more than one set of columns, load and save has to be called for each column individually. This also allows for fine-grained control over which set of columns is persisted and which isn't.

On a side-note, this is how a user interface can use this columns collection to set up a multi-column list in the UI:

```xml
<ListBox cf:ListEx.Columns="{Binding Columns}" />
```

Or, in the case of a combobox:

```xml
<ComboBox cf:ListEx.Columns="{Binding Columns}" />
```

## Most-Recently-Used

One very common scenario for serialization need is a "most-recently-used" (a.k.a. MRU) list. In applications like Word, this could be a list of recent documents the user has opened. In business applications, this is usually a list of data elements the user recently edited or viewed, and there are typically different types (such as the most-recently edited customers and the most-recently viewed invoices). Therefore, the system has to be able to track different categories of MRU lists. 

In CODE Framework, this is handled through a specialty feature of the <code>SettingsManager</code> class. Here is an example that saves the fact that the user edited an invoice in the invoice-MRU-list:

```cs
var data = new Dictionary<string, string>
{
    {"InvocieId", this.Id},
    {"Number", this.Number},
    {"SomeOtherData", "yada yada yada"}
};
SettingsManager.SaveMostRecentlyUsed("Invoices",
    Id,
    "Invoice #" + Number, // Display text for the user
    scope: SettingScope.Workstation, // If needed
    data: data);
```

This feature saves away a name-value collection of arbitrary data (a <code>Dictionary<string, string></code>) associated with an ID (the ID is needed so the system knows when to update older settings) and display text that can be presented to the user (often in a menu or list). In addition, it is possible to define the scope of the list, such as saving on a workstation or user-specific.

The load operation often looks like this:

```cs
var recentUses = SettingsManager.LoadMostRecentlyUsed("Invoices", SettingScope.Workstation);
mruList.ItemsSource = recentUses;
```

This retrieves a complete list of most recently used invoices (with a name/value collection for each item) and then assigns it as the items source of some sort of list (this isn't required of course, but it is a very common scenario). From there, the user could then pick an entry, and the developer can react by loading the chosen entry (invoice in this case). This is just one possible scenario of course. We could also use this to automatically load the very most recent item automatically, to name just one further idea.

## Custom Serializers

The provided serializers cover many common scenarios, but it is also sometimes required to create a different type of serializer. For instance, if we needed a serializer that can save away a color setting provided by a hypothetical <code>IHaveColor</code> interface, we could create a custom serializer like so:

```cs
public class ColorSettingSerializer : ISettingsSerializer
{
    public string SerializeToJson(object stateObject)
    {
        var colorObject = stateObject as IHaveColor;
        if (colorObject == null) return string.Empty;
        
        var jb = new JsonBuilder();
        foreach (var property in properties)
            jb.Append("Color", colorObject.GetColorString());
        return jb.ToString();
    }

    public void DeserializeFromJson(object stateObject, string state)
    {
        if (string.IsNullOrEmpty(state) || stateObject == null) return;
        var colorObject = stateObject as IHaveColor;
        if (colorObject == null) return string.Empty;

        JsonHelper.QuickParse(state, (n, v) =>
        {
            if (n == "Color")
                colorObject.SetColorString(v);
        });
    }

    public bool CanHandle(object stateObject, string id, SettingScope scope)
    {
        return stateObject is IHaveColor;
    }

    public string GetSuggestedFileName(object stateObject, string id, SettingScope scope)
    {
        return id + "." + typeof(T).Name + ".Color.json";
    }

    public bool UseInAdditionToOtherAppliedSerializers => true;
}
```

Setting serializers, fundamentally, are relatively simple objects. The main functionality is in the <code>SerializeToJson()</code> and <code>DeserializeFromJson()</code> methods, which - you guessed it - handle serialization and deserialization of object state. 

In addition, a serializer has to indicate whether it can handle a certain object (ours can handle anything that implements <code>IHaveColor</code>). Then, it is considered good form to suggest a file name for saving the state in case the associated handler creates files (this isn't strictly needed, but is makes file name creation more predictable, which is helpful for debugging and also for maintainability... otherwise you would deal with some very cryptic file names). Finally, the serializer indicates whether it plays well with others (ours certainly does and is not bothered if other serializers, such as the window position serializer, also want to save information about the current object).

Here is an example of how to use this serializer in the constructor of a window:

```cs
SettingsManager.RegisterSerializer<ColorSettingSerializer>();
SettingsManager.LoadSettings(this);

window.Closing += (s2, e2) => 
{ 
    SettingsManager.SaveSettings(this); 
};
```

## Creating and Registering Custom Handlers

"Setting Handlers" are the objects that save settings to the file system or database, or wherever we want to physically save the serialized information. For workstation settings, it is usually not required to create custom handlers, but user settings typically have to be saved into a database or need to be sent to a service, and CODE Framework cannot automatically know how to do that.

The following example is a handler that saves user settings to a service:

```cs
public class MyUserSettingsHandler : ISettingsHandler
{
    public bool Save(string state, string id, string suggestedFileName)
    {
        var username = UserHelper.CurrentUserName;
        if (string.IsNullOrEmpty(username)) return true; // Not an error as such, but we do not save anything

        var saveUserSettingRequest = new SaveUserSettingRequest
        {
            UserSetting = new UserSettingInformation
            {
                UserName = username,
                Name = suggestedFileName,
                Value = state
            }
        };

        ServiceClient.Call<IUserService>(s => { s.SaveUserSetting(saveUserSettingRequest); });
        return true;
    }

    public string Load(string id, string suggestedFileName)
    {
        var username = UserHelper.CurrentUserName;
        if (string.IsNullOrEmpty(username)) return string.Empty; // Not an error as such, but we do not load anything
        
        GetUserSettingResponse loadResponse2 = null;
        ServiceClient.Call<IUserService>(s => 
        { 
            loadResponse2 = s.GetUserSetting(
                new GetUserSettingRequest 
                {
                    UserName = username, 
                    SettingName = suggestedFileName
                    
                }
            ); 
        });

        return loadResponse2.UserSetting.Value;
    }

    public bool Clear(string id, string suggestedFileName)
    {
        var username = UserHelper.CurrentUserName;
        if (string.IsNullOrEmpty(username)) return true; // Not an error as such, but we do not save anything

        ServiceClient.Call<IUserService>(s =>
        {
            s.SaveUserSetting(new SaveUserSettingRequest
            {
                UserSetting = new UserSettingInformation
                {
                    UserName = username,
                    Name = suggestedFileName,
                    Value = string.Empty
                }
            });
        });
        return true;
    }
}
```

This is just one example (which uses CODE Framework services to save the actual state to a server), and your implementation may likely look different, but the idea is always the same: A settings handler needs to be able to save, load, and clear settings by simply saving away the value string (JSON).

Here is how this handler is registered to handle all user settings:

```cs
SettingsManager.RegisterSettingsHandler(new MyUserSettingsHandler(), SettingScope.User);
```

The same approach can also be taken for workstation and workstation&user settings. For instance, it is possible to create a handler that saves the state in an encrypted version to provide better security.
