# Validating Data

When creating business objects and business entities, one needs to worry about validity of the data that is being stored and modified. This How-To assumes that you have already created a business object and a business entity, as described in the HowTo_CreateBusinessObject and HowTo_CreateBusinessEntityObject documents. 

## Adding Simple Rules

We can easily enhance the business object we already created to verify the data's validity. In this example, we assume that we do not want the ```ProductName``` field to be empty. We can achieve this by modifying the product business object in the following fashion:

```cs
public class ProductBusinessObject : Milos.BusinessObjects.BusinessObject 
{
    protected override void Configure()
    {
        this.MasterEntity = "products";
        this.PrimaryKeyField = "product_pk";
        this.BusinessRules.AddRequiredField("ProductName", "products");
    }
}
```

The ```BusinessRules``` collection is the simplest way to define business rules that apply to the data handled by a specific business object. In this case, we used the collection to add a rule that prevents the product name field from being empty. If we attempted to use this business object to save data that contained an empty, the ```Save()``` method would perform a rule check, realize that the data is invalid, and it would then refuse to save the invalid data.

> Note: Business objects have ```AllowSaveWithViolations``` and ```AllowSaveWithWarnings``` properties. If those are set to true, the business object would allow saving data with problems. However, those properties should not be modified unless approved by a supervisor.

## Adding Standard Rules

The above example shows how to flag a field as required, which is the simplest form of adding a rule, which only applies for that standardized scenario. Internally, this is actually achieved through a special rule object. There are several such rule objects included in Milos (and others can be added... see below).

The following is a short list of "out of the box" rules.

> Note: All standard business rules can be found in the EPS.Business.BusinessObjects.Rules namespace.

### Required Field Rule

The above example of indicating a field to be required is a perfectly valid way to indicate that rule. The same can be achieved in the following (equally valid) fashion:

```cs
BusinessRules.Add(new EmptyFieldBusinessRule("ProductName", "Products", "Product name required!"));
```

Note that the ```EmptyFieldBusinessRule``` class is somewhat of a special case in that the first parameter can be a comma-separated list of fields, rather than just a single field. This is unique to this class and has been done for performance reasons.

### Greater Than Rule

The following example indicates that a certain field must be greater than a certain value:

```cs
BusinessRules.Add(new GreaterThanBusinessRule("Price", "Products", 0, "Price must be greater than 0!"));
```

Note that this works with any comparable data type (any type that implements ```IComparable```). The following is valid as well:

```cs
BusinessRules.Add(new GreaterThanBusinessRule("Release", "Product", DateTime.Now, "Release date must be in the future"));
```

This is also possible:

```cs
BusinessRules.Add(new GreaterThanBusinessRule("MtoZNames", "Product", "N", "Name must be greater than N"));
```

Note that it is important that the type of the verified field is the same as the type one compares to. For instance, it would not be possible to compare a decimal price to a date, even though both objects implement ```IComparable```. Similarly, it is not possible to compare an integer to a decimal, and so on.

### Less Than Rule

This is the opposite of the greater-than rule. Here is an example:

```cs
BusinessRules.Add(new LessThanBusinessRule("Date", "Incident", DateTime?.Now, "Incidents can only occur in the past!"));
```

### Field Uniqueness (within an entity) Rule

Sometimes it is necessary to verify that a certain value is unique within a business entity or a DataSet. This can be done with a unique field business rule. Here is an example:

```cs
BusinessRules.Add(new UniqueFieldInEntityBusinessRule("Index","Segments","The segment index must be unique within each item!"));
```

Note that this only verifies uniqueness within each entity or ```DataSet```. It does not check for uniqueness compared to other data that may exist in unrelated entities.

Example: This can check to make sure your line items collection has unique values within an invoice, but it can not check if other invoices use the same value.

## Checking for Broken Rules

When updating data, one usually uses a business entity object. Using business entities, it is very easy to check for broken business rules using the business entitie's ```BrokenRules``` collection. Consider the following example:

```cs
var productEntity = ProductBusinessEntity.LoadEntity("..."); 

// The following line is a problem, since the Product property maps to the ProductName field
productEntity.Product = string.Empty;
productEntity.Verify();

if (productEntity.BrokenRules.Count > 0)
{
   // There are broken rules
   MessageBox.Show(productEntity.BrokenRules[0].Message); 
}
```

Note: The ```Verify()``` method should be called before one uses the ```BrokenRules``` collection. Business entities do not constantly check rules (for performance reasons), which means that the broken rules collection can be outdated if the ```Verify()``` method is not called. However, data is automatically verified during a ```Save()``` operation, so the ```BrokenRules``` collection can be used immediately after a ```Save()``` as well.

Each item in the ```BrokenRules``` collection has detailed information about the rule violation, including a message, the name of the rule class that detected the problem, the severity of the problem, and even a reference to the data row that caused the rule violation.

Of course, developers can also check for broken business rules inside the business object (rather than the business entity). The business object has methods such as ```DataSetHasRuleViolations()``` that indicate whether violations occured.

## Checking for Broken Rules within a Sub-Item Collection

The ```BrokenRules``` collection of the main business entity contains all errors that apply to the entity. This includes errors on the main entity as well as sub-item collections. However, whenever one wants to check for errors on a specific collection, one can use the local ```BrokenRoles``` collection:

```cs
productEntity.Parts.BrokenRules.Count;
```

This collection contains the errors for that particular collection only (Parts in this example).

It is also possible to go a step further and check broken rules for a specific item within the collection:

```cs
productEntity.Parts[3].BrokenRules.Count;
```

In this example, we access the count of broken rules for part number 4 (index 3).

## Getting a Description of All Broken Rules

It is often necessary to get a simple text representation of all broken rules. The ```GetAllViolations()``` method makes this task easy. It provides a simple text representation of all rules. Various parameters that can be passed to this method (optionally) allow for modifications of the exact format, such as whether the table name is to be included, and how different violations are to be separated (default is separation by line feeds).

There also is a similar method called ```GetAllViolationsHtml()```, which returns the same information, but formated as an HTML segment.

## Adding Custom Rules

The default rules supported by Milos are very useful and used a lot. However, all the more sophisticated rules that really define an application have to be created by the developer as they are completely specific to each application. For instance, we may want to add a fictional rule to our business object that makes sure the product name isn't "Microsoft Windows". We could do this in the following fashion:

```cs
public class OurSpecialRule : BusinessRule
{
   public OurSpecialRule() : base ("products", RuleViolationType.Violation)
   {}

   public override void VerifyRow(DataRow currentRow, int rowIndex)
   {
      if (currentRow["ProductName"].ToString() == "Microsoft Windows")
        LogBusinessRuleViolation(currentRow, rowIndex, "ProductName", "Product name can not be 'Microsoft Windows'");
   }
}
```

All business rules have to call the constructor with the name of the table this rule applies to and the severity of the rule violation passed as parameters. In this example, I hard-coded these names since the rule I made up is so specific, but this of course could also have been passed as parameters of the constructor of the current class. The right way depends entirely on the particular need.

The important part of a rule object is the ```VerifyRow()``` method. This method is called for every row in the table this rule applies to. The code inside this method inspects the row and applies any number of rules to the row. When a rule violation is detected, we use the ```LogBusinessRuleViolation()``` method to log that a violated rule has been detected.

Now that we have this new business rule, we can add it to our business object like so:

```cs
public class ProductBusinessObject : Milos.BusinessObjects.BusinessObject 
{
    protected override void Configure()
    {
        MasterEntity = "products";
        PrimaryKeyField = "product_pk";
        BusinessRules.AddRequiredField("ProductName", "products");
        BusinessRules.Add(new OurSpecialRule());
    }
} 
```

> Note: Each business object can have an unlimited number of business rules. Each business rule can also check for any number of conditions. This allows for great flexibility in how developers want to structure their rule checking. Note however, that it is considered "better style" to not overload one particular rule object with a great number of different rules that are not logically connected.

> Note: Theoretically, business rule checks can also be performed by overriding the ```Verify()``` method of a business object. However, this is an advanced operation that only yields advantages in very special cases. In short: Overriding the ```Verify()``` method of a business object is not recommended.

## Overriding Verify()

In Milos, every business object has a ```Verify()``` method. This method is what triggers rule checking. It is possible to simply override ```Verify()```, rather than defining or implementing specific business rules. However, this is only recommended for developers who are familiar with how the ```Verify()``` method works and what it does.