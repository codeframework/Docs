# CODE Framework JSON Features

CODE Framework features a variety of features related to JSON. One of the most significant aspects is that CODE Framework includes a branched version of JSON.NET, which is available through the CODE.Framework.Core.Newtonsoft namespace. You will find all the usual JSON.NET classes in this namespace and can use them according to the JSON.NET documentation.

In addition, CODE Framework offers a JsonHelper class, which provides quick access to the most common JSON operations. For instance, one can serialize an object to JSON using the following code:

```c#
var json = JsonHelper.SerializeToRestJson(customer);
```

Similarly, it is possible to re-hydrate an object from JSON:

```c#
var customer = JsonHelper.DeserializeFromRestJson<Customer>(json):
```

JsonHelper also provides a quick way to format JSON in ways that are easy for humans to read:

```c#
var formattedJson = JsonHelper.Format(json);
```

There also are some nifty features to create JSON on a lower level. For instance JsonHelper.GetJsonNameValuePair() allows you to get a JSON compatible name/value pair. For instance, consider this call:

```c#
var json = JsonHelper.GetJsonNameValuePair(“MyNumber”, number);
```

This would return something like this:

```c#
"MyNumber":123.45
```

This in itself is not real JSON of course, but it is a JSON fragment, if you want to think of it that way. What is important is that this encodes everything correctly, since this uses JSON.NET under the hood. Therefore, something like this works just fine:

```c#
JsonHelper.GetJsonNameValuePair("Timestamp", DateTime.Now);
```

This is very useful if you “just need to create a bit of JSON data” that isn’t based on an actual object you have in memory. In fact, we can do one better, because there now is a new JsonBuilder object. It works very similar to a StringBuilder, but it builds JSON. For instance, you can do this:

```c#
var jb = new JsonBuilder();
jb.Append("Last User", System.Security.Principal.WindowsIdentity.GetCurrent().Name);
jb.Append("Timestamp", DateTime.Now);
var json = jb.ToString();
```

Which returns that:

```json
{
  "Last User":"WIN-HO44B8HH4N3\\SomeUser",
  "Timestamp":"2016-09-23T23:06:18.3865621-10:00"
}
```

As you can see, this is not the kind of information you can simply serialize an entire object for, since there isn’t a single object with these two properties (unless you specifically create an object with these two properties just for the purpose of later turning it into JSON). So being able to just do this is certainly useful.

_Note: As you can probably imagine, JsonBuilder uses JsonHelper.GetJsonNameValuePair() behind the scenes._

The question then is how one would actually use JSON like this later, since there is no easy way to deserialize it back into an object (since there is no object representing that structure). Well, I am glad you asked! We now have a simple parse method that does exactly this. You can hand it some JSON, and then have it call you back for every value it finds:

```c#
JsonHelper.QuickParse(json, (n, v) =>
{
    switch (n)
    {
        case "Last User":
            Console.WriteLine("Last user was " + v);
            break;
        case "Timestamp":
            Console.WriteLine("Last logged in at " + v);
            break;
    }
});
```

So that’s pretty neat, because this way, you can make sure that your parser works, no matter what kind of odd values are in the JSON (because, again, behind the scenes we use JSON.NET) or in what order they appear. 
