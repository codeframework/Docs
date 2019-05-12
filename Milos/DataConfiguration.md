# Configuration for Business Objects and Data Access

Milos (and CODE Framework) provides a native system that allows developers to query application settings. This is similar to using the ```ConfigurationSettings``` class provided by the .NET Framework, but the Milos Configuration System is much more powerful as it fully supports everything in the .NET configuration system, but also a lot more. For this reason, developers should always use the Milos system.

There are a number of ways to set configuration options for applications using this system. The system supports things such as XML configuration files, SQL Server configuration, and much more. (See Spec_ApplicationConfiguration for details on the configuration system). For simplicity's sake, we will assume that you are using an app.config (windows applications) or web.config (web application) file. Just be aware that the system supports a lot of other options as well. Here is the example configuration file we are using here:

```xml
<xml version="1.0" encoding="utf-8" ?>
<configuration>
   <appSettings>
      <add key="database:UserName" value="devuser"/>
      <add key="database:Password" value="devuser"/>
      <add key="database:Server" value="(local)"/>
      <add key="database:Catalog" value="Northwind"/>
      <add key="DataServices" value="SqlDataService"/>
   </appSettings>
</configuration>
```

> Note: that all sample code in this How-To will assume that you have added using Milos.Configuration to the program file.

To query the one of these settings such as the ```DataServices``` setting in the following fashion:

```cs
string result = ConfigurationSettings.Settings["DataServices"];
```

In case the setting doesn't exist, an exception of type ```SettingNotSupportedException``` will be thrown. For this reason, one should first check whether that setting is available/supported. The ```IsSettingSupported()``` method does the trick:

```cs
if (ConfigurationSettings.Settings.IsSettingSupported("DataServices"))
   string result = ConfigurationSettings.Settings["DataServices"];
```

You can also update configuration settings:

```cs
ConfigurationSettings.Settings["DataServices"] = "OracleDataService";
```

This will detirmine where the setting was defined (app.config file in this example) and attempt to update the definition file (an individual configuration file - such as the app.config file - is referred to as a "configuration source"). Notice however that the source might be read-only (such as the native ```AppSettings``` section from the native config file). In such case, attempting to change a setting's value will throw an exception of type ```SettingReadOnlyException```.
 
## Overriding Settings Temporarily

It is possible to override the value of a setting temporarily, so that all the application will make use of the overriden value until shut-down (although the setting will not be preserved in the original source... this is just an "in-memory" override):

```cs
// Override the setting on the native file by adding the setting to the Memory source.
ConfigurationSettings.Sources["Memory"].Settings["DataServices"] = "OracleDataService";

// Do whatever has to be done.....

// This returns "OracleDataService"
var dataService = ConfigurationSettings.Settings["DataServices"];

// Remove overriden setting from memory if you do not want to use it anymore.
ConfigurationSettings.Sources["Memory"].Settings["DataServices"] = null;

// This returns "SqlDataService"
var dataService2 = ConfigurationSettings.Settings["DataServices"];
```

In this example, we are writing the new option to a different source (memory). Settings made in memory always override settings from the app.config file (well, unless you manually change that order).

## Getting Direct Access to a Source

The ```ConfigurationSettings``` object can hold any number of configuration sources. When a setting is getting retrieved as in:

```cs
var result = ConfigurationSettings.Settings["DataServices"];
```

The mechanism will look for the first setting it can find based on the order different sources have been added. However, sometimes the developer may want to retrieve a setting from a specific source. The following example shows how to retrieve a setting from the app.config file and the app.config file only (even if other sources - such as memory - have the same setting):

```cs
var result = ConfigurationSettings.Sources["DotNetConfigurationFile"].Settings["DataServices"];
```

Accessing a config source that doesn't exist on the collection used to throw an exception. That behavior has been changed, and now instead of throwing an exception, just a "null" gets returned, and it's up to the developer to decide on what to do when the source hasn't been found (like instantiating the source and adding it to the collection).

## Supporting Custom Config Files

It is possible to work with specialized custom config files, using the same sort of interface for it. For instance, consider there's a UIConfig.xml file that looks like so:

```cs
<xml version="1.0" encoding="utf-8"?>
<specialsettings>
   <backgroundcolor>Brown</backgroundcolor>
   <foregroundcolor>Yellow</foregroundcolor>
</specialsettings>
```

The content of this file looks very different than the native config file. In order to handle it, a new configuration class must be created, and this class must implement the ```IConfigurationSource``` interface (defined under EPS.Configuration). There's a ```ConfigurationSource``` abstract class that implements that interface, and could be used as well, since it provides some default behavior. The new class could look like the one below:

```cs
using System;
using System.IO;
using System.Xml;

namespace EPS.Configuration
{
    public class SpecialConfiguration : Milos.ConfigurationSource
    {
        FileInfo settingsFile;

        string file = @"C:\Path\UIConfig.xml";

        public SpecialConfiguration()
        {
            Read();
        }

        public override string FriendlyName
        {
            get => "SpecialConfiguration";
        }

        public override void Read()
        {
            settingsFile = new FileInfo(file);

            Settings.Clear();

            var dom = new XmlDocument?();
            dom.Load(this.file);

            var node = dom.DocumentElement.SelectSingleNode("backgroundcolor");
            if (node != null) 
                Settings.Add("backgroundcolor", node.InnerXml);

            node = dom.DocumentElement.SelectSingleNode("foregroundcolor");
            if (node != null) 
                Settings.Add("foregroundcolor", node.InnerXml);
        }

        public override void Write()
        {
            settingsFile = new FileInfo(this.file);
            var dom = new XmlDocument();
            dom.Load(settingsFile.ToString());

            node = dom.DocumentElement.SelectSingleNode("backgroundcolor");
            if (node != null) 
                node.InnerXml = Settings["backgroundcolor"].ToString();

            node = dom.DocumentElement.SelectSingleNode("foregroundcolor");
            if (node != null) 
                node.InnerXml = Settings["foregroundcolor"].ToString();

            dom.Save(this.fiSettingsFile.ToString());
        }
    }
}
```

The most important piece of the class is the Read and the Write methods. It is in those methods that we manually retrieve and store settings into the custom XML file. The Read method basically adds settings from the XML file to a collection (as in ```Settings.Add("backgroundcolor", node.InnerXml)```), whereas the Write method reads the values from the collection, updates the XML document, and save it to disk.

Both the Write and Read methods are inherited from the abstract class, and those two methods are part of other methods that come from the abstract class and must be overridden by this derived class.

The new class can be easily used. It just has to be added to the Sources list, and settings can be retrieved in the same fashion we've already seen:

```cs
ConfigurationSettings.Sources.Add(new SpecialConfiguration());
var backcolor = ConfigurationSettings.Settings["backgroundcolor"];
```

## Creating and Using Strongly-Typed Settings

In order to create and use strongly-typed settings, the configuration source must provide the typed-interface. That can be done by exposing the settings through properties on the object. A good way of doing it is by first creating an Interface that determines what are the settings and their types. Supposing we want to expose the "special configuration" settings created previously, but instead of exposing them as string we want to expose them as Color objects instead, the interface could be defined like so:

```cs
public interface ISpecialConfiguration
{
    Color BackColor { get; set; }
    Color ForeColor { get; set; }
}
```

> Note that instead of exposing one of the settings as ```backgroundcolor``` , we expose it with a better casing and name, as in ```BackColor```.

The ```SpecialConfiguration``` class created previously has to implement the ```ISpecialConfiguration``` interface. The class definition has to be changed to:

```cs
public class SpecialConfiguration : ConfigurationSource, ISpecialConfiguration
```

And the implementation for the interface members could look like so:

```cs
public Color BackColor
{
    get => Color.FromName(Settings["backgroundcolor"]);
    set => Settings["backgroundcolor"] = value.Name;
}

public Color ForeColor
{
    get => Color.FromName(Settings["foregroundcolor"]);
    set => Settings["foregroundcolor"] = value.Name;
}
```

In this implementation, the get method of each property just creates a new ```Color``` object based on the value of the settings (which comes as a string). The set method takes the color's name off of the ```Color``` object and put that value back to the Settings collection.

Now the usage of those settings can happen like so:

```cs
ConfigurationSettings.Sources.Add(new DotNetConfigurationFile());
ConfigurationSettings.Sources.Add(new SpecialConfiguration());

var config = ConfigurationSettings.Sources["SpecialConfiguration"] as ISpecialConfiguration;
if (config == null)
{
    // "It failed retrieving the SpecialConfiguration? source. It shouldn't."
}
else
{
    // Read strongly-typed settings.
    Color backcolor = config.BackColor;
    Color forecolor = config.ForeColor;

    // Values can be changed...
    config.ForeColor = Color.FromName("Blue");

    // ...and can get Saved to disk.
    ConfigurationSettings.Sources["SpecialConfiguration"].Write();
}
```
