# Standard View Model Tips

CODE Framework features a concept called "Standard View-Models". For a detailed discussion on the concept, see this article: http://www.codemag.com/Article/1301041

In addition, here are some tips that are not covered by this article:

For one, standard view models include a boolean IsChecked property, which can be used for things such as indicate selection or checked state.

Another aspect is that there now is a new standard subclass of StandardViewModel called StandardViewModel<TData>. This new class allows attaching a data object to a standard view model without having to subclass. Let’s say you have a UI that shows a list of customers and you want to use a StandardViewModel to show it, but you may also need to keep each customer record around. You can do this like so:

```c#
var x = new StandardViewModel<CustomerInformation> 
{ 
    Key1 = customer.Id, 
    Text1 = customer.Name, 
    Data = customer 
};
```

So this can be real convenient as you now don’t have to create a subclass of the regular StandardViewModel, just to have a way to preserve the original customer object.
