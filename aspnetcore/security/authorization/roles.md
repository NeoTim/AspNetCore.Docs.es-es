---
title: Autorización basada en roles en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo restringir el acceso a la acción y el controlador ASP.NET Core pasando roles al atributo Authorize.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/roles
ms.openlocfilehash: 28aa3df6aa661d0b762df78fe611cd827af43f75
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651641"
---
# <a name="role-based-authorization-in-aspnet-core"></a><span data-ttu-id="cf3dc-103">Autorización basada en roles en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cf3dc-103">Role-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-role-based"></a>

<span data-ttu-id="cf3dc-104">Cuando se crea una identidad, puede pertenecer a uno o varios roles.</span><span class="sxs-lookup"><span data-stu-id="cf3dc-104">When an identity is created it may belong to one or more roles.</span></span> <span data-ttu-id="cf3dc-105">Por ejemplo, Tracy puede pertenecer a los roles de administrador y de usuario, mientras que Scott solo puede pertenecer a la función de usuario.</span><span class="sxs-lookup"><span data-stu-id="cf3dc-105">For example, Tracy may belong to the Administrator and User roles whilst Scott may only belong to the User role.</span></span> <span data-ttu-id="cf3dc-106">La forma en que se crean y administran estos roles depende de la memoria auxiliar del proceso de autorización.</span><span class="sxs-lookup"><span data-stu-id="cf3dc-106">How these roles are created and managed depends on the backing store of the authorization process.</span></span> <span data-ttu-id="cf3dc-107">Los roles se exponen al desarrollador a través del método [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) en la clase [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) .</span><span class="sxs-lookup"><span data-stu-id="cf3dc-107">Roles are exposed to the developer through the [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) method on the [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) class.</span></span>

## <a name="adding-role-checks"></a><span data-ttu-id="cf3dc-108">Agregar comprobaciones de roles</span><span class="sxs-lookup"><span data-stu-id="cf3dc-108">Adding role checks</span></span>

<span data-ttu-id="cf3dc-109">Las comprobaciones de autorización basadas en roles son declarativas&mdash;el desarrollador las inserta en el código, en un controlador o una acción dentro de un controlador, especificando los roles de los que el usuario actual debe ser miembro para tener acceso al recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="cf3dc-109">Role-based authorization checks are declarative&mdash;the developer embeds them within their code, against a controller or an action within a controller, specifying roles which the current user must be a member of to access the requested resource.</span></span>

<span data-ttu-id="cf3dc-110">Por ejemplo, el código siguiente limita el acceso a cualquier acción en el `AdministrationController` a los usuarios que son miembros del rol `Administrator`:</span><span class="sxs-lookup"><span data-stu-id="cf3dc-110">For example, the following code limits access to any actions on the `AdministrationController` to users who are a member of the `Administrator` role:</span></span>

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

<span data-ttu-id="cf3dc-111">Puede especificar varios roles como una lista separada por comas:</span><span class="sxs-lookup"><span data-stu-id="cf3dc-111">You can specify multiple roles as a comma separated list:</span></span>

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

<span data-ttu-id="cf3dc-112">Este controlador solo es accesible para los usuarios que son miembros del rol `HRManager` o del rol `Finance`.</span><span class="sxs-lookup"><span data-stu-id="cf3dc-112">This controller would be only accessible by users who are members of the `HRManager` role or the `Finance` role.</span></span>

<span data-ttu-id="cf3dc-113">Si aplica varios atributos, el acceso de un usuario debe ser miembro de todos los roles especificados. en el ejemplo siguiente, es necesario que un usuario sea miembro del rol `PowerUser` y `ControlPanelUser`.</span><span class="sxs-lookup"><span data-stu-id="cf3dc-113">If you apply multiple attributes then an accessing user must be a member of all the roles specified; the following sample requires that a user must be a member of both the `PowerUser` and `ControlPanelUser` role.</span></span>

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

<span data-ttu-id="cf3dc-114">Puede limitar aún más el acceso aplicando atributos de autorización de rol adicionales en el nivel de acción:</span><span class="sxs-lookup"><span data-stu-id="cf3dc-114">You can further limit access by applying additional role authorization attributes at the action level:</span></span>

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

<span data-ttu-id="cf3dc-115">En los miembros del fragmento de código anterior del rol `Administrator` o el rol de `PowerUser` puede tener acceso al controlador y a la acción `SetTime`, pero solo los miembros del rol `Administrator` pueden tener acceso a la acción `ShutDown`.</span><span class="sxs-lookup"><span data-stu-id="cf3dc-115">In the previous code snippet members of the `Administrator` role or the `PowerUser` role can access the controller and the `SetTime` action, but only members of the `Administrator` role can access the `ShutDown` action.</span></span>

<span data-ttu-id="cf3dc-116">También puede bloquear un controlador, pero permitir el acceso anónimo y no autenticado a acciones individuales.</span><span class="sxs-lookup"><span data-stu-id="cf3dc-116">You can also lock down a controller but allow anonymous, unauthenticated access to individual actions.</span></span>

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="cf3dc-117">Por Razor Pages, el `AuthorizeAttribute` se puede aplicar mediante:</span><span class="sxs-lookup"><span data-stu-id="cf3dc-117">For Razor Pages, the `AuthorizeAttribute` can be applied by either:</span></span>

* <span data-ttu-id="cf3dc-118">Mediante una [Convención](xref:razor-pages/razor-pages-conventions#page-model-action-conventions), o</span><span class="sxs-lookup"><span data-stu-id="cf3dc-118">Using a [convention](xref:razor-pages/razor-pages-conventions#page-model-action-conventions), or</span></span>
* <span data-ttu-id="cf3dc-119">Aplicar el `AuthorizeAttribute` a la instancia de `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="cf3dc-119">Applying the `AuthorizeAttribute` to the `PageModel` instance:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public class UpdateModel : PageModel
{
    public ActionResult OnPost()
    {
    }
}
```

> [!IMPORTANT]
> <span data-ttu-id="cf3dc-120">Los atributos de filtro, incluido `AuthorizeAttribute`, solo se pueden aplicar a PageModel y no se pueden aplicar a métodos de control de página específicos.</span><span class="sxs-lookup"><span data-stu-id="cf3dc-120">Filter attributes, including `AuthorizeAttribute`, can only be applied to PageModel and cannot be applied to specific page handler methods.</span></span>
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a><span data-ttu-id="cf3dc-121">Comprobaciones de roles basadas en directivas</span><span class="sxs-lookup"><span data-stu-id="cf3dc-121">Policy based role checks</span></span>

<span data-ttu-id="cf3dc-122">Los requisitos de rol también se pueden expresar con la nueva sintaxis de Directiva, donde un desarrollador registra una directiva en el inicio como parte de la configuración del servicio de autorización.</span><span class="sxs-lookup"><span data-stu-id="cf3dc-122">Role requirements can also be expressed using the new Policy syntax, where a developer registers a policy at startup as part of the Authorization service configuration.</span></span> <span data-ttu-id="cf3dc-123">Esto se produce normalmente en `ConfigureServices()` en el archivo *Startup.CS* .</span><span class="sxs-lookup"><span data-stu-id="cf3dc-123">This normally occurs in `ConfigureServices()` in your *Startup.cs* file.</span></span>

::: moniker range=">= aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

::: moniker range="< aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

<span data-ttu-id="cf3dc-124">Las directivas se aplican mediante la propiedad `Policy` en el atributo `AuthorizeAttribute`:</span><span class="sxs-lookup"><span data-stu-id="cf3dc-124">Policies are applied using the `Policy` property on the `AuthorizeAttribute` attribute:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

<span data-ttu-id="cf3dc-125">Si desea especificar varios roles permitidos en un requisito, puede especificarlos como parámetros para el método `RequireRole`:</span><span class="sxs-lookup"><span data-stu-id="cf3dc-125">If you want to specify multiple allowed roles in a requirement then you can specify them as parameters to the `RequireRole` method:</span></span>

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

<span data-ttu-id="cf3dc-126">Este ejemplo autoriza a los usuarios que pertenecen a los roles `Administrator`, `PowerUser` o `BackupAdministrator`.</span><span class="sxs-lookup"><span data-stu-id="cf3dc-126">This example authorizes users who belong to the `Administrator`, `PowerUser` or `BackupAdministrator` roles.</span></span>

### <a name="add-role-services-to-identity"></a><span data-ttu-id="cf3dc-127">Agregar servicios de función a la identidad</span><span class="sxs-lookup"><span data-stu-id="cf3dc-127">Add Role services to Identity</span></span>

<span data-ttu-id="cf3dc-128">Anexe [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para agregar servicios de rol:</span><span class="sxs-lookup"><span data-stu-id="cf3dc-128">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

