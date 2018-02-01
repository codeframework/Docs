# AsyncWorker Part 2

_Excerpt from an internal email at EPS/CODE:_

We have been working on performance improving a client application. In that, there are a few things I noticed and a few things I added to the framework to make some performance things easier. Here are a few thoughts:

When loading data initially, we have code in a view model like this:

![](AsnycWorker%20Part%202/AsyncWorker%20Part%202_clip_image001_2.jpg)

So this uses the AsyncWorker to load on a background thread (which is good) and it then loads Drivers, Loaders, and Checkers one after the other on the background thread. What is interesting to note here is that it first loads Drivers from the service, waits for the result, then loads Lo!aders and waits for the result, and then loads Checkers and waits for the result, before passing it all back to the foreground method. It’s good that this happens on a background thread, so the UI is not blocked while all of this loading is going on. However, what is bad is that these 3 load operations happen sequentially. Lots of waiting with a non-busy CPU for no particular reason. What would be better is if these 3 load operations all happened at the same time on 3 different background threads. A simple change would thus be this:

[![clip_image002](http://download-codeplex.sec.s-msft.com/Download?ProjectName=codeframework&DownloadId=743759 "clip_image002")](http://download-codeplex.sec.s-msft.com/Download?ProjectName=codeframework&DownloadId=743758)

As you can see, I changed a bunch of other stuff too (rather than calling the service directly, I made methods like GetDriversFromService, and so on), but that is not of significance for the specific point I am trying to make here. What is more important is that I call AsyncWorker.Execute() 3 individual times, which means that the 3 load operations can now all happen at the same time. So this cuts down on the load time quite a bit.

In this particular case, the 3 calls all load individually and independently. Whenever they are finished, the data gets added to the collections in the view model and then they start showing up in the UI they are bound to. Pretty straightforward. Each of them could be done at drastically different times, and that is fine in this case. Whenever they are done, they show up.

There are other cases however where you may need things to work a bit different. You may want to run several different load operations and once they are ALL completed, you may want to update the collections that are bound to the UI. (Example: If you are loading invoice and line items, you only want to refresh the UI once you are done loading both. You wouldn’t want the line items to finish first and thus show up in the UI while you are still showing a different invoice header). To make this a bit easier, we have added a few overloads to AsyncWorker.Execute that allows running multiple worker threads at once and then a single complete method is called and receives the result from all the workers. Here’s an example:

[![clip_image003](http://download-codeplex.sec.s-msft.com/Download?ProjectName=codeframework&DownloadId=743761 "clip_image003")](http://download-codeplex.sec.s-msft.com/Download?ProjectName=codeframework&DownloadId=743760)

I am now passing 3 different delegates in to perform the work (loading data from services). All three of these will independently run on their own threads. Once ALL 3 of them have completed, the delegate passed as the 4<sup>th</sup> parameter will execute, and it will receive the return value of the first 3 delegates as the parameters for the complete delegate. So as a result, now all 3 load operations run in parallel. Then, when they are all complete, the result is passed to the final delegate and the data is put into the collections from where it will automatically be bound into the UI.

Note: In the example above, the first 3 delegates are pointing to other methods. This is just a coincidence here. You can of course still define these methods inline like this:

[![clip_image004](http://download-codeplex.sec.s-msft.com/Download?ProjectName=codeframework&DownloadId=743763 "clip_image004")](http://download-codeplex.sec.s-msft.com/Download?ProjectName=codeframework&DownloadId=743762)

There are several different overloads, and it is currently possible to do this with up to 5 background tasks at the same time.
