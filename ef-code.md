# Entity Framework Core

Integrate EF Code on ASP.NET project for Code-First applications.

## Steps:

### 1. Necessary packages:
*(Available on Nuget Packages)*

> 1. Microsoft.EntityFrameworkCore.Design
> 2. Microsoft.EntityFrameworkCore.Tools
> 3. Microsoft.EntityFrameworkCore.DATABASE

### 2. Create C# Models:
*(I'm usually creating inside MODELS folder)*

In this example, will be two tables for a hamburger online order:

1. Hamburger.cs
````csharp
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace Restaurante.Models {

    [Table("Hamburgers")]
    public class Hamburger {

        [Key]
        public int HamburgerID { get; set; }

        /* ----- */

        [Display(Name = "Hamburguer Name")]
        [Required(ErrorMessage = "The name must be informed")]
        [StringLength(50, MinimumLength = 10, ErrorMessage = "The name must have at least {2} characters and not exceed {1} caracters")]
        public string HamburgerName { get; set; }

        /* ----- */

        [Display(Name = "Short Description")]
        [Required(ErrorMessage = "The short description must be informed")]
        [StringLength(100, ErrorMessage = "The short description must not exceed {1} characters")]
        public string ShortDescription { get; set; }

        /* ----- */

        [Display(Name = "Long Description")]
        [Required(ErrorMessage = "The long description must be informed")]
        [StringLength(100, ErrorMessage = "The long description must not exceed {1} characters")]
        public string LongDescription { get; set; }

        /* ----- */

        [Display(Name = "Hamburger Price")]
        [Required(ErrorMessage = "The hamburger price must be informed")]
        [Column(TypeName = "decimal(10,2)")]
        [Range(1, 999.99, ErrorMessage = "The price must be between ${1} and ${2}")]
        public decimal Price { get; set; }

        /* ----- */

        [Display(Name = "Image URL Path")]
        [StringLength(200, ErrorMessage = "The image URL path must not exceed {1} characters")]
        public string ImageURL { get; set; }

        /* ----- */

        [Display(Name = "Thumb Image URL Path")]
        [StringLength(200, ErrorMessage = "The thumb image URL path must not exceed {1} characters")]
        public string ImageThumbURL { get; set; }

        /* ----- */

        [Display(Name = "Is Prefered?")]
        public bool IsFavorite { get; set; }

        /* ----- */

        [Display(Name = "Has in Stock?")]
        public bool HasInStock { get; set; }

        /* ----- */

        /* 1 to N relation */
        public int CategoryID { get; set; }
        public virtual Category Category { get; set; }

    }
}
````

2. Category.cs:
````csharp
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace Restaurante.Models {

    [Table("Categories")]
    public class Category {

        [Key]
        public int CategoryID { get; set; }

        /* ----- */

        [Display(Name = "Category Name")]
        [Required(ErrorMessage = "The name must be informed")]
        [StringLength(100, ErrorMessage = "The name must not exceed {1} characters")]
        public string CategoryName { get; set; }

        /* ----- */

        [Display(Name = "Description")]
        [Required(ErrorMessage = "The description must be informed")]
        [StringLength(100, ErrorMessage = "Description must not exceed {1} characters")]
        public string CategoryDescription { get; set; }

        /* ----- */

        public List<Hamburger> Hamburgers { get; set; }

    }
}
````

### 3. App DB Context:
*(Creating inside CONTEXT folder - New folder)*

````csharp
using Microsoft.EntityFrameworkCore;
using Restaurante.Models;

namespace Restaurante.Context {

    public class AppDbContext:DbContext {

        public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { 
        
        }

        // public DbSet<Model> TableName
        public DbSet<Category> Categories { get; set; }
        public DbSet<Hamburger> Hamburgers { get; set; }

    }
}
````

### 4. Connection Strings:
*Inside appsettings.json*

**Don't forget to change the *{host}* property**
````json
{

  "ConnectionStrings": {
    "DefaultConnection": "Data Source={host}\\sqlexpress;Initial Catalog=HamburgerDB;Integrated Security=True"
  },
  "..."
}
````

### 5. Add Connection to Services:
*Inside Program.cs*

````csharp
using Microsoft.EntityFrameworkCore;
using Restaurante.Context;

var builder = WebApplication.CreateBuilder(args);
var configuration = new ConfigurationBuilder()
    .SetBasePath(builder.Environment.ContentRootPath)
    .AddJsonFile("appsettings.json")
    .Build();

// Add services to the container.
builder.Services.AddControllersWithViews();
builder.Services.AddDbContext<AppDbContext>(options => options.UseSqlServer(configuration.GetConnectionString("DefaultConnection")));

var app = builder.Build();

/* ... */

app.Run();
````

### 6. Do Implementations:

I'll be doing by using *Package Manager Console*, but you can use *NET CLI* as well.

The comands are:

1. Create Migration:

> add-migration NAME [options]

2. Apply Migration:

> update-database [options]

3. Remove Migration:

> remove-migration

So, for the first database commit:

> add-migration InitialCommit<br>
> update-database

````
For NET CLI:

Add Migration:
    dotnet ef migrations add NAME

Update on DB:
    dotnet ef database update

Remove last Migration:
    dotnet ef migrations remove
````

You can see what changes this will cause inside the *Migrations* folder

This, will create the Database based on the [ConnectionString](https://github.com/raphaelfrei/asp-net/blob/main/ef-code.md#4-connection-strings).
