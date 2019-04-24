# Input Validation

As discussed previously, you can now use standard .NET data annotation attributes to add input validation to view-model properties. For instance, you can have a view-model like this:

```cs
public class EditViewModel : ViewModel
{
    [Required]
    public string FirstName { get; set; }
    
    [Required]
    public string LastName { get; set; }
    
    public string Company { get; set; }
    public string Division { get; set; }
}
```

In this case, two properties are fundamentally flagged as required by means of a validation attribute (these are found in the System.ComponentModel.DataAnnotations namespace of the .NET Framework). By themselves, they do not do anything, but these attributes can be used in various ways. For instance, we could add a method in the view model that checks whether the model is valid:

```cs
public void Validate()
{
    var result = InputValidation.ValidateObject(this);

    if (!result.IsValid)
        foreach (var properties in result.InvalidProperties)
            foreach (var message in properties.ErrorMessages)
                Controller.Message(message);
}
```

The ValidateObject() method inspects an object for all properties with validation attributes and then performs the validation defined in these attributes. If any of them fail, IsValid is false. In that case, a list of all invalid properties can be used to find out what is wrong. Each property with validation errors specifies all errors that occurred (there could be more than one validation attribute that indicated validation errors).

Note that the ValidateObject() method does not go through object hierarchies. For instance, if you had a InvoiceViewModel object, it would NOT automatically iterate over all line items. However, it is easy to add this on your own:

```cs
protected ValidationResults Validate()
{
    var results = InputValidation.ValidateObject(this);
    foreach (var lineItem in LineItems)
        InputValidation.ValidateObject(lineItem, results);
    return results;
}
```

There are quite a few validation attributes the .NET Framework defines. This will cover a very wide range of needs, ranging from simply validation such as the [Required] attribute, to sophisticated [RegularExpression] validation patterns, and even specialized attributes, such as [Phone]. In addition, it is possible to create custom validation attributes. Here is an example:

```cs
public class NotEmptyOnSundayAttribute : ValidationAttribute
{
    public override bool IsValid(object value)
    {
        if (DateTime.Today.DayOfWeek != DayOfWeek.Sunday) return true;
        return !string.IsNullOrEmpty(value.ToString());
    }
}
```

This special rule doesn’t allow empty values on Sundays. It can be applied to a property like so:

```cs
[NotEmptyOnSunday]
public string LastName { get; set; }
```

Note that it is perfectly acceptable, and indeed common, to have multiple validation attributes on a single property:

```cs
[MinLength(10, ErrorMessage = "Please provide at least 10 characters for a description.")]
[MaxLength(50, ErrorMessage = "Please provide no more than 50 characters for a description.")]
public string Description { get; set; }
```

It is of course also important to have the UI layer respect these rules. Therefore, we can now bind all validation attributes to UI elements in the following fashion:

```xml
<TextBox Text="{Binding LastName}"
         val:InputValidation.ValidationAttributes="{ex:AttributeValidationBinding LastName}"/>
```

However, there is an even simpler way. Instead of binding the text and the attributes separately, we can use the new CODE Framework binding syntax:

```xml
<TextBox Text="{cf:Bind LastName}"/>
```