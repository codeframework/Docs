# Binding to Enums

_Excerpt from an internal email at EPS/CODE:_

FYI: Based on a suggestion from Antonio (and Mike), I have added a new feature to the CODE Framework. This has to do with the ability to bind to enum lists. Let’s say you have the following enum and want to display a drop-down list that displays all possible values:

```c#
public enum PaymentMethods
{
    BillMe,
    CreditCard,
    DirectDebit
}
```

So now if you want to display a drop down list with these 3 values, you typically need a property in your view model that exposes these 3 values as a list and you need to preserve the values as well as the name and potentially a separate display text. This can now be done easily. You can simply create a view model (in ASP.NET or WPF or anything, really) with this property:

```c#
public IEnumerable<EnumInformation> PaymentOptions
{
    get
    {
        return EnumHelper.GetEnumInformation<PaymentMethods>();
    }
}
```

And that’s it, really.

You can now bind to this in a variety of ways. For instance, in a WPF listbox, you could simply bind the items source to “{Binding PaymentOptions}”. Furthermore, you can then either create a data template or set display values and such to properties on the object in the list. Typically, the value field would be bound to the Value: “{Binding Value}”. The display would be bound either to the enum names or a more suitable display name. Setting the display template to this {Binding Name} shows the following items in the list:

```
BillMe
CreditCard
DirectDebit
```

Setting it to {Binding DisplayText} on the other hand, displays a usually more suitable list:

```
Bill Me
Credit Card
Direct Debit 
```

Note: The DisplayText property can be overridden manually if you feel like it.

Also, note that this is very fast, as the EnumHelper class is smart enough to cache known types. So repeatedly calling the GetEnumInformation() metod with the same type is fine as it will just serve up the cached values.

```c#
public enum PaymentMethods
{
    BillMe,
    CreditCard,
    DirectDebit
}
```