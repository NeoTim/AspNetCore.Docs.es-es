---
title: Migración de la autenticación y la identidad a ASP.NET Core
author: ardalis
description: Obtenga información sobre cómo migrar la autenticación y la identidad de un proyecto de MVC de ASP.NET a un proyecto de MVC de ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: migration/identity
ms.openlocfilehash: f821930dbd36de18db31104cddf34c563009a506
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022267"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core"></a><span data-ttu-id="a2cc6-103">Migración de la autenticación y la identidad a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a2cc6-103">Migrate Authentication and Identity to ASP.NET Core</span></span>

<span data-ttu-id="a2cc6-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a2cc6-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a2cc6-105">En el artículo anterior, se [migró la configuración de un proyecto de ASP.NET MVC a ASP.net Core MVC](xref:migration/configuration).</span><span class="sxs-lookup"><span data-stu-id="a2cc6-105">In the previous article, we [migrated configuration from an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/configuration).</span></span> <span data-ttu-id="a2cc6-106">En este artículo, migraremos las características de registro, Inicio de sesión y administración de usuarios.</span><span class="sxs-lookup"><span data-stu-id="a2cc6-106">In this article, we migrate the registration, login, and user management features.</span></span>

## <a name="configure-identity-and-membership"></a><span data-ttu-id="a2cc6-107">Configurar la identidad y la pertenencia</span><span class="sxs-lookup"><span data-stu-id="a2cc6-107">Configure Identity and Membership</span></span>

<span data-ttu-id="a2cc6-108">En ASP.NET MVC, las características de autenticación e identidad se configuran mediante ASP.NET Identity en *Startup.auth.CS* y *IdentityConfig.CS*, que se encuentra en la carpeta *App_Start* .</span><span class="sxs-lookup"><span data-stu-id="a2cc6-108">In ASP.NET MVC, authentication and identity features are configured using ASP.NET Identity in *Startup.Auth.cs* and *IdentityConfig.cs*, located in the *App_Start* folder.</span></span> <span data-ttu-id="a2cc6-109">En ASP.NET Core MVC, estas características se configuran en *Startup.CS*.</span><span class="sxs-lookup"><span data-stu-id="a2cc6-109">In ASP.NET Core MVC, these features are configured in *Startup.cs*.</span></span>

<span data-ttu-id="a2cc6-110">Instale los `Microsoft.AspNetCore.Identity.EntityFrameworkCore` paquetes `Microsoft.AspNetCore.Authentication.Cookies` de NuGet y.</span><span class="sxs-lookup"><span data-stu-id="a2cc6-110">Install the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` and `Microsoft.AspNetCore.Authentication.Cookies` NuGet packages.</span></span>

<span data-ttu-id="a2cc6-111">A continuación, Abra *Startup.CS* y actualice `Startup.ConfigureServices` el método para usar Entity Framework e Identity Services:</span><span class="sxs-lookup"><span data-stu-id="a2cc6-111">Then, open *Startup.cs* and update the `Startup.ConfigureServices` method to use Entity Framework and Identity services:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add EF services to the services container.
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

     services.AddMvc();
}
```

<span data-ttu-id="a2cc6-112">En este momento, hay dos tipos a los que se hace referencia en el código anterior que todavía no se han migrado desde el `ApplicationDbContext` proyecto `ApplicationUser`ASP.NET MVC: y.</span><span class="sxs-lookup"><span data-stu-id="a2cc6-112">At this point, there are two types referenced in the above code that we haven't yet migrated from the ASP.NET MVC project: `ApplicationDbContext` and `ApplicationUser`.</span></span> <span data-ttu-id="a2cc6-113">Cree una nueva carpeta models en el proyecto de ASP.net Core y agregue dos clases a ella que se correspondan con estos tipos.</span><span class="sxs-lookup"><span data-stu-id="a2cc6-113">Create a new *Models* folder in the ASP.NET Core project, and add two classes to it corresponding to these types.</span></span> <span data-ttu-id="a2cc6-114">Encontrará las versiones de ASP.NET MVC de estas clases en */Models/IdentityModels.CS*, pero usaremos un archivo por clase en el proyecto migrado, ya que eso es más claro.</span><span class="sxs-lookup"><span data-stu-id="a2cc6-114">You will find the ASP.NET MVC versions of these classes in */Models/IdentityModels.cs*, but we will use one file per class in the migrated project since that's more clear.</span></span>

<span data-ttu-id="a2cc6-115">*ApplicationUser.cs*:</span><span class="sxs-lookup"><span data-stu-id="a2cc6-115">*ApplicationUser.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

<span data-ttu-id="a2cc6-116">*ApplicationDbContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="a2cc6-116">*ApplicationDbContext.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.Data.Entity;

namespace NewMvcProject.Models
{
    public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);
            // Customize the ASP.NET Core Identity model and override the defaults if needed.
            // For example, you can rename the ASP.NET Core Identity table names and more.
            // Add your customizations after calling base.OnModelCreating(builder);
        }
    }
}
```

<span data-ttu-id="a2cc6-117">El proyecto Web de inicio de MVC de ASP.NET Core no incluye gran personalización de usuarios `ApplicationDbContext`o.</span><span class="sxs-lookup"><span data-stu-id="a2cc6-117">The ASP.NET Core MVC Starter Web project doesn't include much customization of users, or the `ApplicationDbContext`.</span></span> <span data-ttu-id="a2cc6-118">Al migrar una aplicación real, también debe migrar todas las propiedades y métodos personalizados del usuario y `DbContext` las clases de la aplicación, así como cualquier otra clase de modelo que use la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a2cc6-118">When migrating a real app, you also need to migrate all of the custom properties and methods of your app's user and `DbContext` classes, as well as any other Model classes your app utilizes.</span></span> <span data-ttu-id="a2cc6-119">Por ejemplo, si su `DbContext` tiene un `DbSet<Album>`, debe migrar la `Album` clase.</span><span class="sxs-lookup"><span data-stu-id="a2cc6-119">For example, if your `DbContext` has a `DbSet<Album>`, you need to migrate the `Album` class.</span></span>

<span data-ttu-id="a2cc6-120">Con estos archivos en su lugar, se puede realizar el archivo *Startup.CS* para compilar `using` mediante la actualización de sus instrucciones:</span><span class="sxs-lookup"><span data-stu-id="a2cc6-120">With these files in place, the *Startup.cs* file can be made to compile by updating its `using` statements:</span></span>

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="a2cc6-121">Nuestra aplicación ya está lista para admitir servicios de autenticación e identidad.</span><span class="sxs-lookup"><span data-stu-id="a2cc6-121">Our app is now ready to support authentication and Identity services.</span></span> <span data-ttu-id="a2cc6-122">Solo es necesario que estas características se expongan a los usuarios.</span><span class="sxs-lookup"><span data-stu-id="a2cc6-122">It just needs to have these features exposed to users.</span></span>

## <a name="migrate-registration-and-login-logic"></a><span data-ttu-id="a2cc6-123">Migración de la lógica de inicio de sesión y registro</span><span class="sxs-lookup"><span data-stu-id="a2cc6-123">Migrate registration and login logic</span></span>

<span data-ttu-id="a2cc6-124">Con los servicios de identidad configurados para la aplicación y el acceso a los datos configurados con Entity Framework y SQL Server, estamos preparados para agregar compatibilidad con el registro y el inicio de sesión en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a2cc6-124">With Identity services configured for the app and data access configured using Entity Framework and SQL Server, we're ready to add support for registration and login to the app.</span></span> <span data-ttu-id="a2cc6-125">Recuerde que [anteriormente en el proceso de migración](xref:migration/mvc#migrate-the-layout-file) hemos comentado una referencia a *_LoginPartial* en *_Layout. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a2cc6-125">Recall that [earlier in the migration process](xref:migration/mvc#migrate-the-layout-file) we commented out a reference to *_LoginPartial* in *_Layout.cshtml*.</span></span> <span data-ttu-id="a2cc6-126">Ahora es el momento de volver a ese código, quitar su comentario y agregar los controladores y las vistas necesarios para admitir la funcionalidad de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="a2cc6-126">Now it's time to return to that code, uncomment it, and add in the necessary controllers and views to support login functionality.</span></span>

<span data-ttu-id="a2cc6-127">Quite la marca `@Html.Partial` de comentario de la línea en *_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a2cc6-127">Uncomment the `@Html.Partial` line in *_Layout.cshtml*:</span></span>

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

<span data-ttu-id="a2cc6-128">Ahora, agregue una nueva vista de Razor denominada *_LoginPartial* a la carpeta *views/Shared* :</span><span class="sxs-lookup"><span data-stu-id="a2cc6-128">Now, add a new Razor view called *_LoginPartial* to the *Views/Shared* folder:</span></span>

<span data-ttu-id="a2cc6-129">Actualice *_LoginPartial. cshtml* con el código siguiente (reemplace todo su contenido):</span><span class="sxs-lookup"><span data-stu-id="a2cc6-129">Update *_LoginPartial.cshtml* with the following code (replace all of its contents):</span></span>

```cshtml
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager

@if (SignInManager.IsSignedIn(User))
{
    <form asp-area="" asp-controller="Account" asp-action="Logout" method="post" id="logoutForm" class="navbar-right">
        <ul class="nav navbar-nav navbar-right">
            <li>
                <a asp-area="" asp-controller="Manage" asp-action="Index" title="Manage">Hello @UserManager.GetUserName(User)!</a>
            </li>
            <li>
                <button type="submit" class="btn btn-link navbar-btn navbar-link">Log out</button>
            </li>
        </ul>
    </form>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="" asp-controller="Account" asp-action="Register">Register</a></li>
        <li><a asp-area="" asp-controller="Account" asp-action="Login">Log in</a></li>
    </ul>
}
```

<span data-ttu-id="a2cc6-130">En este punto, debería poder actualizar el sitio en el explorador.</span><span class="sxs-lookup"><span data-stu-id="a2cc6-130">At this point, you should be able to refresh the site in your browser.</span></span>

## <a name="summary"></a><span data-ttu-id="a2cc6-131">Resumen</span><span class="sxs-lookup"><span data-stu-id="a2cc6-131">Summary</span></span>

<span data-ttu-id="a2cc6-132">ASP.NET Core introduce cambios en las características de ASP.NET Identity.</span><span class="sxs-lookup"><span data-stu-id="a2cc6-132">ASP.NET Core introduces changes to the ASP.NET Identity features.</span></span> <span data-ttu-id="a2cc6-133">En este artículo, ha aprendido a migrar las características de autenticación y administración de usuarios de ASP.NET Identity a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a2cc6-133">In this article, you have seen how to migrate the authentication and user management features of ASP.NET Identity to ASP.NET Core.</span></span>
