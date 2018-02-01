# Splitting WPF Apps into Muiltiple Projects

_Excerpt from an internal email at CODE/EPS:_

We just completed a new CODE Framework feature. It allows splitting WPF apps into multiple projects. This isn’t really as such a new feature in that this has always been possible, but it was a pain, so nobody did it. Using the new feature, it has now become trivial. 

So the main idea is this: Rather than having everything that goes with your client app in a single project, you can create secondary projects that have internally the same structure, but compile to DLLs rather than EXEs. For instance, you may have a business application that deals with invoices and customers, and you have one project each for the customer and the invoice UIs, rather than having it all in one big project. 

These “secondary” projects need to be structurally similar to standard CODE Framework WPF applications. In particular, they should have a Controller and an Views folder (and probably a Models folder). They will NOT have a Themes folder, since the theme is driven by the main EXE only! They also do not have an app.config or app.xaml file, since that is also exclusive to the main startup project. You can get such a project either by creating a CODE Framework WPF project and then remove everything unneeded and then setting the compiler output to produce a DLL rather than an EXE. You can also create such a project by adding a standard WPF class library project and then adding CODE Framework DLL references and folders as needed. 

Anyway: Once you have such a project, you can use it from a main startup project by first adding a standard .NET assembly reference. Then, you also need to let CODE Framework know that there now is a new DLL that may have elements that are loaded dynamically (such as controllers and views). This is done by calling ApplicationEx.RegisterSecondaryAssembly(…); Usually, this is done as the first line of the startup code in the application object (app.xaml.cs). Here is an example: 

```cs
public partial class App
{
    public App()
    {
        Startup += ApplicationStartup;
    }
    
    private void ApplicationStartup(object sender, StartupEventArgs e)
    {
        // This registers the components in the secondary assembly
        RegisterSecondaryAssembly("SecondaryComponents");
        
        Controller.Action("Home", "Start");
        Controller.Action("User", "Login");
    }
}
```

And that’s it! Everything now works as always, except you can put your stuff into multiple projects. 

Here’s an example setup for a CODE Framework WPF solution with two projects: 

![](Splitting%20WPF%20Apps%20into%20Multiple%20Projects/Splitting%20WPF%20Apps%20into%20Multiple%20Projects_clip_image004_2.jpg)
