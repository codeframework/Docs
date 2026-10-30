# Using the Transparent Proxy Generator

`TransparentProxyGenerator`, in `CODE.Framework.Fundamentals.Utilities`, creates an object implementing a supplied interface at runtime. It emits a .NET type using Reflection.Emit and forwards each method call to an `IProxyHandler`. The handler supplies the operation's behavior and return value.

CODE Framework uses this utility for [interface-based REST calls](Calling-REST-Services-through-ServiceClient), but the generator itself is independent of HTTP, service contracts, and the single-request-message service model. It can dispatch ordinary interface methods with multiple parameters as well.

This article describes the active .NET Standard implementation, including the pending proxy cache and return-conversion corrections. Older full-framework implementations can have different cache behavior.

## Create a Proxy and Handler

For example, define a public interface and a handler:

```csharp
using System;
using System.Reflection;
using CODE.Framework.Fundamentals.Utilities;

public interface IGreeter
{
    string Greet(string name);
}

public class GreetingHandler : IProxyHandler
{
    private readonly string _prefix;

    public GreetingHandler(string prefix) => _prefix = prefix;

    public object OnMethod(MethodInfo method, object[] args)
    {
        if (method.Name != nameof(IGreeter.Greet))
            throw new NotSupportedException(method.Name);
        return _prefix + (string)args[0];
    }
}
```

Create a proxy and call it through the interface:

```csharp
var greeter = TransparentProxyGenerator.GetProxy<IGreeter>(
    new GreetingHandler("Hello, "));
Console.WriteLine(greeter.Greet("World"));
```

The nongeneric overload, `GetProxy(Type proxyType, IProxyHandler handler, bool useProxyCache = true)`, accepts a runtime interface type and returns the proxy as `object`. Both overloads require an interface. Use publicly accessible interfaces and types so the emitted assembly can implement and reference them.

## Dispatch and Return Values

`OnMethod(MethodInfo method, object[] args)` receives the invoked interface method and an array of arguments in parameter order. Value-type arguments are boxed. The handler must return a value compatible with the declared return type:

| Declared return | Handler result |
| --- | --- |
| Reference type | A compatible instance, or `null` when that result is meaningful for the caller. Incompatible instances fail with `InvalidCastException`. |
| Value type | A boxed value compatible with the required unboxing conversion, such as a boxed `int` for an `int` result. The generator does not perform general numeric conversions. |
| `void` | The handler's result is discarded. |
| `Task<T>` | An actual compatible task. The generated method returns that task directly; the generator does not await or serialize it. |

Exceptions thrown directly by the handler propagate through the generated method. For a task-returning method, later task faults are observed by awaiting the returned task. The generator does not add standard service failure conversion, retries, cancellation, or an async state machine; those behaviors belong to the handler or hosting layer.

Creating `Activator.CreateInstance(method.ReturnType)` is not a general implementation strategy: many return types have no suitable constructor, and a task result needs a real task supplied by the handler.

## Generated Types and Proxy Instances

With the pending cache correction, `useProxyCache: true` reuses the generated **type**, while every `GetProxy` call creates a new **instance** bound to its supplied handler:

```csharp
var first = TransparentProxyGenerator.GetProxy<IGreeter>(
    new GreetingHandler("Hello, "));
var second = TransparentProxyGenerator.GetProxy<IGreeter>(
    new GreetingHandler("Welcome, "));

Console.WriteLine(ReferenceEquals(first, second)); // False
Console.WriteLine(first.GetType() == second.GetType()); // True
Console.WriteLine(first.Greet("World")); // Hello, World
Console.WriteLine(second.Greet("World")); // Welcome, World
```

This prevents a cached proxy from accidentally retaining another caller's handler or REST endpoint. If an application needs to share one proxy instance, retain that instance explicitly and make its handler safe for the application's usage.

The generator serializes cache lookup and type generation for concurrent first use. This does not synchronize subsequent handler calls or make mutable handler state thread-safe. `ServiceGardenLocal` separately retains its registered service proxies; fresh proxy creation does not imply transient in-process service implementations.

## Cache Bypass and Clearing

`useProxyCache: false` generates a new type without reading or updating the cached type entry. It is not needed merely to bind a different handler.

```csharp
TransparentProxyGenerator.ClearProxyCache(typeof(IGreeter));
// Or remove all cached type entries:
TransparentProxyGenerator.ClearProxyCache();
```

Clearing the cache leaves existing instances usable. It removes entries rather than unloading the emitted types. The dynamic assembly is non-collectible; repeatedly bypassing or clearing the cache can accumulate generated types.

Service endpoint changes do not require regenerating types: endpoint configuration belongs to a handler instance. [ServiceClient Configuration and Caching](ServiceClient-Configuration-and-Caching) explains configuration invalidation, which does not clear the generated-type cache.

## Supported Shapes and Limits

The regression suite exercises ordinary reference, value, void, and task returns, handler isolation, concurrent first use, and argument preservation across the short argument-index boundary. The pending return-conversion fixes reject incompatible reference results at the proxy boundary.

These tests do not establish support for every C# interface shape or constitute formal IL verification. Generic methods, generic declaring interfaces, inherited interfaces, and by-reference signatures such as `ref`/`out` are outside the verified scope. Do not infer support for them from the generator's general-purpose role. REST handlers separately enforce their own operation shapes, including one request message and, for `CallAsync`, a `Task<TResponse>` return.
