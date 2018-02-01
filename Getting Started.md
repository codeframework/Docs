# Getting Started

## Installing the Framework Tools

The easiest way to get started with the CODE Framework, is to install the CODE Framework Tools Visual Studio Extension through the Visual Studio Extension Manager:

![](Getting%20Started/Figure1.png)

Once these extensions are installed, new project templates become available in Visual Studio, such as these:

![](Getting%20Started/Figure2.png)

Note that this does not install the actual framework, but only the tools. However, if you use a template that requires framework components (such as the CODE Framework WPF MVVM/MVC template), the tools detect the dependency and show a dialog like this:

![](Getting%20Started/Figure3.png)

Using this dialog, CODE Framework assemblies can be added to the current project, either by adding files stored locally in a folder or ZIP file (after having downloaded the desired version manually from CodePlex), or you can let the tool retrieve the latest version from CodePlex for you and add the assemblies directly.

Note that CODE Framework assemblies are generally not installed in the GAC (although you could do so yourself if you prefer this option) but they are put into a dummy location in the local solution. This has the advantage that different solutions built with the CODE Framework can use different versions without colliding with each other, and allowing the developer full control over when and to what version a certain solution is to be upgraded to. It also has the advantage that all external assemblies can be checked into source control, allowing other developers to simply join the project through source control without relying on additional installs. This also allows them to always retrieve the correct version of these dependencies automatically. This generally works very well for typical business applications.

For more information and samples, please see the article links at the top of this page.

## Next Steps

CODE Framework covers a wide range of features. This includes handling of services (from server-side service creation and hosting, to client-side service calling) as well as a rich framework for client-side development.

To understand services, see [Understanding Services](Understanding-Services). To learn more about the WPF UI framework, see topics such as [Understanding Layout](Understanding-Layout).

## Training

We also offer training related to CODE Framework (as well as other topics). Classes range from free events (online and in-person) to extended premium training classes, and even personalized on-site training. For more information, see http://codemag.com/training. 

## Articles

We also recommend the following articles, which are either specifically about CODE Framework, or related topics important for CODE Framework development.

### CODE Framework Articles in CODE Magazine

*   [What’s New in CODE Framework in 2016](http://localhost:55759/article/1609111)
*   [CODE Framework Cheat Sheet](http://localhost:55759/article/9995011)
*   [What’s New in CODE Framework in 2014](http://localhost:55759/article/1407091)
*   [CODE Framework: WPF Standard Themes](http://localhost:55759/article/1309041)
*   [Building a CODE Framework Service and Consuming It on an iPhone Application](http://localhost:55759/article/1305071)
*   [CODE Framework: Documents, Printing, and Interactive Document UIs](http://localhost:55759/article/1304041)
*   [CODE Framework: Accelerating Development with Standard Views and Standard View-Models](http://localhost:55759/article/1301041)
*   [CODE Framework: Creating Application Themes](http://localhost:55759/article/1211091)
*   [CODE Framework: Testing](http://localhost:55759/article/1210081)
*   [CODE Framework: Building Productive, Powerful, and Reusable WPF (XAML) UIs with the CODE Framework](http://localhost:55759/article/1206101)
*   [CODE Framework: Building Services and SOA Business Layers](http://localhost:55759/article/1203061)
*   [CODE Framework: Writing MVVM/MVC WPF Applications](http://localhost:55759/article/1201061)
   
### Related CODE Magazine Articles

*   [XAML Anti-Patterns: Layout SNAFUs](http://localhost:55759/article/1509091)
*   [XAML Anti-Patterns: Code-Behind](http://localhost:55759/article/1505101)
*   [XAML Anti-Patterns: Resource Overuse](http://localhost:55759/article/1501091)
*   [XAML Anti-Patterns: Virtualization](http://localhost:55759/article/1407081)
*   [XAML Magic: Attached Properties](http://localhost:55759/article/1405061)
*   [Editorial](http://localhost:55759/article/1403011)
*   [Simplest Thing Possible: Introduction to Knockout.js](http://localhost:55759/article/1304061)
*   [Converting XAML-Based Applications to Windows 8](http://localhost:55759/article/1208051)
*   [New at CODE Magazine!](http://localhost:55759/article/1206031)
*   [Building an Android Application to Search Twitter](http://localhost:55759/article/1109051)
*   [WPF and Silverlight Super-Productivity: ListBoxes](http://localhost:55759/article/112091)
*   [Super Productivity: Using WPF and Silverlight’s Automatic Layout Features in Business Applications](http://localhost:55759/article/1011071)
