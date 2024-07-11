# List View Model

Steps on how to implement a List View Model inside ASP.NET

## Steps:

### 1. Create Folder and Class:

Inside your Project folder, create the ````ViewModels```` folder.

For each object that you want a ListViewModel, create an class using the pattern ````[OBJ]ListViewModel.cs````.

In my case, I'll be creating the ````HamburgerListViewModel.cs```` for my [Online Burger Store project](https://github.com/raphaelfrei/hamburger-website).

### 2. Classes:

In the ````HamburgerListViewModel.cs````:

````csharp
using Restaurante.Models;

namespace Restaurante.ViewModels {

    public class HamburgerListViewModel {

        public IEnumerable<Hamburger> Hamburgers { get; set; }
        public string CurrentCategory { get; set; }

    }
}
````

Modify the Controller from the object (In my case, the Action that shows a List:

Current:
````csharp
using Microsoft.AspNetCore.Mvc;
using Restaurante.Repositories.Interfaces;

namespace Restaurante.Controllers {

    public class HamburgerController : Controller {

        private readonly IHamburgerRepository _hamburgerRepository;

        public HamburgerController(IHamburgerRepository hamburgerRepository) {
            _hamburgerRepository = hamburgerRepository;
        }

        public IActionResult List() {

            ViewData["PageTitle"] = "All Burgers";

            var burgers = _hamburgerRepository.Hamburgers;
            var totalBurgers = burgers.Count();

            ViewBag.TotalTitle = "Burger count: ";
            ViewBag.TotalBurgers = totalBurgers;

            return View(burgers);

        }

    }

}
````

After modification:
````csharp
using Microsoft.AspNetCore.Mvc;
using Restaurante.Repositories.Interfaces;
using Restaurante.ViewModels;

namespace Restaurante.Controllers {

    public class HamburgerController : Controller {

        private readonly IHamburgerRepository _hamburgerRepository;

        public HamburgerController(IHamburgerRepository hamburgerRepository) {
            _hamburgerRepository = hamburgerRepository;
        }

        public IActionResult List() {

            ViewData["PageTitle"] = "All Burgers";

            var hamburgerListViewModel = new HamburgerListViewModel();
            hamburgerListViewModel.Hamburgers = _hamburgerRepository.Hamburgers;
            var totalBurgers = hamburgerListViewModel.Hamburgers.Count();
            hamburgerListViewModel.CurrentCategory = "Current Category"; // Missing Category Implementation
            
            ViewBag.TotalTitle = "Burger count: ";
            ViewBag.TotalBurgers = totalBurgers;
            
            return View(hamburgerListViewModel);

        }

    }

}
````

### 3. Modifying the List.cshtml:

My current class looks like this, which is heavily typped:

````cshtml
@model IEnumerable<Restaurante.Models.Hamburger>

@{
    ViewData["Title"] = "Burger List";
}

<div>

    <h2>@ViewData["PageTitle"]</h2>
    <hr/>
    <h8>
        @ViewBag.TotalTitle @ViewBag.TotalBurgers
    </h8>
    <hr/>

</div>

<div class="container">
    <div class="row">
        @foreach (var burger in Model) {

            <div class="col-sm-4 col-md-4 col-lg-4">
                <p>
                    <img src="@burger.ImageURL" class="img-fluid"/>
                </p>
                <h4>
                    @burger.HamburgerName
                </h4>
                
                <h3>
                    Price: @burger.Price.ToString("C")
                </h3>

                <p>@burger.ShortDescription</p>
            </div>

        }
    </div>
</div>
````

To this:

````cshtml
@model HamburgerListViewModel

@{
    ViewData["Title"] = "Burger List";
}

<div>

    <h2>@ViewData["PageTitle"]</h2>
    <hr/>
    <h8>
        @ViewBag.TotalTitle @ViewBag.TotalBurgers
    </h8>
    <hr/>

</div>

<div class="container">
    <div class="row">
        @foreach (var burger in Model.Hamburgers) {

            <div class="col-sm-4 col-md-4 col-lg-4">
                <p>
                    <img src="@burger.ImageURL" class="img-fluid"/>
                </p>
                <h4>
                    @burger.HamburgerName
                </h4>
                
                <h3>
                    Price: @burger.Price.ToString("C")
                </h3>

                <p>@burger.ShortDescription</p>
            </div>

        }
    </div>
</div>
````
