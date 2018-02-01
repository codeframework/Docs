# RedirectToActionWithData()

CODE Framework provides a RedirectToActionWithData() method for use in ASP.NET MVC apps. So you can now do this:

```c#
using System.Web.Mvc;
using CODE.Framework.Web.Mvc;

namespace ControllerExTest.Controllers
{
    public class TestController : ControllerEx
    {
        public ActionResult Index()
        {
            var model = new DummyModel
                            {
                                FirstName = "Markus",
                                LastName = "Egger",
                                Company = "CODE"
                            };
            return RedirectToActionWithData("Target", model);
        }

        public ActionResult Target(DummyModel model)
        {
            return View(model);
        }
    }

    public class DummyModel
    {
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public string Company { get; set; }
    }
}
```

This solves a problem that is somewhat frequently encountered in ASP.NET MVC, when a redirect to an action is required, but data/state needs to be preserved in the process. One possible way to solve this is to use TempData or Session State. While this works, it introduces questions around scalability (especially in web-farm scenarios). The solution presented here uses cookies. This means that this works well in web-farm scenarios, as there is no overhead on the server whatsoever (the state does however travel back and forth to the client, so there is some overhead in that and developers should be mindful of the amount of data they send back and forth… as is the case in all ASP.NET scenarios). 

The actual implementation of this is quite sophisticated. The cookie created is specific to the controller and method the redirect targets. This way, multiple redirect-with-data operations could be going on in the browser at the same time. Granted, the timeframe for this is very short, so it is very unlikely for this to happen, but it is still a possibility. The only scenario I could see where this is a real problem is if the same user in the same browser session manages to somehow trigger two absolutely identical redirect with data operations to the same controller and action. I don’t think it is really feasible to pull this off. Smile It is however important that we can differentiate between multiple different redirects-with-data, because it could in fact happen that a corrupt cookie hangs around for a little while, which could otherwise cause system confusion. (Note: The temporary cookie used by this is discarded immediately once the redirect operation finishes). 

The actual process of passing data along is also quite sophisticated. For one, one can pretty much pass any amount of data this way (again: be mindful of the overhead!). Cookies are limited in size of course (4k each, typically), but the implementation used here splits the data into multiple cookies if need be. The data itself is JSON serialized. So whatever object one passes needs to be JSON serializable (just make all properties read-write and you should be fine). However, the JSON data is not passed as clear text. Instead, the system creates a temporary and random encryption key for every redirect and encrypts the JSON while it is in transfer. 

Anyway: What’s probably more important to you guys is that it is exceptionally simple to use this system, as you really do not need to know that any of this is going on. Just make sure the ASP.NET MVC controller you create derives from the CODE Framework ControllerEx class, and voila!, you now have the RedirectToActionWidhData() method available. The data then shows up “magically” in the target action/method. 
