---
title: Uso de ensamblados de inicio de hospedaje en ASP.NET Core
author: rick-anderson
description: Sepa cómo mejorar una aplicación ASP.NET Core desde un ensamblado externo con una implementación de IHostingStartup.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: 8cf6a4467f041fa71b75ee8d1e7a08d8f572acf3
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106356"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="5e953-103">Uso de ensamblados de inicio de hospedaje en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5e953-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="5e953-104">Por [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="5e953-104">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5e953-105">Una implementación de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (inicio de hospedaje) agrega mejoras a una aplicación al iniciarla a partir de un ensamblado externo.</span><span class="sxs-lookup"><span data-stu-id="5e953-105">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="5e953-106">Por ejemplo, una biblioteca externa puede usar una implementación de inicio de hospedaje para suministrar más servicios o proveedores de configuración a una aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="5e953-107">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5e953-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="5e953-108">Atributo HostingStartup</span><span class="sxs-lookup"><span data-stu-id="5e953-108">HostingStartup attribute</span></span>

<span data-ttu-id="5e953-109">Un atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) indica la presencia de un ensamblado de inicio de hospedaje para activar en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="5e953-109">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="5e953-110">El ensamblado de entrada o el ensamblado que contiene la clase `Startup` se analiza automáticamente para detectar el atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5e953-110">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="5e953-111">La lista de ensamblados para buscar atributos `HostingStartup` se carga en tiempo de ejecución desde la configuración de [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="5e953-111">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="5e953-112">La lista de ensamblados que se excluirán de la detección se carga desde [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="5e953-112">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="5e953-113">En el ejemplo siguiente, el espacio de nombres del ensamblado de inicio de hospedaje es `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="5e953-113">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="5e953-114">La clase que contiene el código de inicio de hospedaje es `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="5e953-114">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="5e953-115">Normalmente el atributo `HostingStartup` se encuentra en el archivo de clase de implementación `IHostingStartup` del ensamblado de inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="5e953-115">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="5e953-116">Detectar los ensamblados de inicio de hospedaje cargados</span><span class="sxs-lookup"><span data-stu-id="5e953-116">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="5e953-117">Para detectar los ensamblados de inicio de hospedaje cargados, habilite el registro y consulte los registros de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-117">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="5e953-118">En ellos se registran los errores que se producen al cargar ensamblados.</span><span class="sxs-lookup"><span data-stu-id="5e953-118">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="5e953-119">Los ensamblados de inicio de hospedaje se registran en el nivel de depuración y se registran todos los errores.</span><span class="sxs-lookup"><span data-stu-id="5e953-119">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="5e953-120">Deshabilitar la carga automática de ensamblados de inicio de hospedaje</span><span class="sxs-lookup"><span data-stu-id="5e953-120">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="5e953-121">Para deshabilitar la carga automática de los ensamblados de inicio de hospedaje, use uno de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="5e953-121">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="5e953-122">Para evitar que se carguen todos los ensamblados de inicio de hospedaje, establezca uno de los procedimientos siguientes en `true` o `1`:</span><span class="sxs-lookup"><span data-stu-id="5e953-122">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="5e953-123">La opción de configuración de hospedaje para evitar el inicio de hospedaje:</span><span class="sxs-lookup"><span data-stu-id="5e953-123">Prevent Hosting Startup host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="5e953-124">La variable de entorno `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="5e953-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="5e953-125">Para evitar la carga de ensamblados de inicio de hospedaje específicos, establezca una de las opciones siguientes en una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para excluir durante el inicio:</span><span class="sxs-lookup"><span data-stu-id="5e953-125">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="5e953-126">La opción de configuración de host para ensamblados de exclusión de inicio de hospedaje:</span><span class="sxs-lookup"><span data-stu-id="5e953-126">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="5e953-127">La variable de entorno `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="5e953-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="5e953-128">Si se establecen la opción de configuración de host y la variable de entorno, la configuración de host controla el comportamiento.</span><span class="sxs-lookup"><span data-stu-id="5e953-128">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="5e953-129">Deshabilitar los ensamblados de inicio de hospedaje a través de la variable de entorno o de la configuración de host hace que el ensamblado se deshabilite globalmente y, asimismo, puede deshabilitar también otras características de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-129">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="5e953-130">Proyecto</span><span class="sxs-lookup"><span data-stu-id="5e953-130">Project</span></span>

<span data-ttu-id="5e953-131">Cree un inicio de hospedaje con cualquiera de los siguientes tipos de proyecto:</span><span class="sxs-lookup"><span data-stu-id="5e953-131">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="5e953-132">Biblioteca de clases</span><span class="sxs-lookup"><span data-stu-id="5e953-132">Class library</span></span>](#class-library)
* [<span data-ttu-id="5e953-133">Aplicación de consola sin un punto de entrada</span><span class="sxs-lookup"><span data-stu-id="5e953-133">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="5e953-134">Biblioteca de clases</span><span class="sxs-lookup"><span data-stu-id="5e953-134">Class library</span></span>

<span data-ttu-id="5e953-135">Una mejora de inicio de hospedaje se puede proporcionar en una biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="5e953-135">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="5e953-136">La biblioteca contiene un atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5e953-136">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="5e953-137">El [código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) incluye una aplicación de Razor Pages, *HostingStartupApp*, y una biblioteca de clases, *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="5e953-137">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="5e953-138">La biblioteca de clases:</span><span class="sxs-lookup"><span data-stu-id="5e953-138">The class library:</span></span>

* <span data-ttu-id="5e953-139">Contiene una clase de inicio de hospedaje, `ServiceKeyInjection`, que implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5e953-139">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="5e953-140">`ServiceKeyInjection` agrega un par de cadenas de servicio a la configuración de la aplicación mediante el proveedor de configuración en memoria ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="5e953-140">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="5e953-141">Incluye un atributo `HostingStartup` que identifica espacio de nombres y la clase del inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="5e953-141">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="5e953-142">El método <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> de la clase `ServiceKeyInjection` usa <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> para agregar mejoras a una aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-142">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="5e953-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="5e953-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="5e953-144">La página de índice de la aplicación lee y procesa los valores de configuración para las dos claves establecidas por el ensamblado de inicio de hospedaje de la biblioteca de clases:</span><span class="sxs-lookup"><span data-stu-id="5e953-144">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="5e953-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="5e953-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="5e953-146">El [código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) también incluye un proyecto de paquete NuGet que proporciona un inicio de hospedaje independiente, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="5e953-146">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="5e953-147">El paquete tiene las mismas características que la biblioteca de clases que se ha descrito anteriormente.</span><span class="sxs-lookup"><span data-stu-id="5e953-147">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="5e953-148">El paquete:</span><span class="sxs-lookup"><span data-stu-id="5e953-148">The package:</span></span>

* <span data-ttu-id="5e953-149">Contiene una clase de inicio de hospedaje, `ServiceKeyInjection`, que implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5e953-149">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="5e953-150">`ServiceKeyInjection` agrega un par de cadenas de servicio a la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-150">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="5e953-151">Incluye un atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5e953-151">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="5e953-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="5e953-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="5e953-153">La página de índice de la aplicación lee y procesa los valores de configuración para las dos claves establecidas por el ensamblado de inicio de hospedaje del paquete:</span><span class="sxs-lookup"><span data-stu-id="5e953-153">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="5e953-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="5e953-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="5e953-155">Aplicación de consola sin un punto de entrada</span><span class="sxs-lookup"><span data-stu-id="5e953-155">Console app without an entry point</span></span>

<span data-ttu-id="5e953-156">*Este enfoque solo está disponible para las aplicaciones de .NET Core, no de .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="5e953-156">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="5e953-157">Se puede proporcionar una mejora del inicio de hospedaje dinámico que no requiere una referencia de tiempo de compilación para la activación en una aplicación de consola sin un punto de entrada que contiene un atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5e953-157">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="5e953-158">Publicar la aplicación de consola genera un ensamblado de inicio de hospedaje que se puede consumir desde el almacén en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="5e953-158">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="5e953-159">En este proceso se usa una aplicación de consola sin un punto de entrada porque:</span><span class="sxs-lookup"><span data-stu-id="5e953-159">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="5e953-160">Se requiere un archivo de dependencias para consumir el inicio de hospedaje en el ensamblado de inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="5e953-160">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="5e953-161">Un archivo de dependencias es un recurso de aplicación ejecutable que se genera mediante la publicación de una aplicación, no una biblioteca.</span><span class="sxs-lookup"><span data-stu-id="5e953-161">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="5e953-162">Una biblioteca no se puede agregar directamente al [almacén de paquetes en tiempo de ejecución](/dotnet/core/deploying/runtime-store), lo que requiere un proyecto ejecutable que tenga como destino el tiempo de ejecución compartido.</span><span class="sxs-lookup"><span data-stu-id="5e953-162">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="5e953-163">En la creación de un inicio de hospedaje dinámico:</span><span class="sxs-lookup"><span data-stu-id="5e953-163">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="5e953-164">Se crea un ensamblado de inicio de hospedaje desde la aplicación de consola sin un punto de entrada que:</span><span class="sxs-lookup"><span data-stu-id="5e953-164">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="5e953-165">Incluye una clase que contiene la implementación de `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5e953-165">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="5e953-166">Incluye un atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) para identificar la clase de implementación `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5e953-166">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="5e953-167">La aplicación de consola se publica para obtener las dependencias del inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="5e953-167">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="5e953-168">Una consecuencia de la publicación de la aplicación de consola es que las dependencias no usadas se cortan en el archivo de dependencias.</span><span class="sxs-lookup"><span data-stu-id="5e953-168">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="5e953-169">El archivo de dependencias se modifica para establecer la ubicación en tiempo de ejecución del ensamblado de inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="5e953-169">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="5e953-170">El ensamblado de inicio de hospedaje y su archivo de dependencias se colocan en el almacén de paquetes en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="5e953-170">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="5e953-171">Para detectar el ensamblado de inicio de hospedaje y su archivo de dependencias, se enumeran en un par de variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="5e953-171">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="5e953-172">La aplicación de consola hace referencia al paquete [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="5e953-172">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="5e953-173">Un atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifica una clase como una implementación de `IHostingStartup` para cargarla e implementarla al compilar <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span><span class="sxs-lookup"><span data-stu-id="5e953-173">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="5e953-174">En el siguiente ejemplo, el espacio de nombres es `StartupEnhancement` y la clase, `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="5e953-174">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="5e953-175">Una clase implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5e953-175">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="5e953-176">El método <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> de la clase usa <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> para agregar mejoras a una aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-176">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="5e953-177">El tiempo de ejecución llama a `IHostingStartup.Configure` en el ensamblado de inicio del hospedaje antes de `Startup.Configure` en el código de usuario, lo que permite que el código de usuario sobrescriba cualquier configuración facilitada por el ensamblado de inicio del hospedaje.</span><span class="sxs-lookup"><span data-stu-id="5e953-177">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="5e953-178">Al crear un proyecto de `IHostingStartup`, el archivo de dependencias ( *.deps.json*) establece la ubicación de `runtime` del ensamblado en la carpeta *bin*:</span><span class="sxs-lookup"><span data-stu-id="5e953-178">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="5e953-179">Solo se muestra parte del archivo.</span><span class="sxs-lookup"><span data-stu-id="5e953-179">Only part of the file is shown.</span></span> <span data-ttu-id="5e953-180">El nombre del ensamblado en el ejemplo es `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="5e953-180">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="5e953-181">Configuración proporcionada por el inicio de hospedaje</span><span class="sxs-lookup"><span data-stu-id="5e953-181">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="5e953-182">Existen dos enfoques para controlar la configuración según si quiere que la configuración del inicio de hospedaje tenga prioridad o si la configuración de la aplicación tenga prioridad:</span><span class="sxs-lookup"><span data-stu-id="5e953-182">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="5e953-183">Proporcione la configuración a la aplicación utilizando <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> para cargar la configuración después de que se ejecuten los delegados <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-183">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="5e953-184">La configuración del inicio de hospedaje tiene prioridad sobre la configuración de la aplicación con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="5e953-184">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="5e953-185">Proporcione la configuración a la aplicación utilizando <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> para cargar la configuración antes de que se ejecuten los delegados <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-185">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="5e953-186">Los valores de configuración de la aplicación tienen prioridad a sobre los proporcionados por el inicio de hospedaje con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="5e953-186">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="5e953-187">Especificar el ensamblado de inicio de hospedaje</span><span class="sxs-lookup"><span data-stu-id="5e953-187">Specify the hosting startup assembly</span></span>

<span data-ttu-id="5e953-188">En el caso de un inicio de hospedaje proporcionado por una biblioteca de clase o una aplicación de consola, especifique el nombre del ensamblado de inicio de hospedaje en la variable de entorno `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="5e953-188">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="5e953-189">La variable de entorno es una lista de ensamblados delimitada por punto y coma.</span><span class="sxs-lookup"><span data-stu-id="5e953-189">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="5e953-190">Solo se examinan los ensamblados de inicio de hospedaje en busca del atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5e953-190">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="5e953-191">En la aplicación de ejemplo, *HostingStartupApp*, para descubrir los nuevos inicios de hospedaje descritos anteriormente, la variable de entorno se establece en el siguiente valor:</span><span class="sxs-lookup"><span data-stu-id="5e953-191">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="5e953-192">Un ensamblado de inicio de hospedaje también se puede establecer mediante la configuración del host de ensamblados de inicio de hospedaje:</span><span class="sxs-lookup"><span data-stu-id="5e953-192">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

<span data-ttu-id="5e953-193">Cuando existen varios ensamblados de inicio de hospedaje, sus métodos <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> se ejecutan en el orden en el que aparecen los ensamblados.</span><span class="sxs-lookup"><span data-stu-id="5e953-193">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="5e953-194">Activación</span><span class="sxs-lookup"><span data-stu-id="5e953-194">Activation</span></span>

<span data-ttu-id="5e953-195">Las opciones de activación del inicio de hospedaje son:</span><span class="sxs-lookup"><span data-stu-id="5e953-195">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="5e953-196">[Almacén de tiempo de ejecución](#runtime-store): no se requiere una referencia de tiempo de compilación para la activación.</span><span class="sxs-lookup"><span data-stu-id="5e953-196">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="5e953-197">La aplicación de ejemplo coloca el ensamblado de inicio de hospedaje y los archivos de dependencias en una carpeta, *implementación*, para facilitar la implementación del inicio del hospedaje en un entorno de varios equipos.</span><span class="sxs-lookup"><span data-stu-id="5e953-197">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="5e953-198">La carpeta *implementación* también incluye un script de PowerShell que crea o modifica las variables de entorno en el sistema de implementación para habilitar el inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="5e953-198">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="5e953-199">Se requiere una referencia al tiempo de compilación para la activación</span><span class="sxs-lookup"><span data-stu-id="5e953-199">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="5e953-200">Paquete NuGet</span><span class="sxs-lookup"><span data-stu-id="5e953-200">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="5e953-201">Carpeta bin del proyecto</span><span class="sxs-lookup"><span data-stu-id="5e953-201">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="5e953-202">Almacén en tiempo de ejecución</span><span class="sxs-lookup"><span data-stu-id="5e953-202">Runtime store</span></span>

<span data-ttu-id="5e953-203">La implementación de inicio de hospedaje se coloca en el [almacén en tiempo de ejecución](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="5e953-203">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="5e953-204">No es necesario que la aplicación mejorada haga ninguna referencia de tiempo de compilación al ensamblado.</span><span class="sxs-lookup"><span data-stu-id="5e953-204">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="5e953-205">Una vez compilado el inicio de hospedaje, se genera un almacén en tiempo de ejecución mediante el archivo del proyecto de manifiesto y el comando [dotnet store](/dotnet/core/tools/dotnet-store).</span><span class="sxs-lookup"><span data-stu-id="5e953-205">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="5e953-206">En la aplicación de ejemplo (proyecto *RuntimeStore*), se usa el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="5e953-206">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="5e953-207">Para que el tiempo de ejecución detecte el almacén en tiempo de ejecución, la ubicación del almacén en tiempo de ejecución se agrega a la variable de entorno `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="5e953-207">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="5e953-208">**Modificar y colocar el archivo de dependencias del inicio de hospedaje**</span><span class="sxs-lookup"><span data-stu-id="5e953-208">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="5e953-209">Para activar la mejora sin una referencia de paquete a la mejora, especifique dependencias adicionales del tiempo de ejecución en `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="5e953-209">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="5e953-210">`additionalDeps` permite:</span><span class="sxs-lookup"><span data-stu-id="5e953-210">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="5e953-211">Ampliar el gráfico de la biblioteca de la aplicación al proporcionar un conjunto de archivos *.deps.json* adicionales para combinarlos con el propio archivo *.deps.json* de la aplicación durante el inicio.</span><span class="sxs-lookup"><span data-stu-id="5e953-211">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="5e953-212">Permitir la detección y la carga del ensamblado de inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="5e953-212">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="5e953-213">El enfoque recomendado para generar el archivo de dependencias adicionales es:</span><span class="sxs-lookup"><span data-stu-id="5e953-213">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="5e953-214">Ejecutar `dotnet publish` en el archivo de manifiesto del almacén en tiempo de ejecución al que se hace referencia en la sección anterior.</span><span class="sxs-lookup"><span data-stu-id="5e953-214">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="5e953-215">Quitar la referencia del manifiesto de las bibliotecas y de la sección `runtime` del archivo *deps.json* resultante.</span><span class="sxs-lookup"><span data-stu-id="5e953-215">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="5e953-216">En el proyecto de ejemplo, la propiedad `store.manifest/1.0.0` se quita de las secciones `targets` y `libraries`:</span><span class="sxs-lookup"><span data-stu-id="5e953-216">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="5e953-217">Coloque el archivo *.deps.json* en la siguiente ubicación:</span><span class="sxs-lookup"><span data-stu-id="5e953-217">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="5e953-218">`{ADDITIONAL DEPENDENCIES PATH}`: ubicación agregada a la variable de entorno `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="5e953-218">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="5e953-219">`{SHARED FRAMEWORK NAME}`: marco compartido necesario para este archivo de dependencias adicionales.</span><span class="sxs-lookup"><span data-stu-id="5e953-219">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="5e953-220">`{SHARED FRAMEWORK VERSION}`: versión mínima del marco compartido.</span><span class="sxs-lookup"><span data-stu-id="5e953-220">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="5e953-221">`{ENHANCEMENT ASSEMBLY NAME}`: nombre del ensamblado de la mejora.</span><span class="sxs-lookup"><span data-stu-id="5e953-221">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="5e953-222">En la aplicación de ejemplo (proyecto *RuntimeStore*), el archivo de dependencias adicionales se coloca en la siguiente ubicación:</span><span class="sxs-lookup"><span data-stu-id="5e953-222">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="5e953-223">Para que el tiempo de ejecución detecte la ubicación del almacén en tiempo de ejecución, la ubicación del archivo de dependencias adicionales se agrega a la variable de entorno `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="5e953-223">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="5e953-224">En la aplicación de ejemplo (proyecto *RuntimeStore*), la creación del almacén en tiempo de ejecución y la generación del archivo de dependencias adicionales se realizan con un script de [PowerShell](/powershell/scripting/powershell-scripting).</span><span class="sxs-lookup"><span data-stu-id="5e953-224">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="5e953-225">Para obtener ejemplos de cómo establecer variables de entorno en distintos sistemas operativos, vea [Uso de varios entornos](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="5e953-225">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="5e953-226">**Implementación**</span><span class="sxs-lookup"><span data-stu-id="5e953-226">**Deployment**</span></span>

<span data-ttu-id="5e953-227">Para facilitar la implementación de un inicio de hospedaje en un entorno de varios equipos, la aplicación de ejemplo crea una carpeta de *implementación* en el resultado publicado que contiene:</span><span class="sxs-lookup"><span data-stu-id="5e953-227">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="5e953-228">El almacén en tiempo de ejecución de inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="5e953-228">The hosting startup runtime store.</span></span>
* <span data-ttu-id="5e953-229">El archivo de dependencias del inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="5e953-229">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="5e953-230">Un script de PowerShell que crea o modifica `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` y `DOTNET_ADDITIONAL_DEPS` para admitir la activación del inicio del hospedaje.</span><span class="sxs-lookup"><span data-stu-id="5e953-230">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="5e953-231">Ejecute el script desde un símbolo del sistema de PowerShell administrativo en el sistema de implementación.</span><span class="sxs-lookup"><span data-stu-id="5e953-231">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="5e953-232">Detección de</span><span class="sxs-lookup"><span data-stu-id="5e953-232">NuGet package</span></span>

<span data-ttu-id="5e953-233">Una mejora de inicio de hospedaje se puede proporcionar en un paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="5e953-233">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="5e953-234">El paquete tiene un atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5e953-234">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="5e953-235">Los tipos de inicio de hospedaje proporcionados por el paquete están disponibles en la aplicación mediante cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="5e953-235">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="5e953-236">El archivo de proyecto de la aplicación mejorada hace una referencia al paquete para el inicio de hospedaje en el archivo de proyecto de la aplicación (una referencia de tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="5e953-236">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="5e953-237">Con la referencia de tiempo de compilación realizada, el ensamblado de inicio de hospedaje y todas sus dependencias se incorporan en el archivo de dependencia de la aplicación ( *.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="5e953-237">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="5e953-238">Este enfoque se aplica a un paquete de ensamblado de inicio de hospedaje publicado en [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="5e953-238">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="5e953-239">El archivo de dependencias del inicio de hospedaje está disponible para la aplicación mejorada como se describe en la sección [Almacén en tiempo de ejecución](#runtime-store) (sin una referencia de tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="5e953-239">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="5e953-240">Para obtener más información sobre los paquetes NuGet y el almacén en tiempo de ejecución, vea los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="5e953-240">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="5e953-241">Cómo crear un paquete NuGet con herramientas multiplataforma</span><span class="sxs-lookup"><span data-stu-id="5e953-241">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="5e953-242">Publicar paquetes</span><span class="sxs-lookup"><span data-stu-id="5e953-242">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* <span data-ttu-id="5e953-243">[Runtime package store](/dotnet/core/deploying/runtime-store) (Almacenamiento de paquetes en tiempo de ejecución)</span><span class="sxs-lookup"><span data-stu-id="5e953-243">[Runtime package store](/dotnet/core/deploying/runtime-store)</span></span>

### <a name="project-bin-folder"></a><span data-ttu-id="5e953-244">Carpeta bin del proyecto</span><span class="sxs-lookup"><span data-stu-id="5e953-244">Project bin folder</span></span>

<span data-ttu-id="5e953-245">Un ensamblado implementado por *bin* puede proporcionar una mejora del inicio de hospedaje en la aplicación mejorada.</span><span class="sxs-lookup"><span data-stu-id="5e953-245">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="5e953-246">Los tipos de inicio de hospedaje que proporciona el ensamblado están disponibles en la aplicación mediante uno de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="5e953-246">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="5e953-247">El archivo de proyecto de la aplicación mejorada hace referencia de ensamblado al inicio de hospedaje (una referencia de tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="5e953-247">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="5e953-248">Con la referencia de tiempo de compilación realizada, el ensamblado de inicio de hospedaje y todas sus dependencias se incorporan en el archivo de dependencia de la aplicación ( *.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="5e953-248">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="5e953-249">Este método se aplica cuando el escenario de implementación llama para realizar una referencia en tiempo de compilación al ensamblado del inicio del hospedaje (archivo *.dll*) y mover el ensamblado a una de las siguientes ubicaciones:</span><span class="sxs-lookup"><span data-stu-id="5e953-249">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="5e953-250">Proyecto utilizado.</span><span class="sxs-lookup"><span data-stu-id="5e953-250">The consuming project.</span></span>
  * <span data-ttu-id="5e953-251">Ubicación a la que puede acceder el proyecto utilizado.</span><span class="sxs-lookup"><span data-stu-id="5e953-251">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="5e953-252">El archivo de dependencias del inicio de hospedaje está disponible para la aplicación mejorada como se describe en la sección [Almacén en tiempo de ejecución](#runtime-store) (sin una referencia de tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="5e953-252">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="5e953-253">Si el destino es .NET Framework, el ensamblado se puede cargar en el contexto de carga predeterminado, lo que en .NET Framework significa que el ensamblado se encuentra en cualquiera de las siguientes ubicaciones:</span><span class="sxs-lookup"><span data-stu-id="5e953-253">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="5e953-254">Ruta de acceso base de la aplicación: carpeta *bin* en la que se encuentra el archivo ejecutable ( *.exe*) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-254">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="5e953-255">Caché global de ensamblados (GAC): almacena los ensamblados que comparten varias aplicaciones .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="5e953-255">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="5e953-256">Para obtener más información, vea [Cómo: Instalar un ensamblado en la caché global de ensamblados](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) en la documentación de .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="5e953-256">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="5e953-257">Código de ejemplo</span><span class="sxs-lookup"><span data-stu-id="5e953-257">Sample code</span></span>

<span data-ttu-id="5e953-258">En el [código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([cómo descargar](xref:index#how-to-download-a-sample)) se muestran escenarios de implementación de inicio de hospedaje:</span><span class="sxs-lookup"><span data-stu-id="5e953-258">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="5e953-259">Cada uno de los dos ensamblados de inicio de hospedaje (bibliotecas de clases) establece un par clave-valor de configuración en memoria:</span><span class="sxs-lookup"><span data-stu-id="5e953-259">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="5e953-260">Paquete NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="5e953-260">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="5e953-261">Biblioteca de clases (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="5e953-261">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="5e953-262">Se activa un inicio de hospedaje desde un ensamblado implementado por el almacén de tiempo de ejecución (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="5e953-262">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="5e953-263">Este ensamblado agrega dos middleware a la aplicación (mientras se inicia) que proporcionan información de diagnóstico en:</span><span class="sxs-lookup"><span data-stu-id="5e953-263">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="5e953-264">Servicios registrados</span><span class="sxs-lookup"><span data-stu-id="5e953-264">Registered services</span></span>
  * <span data-ttu-id="5e953-265">Dirección (esquema, host, ruta de acceso base, ruta de acceso, cadena de consulta)</span><span class="sxs-lookup"><span data-stu-id="5e953-265">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="5e953-266">Conexión (dirección IP remota, puerto remoto, dirección IP local, puerto local, certificado de cliente)</span><span class="sxs-lookup"><span data-stu-id="5e953-266">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="5e953-267">Encabezados de solicitud</span><span class="sxs-lookup"><span data-stu-id="5e953-267">Request headers</span></span>
  * <span data-ttu-id="5e953-268">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="5e953-268">Environment variables</span></span>

<span data-ttu-id="5e953-269">Para ejecutar el ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5e953-269">To run the sample:</span></span>

<span data-ttu-id="5e953-270">**Activación desde un paquete NuGet**</span><span class="sxs-lookup"><span data-stu-id="5e953-270">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="5e953-271">Compile el paquete *HostingStartupPackage* con el comando [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="5e953-271">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="5e953-272">Agregue el nombre de ensamblado del paquete de *HostingStartupPackage* a la variable de entorno `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="5e953-272">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="5e953-273">Compile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-273">Compile and run the app.</span></span> <span data-ttu-id="5e953-274">Una referencia de paquete está presente en la aplicación mejorada (una referencia de tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="5e953-274">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="5e953-275">El parámetro `<PropertyGroup>` del archivo del proyecto de la aplicación especifica el resultado del proyecto de paquete ( *../HostingStartupPackage/bin/Debug*) como origen del paquete.</span><span class="sxs-lookup"><span data-stu-id="5e953-275">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="5e953-276">Esto permite que la aplicación utilice el paquete sin cargar el paquete a [nuget.org](https://www.nuget.org/). Para obtener más información, vea las notas que encontrará en el archivo del proyecto de HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="5e953-276">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="5e953-277">Observe que los valores de la clave de configuración del servicio representados por la página de índice coinciden con los valores establecidos por método `ServiceKeyInjection.Configure` del paquete.</span><span class="sxs-lookup"><span data-stu-id="5e953-277">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="5e953-278">Si realiza cambios en el proyecto *HostingStartupPackage* y lo vuelve a compilar, borre las cachés del paquete NuGet local para asegurarse de que *HostingStartupApp* recibe el paquete actualizado y no un paquete en desuso de la caché local.</span><span class="sxs-lookup"><span data-stu-id="5e953-278">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="5e953-279">Para borrar las cachés de NuGet locales, ejecute el siguiente comando [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals):</span><span class="sxs-lookup"><span data-stu-id="5e953-279">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="5e953-280">**Activación desde una biblioteca de clases**</span><span class="sxs-lookup"><span data-stu-id="5e953-280">**Activation from a class library**</span></span>

1. <span data-ttu-id="5e953-281">Compile la biblioteca de clases *HostingStartupLibrary* con el comando [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="5e953-281">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="5e953-282">Agregue el nombre del ensamblado de la biblioteca de clases *HostingStartupLibrary* a la variable de entorno `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="5e953-282">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="5e953-283">Implemente con *bin*-el ensamblado de la biblioteca de clases en la aplicación copiando el archivo *HostingStartupLibrary.dll* desde el resultado compilado de la biblioteca de aplicaciones en la carpeta *bin/Debug* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-283">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="5e953-284">Compile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-284">Compile and run the app.</span></span> <span data-ttu-id="5e953-285">Un parámetro `<ItemGroup>` del archivo de proyecto de la aplicación hace referencia al ensamblado de la biblioteca de clases ( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (una referencia en tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="5e953-285">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="5e953-286">Para obtener más información, vea las notas que encontrará en el archivo del proyecto de HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="5e953-286">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="5e953-287">Observe que los valores de la clave de configuración del servicio representados por la página de índice coinciden con los valores establecidos por el método `ServiceKeyInjection.Configure` de la biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="5e953-287">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="5e953-288">**Activación desde un ensamblado implementado por el almacén de tiempo de ejecución**</span><span class="sxs-lookup"><span data-stu-id="5e953-288">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="5e953-289">El proyecto *StartupDiagnostics* usa [PowerShell](/powershell/scripting/powershell-scripting) para modificar el archivo *StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="5e953-289">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="5e953-290">PowerShell se instala de forma predeterminada en Windows a partir de Windows 7 SP1 y Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="5e953-290">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="5e953-291">Para obtener PowerShell en otras plataformas, consulte [Instalación de varias versiones de PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="5e953-291">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="5e953-292">Ejecute el script *build.ps1* en la carpeta *RuntimeStore*.</span><span class="sxs-lookup"><span data-stu-id="5e953-292">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="5e953-293">El script:</span><span class="sxs-lookup"><span data-stu-id="5e953-293">The script:</span></span>
   * <span data-ttu-id="5e953-294">Genera el paquete `StartupDiagnostics` en la carpeta *obj\packages*.</span><span class="sxs-lookup"><span data-stu-id="5e953-294">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="5e953-295">Genera el almacén en tiempo de ejecución para `StartupDiagnostics` en la carpeta *store*.</span><span class="sxs-lookup"><span data-stu-id="5e953-295">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="5e953-296">El comando `dotnet store` del script usa el [identificador de tiempo de ejecución (RID)](/dotnet/core/rid-catalog) `win7-x64` para un inicio de hospedaje implementado en Windows.</span><span class="sxs-lookup"><span data-stu-id="5e953-296">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="5e953-297">Para proporcionar el inicio de hospedaje para otro tiempo de ejecución, sustituya el RID correcto en la línea 37 del script.</span><span class="sxs-lookup"><span data-stu-id="5e953-297">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="5e953-298">El almacén en tiempo de ejecución de `StartupDiagnostics` se moverá más tarde al almacén en tiempo de ejecución del usuario o del sistema en la máquina donde se va a consumir el ensamblado.</span><span class="sxs-lookup"><span data-stu-id="5e953-298">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="5e953-299">La ubicación de instalación del almacén en tiempo de ejecución del usuario para el ensamblado `StartupDiagnostics` es *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="5e953-299">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="5e953-300">Genera el parámetro `additionalDeps` para `StartupDiagnostics` en la carpeta *additionalDeps*.</span><span class="sxs-lookup"><span data-stu-id="5e953-300">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="5e953-301">Las dependencias adicionales se moverían posteriormente a las dependencias adicionales del usuario o del sistema.</span><span class="sxs-lookup"><span data-stu-id="5e953-301">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="5e953-302">La ubicación de instalación de las dependencias adicionales `StartupDiagnostics` del usuario es *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="5e953-302">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="5e953-303">Coloca el archivo *deploy.ps1* en la carpeta *deployment*.</span><span class="sxs-lookup"><span data-stu-id="5e953-303">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="5e953-304">Ejecute el script *deploy.ps1* en la carpeta *deployment*.</span><span class="sxs-lookup"><span data-stu-id="5e953-304">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="5e953-305">El script anexa:</span><span class="sxs-lookup"><span data-stu-id="5e953-305">The script appends:</span></span>
   * <span data-ttu-id="5e953-306">`StartupDiagnostics` a la variable de entorno `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="5e953-306">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="5e953-307">La ruta de acceso de las dependencias de inicio de hospedaje (en la carpeta *deployment* del proyecto RuntimeStore) a la variable de entorno `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="5e953-307">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="5e953-308">La ruta de acceso del almacén en tiempo de ejecución (en la carpeta *deployment* del proyecto RuntimeStore) a la variable de entorno `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="5e953-308">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="5e953-309">Ejecute la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="5e953-309">Run the sample app.</span></span>
1. <span data-ttu-id="5e953-310">Solicite al punto de conexión `/services` ver los servicios registrados de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-310">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="5e953-311">Solicite al punto de conexión `/diag` ver la información de diagnóstico.</span><span class="sxs-lookup"><span data-stu-id="5e953-311">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5e953-312">Una implementación de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (inicio de hospedaje) agrega mejoras a una aplicación al iniciarla a partir de un ensamblado externo.</span><span class="sxs-lookup"><span data-stu-id="5e953-312">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="5e953-313">Por ejemplo, una biblioteca externa puede usar una implementación de inicio de hospedaje para suministrar más servicios o proveedores de configuración a una aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-313">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="5e953-314">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5e953-314">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="5e953-315">Atributo HostingStartup</span><span class="sxs-lookup"><span data-stu-id="5e953-315">HostingStartup attribute</span></span>

<span data-ttu-id="5e953-316">Un atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) indica la presencia de un ensamblado de inicio de hospedaje para activar en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="5e953-316">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="5e953-317">El ensamblado de entrada o el ensamblado que contiene la clase `Startup` se analiza automáticamente para detectar el atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5e953-317">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="5e953-318">La lista de ensamblados para buscar atributos `HostingStartup` se carga en tiempo de ejecución desde la configuración de [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="5e953-318">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="5e953-319">La lista de ensamblados que se excluirán de la detección se carga desde [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="5e953-319">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="5e953-320">Para más información, consulte [Host web: Ensamblados de inicio de hospedaje](xref:fundamentals/host/web-host#hosting-startup-assemblies) y [Host web: ensamblados de exclusión de inicio de hospedaje](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="5e953-320">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="5e953-321">En el ejemplo siguiente, el espacio de nombres del ensamblado de inicio de hospedaje es `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="5e953-321">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="5e953-322">La clase que contiene el código de inicio de hospedaje es `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="5e953-322">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="5e953-323">Normalmente el atributo `HostingStartup` se encuentra en el archivo de clase de implementación `IHostingStartup` del ensamblado de inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="5e953-323">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="5e953-324">Detectar los ensamblados de inicio de hospedaje cargados</span><span class="sxs-lookup"><span data-stu-id="5e953-324">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="5e953-325">Para detectar los ensamblados de inicio de hospedaje cargados, habilite el registro y consulte los registros de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-325">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="5e953-326">En ellos se registran los errores que se producen al cargar ensamblados.</span><span class="sxs-lookup"><span data-stu-id="5e953-326">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="5e953-327">Los ensamblados de inicio de hospedaje se registran en el nivel de depuración y se registran todos los errores.</span><span class="sxs-lookup"><span data-stu-id="5e953-327">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="5e953-328">Deshabilitar la carga automática de ensamblados de inicio de hospedaje</span><span class="sxs-lookup"><span data-stu-id="5e953-328">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="5e953-329">Para deshabilitar la carga automática de los ensamblados de inicio de hospedaje, use uno de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="5e953-329">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="5e953-330">Para evitar que se carguen todos los ensamblados de inicio de hospedaje, establezca uno de los procedimientos siguientes en `true` o `1`:</span><span class="sxs-lookup"><span data-stu-id="5e953-330">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="5e953-331">La opción de configuración de hospedaje para [Evitar el inicio de hospedaje](xref:fundamentals/host/web-host#prevent-hosting-startup).</span><span class="sxs-lookup"><span data-stu-id="5e953-331">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="5e953-332">La variable de entorno `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="5e953-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="5e953-333">Para evitar la carga de ensamblados de inicio de hospedaje específicos, establezca una de las opciones siguientes en una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para excluir durante el inicio:</span><span class="sxs-lookup"><span data-stu-id="5e953-333">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="5e953-334">La opción de configuración de host para [Ensamblados de exclusión de inicio de hospedaje](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="5e953-334">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="5e953-335">La variable de entorno `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="5e953-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="5e953-336">Si se establecen la opción de configuración de host y la variable de entorno, la configuración de host controla el comportamiento.</span><span class="sxs-lookup"><span data-stu-id="5e953-336">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="5e953-337">Deshabilitar los ensamblados de inicio de hospedaje a través de la variable de entorno o de la configuración de host hace que el ensamblado se deshabilite globalmente y, asimismo, puede deshabilitar también otras características de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-337">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="5e953-338">Proyecto</span><span class="sxs-lookup"><span data-stu-id="5e953-338">Project</span></span>

<span data-ttu-id="5e953-339">Cree un inicio de hospedaje con cualquiera de los siguientes tipos de proyecto:</span><span class="sxs-lookup"><span data-stu-id="5e953-339">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="5e953-340">Biblioteca de clases</span><span class="sxs-lookup"><span data-stu-id="5e953-340">Class library</span></span>](#class-library)
* [<span data-ttu-id="5e953-341">Aplicación de consola sin un punto de entrada</span><span class="sxs-lookup"><span data-stu-id="5e953-341">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="5e953-342">Biblioteca de clases</span><span class="sxs-lookup"><span data-stu-id="5e953-342">Class library</span></span>

<span data-ttu-id="5e953-343">Una mejora de inicio de hospedaje se puede proporcionar en una biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="5e953-343">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="5e953-344">La biblioteca contiene un atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5e953-344">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="5e953-345">El [código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) incluye una aplicación de Razor Pages, *HostingStartupApp*, y una biblioteca de clases, *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="5e953-345">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="5e953-346">La biblioteca de clases:</span><span class="sxs-lookup"><span data-stu-id="5e953-346">The class library:</span></span>

* <span data-ttu-id="5e953-347">Contiene una clase de inicio de hospedaje, `ServiceKeyInjection`, que implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5e953-347">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="5e953-348">`ServiceKeyInjection` agrega un par de cadenas de servicio a la configuración de la aplicación mediante el proveedor de configuración en memoria ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="5e953-348">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="5e953-349">Incluye un atributo `HostingStartup` que identifica espacio de nombres y la clase del inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="5e953-349">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="5e953-350">El método <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> de la clase `ServiceKeyInjection` usa <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> para agregar mejoras a una aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-350">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="5e953-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="5e953-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="5e953-352">La página de índice de la aplicación lee y procesa los valores de configuración para las dos claves establecidas por el ensamblado de inicio de hospedaje de la biblioteca de clases:</span><span class="sxs-lookup"><span data-stu-id="5e953-352">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="5e953-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="5e953-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="5e953-354">El [código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) también incluye un proyecto de paquete NuGet que proporciona un inicio de hospedaje independiente, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="5e953-354">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="5e953-355">El paquete tiene las mismas características que la biblioteca de clases que se ha descrito anteriormente.</span><span class="sxs-lookup"><span data-stu-id="5e953-355">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="5e953-356">El paquete:</span><span class="sxs-lookup"><span data-stu-id="5e953-356">The package:</span></span>

* <span data-ttu-id="5e953-357">Contiene una clase de inicio de hospedaje, `ServiceKeyInjection`, que implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5e953-357">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="5e953-358">`ServiceKeyInjection` agrega un par de cadenas de servicio a la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-358">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="5e953-359">Incluye un atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5e953-359">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="5e953-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="5e953-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="5e953-361">La página de índice de la aplicación lee y procesa los valores de configuración para las dos claves establecidas por el ensamblado de inicio de hospedaje del paquete:</span><span class="sxs-lookup"><span data-stu-id="5e953-361">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="5e953-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="5e953-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="5e953-363">Aplicación de consola sin un punto de entrada</span><span class="sxs-lookup"><span data-stu-id="5e953-363">Console app without an entry point</span></span>

<span data-ttu-id="5e953-364">*Este enfoque solo está disponible para las aplicaciones de .NET Core, no de .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="5e953-364">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="5e953-365">Se puede proporcionar una mejora del inicio de hospedaje dinámico que no requiere una referencia de tiempo de compilación para la activación en una aplicación de consola sin un punto de entrada que contiene un atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5e953-365">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="5e953-366">Publicar la aplicación de consola genera un ensamblado de inicio de hospedaje que se puede consumir desde el almacén en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="5e953-366">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="5e953-367">En este proceso se usa una aplicación de consola sin un punto de entrada porque:</span><span class="sxs-lookup"><span data-stu-id="5e953-367">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="5e953-368">Se requiere un archivo de dependencias para consumir el inicio de hospedaje en el ensamblado de inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="5e953-368">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="5e953-369">Un archivo de dependencias es un recurso de aplicación ejecutable que se genera mediante la publicación de una aplicación, no una biblioteca.</span><span class="sxs-lookup"><span data-stu-id="5e953-369">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="5e953-370">Una biblioteca no se puede agregar directamente al [almacén de paquetes en tiempo de ejecución](/dotnet/core/deploying/runtime-store), lo que requiere un proyecto ejecutable que tenga como destino el tiempo de ejecución compartido.</span><span class="sxs-lookup"><span data-stu-id="5e953-370">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="5e953-371">En la creación de un inicio de hospedaje dinámico:</span><span class="sxs-lookup"><span data-stu-id="5e953-371">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="5e953-372">Se crea un ensamblado de inicio de hospedaje desde la aplicación de consola sin un punto de entrada que:</span><span class="sxs-lookup"><span data-stu-id="5e953-372">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="5e953-373">Incluye una clase que contiene la implementación de `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5e953-373">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="5e953-374">Incluye un atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) para identificar la clase de implementación `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5e953-374">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="5e953-375">La aplicación de consola se publica para obtener las dependencias del inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="5e953-375">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="5e953-376">Una consecuencia de la publicación de la aplicación de consola es que las dependencias no usadas se cortan en el archivo de dependencias.</span><span class="sxs-lookup"><span data-stu-id="5e953-376">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="5e953-377">El archivo de dependencias se modifica para establecer la ubicación en tiempo de ejecución del ensamblado de inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="5e953-377">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="5e953-378">El ensamblado de inicio de hospedaje y su archivo de dependencias se colocan en el almacén de paquetes en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="5e953-378">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="5e953-379">Para detectar el ensamblado de inicio de hospedaje y su archivo de dependencias, se enumeran en un par de variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="5e953-379">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="5e953-380">La aplicación de consola hace referencia al paquete [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="5e953-380">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="5e953-381">Un atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifica una clase como una implementación de `IHostingStartup` para cargarla e implementarla al compilar <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span><span class="sxs-lookup"><span data-stu-id="5e953-381">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="5e953-382">En el siguiente ejemplo, el espacio de nombres es `StartupEnhancement` y la clase, `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="5e953-382">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="5e953-383">Una clase implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5e953-383">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="5e953-384">El método <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> de la clase usa <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> para agregar mejoras a una aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-384">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="5e953-385">El tiempo de ejecución llama a `IHostingStartup.Configure` en el ensamblado de inicio del hospedaje antes de `Startup.Configure` en el código de usuario, lo que permite que el código de usuario sobrescriba cualquier configuración facilitada por el ensamblado de inicio del hospedaje.</span><span class="sxs-lookup"><span data-stu-id="5e953-385">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="5e953-386">Al crear un proyecto de `IHostingStartup`, el archivo de dependencias ( *.deps.json*) establece la ubicación de `runtime` del ensamblado en la carpeta *bin*:</span><span class="sxs-lookup"><span data-stu-id="5e953-386">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="5e953-387">Solo se muestra parte del archivo.</span><span class="sxs-lookup"><span data-stu-id="5e953-387">Only part of the file is shown.</span></span> <span data-ttu-id="5e953-388">El nombre del ensamblado en el ejemplo es `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="5e953-388">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="5e953-389">Configuración proporcionada por el inicio de hospedaje</span><span class="sxs-lookup"><span data-stu-id="5e953-389">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="5e953-390">Existen dos enfoques para controlar la configuración según si quiere que la configuración del inicio de hospedaje tenga prioridad o si la configuración de la aplicación tenga prioridad:</span><span class="sxs-lookup"><span data-stu-id="5e953-390">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="5e953-391">Proporcione la configuración a la aplicación utilizando <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> para cargar la configuración después de que se ejecuten los delegados <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-391">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="5e953-392">La configuración del inicio de hospedaje tiene prioridad sobre la configuración de la aplicación con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="5e953-392">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="5e953-393">Proporcione la configuración a la aplicación utilizando <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> para cargar la configuración antes de que se ejecuten los delegados <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-393">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="5e953-394">Los valores de configuración de la aplicación tienen prioridad a sobre los proporcionados por el inicio de hospedaje con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="5e953-394">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="5e953-395">Especificar el ensamblado de inicio de hospedaje</span><span class="sxs-lookup"><span data-stu-id="5e953-395">Specify the hosting startup assembly</span></span>

<span data-ttu-id="5e953-396">En el caso de un inicio de hospedaje proporcionado por una biblioteca de clase o una aplicación de consola, especifique el nombre del ensamblado de inicio de hospedaje en la variable de entorno `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="5e953-396">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="5e953-397">La variable de entorno es una lista de ensamblados delimitada por punto y coma.</span><span class="sxs-lookup"><span data-stu-id="5e953-397">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="5e953-398">Solo se examinan los ensamblados de inicio de hospedaje en busca del atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5e953-398">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="5e953-399">En la aplicación de ejemplo, *HostingStartupApp*, para descubrir los nuevos inicios de hospedaje descritos anteriormente, la variable de entorno se establece en el siguiente valor:</span><span class="sxs-lookup"><span data-stu-id="5e953-399">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="5e953-400">Un ensamblado de inicio de hospedaje también se puede establecer mediante la configuración de host [Ensamblados de inicio de hospedaje](xref:fundamentals/host/web-host#hosting-startup-assemblies).</span><span class="sxs-lookup"><span data-stu-id="5e953-400">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="5e953-401">Cuando existen varios ensamblados de inicio de hospedaje, sus métodos <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> se ejecutan en el orden en el que aparecen los ensamblados.</span><span class="sxs-lookup"><span data-stu-id="5e953-401">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="5e953-402">Activación</span><span class="sxs-lookup"><span data-stu-id="5e953-402">Activation</span></span>

<span data-ttu-id="5e953-403">Las opciones de activación del inicio de hospedaje son:</span><span class="sxs-lookup"><span data-stu-id="5e953-403">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="5e953-404">[Almacén de tiempo de ejecución](#runtime-store): no se requiere una referencia de tiempo de compilación para la activación.</span><span class="sxs-lookup"><span data-stu-id="5e953-404">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="5e953-405">La aplicación de ejemplo coloca el ensamblado de inicio de hospedaje y los archivos de dependencias en una carpeta, *implementación*, para facilitar la implementación del inicio del hospedaje en un entorno de varios equipos.</span><span class="sxs-lookup"><span data-stu-id="5e953-405">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="5e953-406">La carpeta *implementación* también incluye un script de PowerShell que crea o modifica las variables de entorno en el sistema de implementación para habilitar el inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="5e953-406">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="5e953-407">Se requiere una referencia al tiempo de compilación para la activación</span><span class="sxs-lookup"><span data-stu-id="5e953-407">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="5e953-408">Paquete NuGet</span><span class="sxs-lookup"><span data-stu-id="5e953-408">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="5e953-409">Carpeta bin del proyecto</span><span class="sxs-lookup"><span data-stu-id="5e953-409">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="5e953-410">Almacén en tiempo de ejecución</span><span class="sxs-lookup"><span data-stu-id="5e953-410">Runtime store</span></span>

<span data-ttu-id="5e953-411">La implementación de inicio de hospedaje se coloca en el [almacén en tiempo de ejecución](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="5e953-411">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="5e953-412">No es necesario que la aplicación mejorada haga ninguna referencia de tiempo de compilación al ensamblado.</span><span class="sxs-lookup"><span data-stu-id="5e953-412">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="5e953-413">Una vez compilado el inicio de hospedaje, se genera un almacén en tiempo de ejecución mediante el archivo del proyecto de manifiesto y el comando [dotnet store](/dotnet/core/tools/dotnet-store).</span><span class="sxs-lookup"><span data-stu-id="5e953-413">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="5e953-414">En la aplicación de ejemplo (proyecto *RuntimeStore*), se usa el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="5e953-414">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="5e953-415">Para que el tiempo de ejecución detecte el almacén en tiempo de ejecución, la ubicación del almacén en tiempo de ejecución se agrega a la variable de entorno `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="5e953-415">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="5e953-416">**Modificar y colocar el archivo de dependencias del inicio de hospedaje**</span><span class="sxs-lookup"><span data-stu-id="5e953-416">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="5e953-417">Para activar la mejora sin una referencia de paquete a la mejora, especifique dependencias adicionales del tiempo de ejecución en `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="5e953-417">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="5e953-418">`additionalDeps` permite:</span><span class="sxs-lookup"><span data-stu-id="5e953-418">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="5e953-419">Ampliar el gráfico de la biblioteca de la aplicación al proporcionar un conjunto de archivos *.deps.json* adicionales para combinarlos con el propio archivo *.deps.json* de la aplicación durante el inicio.</span><span class="sxs-lookup"><span data-stu-id="5e953-419">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="5e953-420">Permitir la detección y la carga del ensamblado de inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="5e953-420">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="5e953-421">El enfoque recomendado para generar el archivo de dependencias adicionales es:</span><span class="sxs-lookup"><span data-stu-id="5e953-421">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="5e953-422">Ejecutar `dotnet publish` en el archivo de manifiesto del almacén en tiempo de ejecución al que se hace referencia en la sección anterior.</span><span class="sxs-lookup"><span data-stu-id="5e953-422">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="5e953-423">Quitar la referencia del manifiesto de las bibliotecas y de la sección `runtime` del archivo *deps.json* resultante.</span><span class="sxs-lookup"><span data-stu-id="5e953-423">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="5e953-424">En el proyecto de ejemplo, la propiedad `store.manifest/1.0.0` se quita de las secciones `targets` y `libraries`:</span><span class="sxs-lookup"><span data-stu-id="5e953-424">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="5e953-425">Coloque el archivo *.deps.json* en la siguiente ubicación:</span><span class="sxs-lookup"><span data-stu-id="5e953-425">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="5e953-426">`{ADDITIONAL DEPENDENCIES PATH}`: ubicación agregada a la variable de entorno `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="5e953-426">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="5e953-427">`{SHARED FRAMEWORK NAME}`: marco compartido necesario para este archivo de dependencias adicionales.</span><span class="sxs-lookup"><span data-stu-id="5e953-427">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="5e953-428">`{SHARED FRAMEWORK VERSION}`: versión mínima del marco compartido.</span><span class="sxs-lookup"><span data-stu-id="5e953-428">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="5e953-429">`{ENHANCEMENT ASSEMBLY NAME}`: nombre del ensamblado de la mejora.</span><span class="sxs-lookup"><span data-stu-id="5e953-429">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="5e953-430">En la aplicación de ejemplo (proyecto *RuntimeStore*), el archivo de dependencias adicionales se coloca en la siguiente ubicación:</span><span class="sxs-lookup"><span data-stu-id="5e953-430">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="5e953-431">Para que el tiempo de ejecución detecte la ubicación del almacén en tiempo de ejecución, la ubicación del archivo de dependencias adicionales se agrega a la variable de entorno `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="5e953-431">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="5e953-432">En la aplicación de ejemplo (proyecto *RuntimeStore*), la creación del almacén en tiempo de ejecución y la generación del archivo de dependencias adicionales se realizan con un script de [PowerShell](/powershell/scripting/powershell-scripting).</span><span class="sxs-lookup"><span data-stu-id="5e953-432">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="5e953-433">Para obtener ejemplos de cómo establecer variables de entorno en distintos sistemas operativos, vea [Uso de varios entornos](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="5e953-433">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="5e953-434">**Implementación**</span><span class="sxs-lookup"><span data-stu-id="5e953-434">**Deployment**</span></span>

<span data-ttu-id="5e953-435">Para facilitar la implementación de un inicio de hospedaje en un entorno de varios equipos, la aplicación de ejemplo crea una carpeta de *implementación* en el resultado publicado que contiene:</span><span class="sxs-lookup"><span data-stu-id="5e953-435">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="5e953-436">El almacén en tiempo de ejecución de inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="5e953-436">The hosting startup runtime store.</span></span>
* <span data-ttu-id="5e953-437">El archivo de dependencias del inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="5e953-437">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="5e953-438">Un script de PowerShell que crea o modifica `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` y `DOTNET_ADDITIONAL_DEPS` para admitir la activación del inicio del hospedaje.</span><span class="sxs-lookup"><span data-stu-id="5e953-438">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="5e953-439">Ejecute el script desde un símbolo del sistema de PowerShell administrativo en el sistema de implementación.</span><span class="sxs-lookup"><span data-stu-id="5e953-439">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="5e953-440">Detección de</span><span class="sxs-lookup"><span data-stu-id="5e953-440">NuGet package</span></span>

<span data-ttu-id="5e953-441">Una mejora de inicio de hospedaje se puede proporcionar en un paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="5e953-441">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="5e953-442">El paquete tiene un atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5e953-442">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="5e953-443">Los tipos de inicio de hospedaje proporcionados por el paquete están disponibles en la aplicación mediante cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="5e953-443">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="5e953-444">El archivo de proyecto de la aplicación mejorada hace una referencia al paquete para el inicio de hospedaje en el archivo de proyecto de la aplicación (una referencia de tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="5e953-444">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="5e953-445">Con la referencia de tiempo de compilación realizada, el ensamblado de inicio de hospedaje y todas sus dependencias se incorporan en el archivo de dependencia de la aplicación ( *.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="5e953-445">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="5e953-446">Este enfoque se aplica a un paquete de ensamblado de inicio de hospedaje publicado en [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="5e953-446">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="5e953-447">El archivo de dependencias del inicio de hospedaje está disponible para la aplicación mejorada como se describe en la sección [Almacén en tiempo de ejecución](#runtime-store) (sin una referencia de tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="5e953-447">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="5e953-448">Para obtener más información sobre los paquetes NuGet y el almacén en tiempo de ejecución, vea los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="5e953-448">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="5e953-449">Cómo crear un paquete NuGet con herramientas multiplataforma</span><span class="sxs-lookup"><span data-stu-id="5e953-449">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="5e953-450">Publicar paquetes</span><span class="sxs-lookup"><span data-stu-id="5e953-450">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* <span data-ttu-id="5e953-451">[Runtime package store](/dotnet/core/deploying/runtime-store) (Almacenamiento de paquetes en tiempo de ejecución)</span><span class="sxs-lookup"><span data-stu-id="5e953-451">[Runtime package store](/dotnet/core/deploying/runtime-store)</span></span>

### <a name="project-bin-folder"></a><span data-ttu-id="5e953-452">Carpeta bin del proyecto</span><span class="sxs-lookup"><span data-stu-id="5e953-452">Project bin folder</span></span>

<span data-ttu-id="5e953-453">Un ensamblado implementado por *bin* puede proporcionar una mejora del inicio de hospedaje en la aplicación mejorada.</span><span class="sxs-lookup"><span data-stu-id="5e953-453">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="5e953-454">Los tipos de inicio de hospedaje que proporciona el ensamblado están disponibles en la aplicación mediante uno de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="5e953-454">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="5e953-455">El archivo de proyecto de la aplicación mejorada hace referencia de ensamblado al inicio de hospedaje (una referencia de tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="5e953-455">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="5e953-456">Con la referencia de tiempo de compilación realizada, el ensamblado de inicio de hospedaje y todas sus dependencias se incorporan en el archivo de dependencia de la aplicación ( *.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="5e953-456">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="5e953-457">Este método se aplica cuando el escenario de implementación llama para realizar una referencia en tiempo de compilación al ensamblado del inicio del hospedaje (archivo *.dll*) y mover el ensamblado a una de las siguientes ubicaciones:</span><span class="sxs-lookup"><span data-stu-id="5e953-457">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="5e953-458">Proyecto utilizado.</span><span class="sxs-lookup"><span data-stu-id="5e953-458">The consuming project.</span></span>
  * <span data-ttu-id="5e953-459">Ubicación a la que puede acceder el proyecto utilizado.</span><span class="sxs-lookup"><span data-stu-id="5e953-459">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="5e953-460">El archivo de dependencias del inicio de hospedaje está disponible para la aplicación mejorada como se describe en la sección [Almacén en tiempo de ejecución](#runtime-store) (sin una referencia de tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="5e953-460">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="5e953-461">Si el destino es .NET Framework, el ensamblado se puede cargar en el contexto de carga predeterminado, lo que en .NET Framework significa que el ensamblado se encuentra en cualquiera de las siguientes ubicaciones:</span><span class="sxs-lookup"><span data-stu-id="5e953-461">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="5e953-462">Ruta de acceso base de la aplicación: carpeta *bin* en la que se encuentra el archivo ejecutable ( *.exe*) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-462">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="5e953-463">Caché global de ensamblados (GAC): almacena los ensamblados que comparten varias aplicaciones .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="5e953-463">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="5e953-464">Para obtener más información, vea [Cómo: Instalar un ensamblado en la caché global de ensamblados](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) en la documentación de .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="5e953-464">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="5e953-465">Código de ejemplo</span><span class="sxs-lookup"><span data-stu-id="5e953-465">Sample code</span></span>

<span data-ttu-id="5e953-466">En el [código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([cómo descargar](xref:index#how-to-download-a-sample)) se muestran escenarios de implementación de inicio de hospedaje:</span><span class="sxs-lookup"><span data-stu-id="5e953-466">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="5e953-467">Cada uno de los dos ensamblados de inicio de hospedaje (bibliotecas de clases) establece un par clave-valor de configuración en memoria:</span><span class="sxs-lookup"><span data-stu-id="5e953-467">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="5e953-468">Paquete NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="5e953-468">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="5e953-469">Biblioteca de clases (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="5e953-469">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="5e953-470">Se activa un inicio de hospedaje desde un ensamblado implementado por el almacén de tiempo de ejecución (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="5e953-470">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="5e953-471">Este ensamblado agrega dos middleware a la aplicación (mientras se inicia) que proporcionan información de diagnóstico en:</span><span class="sxs-lookup"><span data-stu-id="5e953-471">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="5e953-472">Servicios registrados</span><span class="sxs-lookup"><span data-stu-id="5e953-472">Registered services</span></span>
  * <span data-ttu-id="5e953-473">Dirección (esquema, host, ruta de acceso base, ruta de acceso, cadena de consulta)</span><span class="sxs-lookup"><span data-stu-id="5e953-473">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="5e953-474">Conexión (dirección IP remota, puerto remoto, dirección IP local, puerto local, certificado de cliente)</span><span class="sxs-lookup"><span data-stu-id="5e953-474">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="5e953-475">Encabezados de solicitud</span><span class="sxs-lookup"><span data-stu-id="5e953-475">Request headers</span></span>
  * <span data-ttu-id="5e953-476">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="5e953-476">Environment variables</span></span>

<span data-ttu-id="5e953-477">Para ejecutar el ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5e953-477">To run the sample:</span></span>

<span data-ttu-id="5e953-478">**Activación desde un paquete NuGet**</span><span class="sxs-lookup"><span data-stu-id="5e953-478">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="5e953-479">Compile el paquete *HostingStartupPackage* con el comando [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="5e953-479">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="5e953-480">Agregue el nombre de ensamblado del paquete de *HostingStartupPackage* a la variable de entorno `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="5e953-480">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="5e953-481">Compile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-481">Compile and run the app.</span></span> <span data-ttu-id="5e953-482">Una referencia de paquete está presente en la aplicación mejorada (una referencia de tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="5e953-482">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="5e953-483">El parámetro `<PropertyGroup>` del archivo del proyecto de la aplicación especifica el resultado del proyecto de paquete ( *../HostingStartupPackage/bin/Debug*) como origen del paquete.</span><span class="sxs-lookup"><span data-stu-id="5e953-483">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="5e953-484">Esto permite que la aplicación utilice el paquete sin cargar el paquete a [nuget.org](https://www.nuget.org/). Para obtener más información, vea las notas que encontrará en el archivo del proyecto de HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="5e953-484">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="5e953-485">Observe que los valores de la clave de configuración del servicio representados por la página de índice coinciden con los valores establecidos por método `ServiceKeyInjection.Configure` del paquete.</span><span class="sxs-lookup"><span data-stu-id="5e953-485">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="5e953-486">Si realiza cambios en el proyecto *HostingStartupPackage* y lo vuelve a compilar, borre las cachés del paquete NuGet local para asegurarse de que *HostingStartupApp* recibe el paquete actualizado y no un paquete en desuso de la caché local.</span><span class="sxs-lookup"><span data-stu-id="5e953-486">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="5e953-487">Para borrar las cachés de NuGet locales, ejecute el siguiente comando [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals):</span><span class="sxs-lookup"><span data-stu-id="5e953-487">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="5e953-488">**Activación desde una biblioteca de clases**</span><span class="sxs-lookup"><span data-stu-id="5e953-488">**Activation from a class library**</span></span>

1. <span data-ttu-id="5e953-489">Compile la biblioteca de clases *HostingStartupLibrary* con el comando [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="5e953-489">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="5e953-490">Agregue el nombre del ensamblado de la biblioteca de clases *HostingStartupLibrary* a la variable de entorno `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="5e953-490">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="5e953-491">Implemente con *bin*-el ensamblado de la biblioteca de clases en la aplicación copiando el archivo *HostingStartupLibrary.dll* desde el resultado compilado de la biblioteca de aplicaciones en la carpeta *bin/Debug* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-491">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="5e953-492">Compile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-492">Compile and run the app.</span></span> <span data-ttu-id="5e953-493">Un parámetro `<ItemGroup>` del archivo del proyecto de la aplicación hace referencia al ensamblado de la biblioteca de clases ( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (una referencia de tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="5e953-493">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="5e953-494">Para obtener más información, vea las notas que encontrará en el archivo del proyecto de HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="5e953-494">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="5e953-495">Observe que los valores de la clave de configuración del servicio representados por la página de índice coinciden con los valores establecidos por el método `ServiceKeyInjection.Configure` de la biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="5e953-495">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="5e953-496">**Activación desde un ensamblado implementado por el almacén de tiempo de ejecución**</span><span class="sxs-lookup"><span data-stu-id="5e953-496">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="5e953-497">El proyecto *StartupDiagnostics* usa [PowerShell](/powershell/scripting/powershell-scripting) para modificar el archivo *StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="5e953-497">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="5e953-498">PowerShell se instala de forma predeterminada en Windows a partir de Windows 7 SP1 y Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="5e953-498">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="5e953-499">Para obtener PowerShell en otras plataformas, consulte [Instalación de varias versiones de PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="5e953-499">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="5e953-500">Ejecute el script *build.ps1* en la carpeta *RuntimeStore*.</span><span class="sxs-lookup"><span data-stu-id="5e953-500">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="5e953-501">El script:</span><span class="sxs-lookup"><span data-stu-id="5e953-501">The script:</span></span>
   * <span data-ttu-id="5e953-502">Genera el paquete `StartupDiagnostics` en la carpeta *obj\packages*.</span><span class="sxs-lookup"><span data-stu-id="5e953-502">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="5e953-503">Genera el almacén en tiempo de ejecución para `StartupDiagnostics` en la carpeta *store*.</span><span class="sxs-lookup"><span data-stu-id="5e953-503">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="5e953-504">El comando `dotnet store` del script usa el [identificador de tiempo de ejecución (RID)](/dotnet/core/rid-catalog) `win7-x64` para un inicio de hospedaje implementado en Windows.</span><span class="sxs-lookup"><span data-stu-id="5e953-504">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="5e953-505">Para proporcionar el inicio de hospedaje para otro tiempo de ejecución, sustituya el RID correcto en la línea 37 del script.</span><span class="sxs-lookup"><span data-stu-id="5e953-505">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="5e953-506">El almacén en tiempo de ejecución de `StartupDiagnostics` se moverá más tarde al almacén en tiempo de ejecución del usuario o del sistema en la máquina donde se va a consumir el ensamblado.</span><span class="sxs-lookup"><span data-stu-id="5e953-506">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="5e953-507">La ubicación de instalación del almacén en tiempo de ejecución del usuario para el ensamblado `StartupDiagnostics` es *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="5e953-507">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="5e953-508">Genera el parámetro `additionalDeps` para `StartupDiagnostics` en la carpeta *additionalDeps*.</span><span class="sxs-lookup"><span data-stu-id="5e953-508">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="5e953-509">Las dependencias adicionales se moverían posteriormente a las dependencias adicionales del usuario o del sistema.</span><span class="sxs-lookup"><span data-stu-id="5e953-509">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="5e953-510">La ubicación de instalación de las dependencias adicionales `StartupDiagnostics` del usuario es *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="5e953-510">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="5e953-511">Coloca el archivo *deploy.ps1* en la carpeta *deployment*.</span><span class="sxs-lookup"><span data-stu-id="5e953-511">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="5e953-512">Ejecute el script *deploy.ps1* en la carpeta *deployment*.</span><span class="sxs-lookup"><span data-stu-id="5e953-512">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="5e953-513">El script anexa:</span><span class="sxs-lookup"><span data-stu-id="5e953-513">The script appends:</span></span>
   * <span data-ttu-id="5e953-514">`StartupDiagnostics` a la variable de entorno `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="5e953-514">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="5e953-515">La ruta de acceso de las dependencias de inicio de hospedaje (en la carpeta *deployment* del proyecto RuntimeStore) a la variable de entorno `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="5e953-515">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="5e953-516">La ruta de acceso del almacén en tiempo de ejecución (en la carpeta *deployment* del proyecto RuntimeStore) a la variable de entorno `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="5e953-516">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="5e953-517">Ejecute la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="5e953-517">Run the sample app.</span></span>
1. <span data-ttu-id="5e953-518">Solicite al punto de conexión `/services` ver los servicios registrados de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e953-518">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="5e953-519">Solicite al punto de conexión `/diag` ver la información de diagnóstico.</span><span class="sxs-lookup"><span data-stu-id="5e953-519">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
