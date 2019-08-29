---
title: Introducción a ASP.NET Core y Entity Framework 6
author: rick-anderson
description: En este artículo se muestra cómo usar Entity Framework 6 en una aplicación ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/entity-framework-6
ms.openlocfilehash: ace937e72efa2343e50b11d52ebc0a2530505758
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975594"
---
# <a name="get-started-with-aspnet-core-and-entity-framework-6"></a><span data-ttu-id="848f5-103">Introducción a ASP.NET Core y Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="848f5-103">Get Started with ASP.NET Core and Entity Framework 6</span></span>

<span data-ttu-id="848f5-104">Por [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex) y [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="848f5-104">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="848f5-105">En este artículo se muestra cómo usar Entity Framework 6 en una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="848f5-105">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>

## <a name="overview"></a><span data-ttu-id="848f5-106">Información general</span><span class="sxs-lookup"><span data-stu-id="848f5-106">Overview</span></span>

<span data-ttu-id="848f5-107">Para usar Entity Framework 6, el proyecto se tiene que compilar con .NET Framework, dado que Entity Framework 6 no es compatible con .NET Core.</span><span class="sxs-lookup"><span data-stu-id="848f5-107">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 doesn't support .NET Core.</span></span> <span data-ttu-id="848f5-108">Si necesita usar características multiplataforma, debe actualizar a [Entity Framework Core](/ef/).</span><span class="sxs-lookup"><span data-stu-id="848f5-108">If you need cross-platform features you will need to upgrade to [Entity Framework Core](/ef/).</span></span>

<span data-ttu-id="848f5-109">La manera recomendada de usar Entity Framework 6 en una aplicación ASP.NET Core es incluir el contexto y las clases de modelo de EF6 en un proyecto de biblioteca de clases que tenga como destino la plataforma completa.</span><span class="sxs-lookup"><span data-stu-id="848f5-109">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets the full framework.</span></span> <span data-ttu-id="848f5-110">Agregue una referencia a la biblioteca de clases desde el proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="848f5-110">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="848f5-111">Vea el ejemplo [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) (Solución de Visual Studio con proyectos de EF6 y ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="848f5-111">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span></span>

<span data-ttu-id="848f5-112">No se puede colocar un contexto de EF6 en un proyecto de ASP.NET Core porque los proyectos de .NET Core no admiten toda la funcionalidad que requieren los comandos de EF6 como *Enable-Migrations*.</span><span class="sxs-lookup"><span data-stu-id="848f5-112">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>

<span data-ttu-id="848f5-113">Independientemente del tipo de proyecto en el que localice el contexto de EF6, solo las herramientas de línea de comandos de EF6 funcionan con un contexto de EF6.</span><span class="sxs-lookup"><span data-stu-id="848f5-113">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="848f5-114">Por ejemplo, `Scaffold-DbContext` solo está disponible en Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="848f5-114">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="848f5-115">Si necesita la utilización de técnicas de ingeniería inversa para una base de datos en un modelo de EF6, vea [Code First to an Existing Database](https://msdn.microsoft.com/jj200620) (Code First para una base de datos existente).</span><span class="sxs-lookup"><span data-stu-id="848f5-115">If you need to do reverse engineering of a database into an EF6 model, see [Code First to an Existing Database](https://msdn.microsoft.com/jj200620).</span></span>

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="848f5-116">Marco de referencia completo y EF6 en el proyecto de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="848f5-116">Reference full framework and EF6 in the ASP.NET Core project</span></span>

<span data-ttu-id="848f5-117">El proyecto de ASP.NET Core debe hacer referencia a .NET Framework y EF6.</span><span class="sxs-lookup"><span data-stu-id="848f5-117">Your ASP.NET Core project needs to reference .NET framework and EF6.</span></span> <span data-ttu-id="848f5-118">Por ejemplo, el archivo *.csproj* del proyecto ASP.NET Core tendrá un aspecto similar al ejemplo siguiente (solo se muestran las partes relevantes del archivo).</span><span class="sxs-lookup"><span data-stu-id="848f5-118">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]

<span data-ttu-id="848f5-119">Al crear un proyecto, use la plantilla **Aplicación web ASP.NET Core (.NET Framework)** .</span><span class="sxs-lookup"><span data-stu-id="848f5-119">When creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>

## <a name="handle-connection-strings"></a><span data-ttu-id="848f5-120">Controlar las cadenas de conexión</span><span class="sxs-lookup"><span data-stu-id="848f5-120">Handle connection strings</span></span>

<span data-ttu-id="848f5-121">Las herramientas de línea de comandos de EF6 que se van a usar en el proyecto de biblioteca de clases de EF6 requieren un constructor predeterminado para poder crear instancias del contexto.</span><span class="sxs-lookup"><span data-stu-id="848f5-121">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="848f5-122">Pero, probablemente querrá especificar la cadena de conexión que se va a usar en el proyecto de ASP.NET Core, en cuyo caso el constructor de contexto debe tener un parámetro que permita pasar la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="848f5-122">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="848f5-123">Este es un ejemplo.</span><span class="sxs-lookup"><span data-stu-id="848f5-123">Here's an example.</span></span>

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]

<span data-ttu-id="848f5-124">Como el contexto de EF6 no tiene un constructor sin parámetros, el proyecto de EF6 tiene que proporcionar una implementación de [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span><span class="sxs-lookup"><span data-stu-id="848f5-124">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span></span> <span data-ttu-id="848f5-125">Las herramientas de línea de comandos de EF6 buscarán y usarán esa implementación para poder crear instancias del contexto.</span><span class="sxs-lookup"><span data-stu-id="848f5-125">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="848f5-126">Este es un ejemplo.</span><span class="sxs-lookup"><span data-stu-id="848f5-126">Here's an example.</span></span>

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]

<span data-ttu-id="848f5-127">En este ejemplo de código, la implementación de `IDbContextFactory` pasa una cadena de conexión codificada de forma rígida.</span><span class="sxs-lookup"><span data-stu-id="848f5-127">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="848f5-128">Se trata de la cadena de conexión que van a usar las herramientas de línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="848f5-128">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="848f5-129">Querrá implementar una estrategia para asegurarse de que la biblioteca de clases usa la misma cadena de conexión que la aplicación que realiza la llamada.</span><span class="sxs-lookup"><span data-stu-id="848f5-129">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="848f5-130">Por ejemplo, podría obtener el valor de una variable de entorno en los dos proyectos.</span><span class="sxs-lookup"><span data-stu-id="848f5-130">For example, you could get the value from an environment variable in both projects.</span></span>

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="848f5-131">Configurar la inserción de dependencias en el proyecto de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="848f5-131">Set up dependency injection in the ASP.NET Core project</span></span>

<span data-ttu-id="848f5-132">En el archivo *Startup.cs* del proyecto de Core, establezca el contexto de EF6 para la inserción de dependencias (DI) en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="848f5-132">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="848f5-133">La duración de cada solicitud se debe configurar como ámbito de los objetos de contexto de EF.</span><span class="sxs-lookup"><span data-stu-id="848f5-133">EF context objects should be scoped for a per-request lifetime.</span></span>

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

<span data-ttu-id="848f5-134">Después, puede obtener una instancia del contexto en los controladores mediante DI.</span><span class="sxs-lookup"><span data-stu-id="848f5-134">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="848f5-135">El código es similar al que escribiría para un contexto de EF Core:</span><span class="sxs-lookup"><span data-stu-id="848f5-135">The code is similar to what you'd write for an EF Core context:</span></span>

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]

## <a name="sample-application"></a><span data-ttu-id="848f5-136">Aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="848f5-136">Sample application</span></span>

<span data-ttu-id="848f5-137">Para obtener una aplicación de ejemplo funcional, vea la [solución de Visual Studio de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) que se incluye en este artículo.</span><span class="sxs-lookup"><span data-stu-id="848f5-137">For a working sample application, see the [sample Visual Studio solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>

<span data-ttu-id="848f5-138">Este ejemplo se puede crear desde cero mediante los pasos siguientes en Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="848f5-138">This sample can be created from scratch by the following steps in Visual Studio:</span></span>

* <span data-ttu-id="848f5-139">Cree una solución.</span><span class="sxs-lookup"><span data-stu-id="848f5-139">Create a solution.</span></span>

* <span data-ttu-id="848f5-140">**Agregar** > **Nuevo proyecto** > **Web** > **Aplicación web de ASP.NET Core**</span><span class="sxs-lookup"><span data-stu-id="848f5-140">**Add** > **New Project** > **Web** > **ASP.NET Core Web Application**</span></span>
  * <span data-ttu-id="848f5-141">En el cuadro de diálogo de selección de la plantilla de proyecto, seleccione la API y .NET Framework en la lista desplegable.</span><span class="sxs-lookup"><span data-stu-id="848f5-141">In project template selection dialog, select API and .NET Framework in dropdown</span></span>

* <span data-ttu-id="848f5-142">**Agregar** > **Nuevo proyecto** > **Escritorio de Windows** > **Biblioteca de clases (.NET Framework)**</span><span class="sxs-lookup"><span data-stu-id="848f5-142">**Add** > **New Project** > **Windows Desktop** > **Class Library (.NET Framework)**</span></span>

* <span data-ttu-id="848f5-143">En la **Consola del Administrador de paquetes** (PMC) para ambos proyectos, ejecute el comando `Install-Package Entityframework`.</span><span class="sxs-lookup"><span data-stu-id="848f5-143">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>

* <span data-ttu-id="848f5-144">En el proyecto de biblioteca de clases, cree clases de modelo de datos, una clase de contexto y una implementación de `IDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="848f5-144">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>

* <span data-ttu-id="848f5-145">En PMC para el proyecto de biblioteca de clases, ejecute los comandos `Enable-Migrations` y `Add-Migration Initial`.</span><span class="sxs-lookup"><span data-stu-id="848f5-145">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="848f5-146">Si ha configurado el proyecto de ASP.NET Core como proyecto de inicio, agregue `-StartupProjectName EF6` a estos comandos.</span><span class="sxs-lookup"><span data-stu-id="848f5-146">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span>

* <span data-ttu-id="848f5-147">En el proyecto de Core, agregue una referencia de proyecto al proyecto de biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="848f5-147">In the Core project, add a project reference to the class library project.</span></span>

* <span data-ttu-id="848f5-148">En el proyecto de Core, en *Startup.cs*, registre el contexto para DI.</span><span class="sxs-lookup"><span data-stu-id="848f5-148">In the Core project, in *Startup.cs*, register the context for DI.</span></span>

* <span data-ttu-id="848f5-149">En el proyecto de Core, en *appsettings.json*, agregue la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="848f5-149">In the Core project, in *appsettings.json*, add the connection string.</span></span>

* <span data-ttu-id="848f5-150">En el proyecto de Core, agregue un controlador y vistas para comprobar que puede leer y escribir datos.</span><span class="sxs-lookup"><span data-stu-id="848f5-150">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="848f5-151">(Tenga en cuenta que el scaffolding de ASP.NET Core MVC no funcionará con el contexto de EF6 al que se hace referencia desde la biblioteca de clases).</span><span class="sxs-lookup"><span data-stu-id="848f5-151">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

## <a name="summary"></a><span data-ttu-id="848f5-152">Resumen</span><span class="sxs-lookup"><span data-stu-id="848f5-152">Summary</span></span>

<span data-ttu-id="848f5-153">En este artículo se proporcionan instrucciones básicas para el uso de Entity Framework 6 en una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="848f5-153">This article has provided basic guidance for using Entity Framework 6 in an ASP.NET Core application.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="848f5-154">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="848f5-154">Additional resources</span></span>

* <span data-ttu-id="848f5-155">[Entity Framework - Code-Based Configuration](https://msdn.microsoft.com/data/jj680699.aspx) (Entity Framework: configuración basada en código)</span><span class="sxs-lookup"><span data-stu-id="848f5-155">[Entity Framework - Code-Based Configuration](https://msdn.microsoft.com/data/jj680699.aspx)</span></span>
