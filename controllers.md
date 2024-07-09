# Controllers with ASP.NET

For this example, I'll be creating an Controller to control the Hamburgers in this [project](https://github.com/raphaelfrei/hamburger-website).

## Creating the Controller:

> Select *Controllers* folders > Right Click > Add > Controller...<br>
> Select the template file - In this case, *MVC Controller - Empty* <br>
> Click on Add<br>
> Select the Name > Click on Create

For best pratices, the file name should follow the *[NAME]Controller.cs*.

For this example, will be creating the *HamburgerController.cs*

Inside this Controller, will be needed to call the [interfaces](https://github.com/raphaelfrei/asp-net/blob/main/repository-pattern.md) responsable for searching the database...

````csharp
using Microsoft.AspNetCore.Mvc;
using Restaurante.Repositories.Interfaces;

namespace Restaurante.Controllers {

    public class HamburgerController : Controller {

        /* Always calls the Interface and put it into the Constructor */
        private readonly IHamburgerRepository _hamburgerRepository;

        public HamburgerController(IHamburgerRepository hamburgerRepository) {
            _hamburgerRepository = hamburgerRepository;
        }

        /* Renamed the INDEX to List - This function will list all burgers from my db */
        public IActionResult List() {

            var burgers = _hamburgerRepository.Hamburgers;

            return View(burgers);

        }

    }

}
````

## Creating Views:

### List View:

ASP.NET offers a way to create an Razor View template based on the Controller.

For this case, we'll create a List View.

For this:

On the Controller class, Right Click on code and click on *Add View...*, selecting the *Razor View* scaffolder. Click on next.

Settings for this page:

> View name: In my case, will be only *List*<br>
> Template: *List*<br>
> Model class: Select the object class *(Not Interface nor Repository)*<br>
> Db Context class: The Db Context you created<br>

Now, create the page.

After creating the view, this will create the *[ViewName].cshtml* file inside /Views/[Controller]/ folder.
