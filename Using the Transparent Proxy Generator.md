# Using the Transparent Proxy Generator

Some operations require what is called a "transparent proxy". For instance, when you make a WCF service call using a channel factory, you specify that you want to make a call to a service that conforms with a certain interface. WCF then gives you **a real object** that implements that interface. But where did that object/class come from? It doesn't exist as a real class that is part of your project. Instead, WCF generated it on the fly. That is what is called a "transparent proxy". CODE Framework also uses transparent proxies. One such scenario is when making a strongly typed call to a REST service. (See also: [Calling REST Services through ServiceClient](Calling%20REST%20Services%20through%20ServiceClient).

Creating transparent proxies is not a trivial matter. Code has to be generated and compiled on the fly to make this happen. CODE Framework has a TransparentProxyGenerator class that does this. It takes an interface and then generates a live class from it. This new class doesn't do much on its own. Instead, it takes a reference to a handler class. It is very generic and can be used for other things.

Let's take a look at a simple example: Let's say we have this interface:

```c#
public interface ITest
{
    Customer GetCustomer(GetCustomerRequest request);
    SaveResult SaveCustomer(Customer customerToSave);
}
```

Using the transparent proxy generator, you can create an implementation of this class on the fly like this:

```c#
var implementation = TransparentProxyGenerator.GetProxy<ITest>(handler);
var customer = implementation.GetCustomer(new GetCustomerRequest());
```

One of the key details here is the "handler" parameter. The transparent proxy generator creates a class that satisfies the requirements of the interface, but it can't know what to do within the methods it creates. This is instead handled by a handler class. For instance, if all you wanted to do is create a class that echos something to the output window whenever a method is called, you could do this:

```c#
public class MyProxyHandler : IProxyHandler
{
    public object OnMethod(MethodInfo method, object[]() args)
    {
        Console.WriteLine("Method called: " + method.Name);
        return Activator.CreateInstance(method.ReturnType);
    }
}
```

The handler object has a single method called "OnMethod" that is called whenever any method in the proxy is called. The OnMethod method receives information about the actual method as its first parameter, and the actual parameters of the method call as an object array. You can use this information to perform whatever task needs to be performed (in the REST case implemented by CODE Framework, this method is used to perform the actual REST call). 

Note that the method absolutely MUST return an object that conforms with the called method. This example satisfies this criteria by creating a new instance of the expected method return type. Your specific implementation may have other needs. (In the REST case, the return value is deserialized from JSON for instance).