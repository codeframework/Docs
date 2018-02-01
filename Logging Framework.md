# Logging Framework

Logging is the process of notifying an entity of a certain event, typically (but not always) with the event being stored away to preserve a record of it. CODE Framework provides an extensive logging framework that provides a central logging mediator, which in turn passes logged events on to all kinds of registered individual logger objects, which can handle the provided events as they see fit. CODE Framework provides a number of loggers out of the box (such as logging to file, Windows event log, email, the console,...). It is also possible to create custom loggers easily.

## Publishing an Event to be Logged

The simplest way of logging an event is to call the logging mediator's log method with a simple string parameter.

```cs
CODE.Framework.Core.Utilities.LoggingMediator.Log("Application started."); 
```

This passes the string on to all registered loggers (see below).

Note: It is recommended to use a using statement in your code so you do not always have to type out the complete namespace name.

It is also possible to provide more detailed information, in particular the type of event:

```cs
LoggingMediator.Log("Application started.",LogEventType.Information); 
```

There are various types of events identified by the LogEventType enumeration. This enumeration is a bit-flag enumeration, so all the different types can be combined as in this example:

```cs
LoggingMediator.Log("Something happened.", LogEventType.Exception | LogEventType.Critical); 
```

In addition to logging strings, it is possible to log entire objects:

```cs
LoggingMediator.Log(customer, LogEventType.Information);
```

In this case, it is up to the registered loggers to make use of the object as they see fit. Many of the default loggers however simply perform an [object].ToString().

Another overload of the logging mediator makes it possible to log exception information easily:

```cs
try
{
}
catch (Exception ex)
{
    LoggingMediator.Log(ex, LogEventType.Exception);
} 
```

In this case, the logging mediator uses the ExceptionHelper class to retrieve a detailed text description of the exception, which is then logged as text.

A related overload allows logging manual text in combination with exception information:

```cs
LoggingMediator.Log("An error has occurred!", ex, LogEventType.Exception);
```

The additional string parameter is combined with the exception text for logging.

## Logger Objects

By default, the logging mediator doesn't perform any action other than passing events on to registered loggers, of which there aren't any unless they are registered with the logger. Here is a simple example of registering a logger with the logging mediator:

```cs
LoggingMediator.ClearLoggers();
LoggingMediator.AddLogger(new ConsoleLogger());
LoggingMediator.Log("Test"); 
```

Once a logger is registered, it will generally receive all logged events.

Note that it is possible to register any number of loggers:

```cs
LoggingMediator.ClearLoggers();
LoggingMediator.AddLogger(new ConsoleLogger());
LoggingMediator.AddLogger(new MessageBoxLogger());
LoggingMediator.Log("Test"); 
```

Loggers always receive events in the order the loggers were registered. In this example, the console logger is triggered before the message box logger.

## Event Filtering

If the logger is to be used for certain events only, then one can set a type filter:

```cs
LoggingMediator.ClearLoggers();
ConsoleLogger logger = new ConsoleLogger();
logger.TypeFilter = LogEventType.Exception;
LoggingMediator.AddLogger(logger);
LoggingMediator.Log("Test"); // Not logged (considered type 'Information')
LoggingMediator.Log("Test 2", LogEventType.Exception); // Logged 
```

Filters are also bit-flags and can thus be combined:

```cs
LoggingMediator.ClearLoggers();
ConsoleLogger logger = new ConsoleLogger();
logger.TypeFilter = LogEventType.Exception | LogEventType.Error;
LoggingMediator.AddLogger(logger);
LoggingMediator.Log("Test"); // Not logged (considered type 'Information')
LoggingMediator.Log("Test 2", LogEventType.Exception); // Logged
LoggingMediator.Log("Test 3", LogEventType.Error); // Logged
LoggingMediator.Log("Test 4", LogEventType.Exception | LogEventType.Error); // Logged
LoggingMediator.Log("Test 5", LogEventType.Critical | LogEventType.Error); // Logged
LoggingMediator.Log("Test 6", LogEventType.Critical | LogEventType.Information); // Not logged 
```

Note that the type filter can be set for each individual logger, so it is possible to have different loggers configured that react to different events.

## Clearing Loggers

It is possible to re-configure loggers during runtime. For this purpose it is often convenient to clear the list of all previously registered loggers, which can be done like so:

```cs
LoggingMediator.ClearLoggers(); 
```

## Provided Standard Loggers

CODE Framework ships with a number of standard logger objects that cover a wide range of logging needs.

### The Console Logger

The console logger object logs events to the console. It is very similar in concept to issuing a Console.WriteLine() call. Console loggers are set up like so:

```cs
LoggingMediator.AddLogger(new ConsoleLogger());
LoggingMediator.Log("Test"); 
```

### Multi File Logger

Multi file loggers log events to multiple files (each event gets its own log file). This logger must be configured with a path the log files are to be written to. This can be done by specifying the path as a string, or by providing a special folder:

```cs
LoggingMediator.AddLogger(new MultiFileLogger(@"c:\logs\"));
LoggingMediator.Log("Test");
LoggingMediator.AddLogger(new MultiFileLogger(SpecialFolder.ApplicationData));
LoggingMediator.Log("Test"); 
```

It is possible to omit the folder information. In that case, SpecialFolder.ApplicationData is the default.

By default, all log files have a ".log" extension. This can be changed:

```cs
var logger = new MultiFileLogger(@"c:\logs\");
logger.Extension = "txt";
LoggingMediator.AddLogger(logger);
LoggingMediator.Log("Test"); 
```

The name for each file is a Guid converted to a string. To change the way file names are created, you must subclass the logger and override the GetNextFileName() method:

```cs
public class MyMultiFileLogger : MultiFileLogger
{
    public MyMultiFileLogger(string path) : base(path) { }
    public override string GetNextFileName()
    {
        return System.Environment.TickCount.ToString() + "." + this.Extension;
    }
}

var logger = new MyMultiFileLogger(@"c:\logs\");
logger.Extension = "txt";
LoggingMediator.AddLogger(logger);
LoggingMediator.Log("Test");
```

Note that this method must return the complete file name including the extension. Note also that it is important to create unique file names to avoid overwriting existing log files.

### Multi XML File Logger

The multi-file XML logger object is similar in concept to the regular multi-file logger. However, it stores logged information in XML files, with an XML structure. Consider this example:

```cs
LoggingMediator.AddLogger(new MultiXmlFileLogger(@"c:\logs\"));
LoggingMediator.Log("Test"); 
```

The resulting XML file has the following structure:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<log>
  <event type="Information" timeStamp="2/4/2008 3:02pm">Test</event>
</log> 
```

Note that the element and attribute names can be modified:

```cs
var logger = new MultiXmlFileLogger(@"c:\logs\")
logger.XmlRootNode = "root";
logger.XmlEventNode = "record";
logger.XmlEventTypeAttribute = "significance";
logger.XmlEventTimeStampAttribute = "time";
LoggingMediator.AddLogger(logger);
LoggingMediator.Log("Test"); 
```

This produces the following result

```xml
<?xml version="1.0" encoding="utf-8" ?>
<root>
  <record significance="Information" time="2/4/2008 3:02pm">Test</record>
</root> 
```

Note that it is also possible to pass actual XML to the logger as the text string. Whenever well-formed XML is passed to the logger, the XML structure will not be changed and instead, the provided XML will be serialized to disk as is:

```cs
LoggingMediator.Log("Hello World!"); 
```

Result:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<log>Hello World!</log> 
```

The XML multi-file logger object is a subclass of the regular multi-file logger class. Therefore, overriding file extension or creating a new file name algorithm are identical (see above).

Note: The actual value logged is properly encoded to make sure the XML file can accommodate all data passed to the logger.

### Single File Logger

The single file logger object logs all events into a single text file. It requires a file name and a path when instantiated:

```cs
LoggingMediator.AddLogger(new SingleFileLogger(@"c:\logs\", "MyLogFile.log"));
LoggingMediator.Log("Test"); 
```

There also is a constructor overload without a file name. In that case, the default file name is "Log.log". It is even possible to not provide a folder name. In that case, SpecialFolder.ApplicationData is used (whatever that translates to on each machine).

All events are logged by adding them to the bottom of the file. The file grows unrestricted and can thus get very large.

### Single XML File Logger

The single XML file logger is very similar to the regular single file logger, except that all information is logged into a single, structured XML file. Just like the regular single file logger, the XML single file logger can be configured with a folder and file name. Both are optional. The default file name is Log.xml. The default folder is SpecialFolder.ApplicationData :

```cs
LoggingMediator.AddLogger(new SingleXmlFileLogger(@"c:\logs\", "MyLog.xml"));
LoggingMediator.Log("Test");
LoggingMediator.Log("Some Exception", LogEventType.Exception | LogEventType.Critical); 
```

This creates the following result:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<log>
  <event type="Information" timeStamp="2/4/2008 3:02pm">Test</event>
  <event type="Exception, Critical" timeStamp="2/4/2008 3:03pm">Some Exception</event>
</log> 
```

New events are always added at the bottom of the list. By default, the file grows unrestricted. However, it is possible to limit the list to a maximum number of entries. In that case, the oldest entries will be removed from the list:

```cs
var logger= new SingleXmlFileLogger(@"c:\logs\", "MyLog.xml")
logger.MaximumEntries = 100;
LoggingMediator.AddLogger(logger); 
```

If MaximumEntries is set to -1, the list can grow unrestricted.

Just like with the XML multi-file logger, it is possible to change the names of the elements and attributes used by the logger:

```cs
var logger = new MultiXmlFileLogger(@"c:\logs\")
logger.XmlRootNode = "root";
logger.XmlEventNode = "record";
logger.XmlEventTypeAttribute = "significance";
logger.XmlEventTimeStampAttribute = "time";
LoggingMediator.AddLogger(logger);
LoggingMediator.Log("Test");
LoggingMediator.Log("Some Exception", LogEventType.Exception | LogEventType.Critical); 
```

The result is as follows:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<root>
  <record significance="Information" time="2/4/2008 3:02pm">Test</record>
  <record significance="Exception, Critical" time="2/4/2008 3:03pm">Some Exception</record>
</root> 
```

Note that if the file already exists, the root node name needs to match the name configured in the logger, otherwise the XML file will be overwritten, since CODE Framework considers the file structurally incompatible. However, it is possible to have child nodes of different names. The following is a valid XML log file (for the default logger configuration):

```xml
<?xml version="1.0" encoding="utf-8" ?>
<log>
  <event type="Information" timeStamp="2/4/2008 3:02pm">Test</event>
  <test>Some other node</test>
  <event type="Exception, Critical" timeStamp="2/4/2008 3:03pm">Some Exception</event>
</log> 
```

In this example, the test node is simply ignored.

Note: The actual value logged is properly encoded to make sure the XML file can accommodate all data passed to the logger.

### Event Log Logger

This logger logs event information to the Windows Event Log. The log can reside on the local machine or on a remote machine.

This logger is set up like this:

```cs
var logger = new EventLogger("Log Name")
logger.Source = "Application Name";
LoggingMediator.AddLogger(logger);
LoggingMediator.Log("Test"); 
```

This creates a logger that logs to the windows event log. The name of the log in this example is "Log Name". If such a log does not exist on the local machine, it will be created.

It is also possible to log to a remote machine:

```cs
var logger = new EventLogger("Log Name", "ComputerName")
logger.Source = "Application Name";
LoggingMediator.AddLogger(logger);
LoggingMediator.Log("Test"); 
```

By default however, the log is created on the local machine. If you want to explicitly create a log on the local machine without having to know the current machine name, set the name to ".".

The event log logger logs the provided event as is, meaning that the provided text is simply written to the log without applying any changes. The Windows event log also assigns event types to each entry. These types are different from the CODE Framework event types, but they are similar. They are also automatically mapped:

```
CODE Framework Event Type Windows Event Type 
LogEventType.Critical EventLogEntryType.Error 
LogEventType.Error EventLogEntryType.FailureAudit 
LogEventType.Exception EventLogEntryType.FailureAudit 
LogEventType.Warning EventLogEntryType.Warning 
LogEventType.Success EventLogEntryType.SuccessAudit 
All other: EventLogEntryType.Information 
```
 
### Email Logger

The email logger sends all events as emails via an SMTP server. It requires a file name and a path when instantiated:

```cs
var recipients = EmailLogger.GetRecipientsFromConfigFile();
if (recipients.Count > 0)
{
    var emailLogger = new EmailLogger("My App", "support@mycompany.com", recipients, "My Comapany's App");
    LoggingMediator.AddLogger(emailLogger);
}
```

Sample .config settings:

```xml
<add key="DefaultMailServer" value="smtp.mycompany.com"/>
<add key="EmailLoggerRecipients" value="support@mycompany.com|Support Desk;fred@mycompany.com|Fred Flintstone"/>
```
 
Emails are sent on a background thread using the ThreadPool.

## Creating Custom Loggers

For many scenarios it is beneficial to add custom loggers. This can be done by deriving from the abstract Logger class. For more control, it is also possible to implement the  ILogger interface.

### Deriving from Logger

The abstract Logger class provides a good starting point for most needs. This class allows developers to quickly create custom loggers without having to implement the entire ILogger interface. The following example shows a simple logger that logs to a message box:

```cs
public class MyLogger : CODE.Framework.Core.Utilities.Logger
{
    public override void Log(string event, LogEventType type)
    {
        MessageBox.Show("Event occured: " + event);
    }
} 
```

This class can then be registered like all other event loggers:

```cs
LoggingMediator.AddLogger(new MyLogger()); 
```

What's nice about using the abstract Logger class is that it provides basical functionality such as the TypeFilter property.

### Special Handling of Exception Logging

The abstract Logger class implements the ILogger interface (see below) as well as the extended IExceptionLogger interface, which provides two special methods for logging exception information. This is an optional interface in the logging system, which provides greater control over how exception information is logged. (If a logger doesn’t implement this interface, then exception information will just be turned into text and logged as regular text). 

The abstract Logger class implements both methods supported by the IExceptionLogger interface. Both are implemented as virtual, so they can be overridden. Furthermore, the default implementation uses a GetSerialziedExceptionText() method, which turns a logged exception into its text representation, which is what is logged by default. This method is also virtual, and can thus be overridden in subclasses.

Common scenarios for changing exception logging behavior is to override the Log() methods and thus replacing the default behavior, which provides developers with full control over how exceptions are logged. If all that is needed on the other hand is a change in how the exception information is serialized as text (perhaps because the developer wishes to log different information), then one can simply override the GetSerializedExceptionText() method.

### Implementing the ILogger Interface

For those developers who want more control, it is also possible to implement the ILogger interface:

```cs
public class MyLogger : ILogger
{
    public MyLogger()
    {
        TypeFilter = LogEventType.Undefined;
    }

    public void Log(string event, LogEventType type)
    {
        MessageBox.Show("Event occured: " + event);
    }

    public void Log(object event, LogEventType type)
    {
        this.Log(event.ToString(), type);
    }

    public LogEventType TypeFilter { get; set; }
} 
```

Note: This is very similar to the implementation of the Logger abstract class, except that the Logger class defined all members as virtual so they can be overridden in subclasses.
