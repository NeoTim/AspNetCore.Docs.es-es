---
title: Versión de compatibilidad para ASP.NET Core MVC
author: rick-anderson
description: Descubra cómo la clase Startup de ASP.NET Core configura los servicios y la canalización de solicitudes de la aplicación.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 9/25/2019
uid: mvc/compatibility-version
ms.openlocfilehash: b29e2ee49aaf0f557f1acd0cf03e9e82d5ea0105
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75357736"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a><span data-ttu-id="d3e3e-103">Versión de compatibilidad para ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="d3e3e-103">Compatibility version for ASP.NET Core MVC</span></span>

<span data-ttu-id="d3e3e-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d3e3e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d3e3e-105">El método <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> es una operación inefectiva para las aplicaciones ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="d3e3e-105">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method is a no-op for ASP.NET Core 3.0 apps.</span></span> <span data-ttu-id="d3e3e-106">Es decir, llamar a `SetCompatibilityVersion` con cualquier valor de <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> no tiene ningún impacto en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d3e3e-106">That is, calling `SetCompatibilityVersion` with any value of <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> has no impact on the application.</span></span>

* <span data-ttu-id="d3e3e-107">La siguiente versión secundaria ASP.NET Core puede proporcionar un nuevo valor de `CompatibilityVersion`.</span><span class="sxs-lookup"><span data-stu-id="d3e3e-107">The next minor version of ASP.NET Core may provide a new `CompatibilityVersion` value.</span></span>
* <span data-ttu-id="d3e3e-108">Los valores `Version_2_0` a `Version_2_2` de `CompatibilityVersion` están marcados como `[Obsolete(...)]`.</span><span class="sxs-lookup"><span data-stu-id="d3e3e-108">`CompatibilityVersion` values `Version_2_0` through `Version_2_2` are marked `[Obsolete(...)]`.</span></span>
* <span data-ttu-id="d3e3e-109">Consulte [Cambios importantes en la API en Antiforgery, CORS, Diagnostics, Mvc y Routing](https://github.com/aspnet/Announcements/issues/387).</span><span class="sxs-lookup"><span data-stu-id="d3e3e-109">See [Breaking API changes in Antiforgery, CORS, Diagnostics, Mvc, and Routing](https://github.com/aspnet/Announcements/issues/387).</span></span> <span data-ttu-id="d3e3e-110">Esta lista incluye cambios importantes para los modificadores de compatibilidad.</span><span class="sxs-lookup"><span data-stu-id="d3e3e-110">This list includes breaking changes for compatibility switches.</span></span>

<span data-ttu-id="d3e3e-111">Para ver cómo funciona `SetCompatibilityVersion` con las aplicaciones ASP.NET Core 2.x, seleccione la [versión ASP.NET Core 2.2 de este artículo](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="d3e3e-111">To see how `SetCompatibilityVersion` works with ASP.NET Core 2.x apps, select the [ASP.NET Core 2.2 version of this article](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d3e3e-112">El método <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> permite a una aplicación ASP.NET Core 2.x participar o no en los cambios de comportamiento importantes incorporados en ASP.NET Core MVC 2.1 o 2.2.</span><span class="sxs-lookup"><span data-stu-id="d3e3e-112">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an ASP.NET Core 2.x app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or 2.2.</span></span> <span data-ttu-id="d3e3e-113">Estos cambios de comportamiento importantes suelen estar relacionados con cómo se comporta el subsistema de MVC y cómo el tiempo de ejecución llama al **código**.</span><span class="sxs-lookup"><span data-stu-id="d3e3e-113">These potentially breaking behavior changes are generally in how the MVC subsystem behaves and how **your code** is called by the runtime.</span></span> <span data-ttu-id="d3e3e-114">Si la aplicación participa, obtendrá el comportamiento más reciente y a largo plazo de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d3e3e-114">By opting in, you get the latest behavior, and the long-term behavior of ASP.NET Core.</span></span>

<span data-ttu-id="d3e3e-115">El siguiente código establece el modo de compatibilidad en ASP.NET Core 2.2:</span><span class="sxs-lookup"><span data-stu-id="d3e3e-115">The following code sets the compatibility mode to ASP.NET Core 2.2:</span></span>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

<span data-ttu-id="d3e3e-116">Le recomendamos que pruebe la aplicación con la versión más reciente (`CompatibilityVersion.Latest`).</span><span class="sxs-lookup"><span data-stu-id="d3e3e-116">We recommend you test your app using the latest version (`CompatibilityVersion.Latest`).</span></span> <span data-ttu-id="d3e3e-117">Prevemos que la mayoría de las aplicaciones no tendrán cambios de comportamiento importantes usando la versión más reciente.</span><span class="sxs-lookup"><span data-stu-id="d3e3e-117">We anticipate that most apps won't have breaking behavior changes using the latest version.</span></span>

<span data-ttu-id="d3e3e-118">Las aplicaciones que llaman a `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` están protegidas frente a los cambios de comportamiento importantes incorporados en ASP.NET Core 2.1/2.2 MVC.</span><span class="sxs-lookup"><span data-stu-id="d3e3e-118">Apps that call `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` are protected from potentially breaking behavior changes introduced in the ASP.NET Core 2.1/2.2 MVC versions.</span></span> <span data-ttu-id="d3e3e-119">Esta protección:</span><span class="sxs-lookup"><span data-stu-id="d3e3e-119">This protection:</span></span>

* <span data-ttu-id="d3e3e-120">No es aplicable a todos los cambios de 2.1 y versiones posteriores, sino que tiene como destino los cambios importantes de comportamiento en tiempo de ejecución de ASP.NET Core en el subsistema de MVC.</span><span class="sxs-lookup"><span data-stu-id="d3e3e-120">Does not apply to all 2.1 and later changes, it's targeted to potentially breaking ASP.NET Core runtime behavior changes in the MVC subsystem.</span></span>
* <span data-ttu-id="d3e3e-121">No se extiende a ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="d3e3e-121">Does not extend to ASP.NET Core 3.0.</span></span>

<span data-ttu-id="d3e3e-122">La compatibilidad predeterminada de las aplicaciones ASP.NET Core 2.1 y 2.2 que **no** llaman a `SetCompatibilityVersion` es la compatibilidad 2.0.</span><span class="sxs-lookup"><span data-stu-id="d3e3e-122">The default compatibility for ASP.NET Core 2.1 and 2.2 apps that do **not** call `SetCompatibilityVersion` is 2.0 compatibility.</span></span> <span data-ttu-id="d3e3e-123">Es decir, no llamar a `SetCompatibilityVersion` es igual que llamar a `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span><span class="sxs-lookup"><span data-stu-id="d3e3e-123">That is, not calling `SetCompatibilityVersion` is the same as calling `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span></span>

<span data-ttu-id="d3e3e-124">El siguiente código establece el modo de compatibilidad en ASP.NET Core 2.2, salvo en los siguientes comportamientos:</span><span class="sxs-lookup"><span data-stu-id="d3e3e-124">The following code sets the compatibility mode to ASP.NET Core 2.2, except for the following behaviors:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="d3e3e-125">En el caso de las aplicaciones que encuentran cambios de comportamiento importantes, si se usan los modificadores de compatibilidad adecuados:</span><span class="sxs-lookup"><span data-stu-id="d3e3e-125">For apps that encounter breaking behavior changes, using the appropriate compatibility switches:</span></span>

* <span data-ttu-id="d3e3e-126">Se podrá usar la versión más reciente y descartar cambios de comportamiento importantes específicos.</span><span class="sxs-lookup"><span data-stu-id="d3e3e-126">Allows you to use the latest release and opt out of specific breaking behavior changes.</span></span>
* <span data-ttu-id="d3e3e-127">Se dispondrá de tiempo para actualizar la aplicación para que funcione con los cambios más recientes.</span><span class="sxs-lookup"><span data-stu-id="d3e3e-127">Gives you time to update your app so it works with the latest changes.</span></span>

<span data-ttu-id="d3e3e-128">En la documentación de <xref:Microsoft.AspNetCore.Mvc.MvcOptions> se incluye una completa explicación de los cambios y por qué son una mejora para la mayoría de los usuarios.</span><span class="sxs-lookup"><span data-stu-id="d3e3e-128">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions> documentation has a good explanation of what changed and why the changes are an improvement for most users.</span></span>

<span data-ttu-id="d3e3e-129">Con ASP.NET Core 3.0, los comportamientos anteriores admitidos por los modificadores de compatibilidad se han quitado.</span><span class="sxs-lookup"><span data-stu-id="d3e3e-129">With ASP.NET Core 3.0, old behaviors supported by compatibility switches have been removed.</span></span> <span data-ttu-id="d3e3e-130">Estamos convencidos de que estos son cambios positivos que beneficiarán a prácticamente todos los usuarios.</span><span class="sxs-lookup"><span data-stu-id="d3e3e-130">We feel these are positive changes benefitting nearly all users.</span></span> <span data-ttu-id="d3e3e-131">Al introducir estos cambios en 2.1 y 2.2, la mayoría de las aplicaciones pueden beneficiarse, mientras que otras tienen tiempo para actualizarse.</span><span class="sxs-lookup"><span data-stu-id="d3e3e-131">By introducing these changes in 2.1 and 2.2, most apps can benefit, while others have time to update.</span></span>
::: moniker-end
