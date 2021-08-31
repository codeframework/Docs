# Standard Views and View Models

In prior articles, I have shown how to create WPF-based applications using the CODE Framework and the MVVM and MVC patterns. This enabled developers to create quality applications quickly and in a fashion that can easily be understood by developers of all skill levels. In those articles I showed how to use view-models and views to create UIs. In this article, I am going to take this concept further by showing you how you do not even have to create new views and view-models, but instead can use the ones CODE Framework defines for you out of the box.

When developing applications using the MVVM pattern in any development environment, the basic idea remains the same: The actual “view” of the user interface (the visible part, such as controls and windows, and so forth) is developed separately from the “view-model”, which is the part that contains the logic and the data for the view. The two parts bound together by some kind of coupling mechanism. There are a number of reasons why this is appealing, one of which being a simplification of the development task. 

Not all MVVM setups necessarily exhibit this particular attribute, but as the Chief Software Architect in charge of the architecture used by CODE Framework, I like to believe that we have achieved this goal. We quite frequently hear from developers who are going out of their way to thank us for “bringing sanity back to WPF development” by making MVVM as simple as it should be. (Note that CODE Framework isn’t just a WPF technology and you can use the concepts in the framework and described in this series of articles in a number of other environments such as Windows Phone and WinRT). 

But there is more to this story! Not only do the CODE Framework UI development features aim to enable developers to create high-quality and highly reusable UIs in an easy and straightforward fashion, but the CODE Framework also aims to create ready-to-go components out of the box. Providing useful UI components and features as a standard part of the framework is a pattern we repeat over and over in numerous ways. In previous articles we have explored standard themes, standard controls, and quite a few other standard elements. This time around, we are going to venture into the world of standard views and standard view-models.

## Introducing Standard Views and View-Models

When creating an MVVM application, the first step developers usually take is the creation of a view and a view-model that fits one’s needs. For instance, when creating a customer edit UI, one could create a view that shows textboxes and labels and a whole set of other controls that for the basis of the edit experience. In addition, the view-model defines the logical parts and the data that goes with that. Each UI control has a matching property in the view model to bind to (for instance, the first-name textbox binds to a FirstName property on the view-model, the company textbox to the CompanyName property, and so forth…). The view-model also contains logic that goes along with editing a customer. Perhaps a rule is that a customer always has to provide either a last name or a company name to be valid, and the view-model can enforce that rule in one way or another.

In short: The view and the view-model created for customer editing are highly customized and specific to the scenario at hand, which is editing customer information. To implement such a scenario, developers always have to craft a custom user interface and custom logic. However, not all scenarios are like that. Consider the need to display a searchable list of customers (practically all edit UIs also have the need to search the entity they edit and provide a list). In that case, the view needs to expose search UI (perhaps a textbox and a “Search” button) and it also needs to display a list of customers as the search results. This list shows data such as the customer name or addresses and more. At first glance, this appears just as unique as editing a customer, but is it really?

For the customer search and list scenario, I would argue that it isn’t very unique. Admittedly, the list shows some unique data such as first name and last name and so on, but looking at the scenario from a slightly different angle, we need a list that displays several data elements (all of which can arguably be expressed as text) and ideally do so in a fashion that is consistent with the rest of our application. If we were to think outside the box for a moment (or should I say “outside our rectangular data structures”?), we could also look at this scenario as displaying a list of items with several text fields and perhaps an image for the customer. Looking at it from this point of view, at least a very considerable portion of our view-model (the list portion) becomes fairly mundane and reusable. We simply need a way to implement the ability to display several text elements and we can then reuse the same sort of setup across large parts of our applications. And this is exactly what CODE Framework standard views and view-models do!

It is also interesting that once you start thinking along the lines of these ideas, you realize that even our customer edit scenario isn’t quite as unique as we may have thought at first. For instance, the customer edit UI may call for a list of email addresses or a list of phone numbers. Do those really require custom setups? I would argue that there are at least parts that are fairly generic. I can imagine displaying a list of phone numbers where each item shows a phone icon and a text element displaying the phone number. Again, we could use a standard setup to express that part of the application.

At this point you may say “OK, I can see how some parts of my application can benefit from this, but I still have a lot of custom aspects”, and that is fine! A key design idea of CODE Framework custom views and view-models is to provide ready-to-go features where needed, but allow developers to easily supplement those features with their own ideas.

## A Customer Example

To illustrate these fundamental ideas, let’s create a simple customer search form. To start out, create the same customer search example we have used in prior articles (see article “CODE Framework: Writing MVVM/MVC Applications”). Create a new view called “Search” and a view-model called “SearchViewModel”. (If you do not have CODE Framework installed, see sidebar on how to download the framework). The new view-model needs to support the search UI as well as list UI exposed by the view, as well as the code that actually searches for customers. Conceptually, the following simple view-model fits the bill:

```cs
public class CustomerViewModel : ViewModel
{
    public string SearchString { get; set; }
    public ObservableCollection<CustomerInfo> Customers { get; set; }
}
```

This provides a text property for the search string, which we can bind to a textbox in the UI allowing the user to enter the desired search term. (Again, if you are unclear about these concepts, I encourage you to read my earlier articles in this series). Once the user enters the search term, the collection of customers is with appropriate results retrieved from a service or a database. In this example, I am assuming that there is a “CustomerInfo” class which will presumably have properties such as `FirstName`, `LastName`, `CompanyName`, and so forth. Since we do not have this class yet, our next step would be to go out and create this class and add a whole bunch of properties to it, like so:

```cs
public class CustomerInfo : ViewModel
{
    private string _firstName;
    public string FirstName
    {
        get { return _firstName; }
        set
        {
            _firstName = value;
            NotifyChanged("FirstName");
        }
    }
 
    // Repeat the above property definition for each needed property
}
```

This is a relatively simple task, but it is also quite tedious and sometimes even error prone (the above example only shows a single property, but a real-world implementation would have many more). This class doesn’t do anything particularly interesting. It’s just a verbose way to express the structure of the data we expect in the list of customers. Once we have this class all defined, we can use it to populate our collection with search results. Here’s a simple that shows the most important elements needed to accomplish this task:

```cs
private void Search()
{
    // TODO: Search based on the text in SearchString
 
    Customers.Clear();
    
    foreach (var result in results)
    {
        Customers.Add(new CustomerInfo
        {
            FirstName = result.FirstName,
            LastName = result.LastName,
            CompanyName = result.Company
            // More fields to go here…
        });
    }
}
```

We now presumably have a list of search results represented by our CustomerInfo class and this works fine. But it also is quite a bit of work that really doesn’t provide a whole lot of benefit in this scenario. This is where standard view-models come into play. Instead of creating a brand new class for customer information, we can use our standardized model like so:

```cs
public class CustomerViewModel2 : ViewModel
{
    public string SearchString { get; set; }
    public ObservableCollection<StandardViewModel> Customers { get; set; }
}
```

Instead of a special CustomerInfo class, we now simply use the `StandardViewModel` class provided by the CODE Framework. The main difference here is that the `StandardViewModel` class is not aware of customer-specific information such as “first name” or “company”. Instead, the StandardViewModel class simply calls these things `Text1`, `Text2`, and so forth. With this in mind, we can now re-write our search method:

```cs
private void Search()
{
    // TODO: Search based on the text in SearchString
 
    Customers.Clear();
    foreach (var result in results)
    {
        Customers.Add(new StandardViewModel
        {
            Text1 = result.FirstName + " " +
                    result.LastName,
            Text2 = result.Company
        });
    }
}
```

At this point, some developers may point out that using properties such as `Text1` instead of `FirstName` makes them feel a bit dirty. It simply goes against the believes most passionate developers have. “The FirstName data element should be named appropriately” they say. And that is an interesting point. It is a bit of a trade-off. However, if this small and functionally completely irrelevant trade-off can provide massive benefits, then it is probably a trade-off worth making. (Plus, we will talk about overcoming this stylistic problem below). 

The `StandardViewModel` (which is simply an implementation of the IStandardViewModel interface, which represents the true basis of standard view-models) implements a number of different properties. It provides ten different text properties (`Text1` through `Text10`), two string identifier properties (`Identifier1` and `Identifier2`) which are generally used to keep track of data that is not displayed to the user such as keys, two numeric properties (`Number1` and `Number2`), a tool-tip property (`ToolTipText`), five properties for images (`Image1` through `Image5`) which are expressed as brushes and can be used to display all kinds of images such as photos, and two logo properties (`Logo1` and `Logo2`) which are also brushes and can be used for image display as well. (The keen observer may notice that this also happens to map quite closely to data structures used for standard notifications and tiles and similar things in Windows8/WinRT, and that keen observer would be right. This is no coincidence as we aim to keep these features compatible across different scenarios.)

Some of the features provided by the standard view-model architecture are quite interesting. For instance, we just gained a relatively simple approach for attaching images or graphics to our customers. If our search results included a customer photo for instance, we could have simply attached that to the Image1 property. Similarly, we could have used one of the standard icon/graphics resources that ship with CODE Framework (or art we created on our own for that matter) and attached those assets as images. If you have art assets stored as standard XAML resources, you could retrieve those resources from the resource system and attach them. If this statement just made you scratch your head, fret not! The StandardViewModel class makes that task easy as well by providing a GetBrushFromResource() method. Here’s an updated version of the search method that takes advantage of this capability:

```cs
private void Search()
{
    // TODO: Search based on the text in SearchString
 
    Customers.Clear();
    foreach (var result in results)
    {
        var customer = new StandardViewModel
        {
            Text1 = result.FirstName + " " + result.LastName,
            Text2 = result.Company
        });
        customer.Image1 = customer.GetBrushFromResource("CODE.Framework-Icon-ContactInfo");
        Customers.Add(customer);
    }
}
```

Note that this uses one of the standard icons provided by CODE Framework. Each CODE Framework theme supports a standardized list of graphical resources. The icon specified in this example will be different for an app that runs the Metro theme vs. an app that runs a Windows 95 theme, and so forth. Each theme is responsible for producing the requested icon in a style that is consistent with the overall theme. (The exact list of available standard icons can be found [here](/Themes-Standard-Icon-Resources)).
