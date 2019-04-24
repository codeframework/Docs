# Configuration System

CODE Framework provides a native system that allows developers to query application settings. This is similar to using the ConfigurationSettings class provided by the .NET Framework, but the CODE Framework Configuration System is much more powerful as it fully supports everything in the .NET configuration system, but also a lot more. For this reason, developers should always use the CODE Framework system.

In order to use the CODE Framework Configuration System, make sure to add a reference to the CODE.Framework.Core.dll assembly, which should be part of every CODE Framework application anyway.

There are a number of ways to set configuration options for applications using this system. The system supports things such as XML configuration files, SQL Server configuration, and much more. For simplicity's sake, we will assume that you are using an app.config (windows applications) or web.config (web application) file. Just be aware that the system supports a lot of other options as well. Here is the example configuration file we are using here:

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

To query the one of these settings such as the "DataServices" setting in the following fashion:

```c#
string result = ConfigurationSettings.Settings["DataServices"];
```

In case the setting doesn't exist, an exception of type SettingNotSupportedException will be thrown. For this reason, one should first check whether that setting is available/supported. The IsSettingSupported() method does the trick:

```c#
if (ConfigurationSettings.Settings.IsSettingSupported("DataServices"))
    var result = ConfigurationSettings.Settings["DataServices"];
```

You can also update configuration settings:

```c#
ConfigurationSettings.Settings["DataServices"] = "OracleDataService";
```

This will determine where the setting was defined (app.config file in this example) and attempt to update the definition file (an individual configuration file - such as the app.config file - is referred to as a "configuration source"). Notice however that the source might be read-only (such as the native AppSettings section from the native config file). In such case, attempting to change a setting's value will throw an exception of type SettingReadOnlyException.

## Overriding Settings Temporarily

It is possible to override the value of a setting temporarily, so that all the application will make use of the overridden value until shut-down (although the setting will not be preserved in the original source... this is just an "in-memory" override):

```c#
// Override the setting on the native file by adding the setting to the Memory source. ConfigurationSettings.Sources["Memory"].Settings["DataServices"] = "OracleDataService";
// Do whatever has to be done..... 

// This returns "OracleDataService" 
var dataService = ConfigurationSettings.Settings["DataServices"]; 
// Remove overriden setting from memory if you do not want to use it anymore. 
ConfigurationSettings.Sources["Memory"].Settings["DataServices"] = null; 
// This returns "SqlDataService?" 
var dataService2 = ConfigurationSettings.Settings["DataServices"];
```

In this example, we are writing the new option to a different source (memory). Settings made in memory always override settings from the app.config file (well, unless you manually change that order).

## Getting Direct Access to a Source

The ConfigurationSettings object can hold any number of configuration sources. When a setting is getting retrieved as in

```c#
var result = ConfigurationSettings.Settings["DataServices"];
```

The mechanism will look for the first setting it can find based on the order different sources have been added. However, sometimes the developer may want to retrieve a setting from a specific source. The following example shows how to retrieve a setting from the app.config file and the app.config file only (even if other sources - such as memory - have the same setting):

```c#
var result = ConfigurationSettings.Sources["DotNetConfigurationFile"].Settings["DataServices"];
```

Accessing a config source that doesn't exist on the collection used to throw an exception. That behavior has been changed, and now instead of throwing an exception, just a "null" gets returned, and it's up to the developer to decide on what to do when the source hasn't been found (like instantiating the source and adding it to the collection).

## Supporting Custom Config Files/Sources

It is possible to work with specialized custom config files (or even non-file based configuration sources, such as databases), using the same sort of interface for it. As an example, consider a UIConfig.xml file that looks like so:

```xml
<xml version="1.0" encoding="utf-8"?>
<specialsettings>
  <backgroundcolor>Brown</backgroundcolor>
  <foregroundcolor>Yellow</foregroundcolor>
</specialsettings>
```

The content of this file looks very different than the native config file. In order to handle it, a new configuration class must be created, and this class must implement the IConfigurationSource interface. There's a ConfigurationSource abstract class that implements that interface, and could be used as well, since it provides some default behavior. The new class could look like the one below:

```c#
using System;
using System.IO;
using System.Xml; 

namespace MyConfig
{
   public class SpecialConfiguration : ConfigurationSource
   {
      FileInfo fiSettingsFile;
      string file = @"C:\Path\UIConfig.xml";

      public SpecialConfiguration()
      {
         Read();
      }

      public override string FriendlyName
      {
         get { return "SpecialConfiguration"; }
      }

      public override void Read()
      {     
         XmlDocument dom = null;
         XmlNode node = null;

         fiSettingsFile = new FileInfo(file);
         Settings.Clear();
         dom = new XmlDocument();
         dom.Load(this.file);
         node = dom.DocumentElement.SelectSingleNode("backgroundcolor");
       
         if (node != null) 
            Settings.Add("backgroundcolor", node.InnerXml);
         node = dom.DocumentElement.SelectSingleNode("foregroundcolor");
         if (node != null) 
            Settings.Add("foregroundcolor", node.InnerXml);
      }

      public override void Write()
      {
         XmlDocument dom = null;
         XmlNode node = null;

         fiSettingsFile = new FileInfo(file);
         dom = new XmlDocument();
         dom.Load(this.fiSettingsFile.ToString());
         node = dom.DocumentElement.SelectSingleNode("backgroundcolor");
         if (node != null) 
            node.InnerXml = this.Settings["backgroundcolor"].ToString();
         node = dom.DocumentElement.SelectSingleNode("foregroundcolor");
         if (node != null) 
            node.InnerXml = this.Settings["foregroundcolor"].ToString();

         dom.Save(fiSettingsFile.ToString());
      }
   }
}
```

The most important piece of the class is the Read and the Write methods. It is in those methods that we manually retrieve and store settings into the custom XML file. The Read method basically adds settings from the XML file to a collection (as in this.Settings.Add("backgroundcolor", node.InnerXml)), whereas the Write method reads the values from the collection, updates the XML document, and save it to disk.

Both the Write and Read methods are inherited from the abstract class, and those two methods are part of other methods that come from the abstract class and must be overridden by this derived class.

The new class can be easily used. It just has to be added to the Sources list, and settings can be retrieved in the same fashion we've already seen:

```c#
ConfigurationSettings.Sources.Add(new SpecialConfiguration());
string backcolor = ConfigurationSettings.Settings["backgroundcolor"];
```