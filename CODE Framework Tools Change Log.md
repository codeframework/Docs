# CODE Framework Tools Change Log

_Note: CODE Framework Tools can be installed through the Visual Studio Extension Manager or directly from the [Visual Studio Gallery](http://visualstudiogallery.msdn.microsoft.com/91b58827-9720-487a-930c-c19a76c0b853)_

## 4.0.61203.0

* Updated some incorrect spelling and similar cleanups
* Default View (and Document view) templates now include the cf namespace
* The default template for the login view now includes a caps-lock warning
* All color dictionaries in all themes have been updated in the templates to include all the latest settings
* The new-project template now includes support for the Universe theme.
* Templates for the Universe theme now include a second set of font sizes to support a more delicate version of Universe.
* The new-view wizard now includes a complete set of standard layout templates. The UI has been updated to show a preview of the template and it includes an explanation of the template.
* The new-view wizard now uses the StandardLayout property, rather than the Style property to define the layout.
* The new-view wizard now includes a better way to set icons. The UI shows a preview of the icon.
* The new-view wizard now uses the StandardIcon property, rather than the brush resource property to define the icon.

## 4.0.40408.0
* Added support for the new Wildcat theme in WPF projects
* Default .NET framework target version is now 4.5.1 (Note: This does NOT change the .NET Framework version required by CODE Framework as a minimum, which is still 4.0).
* Whenever a project templates is used in VS2012 or VS2010, the version is automatically changed to older targets. (Note: This will trigger a dialog (confirm framework version change there) and potentially a confusing warning/error message in Visual Studio which can be ignored).

## 4.0.31203.0
* Added support for Visual Studio 2013 (this was actually supported before, but some people seemed to have trouble finding the extension in the Extension Manager dialog, so we re-deployed)
* Updated to the latest assemblies

## 4.0.30618.0
* Added support for the Geek theme
* Added support for the Vapor theme

## 4.0.30408.0
* Updated some of the visual to match the latest version of CODE Framework
* Fixed an issue with curly bracket positions related to service interface parsing for auto-implemented interfaces.
* Fixed an issue with service parsing for both the development service host and runtime service host wizards.
* Added an application icon to the Workplace theme template.
* Enabled support for the VS12 beta (the next version after VS2012).

## 4.0.30219.0
* The service contract project now defines a Namespace tag on the ServiceContract attribute.
* There now is a generic service template item that can be added to service existing contract projects.
* The service implementation wizard now supports Namespace tag generation for service implementations.

## 1/23/2013
* Added support for the new Workplace them for WPF projects
* Minor changes in the default resource dictionaries generated with new WPF projects.

## 1/7/2013
* The add-view dialog now supports creating additional resource dictionaries (see also [WPF Resource Dictionary Loading](WPF-Resource-Dictionary-Loading))
* The default login.xaml view now uses CODE.Framework-Icon-Login as the display icon (see also [Standard Icon Resources](Standard-Icon-Resources))
* WPF Document features are now included by default with new WPF projects
* There now is a new template for document views
* Fixed a problem with Visual Studio 2012 and Windows Phone 8 SDK installed where the templates incorrectly used a Windows Phone 8 class library template (which on some machines is the default class library template) for the _ExternalComponents project.
* Various enhancements to all project and item template engines have been made to support deeper project hierarchies and exotic project types. (The combination of some of these things caused null reference exceptions before).

## 7/11/2012
* Automatic component download from CodePlex has been re-enabled (thanks to Microsoft for some help and making an exception for us!)
* WPF MVVM/MVC templates now put more commented out color settings into the Metro theme color dictionary for easy access/changing.
* The DebugVisualizer in default WPF project templates is now checking for the DEBUG setting and is only displayed for debug builds.

## 5/23/2012
* Support for Visual Studio 11 (beta) added
* Project templates now support solution folders.
* The “add references to CODE Framework” dialog now does a lot more checking to make sure the things people pick are in fact right.
* The same dialog now disables the direct “download from CodePlex” option. This is due to changes in the CodePlex web site and the lack of support for this feature. Hopefully this is only temporary. Otherwise, we will replace it with a similar feature.
* Quite a few tweaks to the templates to take advantage of many of the latest CODE Framework features (such as View.WidthEx).
* Some fixes to templates, such as missing DataContract attributes and more.
 
## Prior Versions
For prior versions, change logs are not available on Codeplex. If you have any specific questions about prior changes, please contact us.