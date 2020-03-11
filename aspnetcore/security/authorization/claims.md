---
title: Autorización basada en notificaciones en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo agregar comprobaciones de notificaciones para la autorización en una aplicación ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/claims
ms.openlocfilehash: e289851aafcbc7e3b3f60ab9fbe4b182a78bdf8a
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652973"
---
# <a name="claims-based-authorization-in-aspnet-core"></a><span data-ttu-id="16a52-103">Autorización basada en notificaciones en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="16a52-103">Claims-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-claims-based"></a>

<span data-ttu-id="16a52-104">Cuando se crea una identidad, se le puede asignar una o varias notificaciones emitidas por una entidad de confianza.</span><span class="sxs-lookup"><span data-stu-id="16a52-104">When an identity is created it may be assigned one or more claims issued by a trusted party.</span></span> <span data-ttu-id="16a52-105">Una demanda es un par de valor de nombre que representa lo que es el sujeto, no lo que puede hacer el sujeto.</span><span class="sxs-lookup"><span data-stu-id="16a52-105">A claim is a name value pair that represents what the subject is, not what the subject can do.</span></span> <span data-ttu-id="16a52-106">Por ejemplo, puede tener una licencia de controlador, emitida por una entidad de licencia de conducción local.</span><span class="sxs-lookup"><span data-stu-id="16a52-106">For example, you may have a driver's license, issued by a local driving license authority.</span></span> <span data-ttu-id="16a52-107">La licencia de su controlador tiene su fecha de nacimiento.</span><span class="sxs-lookup"><span data-stu-id="16a52-107">Your driver's license has your date of birth on it.</span></span> <span data-ttu-id="16a52-108">En este caso, el nombre de la demanda sería `DateOfBirth`, el valor de la demanda sería su fecha de nacimiento, por ejemplo `8th June 1970` y el emisor sería la entidad de la licencia de conducción.</span><span class="sxs-lookup"><span data-stu-id="16a52-108">In this case the claim name would be `DateOfBirth`, the claim value would be your date of birth, for example `8th June 1970` and the issuer would be the driving license authority.</span></span> <span data-ttu-id="16a52-109">La autorización basada en notificaciones, en su forma más simple, comprueba el valor de una notificación y permite el acceso a un recurso en función de ese valor.</span><span class="sxs-lookup"><span data-stu-id="16a52-109">Claims based authorization, at its simplest, checks the value of a claim and allows access to a resource based upon that value.</span></span> <span data-ttu-id="16a52-110">Por ejemplo, si desea tener acceso a un club nocturno, el proceso de autorización podría ser:</span><span class="sxs-lookup"><span data-stu-id="16a52-110">For example if you want access to a night club the authorization process might be:</span></span>

<span data-ttu-id="16a52-111">El responsable de seguridad de la puerta evaluaría el valor de la demanda de la fecha de nacimiento y si confía en el emisor (la entidad de la licencia de conducción) antes de concederle acceso.</span><span class="sxs-lookup"><span data-stu-id="16a52-111">The door security officer would evaluate the value of your date of birth claim and whether they trust the issuer (the driving license authority) before granting you access.</span></span>

<span data-ttu-id="16a52-112">Una identidad puede contener varias notificaciones con varios valores y puede contener varias notificaciones del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="16a52-112">An identity can contain multiple claims with multiple values and can contain multiple claims of the same type.</span></span>

## <a name="adding-claims-checks"></a><span data-ttu-id="16a52-113">Agregar comprobaciones de notificaciones</span><span class="sxs-lookup"><span data-stu-id="16a52-113">Adding claims checks</span></span>

<span data-ttu-id="16a52-114">Las comprobaciones de autorización basadas en notificaciones son declarativas: el desarrollador las inserta en el código, en un controlador o una acción dentro de un controlador, especificando las notificaciones que el usuario actual debe poseer y, opcionalmente, el valor que debe tener la notificación para tener acceso al recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="16a52-114">Claim based authorization checks are declarative - the developer embeds them within their code, against a controller or an action within a controller, specifying claims which the current user must possess, and optionally the value the claim must hold to access the requested resource.</span></span> <span data-ttu-id="16a52-115">Los requisitos de las notificaciones están basados en directivas, el desarrollador debe compilar y registrar una directiva que exprese los requisitos de las notificaciones.</span><span class="sxs-lookup"><span data-stu-id="16a52-115">Claims requirements are policy based, the developer must build and register a policy expressing the claims requirements.</span></span>

<span data-ttu-id="16a52-116">El tipo más sencillo de la Directiva de notificaciones busca la presencia de una demanda y no comprueba el valor.</span><span class="sxs-lookup"><span data-stu-id="16a52-116">The simplest type of claim policy looks for the presence of a claim and doesn't check the value.</span></span>

<span data-ttu-id="16a52-117">En primer lugar, debe compilar y registrar la Directiva.</span><span class="sxs-lookup"><span data-stu-id="16a52-117">First you need to build and register the policy.</span></span> <span data-ttu-id="16a52-118">Esto se produce como parte de la configuración del servicio de autorización, que normalmente participa en `ConfigureServices()` en el archivo *Startup.CS* .</span><span class="sxs-lookup"><span data-stu-id="16a52-118">This takes place as part of the Authorization service configuration, which normally takes part in `ConfigureServices()` in your *Startup.cs* file.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
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
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

<span data-ttu-id="16a52-119">En este caso, la Directiva de `EmployeeOnly` comprueba la presencia de una demanda de `EmployeeNumber` en la identidad actual.</span><span class="sxs-lookup"><span data-stu-id="16a52-119">In this case the `EmployeeOnly` policy checks for the presence of an `EmployeeNumber` claim on the current identity.</span></span>

<span data-ttu-id="16a52-120">A continuación, aplique la Directiva mediante la propiedad `Policy` del atributo `AuthorizeAttribute` para especificar el nombre de la Directiva;</span><span class="sxs-lookup"><span data-stu-id="16a52-120">You then apply the policy using the `Policy` property on the `AuthorizeAttribute` attribute to specify the policy name;</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

<span data-ttu-id="16a52-121">El atributo `AuthorizeAttribute` se puede aplicar a un controlador completo; en este caso, solo se permitirá el acceso a cualquier acción del controlador a las identidades que coincidan con la Directiva.</span><span class="sxs-lookup"><span data-stu-id="16a52-121">The `AuthorizeAttribute` attribute can be applied to an entire controller, in this instance only identities matching the policy will be allowed access to any Action on the controller.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

<span data-ttu-id="16a52-122">Si tiene un controlador que está protegido por el `AuthorizeAttribute` atributo, pero desea permitir el acceso anónimo a determinadas acciones, aplique el atributo `AllowAnonymousAttribute`.</span><span class="sxs-lookup"><span data-stu-id="16a52-122">If you have a controller that's protected by the `AuthorizeAttribute` attribute, but want to allow anonymous access to particular actions you apply the `AllowAnonymousAttribute` attribute.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }

    [AllowAnonymous]
    public ActionResult VacationPolicy()
    {
    }
}
```

<span data-ttu-id="16a52-123">La mayoría de las notificaciones tienen un valor.</span><span class="sxs-lookup"><span data-stu-id="16a52-123">Most claims come with a value.</span></span> <span data-ttu-id="16a52-124">Puede especificar una lista de valores permitidos al crear la Directiva.</span><span class="sxs-lookup"><span data-stu-id="16a52-124">You can specify a list of allowed values when creating the policy.</span></span> <span data-ttu-id="16a52-125">El siguiente ejemplo solo se realizaría correctamente para los empleados cuyo número de empleado era 1, 2, 3, 4 o 5.</span><span class="sxs-lookup"><span data-stu-id="16a52-125">The following example would only succeed for employees whose employee number was 1, 2, 3, 4 or 5.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
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
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end
### <a name="add-a-generic-claim-check"></a><span data-ttu-id="16a52-126">Agregar una comprobación de notificaciones genéricas</span><span class="sxs-lookup"><span data-stu-id="16a52-126">Add a generic claim check</span></span>

<span data-ttu-id="16a52-127">Si el valor de la petición no es un valor único o se requiere una transformación, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span><span class="sxs-lookup"><span data-stu-id="16a52-127">If the claim value isn't a single value or a transformation is required, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span></span> <span data-ttu-id="16a52-128">Para obtener más información, vea [usar un FUNC para cumplir una directiva](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy).</span><span class="sxs-lookup"><span data-stu-id="16a52-128">For more information, see [Using a func to fulfill a policy](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy).</span></span>

## <a name="multiple-policy-evaluation"></a><span data-ttu-id="16a52-129">Evaluación de varias directivas</span><span class="sxs-lookup"><span data-stu-id="16a52-129">Multiple Policy Evaluation</span></span>

<span data-ttu-id="16a52-130">Si aplica varias directivas a un controlador o una acción, todas las directivas deben pasar antes de que se conceda el acceso.</span><span class="sxs-lookup"><span data-stu-id="16a52-130">If you apply multiple policies to a controller or action, then all policies must pass before access is granted.</span></span> <span data-ttu-id="16a52-131">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="16a52-131">For example:</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class SalaryController : Controller
{
    public ActionResult Payslip()
    {
    }

    [Authorize(Policy = "HumanResources")]
    public ActionResult UpdateSalary()
    {
    }
}
```

<span data-ttu-id="16a52-132">En el ejemplo anterior, cualquier identidad que cumpla la Directiva de `EmployeeOnly` puede tener acceso a la acción de `Payslip` a medida que se aplica la Directiva en el controlador.</span><span class="sxs-lookup"><span data-stu-id="16a52-132">In the above example any identity which fulfills the `EmployeeOnly` policy can access the `Payslip` action as that policy is enforced on the controller.</span></span> <span data-ttu-id="16a52-133">Sin embargo, para poder llamar a la acción de `UpdateSalary` la identidad debe cumplir *tanto* la directiva de `EmployeeOnly` como la directiva de `HumanResources`.</span><span class="sxs-lookup"><span data-stu-id="16a52-133">However in order to call the `UpdateSalary` action the identity must fulfill *both* the `EmployeeOnly` policy and the `HumanResources` policy.</span></span>

<span data-ttu-id="16a52-134">Si desea directivas más complicadas, como tomar una demanda de fecha de nacimiento, calcular una edad a partir de ella y comprobar la edad es 21 o anterior, debe escribir [controladores de directivas personalizados](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="16a52-134">If you want more complicated policies, such as taking a date of birth claim, calculating an age from it then checking the age is 21 or older then you need to write [custom policy handlers](xref:security/authorization/policies).</span></span>
