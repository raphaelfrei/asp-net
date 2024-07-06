# Entity Framework Core

Integrate EF Code on ASP.NET project for Code-First applications.

## Steps:

### 1. Necessary packages:
*(Available on Nuget Packages)*

````
1. Microsoft.EntityFrameworkCore.Design
2. Microsoft.EntityFrameworkCore.Tools
3. Microsoft.EntityFrameworkCore.DATABASE
````

### 2. Create C# Models:
*(I'm usually creating inside MODELS folder)*

In this example, will be two tables for a hamburger online order:

1. Hamburger.cs
````csharp
namespace Restaurante.Models {

    public class Hamburger {

        public int HamburgerID { get; set; }
        public string HamburgerName { get; set; }
        public string ShortDescription { get; set; }
        public string LongDescription { get; set; }
        public decimal Price { get; set; }
        public string ImageURL { get; set; }
        public string ImageThumbURL { get; set; }
        public bool IsFavorite { get; set; }
        public bool HasInStock { get; set; }

        public int CategoryID { get; set; }
        public virtual Category Category { get; set; }

    }
}
````

2. Category.cs:
````csharp
namespace Restaurante.Models {

    public class Category {

        public int CategoryID { get; set; }
        public string CategoryName { get; set; }
        public string CategoryDescription { get; set; }

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
