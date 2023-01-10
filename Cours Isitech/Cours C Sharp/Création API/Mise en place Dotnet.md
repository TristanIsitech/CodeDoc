```
$ dotnet tool install --global dotnet-ef
```

# Création de l'API    

```  
$ dotnet new webapi -o HeroAPI

$ dotnet run
$ dotnet run --launch-profile https
$ dotnet watch
```

# Mise en place

### Pour ajouter les packets Entity Framework Core :
```
$ dotnet add package Microsoft.EntityFrameworkCore --version 7.0.1
$ dotnet add package Microsoft.EntityFrameworkCore.Design --version 7.0.1
```

### Pour ajouter les packets MySQL :
```
$ dotnet add package Pomelo.EntityFrameworkCore.MySql --version 7.0.0-alpha.1
```

# Arborescence Type :

![[Pasted image 20221214143257.png]]

# Contenu des fichiers

### Création d'un fichier de Context :
```C#
using Microsoft.EntityFrameworkCore;
using Fichier.Entities;

namespace Fichier.Context
{
    public class ApplicationDbContext: DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> option): base(option)
        {

        }
        public DbSet<Class> Classes {get; set;}
    }
}
```

### Création d'un fichier de Controllers : 
```C#
using Microsoft.AspNetCore.Http;
using Microsoft.EntityFrameworkCore;
using Microsoft.AspNetCore.Mvc;
using HeroAPI.Context;
using HeroAPI.Entities;

namespace HeroAPI.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class HeroController : ControllerBase
    {
        private readonly ApplicationDbContext _context;
        public HeroController(ApplicationDbContext dbContext)
        {
            _context = dbContext;
        }
  
        // Get Heroes
        [HttpGet]
        public async Task<ActionResult<List<Hero>>> Get()
        {
            return Ok(await _context.Heroes.ToListAsync());
        }
    }
}
```

### Création du fichier Entities :
```C#
namespace HeroAPI.Entities
{
    public class Hero
    {
        public int Id {get;set;}
        public string Name {get;set;}
        public string FirstName{get;set;}
        public string LastName{get;set;}
    }
}
```

### Changer dans le appsettings.json :
```Json
{
  "ConnectionStrings": {
    "DEfaultConnection": "server=127.0.0.1;user id=root;password=;database=DbApplicationTest"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### Dans Program.cs :
- Ajouter les dépendences :
```C#
using Microsoft.EntityFrameworkCore;
using HeroAPI.Context;
```

- Puis ajouter :
```C#
builder.Services.AddDbContext<ApplicationDbContext>(option =>
{
    option.UseMySql(
	       builder.Configuration.GetConnectionString("DefaultConnection"),
 ServerVersion.AutoDetect(builder.Configuration.GetConnectionString("DefaultConnection")));
});
```

# Execution de la connection
```
$ dotnet-ef migrations add firstMigration
$ dotnet-ef database update
```


# Pour avancer en API C# :
- Faire des certifications (Azure, ...)
- S'améliorer en C#
- Apprendre à déployer des appplications sur Azure
- Apprendre à faire des Cleans architechture (Design partern)