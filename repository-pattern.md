# Implementing Repository pattern
Implementing Repository pattern with E.F. Core

The contents of this file is being applied at my [Hamburger Food Store](https://github.com/raphaelfrei/hamburger-website) project with ASP.NET MVC

## Steps:

### 1. Configure EF Core:

The steps on how to setup E.F. Core is in this [project](https://github.com/raphaelfrei/asp-net/blob/main/ef-code.md).

### 2. Create Folders:

Create this folder structure:

> Main Project
>  - Dependencies
>  - Properties
>  - ...
>  - **Repositories**
>    - **Interfaces**

### 3. Create Interface Class:

In this example, we'll work with **Category** table.

> The name convention for the Interface file is *I[TableName]Repository.cs* - So, in this case *ICategoryRepository.cs*. 

Create the *ICategoryRepository.cs* in Interfaces folder:

````csharp
using Restaurante.Models;

namespace Restaurante.Repositories.Interfaces {

    public interface ICategoryRepository {

        IEnumerable<Category> Categories { get; }

    }
}
````

### 4. Create Repository Class:

Continuing with **Category** table...

> The name convention is *[TableName]Repository.cs*.

Create the *CategoryRepository.cs* under Repository folder:

````csharp
using Restaurante.Context;
using Restaurante.Models;
using Restaurante.Repositories.Interfaces;

namespace Restaurante.Repositories {

    public class CategoryRepository : ICategoryRepository {

        private readonly AppDbContext _context;

        public CategoryRepository(AppDbContext context) {
            _context = context;
        }

        public IEnumerable<Category> Categories => _context.Categories;

    }

}
````

## Additional:

### Using search criteria on DB:

Using *Hamburger* Table.

IHamburgerRepository.cs:
````csharp
using Restaurante.Models;

namespace Restaurante.Repositories.Interfaces {

    public interface IHamburgerRepository {

        IEnumerable<Hamburger> Hamburgers { get; }
        IEnumerable<Hamburger> FavoriteBurgers { get; }
        Hamburger GetBurgerByID(int burgerId);

    }

}
````

HamburgerRepository.cs:
````csharp
using Microsoft.EntityFrameworkCore;
using Restaurante.Context;
using Restaurante.Models;
using Restaurante.Repositories.Interfaces;

namespace Restaurante.Repositories {

    public class HamburgerRepository : IHamburgerRepository {

        private readonly AppDbContext _context;

        public HamburgerRepository(AppDbContext context) {
            _context = context;
        }

        public IEnumerable<Hamburger> Hamburgers => _context.Hamburgers.Include(c => c.Category);

        public IEnumerable<Hamburger> FavoriteBurgers => _context.Hamburgers.Where(h => h.IsFavorite).Include(h => h.Category);

        public Hamburger GetBurgerByID(int burgerId) => _context.Hamburgers.FirstOrDefault(h => h.HamburgerID == burgerId);

    }

}
````
