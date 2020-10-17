# CODE Framework (.NET) Change Log

## Work in Progress / Internal Build

* There now is a new `SettingsManager.ResolveSettingId` delegate that can be used to fine-tune the ID (and by extension things like file names) used to serialize various settings.
* `WindowEx` improvements
  * `WindowEx.AutoSaveWindowPosition` as been improved to not compete with `WindowEx.WindowsStartupLocationStylable`.
  * `WindowEx.HasDropShadow` has been improved to cause less wash-out-glass effect on some graphics cards (although the problem to some extent still exists and is due to a Windows problem).
  * `WindowEx` now has a `HandleCustomMinMax` property. If set to false, it turns of custom sizing of borderless windows (false = maximized borderless windows overlap the taskbar)
* Messageboxes (and other top level dialogs) now associate with active top level windows, so in scenarios with multiple open top-level windows, the window that launched the message still stays on top.
* Service improvements:
  * Supporting a new `ServiceClient:LogCommunicationErrors` configuration setting (and `LogCommunicationErrors` property on the `ServiceClient` class) to optionally log additional information on failed REST calls.
  * Updated some of the service infrastructure to feature identical signatures as the new .NET Core version.
  * The Development Test Host project type now supports services built for .NET Standard 2.0. 
  * The development test host class now has a new `EnableDetailedServiceFaultInformation()` method, which enables default fault information being passed from the server, which is very useful during development. (See also: [Using the Service Test Harness](//Using-the-Service-Test-HostHarness)
  * `ServiceClient.CallRest()` now uses our own branch of JSON.NET to deserialize the result. Also, it is now possible to pass in a function that performs the deserialization instead of the default behavior.
* Controller actions now support optional parameters as well as parameters based on generics.
* Listbox improvements:
  * Listbox grid headers can now sort on filter fields with complex binding paths.
  * Improved multi-column list templates to better support complex binding operations and improve performance.
* There now are standard layouts and templates for both vertical and horizontal multi-panels (with and without headers)
    * All standard themes support these new default templates
* Ribbons can now be used to display only the actions of a selected view, without requiring a main model with actions anymore.
* Fixed a bug with the Ribbon in the Workplace Theme not always showing all actions when the view Title is empty when the view launches.
* Fixed a problem with `LengthToGridLengthConverter` that sparked an exception when the length was less than 0. (This caused a lot of binding warnings to be triggered when using multi-column lists in certain scenarios)
* Improved `ObjectHelper.GetPropertyValue<T>()` to better support dynamic objects
* Added a ribbon panel that can be used like a toolbar (without multiple ribbon pages or a parent ribbon control, for that matter).
* Hamburger menu items in the Universe theme now have a special disabled foreground color.
* Fixed an issue in the Workplace theme that caused notifications to not always display correctly.
* `AsyncWorker.Execute()` now accepts an optional `viewAction` parameter. If a view-action is passed to the async worker, the action will become disabled (`CanExecute() == false`) while the operation is going on. (Note: This also works properly if the same action is passed to multiple different worker processes. It will be disabled until the last one finishes processing.

## 4.1.930.0

* All the default view-actions (like `CloseCurrentViewAction`, or `SwitchThemeViewAction`, and so on...) now have more constructor parameters to make it easier and more consistent to set various options.
* Input mask enhancements (See also: [TextBox Input Masks](TextBox%20Input%20Masks))
	* Input masks for textboxes now support negative values (optionally) on decimal and currency formats. 
	* A new % input mask is now supported for percentage values.
	* Decimal, currency, and percentage input masks now support special properties to fine-tune the number of digits accepted to the left and right of the decimal point.
* Textboxes have been improved throughout all themes to consistently support watermark and validation features. Some themes required new brush resources and the like to implement this.
* Password boxes have been improved across all themes to consistently support watermark and validation features.
* Universe Theme
	* The size of the progress animation in top-level windows has been fixed. 
	* Header font size in multi-column lists have been increased slightly.
* Multi-column listboxes (see also: [Multi-Column Lists](Multi%20Column%20Lists)) have received a visual overhaul in all themes.
	* Some of the themes had various minor offset problems for different types of cells and columns. This has now been fixed.
	* Some of the themes incorrectly showed a scrollbar when there was at least one column with a *-width. This has been fixed.
	* Textboxes, checkboxes, and comboboxes have received a visual overhaul to make sure they look good as editable controls within lists.
	* Filter textboxes in column headers have received a visual overhaul across all themes.
	* All themes now properly support header alignment options.
	* Column-drag now works in all themes and no matter where in the column the user clicks.
	* Columns with a "checkmark" edit control type now support editing in all themes when edit mode is ReadWriteAll.
	* List headers received a visual overhaul in several themes.
	* Sort order indicators have received a visual tweak in some themes to make sure they are well visible.
	* All themes now support setting cell background colors.
	* Some themes now set a minimum height for rows to avoid very small rows with no spacing in special scenarios.
	* List columns now have additional tooltip properties to provide further control over the appearance and behavior of tooltips, without having to create custom cell data templates.
	* List columns now respect the visibility of a column in the "standard" templates, and react to changes of the visibility.
* Multi-column listboxes have also been upgraded with several new function al features
	* The columns collection now has a `ShowHeader` property, which can be used to toggle headers on and off.
	* Multi-column lists now support footers with features equivalent to headers. (All themes have been updated to support new styles for this feature)
* `ListColumn` objects now support `CellControlCreated` events, which fire whenever a control instance within a cell is created. It can be used to manipulate the newly created control.
* ComboBoxes now support multi-column setups 99% identical to ListBoxes.
* Toolbar buttons in the Battleship theme now support a toggled (checked) state.
* When moving custom windows and docking them to the top, it now cannot happen anymore that the window header gets pushed out the top and can't be clicked anymore.
* The WPF Utilities namespace now features a GeometryHelper class.
	* All layout elements in the `Wpf.Layout` namespace now use the `GeometryHelper` class to create sizes and rectangles.
* All layout panels now do a better job handling hidden and collapsed child elements.
* `EditForm` layout improvements
	* This layout panel now does a better job at determining its own size.
	* Collapsed child elements are now ignored for measurements.
	* `EditForm` now supports mouse wheel interaction.
	* `EditForm` now supports stand-alone labels.
	* The size calculation logic for the layout has been re-written to be in sync with the arrangement logic in all cases.
	* `EditForm` now supports `View.SpanFullWidth`.
	* The layout algorithm has been improved for some fringe cases.
* `AsyncWorker.Execute()` can now trigger up to 15 background operations.
* View-models loaded into top-level windows now fire proper closing events in all themes.
* A new `TabItemsPanel` has been added, which arranges all its children as a tab control with individual tab pages. `View.Title` (`SimpleView.Title`) is used as the header.
	* A new `CODE.Framework-Layout-Tabs` layout style has been added to automatically use tabs for layout of View children. It is a new standard layout that is supported by all themes.
	* A new `StandardLayouts.Tab` has been added for simplified access to the new layout style.
* `ViewActionToolbarButton` now responds to changing ribbon captions.
* `ViewAction` (and related objects) now fire better change notifications.
* Custom validation attributes can now (optionally) access a ContextObject for advanced avalidation rules. (See also: [Bind, Security, and Input Validation](Bind,%20Security,%20and%20Input%20Validation))
* Made sure all themes support the minimum set of MultiPanel layout styles.
* Made sure all themes support the minimum set of BladePanel layout styles and polished the visuals of some of these styles.
* `BidirectionalStackPanel` changes:
	* Fixed some layout details.
	* `View.GroupBreak` is now supported.
	* Mouse wheel scrolling is now supported.
* Fixed some layout details in `BladePanel` (especially related to vertical header rendering and scrollbars).
* Fixed a problem with the calendar in the Universe theme that crashed an animation in some special cases.
* Changes to the `Mapper` class:
	* On the `Mapper` class, changed some member visibility from private to protected. 
	* Also exposing the `Maps` collection publicly now.
	* There now are new `Compare()` methods on the `Mapper` class that can be used independent of any other mapper features.
* Added an improvement to `ServiceClient` to better handle channel state issues in scenarios that require multiple proxies.
* `ListBoxEx` commands triggered on "select" now trigger even if the user clicks the same item twice (so technically, selection doesn't change, but they likely still want to trigger the command).
* Now supporting a `BeforeClosing` event on view models (and `IClosable` in general)
* The `Mapper` now supports filtered mapping functions.
* Performance of multi-column lists during re-populate operations has been improved considerably.
* The default `ViewAction` implementation now features a `CanExecuteVisible` property that is `Visible` when the action can execute, and otherwise Collapsed.
* `Controllers` received an upgrade
	* There is a new `GetOpenModel()` method, which makes it easy to retrieve existing instances of view-models (See also: [Activating Existing Views](Activating%20Existing%20Views))
	* There is a new `ActivateView()` method, which allows bringing an existing view to the foreground, rather than always opening a new one. (See also: [Activating Existing Views](Activating%20Existing%20Views))
* `ServiceHostController` has been improved in various ways:
	* There now is a new `PostOrPut` REST method/verb, which allows for the creation of a service method that can be called with either a `POST` or a `PUT` verb.
	* The `ServiceHostController` class now has an `HttpsMode` property, which can be used to define that the controller must be called over HTTPS (options are to require HTTPS, to not care, or to require HTTPS except when running on localhost).
	* `ServiceHostControllers` can now handle ambiguous mappings. For instance, a URL can be /Customer/1 (mapping to a method with empty name that returns customer of ID 1) and another one can be `/Customer/All` (calling a method called "all"). Note that it would not be possible to call the default (unnamed) method with parameter "all" as method names have priority.
	* `ServiceHostController` has been updated to use WebApi packages v5.2.3 (formerly 5.2.2).
	* `ServiceHostController` can now return camelCase JSON (see also: [WebApi Service Hosting](WebApi%20Service%20Hosting))
* When calling a CODE Framework hosted REST service through `ServiceClient`, the service call proxy now properly enforces UTF-8 encoding.
	* Handling HTTP verbs that do not post a body better, so parameters work in all scenarios.
* The `ServiceClient.AfterServiceOperationCall` event now has a `ServiceCallDuration` property on its event arguments, which provide information about how long it took to execute the service call.
* There was a problem in `ServiceClient` where channels weren't properly closed when a secondary proxy was used (as is the case when events on the service client are utlized, such as `AfterServiceOperationCall`).
* Optimized `FocusHelper` to perform better when the control that is to receive focus is not yet visible, and thus the code needs to wait for the control to become visible.
* The `Shell` now opens top-level windows without setting a position. However, there now is a new `WindowEx.WindowStartLocationStylable` attached property, which can be used to style the default startup location of top-level windows. (All themes use this property).
* The `Shell` view-style for the Workplace theme has been changed to fix a bug that keps the first of multiple top-level views to display properly under certain circumstances. (Note: The feature to launch top-level views into the backstage view of the Ribbon is now not supported anymore, as it isn't needed anymore anyway, and it caused this particular problem).


## 4.0.61203.0

* The ServiceHostController (used to host WebApi based services) has been improved in various ways:
	* It now has new methods: BeforeInvokeMethod(), BeforeCreatingResponse(), and BeforeReturningResults(). These methods can be used to fine tune how requests and responses are handled.
	* It now supports CORS out of the box (and has CORS enabled by default for all origins). (See also: [CORS Support in ServiceHostController](CORS%20Support%20in%20ServiceHostController))
	* Added UserHostAddress and UserHostName properties to ServiceHostController.
	* The URL pattern (route pattern) usable for hosting services has been improved (especially as related to hosting on legacy URLs).
* Views now have new convenience properties:
	* StandardIcon allows setting of a view icon (brush resource) using a strongly typed enum that lists all default icons available in the framework.
	* Views now have a StandardLayout property that allows guided and strongly typed setting of the appropriate style.
* ViewAction now has a new StandardIcon property (and a matching constructor parameter). This allows setting the action's brush resource key using the strongly typed StandardIcons enum.
* Automatic drag support for windows in the WindowEx class has been re-written to now also supports snapping.
* Multi-column lists now support drag-rearranging of columns. (This can be turned off on the columns collection).
	* All the visuals associated with the drag operation are stylable.
	* Each theme has default styles for the column drag operations.
* JSON improvements (see also: [JSON Features](JSON%20Features))
	* JsonHelper has been enhanced with a few convenience methods.
	* JsonBuilder class has been added
* A new SettingsManager class has been added that can be used to manage settings such as window positions or most recently used file lists, and more.
* The Shell object now makes sure that all the close-events are called on view-models whenever the Shell is terminated (as opposed to controlled closing of all models for some other reason).
* PasswordBoxEx.Value attached property now triggers source update on PropertyChanged by default.
* Added CapsLockWarning control (as well as appropriate templates for all themes). (See also: [Caps-Lock Warning and Global Key Handlers](Caps%20Lock%20Warning%20and%20Global%20Key%20Handlers))
* Added GlobalKeyboardHookHelper class, to make it easier to listen to global keyboard events in an app. (See also: [Caps-Lock Warning and Global Key Handlers](Caps%20Lock%20Warning%20and%20Global%20Key%20Handlers))
* ServiceClient now raises static events that fire every time before and after a service method/operation is called, allowing for generic interception and manipulation of service input and output. (See also: [ServiceClient Events](ServiceClient%20Events))
* The framework now has a new masked-textbox feature that can be applied by means of an attached property to any textbox. (See also: [TextBox Input Masks](TextBox%20Input%20Masks))
* The framework now supports a ServiceSecurityMode setting for TCP/IP settings, to allow for simple declarative configuration of this setting. (See also: [WCF Service Configuration](WCF%20Service%20Configuration))
* The Shell object now fires a static event that allows impacting which style gets applied to top-level windows on a case-by-case basis.


## 4.0.60902.0

* Themes
	* A new Theme (Universe) has been added to the WPF framework.
	* The Geek shell theme now doesn't force the top-level menu headers to upper-case anymore.
	* ApplicationEx now raises events when the theme switches
	* Theme-related icon handling has been much improved
	* There now is a new ThemeIcon class that simplifies the display of theme-specific icon resources, as well as the general use of CODE Framework standard icons (see also: [Theme Icons](Theme%20Icons))
	* The StandardViewModel implementation now supports the theme switching events and switches icons loaded using the LoadSharedXxx() methods automatically when the theme switches.
	* ApplicationEx now features a static RegisterTheme() method that can be used to register themes compiled into separate DLLs more easily.
	* View-Action Ribbon elements now also use the new theme icon approach. As a result, the Icon property has been removed from the individual RibbonButton controls as the entire control is driven by the brush resource defined on the view action bound to the element.
	* A theme switching problem where switching to the Workplace theme sometimes caused the Backstage (the "File menu overlay" Office apps usually open when clicking the file tab) to open and not go away until a new view was launched, has been fixed.
	* Added a set of standard Office colors (as used by OneNote, for instance) to the colors resource dictionary of the Workplace theme.
	* A new hierarchical Shell template has been added to the Workspace theme, which turns the main Shell into a hierarchical view similar to the one used by OneNote. This means that both the traditional as well as this new Shell are now available in the Workplace theme.
* View Action changes/additions
	* The ViewActionMenu control now allows binding any view-actions collection by means of an Actions property (this is in addition to the previous feature, which allowed binding to a model that implemented IHaveActions).
	* All view action menus and toolbars now use this new element to show icons, which means these icons now switch on the fly with theme switching. (This also includes the Universe hamburger menu).
	* Some themes did not respect view-action order settings (especially the Ribbon and drop-down menus). These elements have been enhanced to support the setting.
	* The ViewActionMenu control now supports tool tips defined on the associated actions (and thus all the themes that use this standard menu control inherit this functionality).
	* The ViewActionToolbar now has an IncludeAction() method that can be overridden in subclasses to define which actions should be included in the toolbar.
	* There now is an IViewActionPolicy interface (with a default implementation) that can be used in the ViewActionRibbon and ViewActionMenu (and others) to impact the view actions collections before they populate the UI. This feature is designed to allow developers to impact the setup of view-actions in a standardized way that can also be theme-specific.
	* ViewAction classes now have a CategoryBrushResourceKey that allows setting an icon for a view-action category. (Currently only respected by the Universe theme).
* The indexer on the view-action collection now returns null when the referenced view-action is not found. It used to throw an error instead.
* WindowEx now supports custom window resizing on borderless windows on scaled high-res displays correctly.
* The Ribbon control now respects the upper-case/lower-case setting for view actions populated by local view categories (used to always be upper case).
* The first page of the Ribbon control in the Workplace theme (also known as the "backstage" view in Office) now supports custom views associated with each action. This makes it easy to create UIs like the "New Document" or "Open Document" view in Office. (See also [Custom Views and View-Models in Ribbons](Custom%20Views%20and%20View%20Models%20in%20Ribbons))
* Async features
	* The AsyncWorker.Execute() method now allows for more (10 max) background threads to be executed in parallel. 
	* AsyncWorker now has an OnForegroundThread() method that allows firing code from a worker thread that runs on the foreground thread (great for reporting progress updates to the UI and such). (See also: [CODE Framework AsyncWorker](CODE%20Framework%20AsyncWorker))
* Fixed an issue with ListEx column definitions that are bound causing continuous refresh of some of the defined columns. Also changed the binding mode of the whole collection to be OneWay by default.
* Added ObservableResourceDictionary class.
* Hotkey support in the Ribbon has been improved to make sure visual hotkey indicators go away when appropriate.
* Layout
	* The MultiPanel layout panel has been updated with considerable new features. (See also: [Automatic Layout - MultiPanel](Automatic%20Layout%20-%20MultiPanel))
	* The scroll bars on the BidirectionalStackPanel now automatically receive more useful settings for small and large change increments.
	* There now is a second title color property on the SimpleView/View object, which can be used for things such as background colors. (Different themes and controls may choose to use this property in different ways).
	* There now is a new BladePanel layout element (and associated styles)
* The service infrastructure has been enhanced in various ways
	* Services now support message sizes of "VeryLarge" and "Max". These are setting meant for extreme cases only. (See also: [WCF Service Configuration](WCF%20Service%20Configuration)).
	* A new BeforeEndpointAdded static event has been added to ServiceClient. (See also: [WCF Service Configuration](WCF%20Service%20Configuration)).
	* The service test harness now supports entering values in service contracts for List<T> where T is a simple type such as a string. (This used to only work when T was itself a data contract and thus a complex object).
* List
	* ListEx Column Header Edit Controls now support setting a custom template as well as a custom data context for the controls.
	* The ListEx.Columns definitions now support ToolTip and ToolTipBindingPath properties for the contents of list columns. (See also: [Multi-Column Lists](Multi-Column%20Lists))
	* The ListEx.Columns definitions now support CellForeground and CellForegroundBindingPath properties for the contents of list columns.
	* The ListEx.Columns definitions now support an AutoSort property, which can be set to true to let the control automatically sort its items. (See also: [Multi-Column Lists](Multi-Column%20Lists))
	* The ListEx.Columns definitions now support an AutoFilter property, which can be set to true to let the control automatically filter by columns. There are a number of associated properties to define the specific behavior on each column. (See also: [Multi-Column Lists](Multi-Column%20Lists))
	* ListBoxEx now has a TriggerCommandOnEnter attached property. When this property is set to True on a ListBox, the associated command fires when the user hits ENTER with the focus in the list.
* There now is a RangeSlider control (see also: [RangeSlider Control](RangeSlider%20Control))
* There used to be ambiguity between two ToStringSafe() extension methods, so we removed one of them. This is a breaking change for some people, but fixing it is trivial, so we decided it would be worth the trade-off.
* View models
	* IStandardViewModel now includes an IsChecked property.
	* IStandardViewModel now includes Color1 and Color2 properties. (All standard implementations of this interface have been updated to include these properties).
	* There now is a new StandardViewModel<TData> object that can be used to attach an original data source object to a standard view model without having to create a new class.
* There now is a new Security class in the CODE.Framework.Wpf.Security namespace, which can be used to define security roles in the user interface as well as on view-model properties. (See also: [Bind, Security, and Input Validation](Bind,%20Security,%20and%20Input%20Validation))
* There now is a new AttributeBinding markup extension that allows binding to attributes. (See also: [Bind, Security, and Input Validation](Bind,%20Security,%20and%20Input%20Validation))
* There now is a new (optional) Bind markup extension that extends the default Binding extension provided by WPF and incorporates features such as security and validation directly. (See also: [Bind, Security, and Input Validation](Bind,%20Security,%20and%20Input%20Validation))
* UI Input validation has been standardized and implemented as a core CODE Framework feature. (See also: [Bind, Security, and Input Validation](Bind,%20Security,%20and%20Input%20Validation) and [Input Validation in WPF](Input%20Validation%20in%20WPF))
	* All control styles included in the various themes have been updated to reflect invalid input.
* Workplace and Metro themes now have model-smart progress animations. (They can be bound to any model that implements IModelStatus. These controls automatically become visible when the model status indicates a busy state).
* TextBoxEx now supports UpdateSourceOnEnterKey attached property, which allows triggering binding updates on Explicit or LostFocus bindings whenever the user hits ENTER.


## 4.0.50824.0

* The default change-over margin of GridPrimarySecondary.SecondaryUIElementAlignmentChangeSize has changed to 1d, which means that by default, the secondary element will be left or right, rather than top or bottom.
* All themes received additional work on their default colors for more polish out of the box.
* Newtonsoft Json.net has been forked and incorporated into the core CODE Framework DLL. (See also: [JSON Features](JSON%20Features))
	* All JSON serialization for service hosting (WebAPI and WCF) is now done with the Json.net serializer.
* Fixed horizontal scroll problem with the Vapor multi-column list header.
* The Workplace theme now adopts the new Office 2016 "colorful" look as it's default appearance. 
	* The color resource dictionary for the Workplace theme now defines additional color settings just for the Ribbon.
	* The old style can still be set by changing these colors
* There was a problem with the "empty" file category in the Ribbon (such as in the Workplace theme) not being changeable from "File". This has been fixed.
* Updated the circular progress animation to make the start appear a little smoother. Also added a StartDelay option to automatically delay display of the animation.
* A new logger TraceLogger has been added. It is equivalent to calling System.Diagnostics.Trace.WriteLine() and is the Trace version of the OutputWindowLogger which only logs when the DEBUG flag is set.
* The WebApi reference has been updated to v5.x (and is now using NuGet)
* A bug that caused partial views not to respect the Model property to set the DataContext has been fixed.
* EmailHelper.SendMail() can now accept atacthments.
* Fixed various minor special cases with Ribbons and odd view action categories.
* Fixed a problem with the GridPrimarySecondary class crashing when it was resized too small.
* Workplace now has improved touch support on all scrollable elements.
* The list of icons across the bottom of the Wildcat theme standard shell now automatically scrolls if need be.
* The Vapor theme has improved support for scrolling and panning in touch scenarios.
* The Wildcat theme has improved support for scrolling and panning in touch scenarios.
* Updated the build packager to work properly with Visual Studio 2015


## 4.0.50402.0

* There is a new PanoramaPanel control in WPF (similar - but not identical - to the Panorama control in Windows Phone)
* The MultiPanel control now supports setting an optional header renderer object.
	* A default header renderer object is provided that is capable of rendering horizontal and vertical headers for contained child elements.
	* All standard themes have default styles for the new header renderer.
	* There now is a new style called CODE.Framework-Layout-MultiPanelLayout in all standard themes that invokes the MultiPanel layout element.
* The SimpleView (and View) class now supports several new attached properties (which are/can be used by other objects to implement the related functionality in different ways).
	* TitleColor
	* Docked and SupportsDocking
	* Closable and CloseAction
* There is a new TransparentProxyGenerator class in the Core.Utilities namespace. (see also: [Using the Transparent Proxy Generator](Using%20the%20Transparent%20Proxy%20Generator))
* ServiceClient.Call() now supports calling REST services. (See also [Calling REST Services through ServiceClient](Calling%20REST%20Services%20through%20ServiceClient))
* WebApi service hosting through ServiceHostController now supports hosting service implementations that implement more than one service interface.
* The Metro theme has received an overhaul
	* The start screen can now be scrolled horizontally with the mouse wheel (same as the start screen in Windows 8)
	* Metro tile layouts can now lay out tiny and large square tiles in addition to the previously available normal and double-width tile sizes.
	* The start screen now supports very large and tiny view actions (Lowest significance view actions end up as tiny tiles, Largest as the double-size square large tiles).
	* All scroll viewers now default to support panning, enabling touch panning on everything as a default.
	* Scrollbar elements now don't have rounded corners anymore, to fit the default WinRT look.
	* Scrollbars now have a different look when the user interacts in touch mode rather than mouse mode. (see also: [TouchEx and Scroll Bars](TouchEx%20and%20Scroll%20Bars))
	* The Metro-Tile start screen has been re-written to offer better performance, more features, and better stylability. (see also: [Metro Start Screen](Metro%20Start%20Screen))
	* The Slider and ZoomSlider controls have a new style more optimized towards touch
* There now is a new TouchEx object that provides useful functionality for touch scenarios (in particular, it can detect whether the user uses touch or the mouse and it can automatically set an attached property on any object (typically a window) based on that information).
* Newsroom was added
* There now is a new PrimarySecondaryHorizontalPanel control that can be used to lay out primary/secondary UIs in a horizontal fashion. It is similar to the features that were there before in the PrimarySecondaryGrid element. The new element is simplified in some ways and has new features added (such as collapsing the secondary element). Both panels have their place and will continue to be supported.
* Added new standard icons to all themes (see also: [Standard Icon Resources](Standard%20Icon%20Resources))
	* There is a new menu icon called CODE.Framework-Icon-Menu 
	* There are 3 generic "data" icons called CODE.Framework-Icon-Data, CODE.Framework-Icon-Data2 and CODE.Framework-Icon-Data3
	* There is a new icon called CODE.Framework-Icon-Money (usually showing a dollar sign or a dollar bill)
	* There is a new icon called CODE.Framework-Icon-User
	* There are 8 new icons for directional arrows (such as CODE.Framework-Icon-ArrowRight and CODE.Framework-Icon-ArrowUpLeft and so on)
	* The set of icons for the Vapor theme has been re-done
* There now is a new ViewActionMenuButton that can display a drop down list of view actions bound to it.
	* Styles have been added to all themes to show appropriate popups (including a Metro one that shows tiles)
* There is now an OutputWindowLogger so that logging can be directed to the Visual Studio Output window. This essentially sends logged messages to System.Diagnostics.Debug.WriteLine().
* A crash-bug (encountered when switching from Metro to Workplace theme on the fly) has been fixed
* The EditForm control has been enhanced so it can optionally show styled lines between controls. (See also: [Automatic Layout and Elasticity](Automatic%20Layout%20and%20Elasticity)
* Partial Views now support passing parameters to a controller action. (See also: [Partial Views in CODE Framework WPF](Partial%20Views%20in%20CODE%20Framework%20WPF))

## 4.0.50121.0

* Fixed a problem with mouse drag behavior on a window being initialized when the wrong mouse button was down.
* Login screen created by the WPF project template now sets default focus to the user name field. The "Login" button is now the default button and will trigger a login if <Enter> is pressed. 
* The service test host has received an overhaul (see also: [Using the Service Test Harness](Using%20the%20Service%20Test%20Harness)).
	* The icons in the list of hosted services has changed to be more obvious
* A major new sub-system has been introduced that allows hosting all services within WebApi (see also: [WebApi Service Hosting](WebApi%20Service%20Hosting))
* A major new sub-system has been introduced that allows designing CODE Framework Services as REST services, while still sticking with the standard service setup that allows hosting those same services in all the previously supported standards. (See also: [Creating REST Services](Creating%20REST%20Services))
* The service test harness has received a major overhaul
	* All the used icons have changed (and the colors match the one in the test host to make it more obvious which service protocol is being called. The window title also indicates the protocol now.
	* New member types are now supported
		* Byte-arrays (binary fields) are now supported and an option to load a file into the array has been added
		* Complex child objects are now supported (unlimited hierarchy depth)
		* Lists and arrays are now supported at unlimited hierarchy depth
	* JSON and XML formats are now supported as editable contract requests (independent of actual protocol or message format)
	* Request objects can now be saved and loaded as JSON files
		* Quick-save and quick-load options are now supported on a contract and operation basis.
* A JsonHelper static class has been added to the core Utilities namespace
* An XmlHelper static class has been added to the core Utilities namespace
* Fixed a bug in ColumnPanel that happened when the collection of columns was defined but had a zero-count (no members).
* The ViewActionRibbon control has received some upgrades
	* There was a bug with hidden buttons accidentally hiding the entire tab page.
	* Ribbon buttons now dynamically react to changing action captions to reflect changes on the fly.
	* Disabled ribbon buttons now have a better visual style.
	* Large ribbon button labels can now wrap across up to two lines.
	* Ribbons now support action's ActionView and ActionViewModel properties. (See also [Custom Views and View-Models in Ribbons](Custom%20Views%20and%20View-Models%20in%20Ribbons))
* A new HttpHelper class has been added to the core Utilities namespace. It provides the ability to UrlEncode/UrlDecode strings without having any dependency on server-side framework components.
* Fixed null argument exception when clearing a list bound to TextBlockEx controls.
* The ViewActionStackPanel now supports an ActionFilter property to switch between showing all visible and available view actions and only showing the ones that are pinned. It now also dynamically refreshes when pinned or visible status changes. This is used in various places, such as the pinned actions in the Workplace theme (appear above the Ribbon in the title bar).
* The ListBoxFastSmartDataTemplate class has been fully implemented (including data binding support).
* Made it easier to split CODE Framework WPF UI apps into multiple projects (see also: [Splitting WPF Apps into Multiple Projects](Splitting%20WPF%20Apps%20into%20Multiple%20Projects)).

## 4.0.41021.0

* The latest CODE Framework source code is now also on GitHub [https://github.com/markuseggerinc/CODEFramework](https://github.com/markuseggerinc/CODEFramework)
* There now is a new ListEx.AutoEnableListColumns which (when set to true) automatically brings in a set of default styles (without dependence on any themes) to enable multi-column support on all list boxes.
	* Default styles have been added to the core WPF CD dll to support this.
* TreeViewEx.SelectedNode now binds two-way by default.
* Reported bug in FocusHelper (happened when the focus was moved when the app was in shutdown mode) has been fixed.
* Reported bug in LitBrushConverter (and ColorHelper) has been fixed.
* IViewAction now has an IsChecked and a ViewActionType property (enables things such as menu items with check marks) (See also: [Checked and Toggle View-Actions](Checked%20and%20Toggle%20View-Actions))
	* ViewActionMenu and ViewActionRibbon controls have been updated to support this new feature. This means that all themes using these controls automatically support these features.
	* There now is a new standard ToggleViewAction that toggles its IsChecked property every time it is executed.
	* The default ViewAction implementation features an IsChecked_Visibility property that returns Visible for checked items and Collapsed for unchecked items, which is very convenient for binding (especially in combination with the new string-based indexer as described below).
* IViewAction now has an Id property, which is typically identical to the caption, except Ids do not change with localized captions, and can thus be used to identify an action.
* IViewAction has changed in a (slightly) breaking way to use a ViewActionCollection, rather than ObservableCollection<IViewAction>. The new collection inherits from the same observable collection, but gives us a way to provide new features. (Note: If you have implemented IHaveActions manually, you need to use this new collection instead, but it is an easy change).
	* The new collection has a secondary, string-based indexer that allows referring to actions simply by their ID. (See also: [String Indexer for ViewActionsCollection](String%20Indexer%20for%20ViewActionsCollection))
* IViewAction now also has a Visibility property that can be set in addition to Availability and CanExecute(). (A view action that is available and can execute can still be hidden by making it invisible).
* Added new styles to the recently added Wildcat theme (PasswordBox)
* Changed the ViewActionRibbon class to be easier to subclass from.
* The ViewActionRibbon now automatically hides empty tabs.
* Fixed the implementation of the attached TextBoxEx.WatermarkText property (which was previously incorrectly called WatermarkTextProperty property) and supporting the property in the Workplace textbox default theme.
* Added BringItemIntoViewWhenSelected DependencyProperty to TreeViewEx. When selected item is programmatically set and this property is True, the item will scroll into view.
* Added UseCustomDictionaries DependencyProperty to TextBoxEx and RichTextBoxEx controls. When set to True, assumes a custom user dictionary named UserDictionary.lex in the \My Documents\Custom Dictionaries\ folder. The spell check context menu will allow user to add words to UserDictionary.lex custom dictionary. If UserDictionary.lex does not exist, it will be created. Other custom dictionaries found in the same folder will be used, but only UserDictionary.lex will get "Added" words. Custom dictionary path can be overridden in the app.config by adding a CustomDictionaryPath key and a value to appSettings.
* The Wildcat theme now has a better implementation of status messages.
* The Wildcat theme now has an implementation of notification messages. (Note: This uses a new [Sticky Notes Feature](Sticky%20Notes%20Feature) which can also be used for other things).
* There was a bug in the PartialView.View property, which was registered with an incorrect name in the dependency property system. This has been fixed.
* There now is a new PropertySheet layout panel, that lays out elements like in a Visual Studio property sheet.
	* The PropertySheet layout panel supports groups of elements (using the same group syntax as other layout elements: View.GroupBreak="True", View.GroupTitle="My Title",...)
* Added TextBlockEx control which allows words to be highlighted within the text block, for example when searching.
* When using the "Backstage" feature of the Workplace theme (i.e. when the File menu is opened), popups no longer close the underlying Top Level form if one is open. 
* When using the "Backstage" feature of the Workplace theme (i.e. when the File menu is opened), notifications are now visible.
* Added optional parameters to Core's EmailHelper to allow users to optionally specify a port, user name and password for the SMTP server.
* Added an Email logger to Core.Utilities allowing LoggingMediator to send emails when log entries are made. Emails are sent in the background using the ThreadPool.
* The Workplace shell now has a build-in zoom slider that sets the zoom of normal views. The zoom slider is also themed appropriately to look right for the Workplace theme.
* The ViewHostTabControl class now has a ContentZoom property.
* The default ViewAction implementation now overrides the ToString() method to show the caption of the action in addition to the default type display.
* Fixed a reported bug with FocusHelper and moving the focus during application shutdown.
* InvalidateCanExecute() has now been promoted to be part of IViewAction and is thus supported by all view actions. (Note: This may require a small addition to custom implementations of IViewModel, but it is basically a one-liner that fires the CanExecuteChanged event).
* The FlowForm layout panel now has an EditControlMinimumWidth property that can be used to make sure edit control have a certain minimum size. (It is set to -1 by default, which means the setting is ignored).
* The Primary/Secondary List layout style has been updated to render more appropriate and keep the secondary elements at the top edge unless it grows larger than 150 vertical pixels, at which point it moves to the left edge.
	* The SimpleFormLayout style within the primary/secondary styles is now using a flow form layout rather than a bidirectional stack to create a more appropriate flow for the horizontal overall alignment used in most cases.
		* The flow form style used has its EditControlMinimumWidth set to 125 by default.
* The Workplace shell Ribbon now does not close the "backstage" view (the overlay that can be opened as a "file menu") when there are no views open and no other categories exist in the Ribbon.
* Wildcat themed top-level views now support a status indicator.
* The Wildcat theme has received additional polish, such as window transition animations and true reflections.
* There now is the concept of a "local" modal view, which can be used as "child" windows in some themes (in particular Wildcat). This way, windows can be opened modal within their parent windows, but one can still switch to other main views while the "local modal" view remains open.
	* ViewResults in controllers now have a ViewScope property (defaults to Global, but can be set to Local). This property can easily be set through the ViewModal() method, which now has an additional (optional) parameter that can be used to set this property conveniently.
* The Wildcat theme now has a built-in style for TabControls.
* MessageBoxes in all themes have been enhanced, so when custom view models are used that change the title on the fly, those changes are reflected in the title in each theme.
* ObjectHelper.SetPropertyValue() and ObjectHelper.GetPropertyValue() now support complex paths in addition to property names. Example Paths: Address.City or Invoice.LineItems[2](2).Description
* Additions have been made to the view action ribbon to ensure that special page activate/deactivate events fire only after the control has fully initialized (this caused problems in some isolated scenarios on theme switching)
* The property sheet (layout panel) scroll bars are behaving better in various situations. (Mouse wheel is now also supported).
* Added a new style for the DatePicker control in the Workplace theme.
* DockHost has been enhanced in various ways, including the introduction of a new floating window object that can be independently styled and enhanced.
* New Expanded and Collapsed icons have been added to the standard set of CODE Framework icons (see also: [Standard Icon Resources](Standard-Icon-Resources))
* A new IExceptionLogger has been added to enable better control over how individual loggers ("logging sinks") handle exception information when logging. (See also: [Logging Framework](Logging-Framework))
	* The abstract Logger class implements the new interface.
* There now is a new MultiPanel layout control
* The SortOrderIndicator control and the list box header control has been enhanced to allow for column clicks and sorting in scenarios where they are contained in a movable GridEx control.
* Added the ability to specify a timeout for individual notifications.

## 4.0.40304.0

* The View Visualizer has been enhanced to show significantly more information about each opened view and its view model, ranging from actual view model data to properties and styles and resources of each element in the view and sophisticated visualization for items. (see also: [Using the View-Visualizer](Using%20the%20View-Visualizer))
* Column-based standard views (as used by many themes such as Workplace, Geek, Vapor,...) can now be used outside of lists as well.
* Fixed some behavior details with the BidirectionalStackPanel. (Scrollbars now respect their proper z-index, and resizing works in some fringe cases where the panel gets too small).
* The FocusHelper class received some minor enhancements, such as setting focus to a hierarchy of controls.
* Buttons in all default theme have received a more obvious keyboard selection indicator.
* All default themes had their message box styles tweaked to make the keyboard focus behave more natural when message boxes are shown.
* ActionGrid instances now automatically pick up input bindings from the actions collection of the object they are bound to.
* MessageBoxes now support shortcut keys. (See also: [MessageBoxes in WPF MVVM/MVC](MessageBoxes%20in%20WPF%20MVV%20MMVC))
* The default layout panel approach for all complex listboxes in all themes is now a simple StackPanel without virtualization, since that simply turned out to be MUCH faster in most scenarios.
* ListBox column definitions now support width definitions based on star notation in addition to the existing pixel notation. (See also: [Multi-Column Lists](Multi-Column%20Lists)).
* There now is a new ColumnPanel layout control, which is a handy (and high performance) mixture of a column-only Grid and a StackPanel
* Default message box view action captions are now pulled from a localizable resource file.
* Added a "View action and view selector" control that combines view actions and open views into a single items control (used, for instance, in the Wildcat theme).
* A BorderEx control has been added which provides some attached properties that are helpful on Border objects.
* There now is a new SimpleView.ViewThemeColor attached property that can be used to define color themes for views which can in turn be utilized by (some) themes.
* A first draft version of the new Wildcat theme has been added to this build.

## 4.0.31028.0

* Multi-column lists have been drastically improved to support more grid-like capabilities without needing custom templates (this is by far the biggest change in this build)
* There now is a ControllerEx class for use in the CODE Framework ASP.NET MVC components
* The ASP.NET MVC ControllerEx class now features a RedirectToActionWithData() method. (See also: [Redirect-to-Action with Data](Redirect-to-Action%20with%20Data))
* Added an ObservableCollectionHelper class to the WPF MVVM components with an AddRange extension method for ObservableCollection<T>.
* The AsyncWorker object now allows setting a thread priority explicitly for continuous background threads.
* Enhanced the AsyncWorker to automatically handle status objects in a thread-safe fashion. (see also: [CODE Framework AsyncWorker](CODE%20Framework%20AsyncWorker))
* The AsyncWorker.Execute() method now has several overloads that allow passing multiple background operations. The result of all those are then passed to a single complete delegate whenever all background operations have completed. (see also: [AsyncWorker Part 2](AsyncWorker%20Part%202))
* The Workplace theme now includes a new default Calendar control style to make calendard appear more like Office's calendars.
* Added ascending and descending sort icons to the [Standard Icon Resources](Standard%20Icon%20Resources) (supported by all themes)
* There now is a new SortOrderIndicator control in the WPF namespace which is a useful helper control whenever one needs a quick way to indicate a sort order.
* The TextBoxEx class now has a WatermarkText attached property.
* More work has been done to strip source control information from the source code deployment. Hopefully, this will take care of this once and for all :-)

## 4.0.30923.0

* The BidirectionalStackPanel class now has a ScrollBarStyle property, which can be used to set the style of the scroll bars the control uses.
* The Workplace theme seems to have had some incorrect standard icons. Those have been replaced with a correct set.
* The Metro theme incorrectly used the icons that were meant for Workplace in the previous build. This has been fixed and the original Metro icons are back.
* Fixed a bug that caused the Vapor theme to not show standard data templates that used multi-column lists properly.
* Added a Controller.LoadView() overload that allows specifying the controller by name (string)
* Fixed a problem in the FlowForm layout element that caused full-row-span controls to sometimes be made too wide.
* Added a SetCanExecuteDelegate() method to view actions.
* Controllers now throw a detailed exception message when an action was not found.
* Multi-column listboxes now support column header templates to support any type of content/UI in column headers. (See also: [Multi-column lists](Multi-column%20lists))
* Multi-column listboxes now support a header click command (See also: [Multi-column lists](Multi-column%20lists))
* The SizeAwareContentContentControl class is now set to not receive focus by default.
* The ServiceClient.Call() method can now more easily make calls to services hosted at arbitrary URLs. (See also: [WCF Service Configuration](WCF%20Service%20Configuration))
* Fixed a typo in the Workplace scrollbar control template that caused a non-existing color to be referenced.
* Fixed a binding problem in the Workplace textbox control template.
* The build packager for the automated framework build process has been enhanced to remove unnecessary files from the source code deployment file.

## 4.0.30618.0

* Added 2 new WPF themes: Geek (looks a lot like Visual Studio) and Vapor (an homage to Valve's Steam)
* Added a standardized approach to launch multiple shell windows and automatically force new views to be loaded in separate shells. (See also: [Tuning the Appearance of the Shell](Tuning%20the%20Appearance%20of%20the%20Shell))
* The Workplace theme now supports disabling tab "headers" (they are actually more "footers" since they show at the bottom) for views opened individually. This is done by setting the ShowHeaders property to false on the normal views host in the shell style. (Without headers, the style looks more like Word, while with headers, it looks more like Excel). (See also: [Tuning the Appearance of the Shell](Tuning%20the%20Appearance%20of%20the%20Shell))
* The view action Ribbon control now has a new HighlightLocalCategories property that indicates whether pages in the Ribbon that are populated from individual views should be highlighted using the special colors. (See also: [Tuning the Appearance of the Shell](Tuning%20the%20Appearance%20of%20the%20Shell))
* The Shell now has a TitleMode property, which can be used to define whether only the application title or also the view title (and in what order) should be displayed as the shell window title. (See also: [Tuning the Appearance of the Shell](Tuning%20the%20Appearance%20of%20the%20Shell))
* Added some specific features to make it easier to override and enhance specific aspects of the EditForm layout panel class.
* In addition to WidthEx, the View class now defines MinWidthEx and MaxWidthEx properties. Same for MinHeightEx and MaxHeightEx.
* The View class now has several new attached properties, such as View.Label and View.LineBreak (which can be attached to controls within views).
* There now is a new FlowForm layout panel which is a different way to do automatic layout (along the same lines as EditForm, but with a slightly different approach to arranging elements). There also is a new CODE.Framework-Layout-FlowFormLayout style that can be used to automatically use this layout approach in views.
* The LoggingMediator.Log() method now has one more overload that allows passing an exception as well as some text (the exception information is added after the text parameter) - (see also: [Logging Framework](Logging%20Framework))
* All the ServiceGarden.TryAddXxx() methods now use the new overload in the LoggingMediator.Log() to log more exception detail when something goes wrong.
* IViewAction now defines an IsDefaultSelection property, which is used by different themes in different ways. (Example: The Worlplace/Office theme uses this property to pre-select the tab in the ribbon the view action is in, if the property is set to true - see also [Ribbon - Setting a Default Page](Ribbon%20-%20Setting%20a%20Default%20Page)).
* There was a bug with automatically populated view action menus and menu items with multiple categories which didn't correctly create sub-menus. This has been fixed.
* The standard view action toolbar class (as used by the Battleship theme, for instance) now has another property to define which actions from the individual views will be displayed in the toolbar. Current defaults are: All view actions from the start view model with Significance = Highest, and all view actions from the individual view with Significance >= BelowNormal will be displayed in the toolbar.
* View actions now support defining access keys and shortcut keys.
* All themes that use standard menus for view actions support the access and shortcut keys.
* All themes that use standard ribbons for view actions support the access and shortcut keys.
* The start screen of Metro themed apps now supports view action shortcut keys.
* The ViewActionMenu class now supports a property to force all top level menu items to upper case text.
* The constructor of the ViewAction class now supports several additional optional parameters for convenience.
* The Workplace theme now uses the same icon set as the Geek theme, which is a slightly more colorful version of the Metro theme.
* There now is a TextBoxEx class which provides a InputMaskRegEx property to define input masks for all text boxes. (see also: [TextBox Input Masks](TextBox%20Input%20Masks))

## 4.0.30403.0

* Changed the LoggingMediator class so when exceptions are logged, the log type actually defaults to "Exception" (rather than "Information" as it did before).
* Added a StringHelper.SubstringSafe() method (and also an extension method) which is really the same as StringHelper.SubStr() but should be more discoverable.
* When individual views expose view actions that are part of the "File" category, these actions now show up in the Workplace theme file menu ("special file button")
* View actions now have an Order property, which can be used to specify the order actions are displayed by themes (typically, that order applies within a group, but each theme is free to interpret this information differently).
* Added change notification for ViewTitle property on ViewResult objects (WPF framework)
* Added MoveFocusToDefaultOnActivate property to views.
* Added IUserInterfaceActivation interface to the framework and implemented it on all views (most important result: views can raise Activated events)
* The ServiceClient class now allows to only aut-retry failed calls for specific exception types through the AutoRetryFailedCallsForExceptions collection (this collection contains a list of all the types auto-retries may be attempted for)
* The ServiceClient class now has a AutoRetryDelay property so second calls to services can be delayed by a certain number of milliseconds.
* There now is a static Shell.Current property that always allows access to the current shell object (see also [Accessing the Shell Object](Accessing%20the%20Shell%20object)).
* Round Metro buttons now have a visual indicator for keyboard focus.
* The Battleship theme has been completely overhauled to be not just a sample but a complete theme.

## 4.0.30220.0

* Fixed a problem with sequential service host port assignments for TCP/IP service with auto-assigned ports. This problem only occurred in the development host (but was nevertheless annoying).
* Added an optimization for WPF Metro tile start screens and changes of action availability. This fix improves performance and also fixes a rare redraw issue.

## 4.0.30219.0

* The EditForm class has been enhanced (and thus by association so have all the styles that use that class, such as the automatic edit form layout style)
	* Edit forms now support multiple controls that flow inline with the "edit" part of controls, thus allowing (for instance) a single label with multiple textboxes. This is used by means of a View.FlowsWithPrevious attached property that can be set on any UI element
	* A bug with dynamically re-flowing edit forms has been fixed, where group breaks were ignored when a column re-flowed.
* The service garden class (root class used for all self-hosted services) now handles more exceptions and logs more detailed errors to make it easier to diagnose service hosting problems.
* The test service host (development service host) now displays more information about services with hosting problems and also has some additional exception handling for scenarios it used to bubble up unhandled to the environment before.
* The ServiceClient class has received a significant overhaul to improve performance and memory consumption (including a fix for a run-away memory use problem).
* The ServiceClient class now has a HandleChannelExceptions property (if set to false, channel exceptions bubble up to the caller more than before) and a ChannelException event (which can be used to get channel exception indications, independent of whether exceptions bubble up or not).
* Framework hosted services now automatically respect the Namespace attribute specified in service contracts and service implementations (see also: [WCF Service Namespaces](WCF%20Service%20Namespaces)).
* Self-hosted services now default to supporting "multi-threaded service hosting" (services now by default use ConcurrencyMode.Multiple unless configured otherwise) (See also: [WCF Self-Hosted Service Concurrency](WCF%20Self%20Hosted%20Service%20Concurrency))
* The development test host environment now hosts all services on a background thread to provide better concurrency support in test environments. (See also: [WCF Self-Hosted Service Concurrency](WCF%20Self%20Hosted%20Service%20Concurrency))
* The ServiceGarden class had many of its overloaded methods replaced with fewer equivalent methods with optional parameters.
* The TestServiceHost class had many of its overloaded methods replaced with fewer equivalent methods with optional parameters. Formerly unsupported additional parameters have been added.
* The ServiceClient class had many of its overloaded methods replaced with fewer equivalent methods with optional parameters.
* All HTTP related service hosting and service calling options now optionally support simplified hosting/calling using the HTTPS protocol (all related methods support a useHttps parameter) (see also [WCF HTTPS Service Hosting](WCF%20HTTPS%20Service%20Hosting))
* The service test harness now supports more data types, including enums and dates.
* The Mapper class (or more accurately, the MappingOptions class) now includes a list of excluded properties which can be used to define the exclusion of certain properties from mapping.
* Added the ability to call REST services through the ServiceClient class. (See also: [Calling REST Services through ServiceClient](Calling%20REST%20Services%20through%20ServiceClient))
* Increased the maximum object graph size to int.MaxValue for those environments where this isn't already the default
* Improved the Mapper class to allow mapping of nullable fields from/to non-nullable ones.

## 4.0.30123.0

* A new theme has been added called "Workplace" (looks very similar to Microsoft Office 2013)
* Views can now define a suggested size which is used if the view's size strategy is set to use the suggested size. Note that it is up to each theme and specific view types to respect the suggested size.
* The WindowEx class now has a DynamicIcon property which makes it easier to style icons for windows.
* IViewAction now has IsPinned and GroupTitle properties (see also [Pinned View Actions](Pinned%20View%20Actions))
* Added a WPF specific ImageHelper class with a method that can convert GDI+ bitmaps/images to WPF image sources (see also [WPF ImageHelper](WPF%20ImageHelper))
* The ActionItemsControl class now respects on the fly changes in the actions it is bound to.
* The standard close view action is now flagged to be pinned by default
* There now is a Controller.CloseAllViews() method in the WPF framework. (And view handlers also have a CloseAllViews() method).
* Generically defined columns in listboxes/lists now support an item template in each column. (See also [Multi-Column Lists](Multi-Column%20Lists))

## 4.0.30107.0

* WPF Document (print and preview) features have been added to this version (an upcoming article in CODE Magazine will describe this feature in detail)
* The WPF resource system now loads resource dictionaries associated with views and documents in an unlimited numbered fashion (see also: [WPF Resource Dictionary Loading](WPF%20Resource%20Dictionary%20Loading))
* There now is an *.AllThemes.xaml naming scheme for resource dictionaries to load with all themes (in addition to theme-specific resource dictionaries) (see also: [WPF Resource Dictionary Loading](WPF%20Resource%20Dictionary%20Loading))
* The Metro theme referenced an incorrect default font resource name in some files. This has been fixed.
* Animated Metro standard view models now start out with the other part visible (used to be that the large image was visible initially, while now the text part is visible).
* Tweaks have been made to the NetworkHelper class to handle certain specific scenarios when checking for a valid internet connection so they don't throw errors anymore.
* Auto-complete UIs now have better support for async loading of the drop-down contents.
* The ResourceHelper class' ReplaceDynamicDrawingBrushResources method has been enhanced, so it also checks pens in addition to brushes.
* New standard icons have been added for print, print preview, users, user roles, user rights, and login (see also: [Standard Icon Resources](Standard%20Icon%20Resources))
* A RichTextBoxEx control has been added with a bindable XAML property

## 4.0.21128.0

* Fixed a generic problem with top level views accidently closing normal views that occured in some very specialized scenarios
* Fixed a problem with our generic feature to bind events to commands. Some people had problems with it in very specific cases. (For more information, see [Generic ability to hook all events as commands](Generic%20ability%20to%20hook%20all%20events%20as%20commands))
* FrameworkContentElements (such as Paragraphs in documents) are now also supporting event to command hookups. (For more information, see [Generic ability to hook all events as commands](Generic%20ability%20to%20hook%20all%20events%20as%20commands))
* Fixed a problem with checking execution rights of ViewActions (timing and threading related... mainly happened in scenarios with slow connections).
* ListBoxEx now has a AutoScrollToSelectedItem property that can be attached to any listbox. Once set, the listbox automatically scrolls to make sure the selected item is visible.
* ListBoxItemEx now has a SelectItemWhenFocusWithin property. This property can be used to make sure the item is considered selected when the focus moves into one of the contained controls (such as a textbox within a listbox). This property is typically set in a style. All default theme listboxes in the framework have this property set to true.
* The ViewModel class now features Opening and Opened events that can be used to run code right before and right after the associated view has loaded in the UI.
* Fixed a binding problem in PasswordBoxEx.Value which caused passwords to not bind correctly in some scenarios.
* Loading performance of standard views and view models with images in large lists (especially in Metro) has been greatly improved. (See also: [Fast loading of Standard ViewModels with image resources](Fast%20loading%20of%20Standard%20ViewModels%20with%20image%20resources))
* This build includes the capability to generically define columns on all kinds of lists (like listboxes). All standard themes include styles that take advantage of that information. (See also: [Multi-Column Lists](Multi-Column%20Lists))
* The Battleship (Windows 95) theme now supports more meaningful standard view templates (See also: [Standard Views and View Models](Standard%20Views%20and%20View%20Models))

## 4.0.21017.0

* Primary/Secondary layouts now have -NoColor variants that perform only layout but do not paint any background colors.
* Metro listboxes now have "stack" variants that use a top-to-bottom stack approach (like conventional listboxes in Windows) rather than the flow approach
* Added a OneToManyPanel to automatically lay out UIs that show one-to-many type of elements (with headers added automatically as an optional feature)
* Added a few additional standard views. Simple data, Text05, PeekImageAndText05, LargeSmallImageAndText06, LargeSmallImageAndText07 (See also: [Standard Views and View Models](Standard%20Views%20and%20View%20Models))
* Fixed some typos in names for standard icons (see also: [Standard Icon Resources](Standard%20Icon%20Resources))
* All Metro Tile layouts now support optional captions for each group.
* Added an InvalidateAllActions() method to the ViewModel class (thus also available in StandardViewModel) which forces all the actions in the Actions collection to re-evaluate their canExecute() methods.
* Added an SelectedItemsCollection attached property to the ListBoxEx class that can bind to multiple selected items on listboxes.
* Added a Metro style for rich text boxes
* Standard View and Standard View Models now support Tool Tips
* The WPF MVVM Shell now has SelectedNormalViewResult and SelectedTopLevelViewResult properties to make it easier to create a style that binds to the selected view only, rather than a list of all views.
* The ActionItemsControl class now respects view-action availability (security) as well as view-action order.
* The automatic EditForm layout template/class/style for WPF had a resize and performance bug, which has been fixed.
* Fixed and performance tweaked various borderline cases in the EditForm panel
* The EditForm panel can now render group backgrounds/borders (see [Automatic Layout and Elasticity](Automatic%20Layout%20and%20Elasticity) for more info)
* Disabled, unchecked checkboxes in Metro style apps had a display problem before, which has now been fixed.
* Added a simplified way to set the margin of items within metro-style listboxes by introducing a Metro-ListBoxItem-ContentMargin resource that can simply be redefned in projects to define the spacing (default is 0). This only applies to listboxes without selection capabilities (those with selection already had this ability before). See Metro-Control-ListBox.xaml for details.
* Standard ViewActions now do not trigger their execute delegate anymore if their Availability isn't set to "Available". This prevents creation of custom skins that circumvent security setup for view actions.
* TextBoxes now support a SelectOnEntry property (See also: [Select WPF TextBoxes on Entry](Select%20WPF%20TextBoxes%20on%20Entry))

## 4.0.20704.0

* Added drastic improvements to the EditForm layout panel (and the standard edit form styles), including elastic layout and more.
* Metro ComboBoxes now show items correctly even when no item template is set.
* The Metro components now feature a ProgressRing in addition to CircularProgressAnimation for better compatibility with WinRT. For the same reason, the control now also features an IsActive property in addition to the start method. (See also [Progress Animations](Progress%20Animations))
* The Framework now supports a Metro-style linear progress animation. (See also [Progress Animations](Progress%20Animations))
* The WPF GridPrimarySecondary element has been improved to support visibility changes in contained elements.
* WPF bi-directional stack panels now support filling the available space with one of the items. (See also [Bidirectional Stack Panel](Bidirectional%20Stack%20Panel)).
* WPF View (and SimpleView) objects now support group headers and column headers as attached properties that can be set on arbitrary objects in conjunction with group breaks and column breaks.
* WPF Views now allow setting where the default keyboard focus goes upon view loading.
* Tuned WPF auto-complete features to ensure the auto-complete drop down opens and closes properly in all cases. Also made the feature a bit more tolerant to 'odd' adorner decorator scenarios people may have styled into their apps. (See also [Auto-Complete in WPF UIs](Auto%20Complete%20in%20WPF%20UIs))
* Added new Metro-ListBoxItem-WithSelection style for Metro listboxes. (See also [Metro Listboxes in WPF](Metro%20Listboxes%20in%20WPF) and [Transparent ListBoxes in WPF Metro-style Apps](Transparent%20ListBoxes%20in%20WPF%20Metro-style%20Apps))
* Changed view action user role checking to an async and bindable mechanism.
* Added the ability to pass custom actions and a custom view model to Controller.Message(). (For a complete overview of message box scenarios, see [MessageBoxes in WPF MVVM/MVC](MessageBoxes%20in%20WPF%20MVVM%20MVC))
* Message box resulting queueing features ("mocking") have been officially released as part of this build. (see [MessageBoxes in WPF MVVM/MVC](MessageBoxes%20in%20WPF%20MVVM%20MVC))
* The standard ViewAction class now has additional optional constructor parameters for more convenient instantiation of this object.
* There now is a standardized Status (Bar) feature. (See also [WPF MVVM/MVC Status Bar](WPF%20MVVM%20MVC%20Status%20Bar))
* The WPF MVVM/MVC Framework now features a standard implementation of a view model called StandardViewModel (based on IStandardViewModel)
* There now is a standardized Notification feature. (See also [WPF MVVM/MVC Notifications](WPF%20MVVM%20MVC%20Notifications))
* Adjustments have been made to the way views and start tile screens are positioned in Metro apps (see also: [Metro-Style Control Margins](Metro%20Style%20Control%20Margins)).
* The WPF framework now supports a standard set of icons of various styles (see also: [Standard Icon Resources](Standard%20Icon%20Resources))
* Default tile sizes in Metro have been brought up to date with the Windows 8 standard for tiles (150x150 and 310x150 pixels).
* Added a large set of standard views which can be used to visualize standard view models. (See also: [Standard Views and View Models](Standard%20Views%20and%20View%20Models) and [Metro Standard Views and Templates](Metro%20Standard%20Views%20and%20Templates)).
* Various minor style/theme changes in both Metro and Battleship themes
* The debug view visualizer's default size is now larger.

## 4.0.20525.0

* Enhanvements in the WPF framework such as improved message box support and many others
* Some minor fixes and tweaks in the WPF framework, such as setting password box data binidng to two-way by default
* Updates to the Metro Theme for WPF applications
	* Includes updates for improved view hosting
* Updates to the Battleship Theme for WPF applications
* Improved async worker class (including support for continuious, interval-based background tasks)
* Various small improvements to the WPF MVVM/MVC framework (such as exposing new events (example: closing) for view models)
* Some minor tweaks in the SOA and general utilities and tools components
* WPF Theme Resource Dicationaries (XAML files) are now provided as a separate source download in addition to the full source download. These files are the same that are also in the full source but are now provided as broken out duplicates for simple access. This should be especially useful for graphics artists. (See also: [Multi-Column Lists](Multi-Column%20Lists))

### Prior Versions
For prior versions, change logs are not available on Codeplex. If you have any specific questions about prior changes, please contact us.
