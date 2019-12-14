---
title: Inicio de la aplicación en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo la clase Startup de ASP.NET Core configura los servicios y la canalización de solicitudes de la aplicación.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: fundamentals/startup
ms.openlocfilehash: 2468c685850f74b8dafb3e0abea6d7b83c417af0
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880518"
---
# <a name="app-startup-in-aspnet-core"></a><span data-ttu-id="ebee9-103">Inicio de la aplicación en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ebee9-103">App startup in ASP.NET Core</span></span>

<span data-ttu-id="ebee9-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), [Luke Latham](https://github.com/guardrex) y [Steve Smith](https://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="ebee9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), [Luke Latham](https://github.com/guardrex), and [Steve Smith](https://ardalis.com)</span></span>

<span data-ttu-id="ebee9-105">La clase `Startup` configura los servicios y la canalización de solicitudes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ebee9-105">The `Startup` class configures services and the app's request pipeline.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="ebee9-106">Clase Startup</span><span class="sxs-lookup"><span data-stu-id="ebee9-106">The Startup class</span></span>

<span data-ttu-id="ebee9-107">Las aplicaciones de ASP.NET Core utilizan una clase `Startup`, que se denomina `Startup` por convención.</span><span class="sxs-lookup"><span data-stu-id="ebee9-107">ASP.NET Core apps use a `Startup` class, which is named `Startup` by convention.</span></span> <span data-ttu-id="ebee9-108">La clase `Startup`:</span><span class="sxs-lookup"><span data-stu-id="ebee9-108">The `Startup` class:</span></span>

* <span data-ttu-id="ebee9-109">Incluye opcionalmente un método <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> para configurar los *servicios* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ebee9-109">Optionally includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method to configure the app's *services*.</span></span> <span data-ttu-id="ebee9-110">Un servicio es un componente reutilizable que proporciona funcionalidades de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ebee9-110">A service is a reusable component that provides app functionality.</span></span> <span data-ttu-id="ebee9-111">Los servicios se *registran* en `ConfigureServices` y se usan en la aplicación a través de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) o <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span><span class="sxs-lookup"><span data-stu-id="ebee9-111">Services are *registered* in `ConfigureServices` and consumed across the app via [dependency injection (DI)](xref:fundamentals/dependency-injection) or <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>
* <span data-ttu-id="ebee9-112">Incluye un método <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> para crear la canalización de procesamiento de solicitudes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ebee9-112">Includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method to create the app's request processing pipeline.</span></span>

<span data-ttu-id="ebee9-113">El tiempo de ejecución ASP.NET Core llama a `ConfigureServices` y `Configure` cuando la aplicación se inicia:</span><span class="sxs-lookup"><span data-stu-id="ebee9-113">`ConfigureServices` and `Configure` are called by the ASP.NET Core runtime when the app starts:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="ebee9-114">El ejemplo anterior es para [Razor Pages](xref:razor-pages/index); la versión para MVC es similar.</span><span class="sxs-lookup"><span data-stu-id="ebee9-114">The preceding sample is for [Razor Pages](xref:razor-pages/index); the MVC version is similar.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

::: moniker-end

<span data-ttu-id="ebee9-115">La clase `Startup` se especifica cuando se crea el [host](xref:fundamentals/index#host) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ebee9-115">The `Startup` class is specified when the app's [host](xref:fundamentals/index#host) is built.</span></span> <span data-ttu-id="ebee9-116">Habitualmente, la clase `Startup` se especifica mediante una llamada al método[WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) en el generador de host:</span><span class="sxs-lookup"><span data-stu-id="ebee9-116">The `Startup` class is typically specified by calling the [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) method on the host builder:</span></span>

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

<span data-ttu-id="ebee9-117">El host proporciona servicios que están disponibles para el constructor de clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="ebee9-117">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="ebee9-118">La aplicación agrega servicios adicionales a través de `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ebee9-118">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="ebee9-119">Los servicios de la aplicación y el host están disponibles en `Configure` y en toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ebee9-119">Both the host and app services are available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="ebee9-120">Solo se pueden insertar los tipos de servicio siguientes en el constructor `Startup` cuando se usa el [host genérico](xref:fundamentals/host/generic-host) (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="ebee9-120">Only the following service types can be injected into the `Startup` constructor when using the [Generic Host](xref:fundamentals/host/generic-host) (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

<span data-ttu-id="ebee9-121">La mayoría de los servicios no están disponibles hasta que se llama al método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="ebee9-121">Most services are not available until the `Configure` method is called.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ebee9-122">El host proporciona servicios que están disponibles para el constructor de clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="ebee9-122">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="ebee9-123">La aplicación agrega servicios adicionales a través de `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ebee9-123">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="ebee9-124">Después, los servicios de la aplicación y el host están disponibles en `Configure` y en toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ebee9-124">Both the host and app services are then available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="ebee9-125">Un uso común de la [inserción de dependencias](xref:fundamentals/dependency-injection) en la clase `Startup` consiste en insertar:</span><span class="sxs-lookup"><span data-stu-id="ebee9-125">A common use of [dependency injection](xref:fundamentals/dependency-injection) into the `Startup` class is to inject:</span></span>

* <span data-ttu-id="ebee9-126"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> para configurar servicios según el entorno.</span><span class="sxs-lookup"><span data-stu-id="ebee9-126"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> to configure services by environment.</span></span>
* <span data-ttu-id="ebee9-127"><xref:Microsoft.Extensions.Configuration.IConfiguration> para leer la configuración.</span><span class="sxs-lookup"><span data-stu-id="ebee9-127"><xref:Microsoft.Extensions.Configuration.IConfiguration> to read configuration.</span></span>
* <span data-ttu-id="ebee9-128"><xref:Microsoft.Extensions.Logging.ILoggerFactory> para crear un registrador en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ebee9-128"><xref:Microsoft.Extensions.Logging.ILoggerFactory> to create a logger in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

<span data-ttu-id="ebee9-129">La mayoría de los servicios no están disponibles hasta que se llama al método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="ebee9-129">Most services are not available until the `Configure` method is called.</span></span>

::: moniker-end

### <a name="multiple-startup"></a><span data-ttu-id="ebee9-130">Inicio múltiple</span><span class="sxs-lookup"><span data-stu-id="ebee9-130">Multiple Startup</span></span>

<span data-ttu-id="ebee9-131">Cuando la aplicación define clases `Startup` independientes para otros entornos (por ejemplo, `StartupDevelopment`), la clase `Startup` correspondiente se selecciona en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="ebee9-131">When the app defines separate `Startup` classes for different environments (for example, `StartupDevelopment`), the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="ebee9-132">La clase cuyo sufijo de nombre coincide con el entorno actual se establece como prioritaria.</span><span class="sxs-lookup"><span data-stu-id="ebee9-132">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="ebee9-133">Si la aplicación se ejecuta en el entorno de desarrollo e incluye tanto la clase `Startup` como la clase `StartupDevelopment`, se utiliza la clase `StartupDevelopment`.</span><span class="sxs-lookup"><span data-stu-id="ebee9-133">If the app is run in the Development environment and includes both a `Startup` class and a `StartupDevelopment` class, the `StartupDevelopment` class is used.</span></span> <span data-ttu-id="ebee9-134">Para obtener más información, consulte [Uso de varios entornos](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span><span class="sxs-lookup"><span data-stu-id="ebee9-134">For more information, see [Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span></span>

<span data-ttu-id="ebee9-135">Para obtener más información sobre el host, vea [El host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="ebee9-135">See [The host](xref:fundamentals/index#host) for more information on the host.</span></span> <span data-ttu-id="ebee9-136">Para obtener información sobre cómo controlar los errores que se producen durante el inicio, consulte [Control de excepciones de inicio](xref:fundamentals/error-handling#startup-exception-handling).</span><span class="sxs-lookup"><span data-stu-id="ebee9-136">For information on handling errors during startup, see [Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling).</span></span>

## <a name="the-configureservices-method"></a><span data-ttu-id="ebee9-137">Método ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="ebee9-137">The ConfigureServices method</span></span>

<span data-ttu-id="ebee9-138">El método <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> es:</span><span class="sxs-lookup"><span data-stu-id="ebee9-138">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method is:</span></span>

* <span data-ttu-id="ebee9-139">Opcional.</span><span class="sxs-lookup"><span data-stu-id="ebee9-139">Optional.</span></span>
* <span data-ttu-id="ebee9-140">Lo llama el host antes del método `Configure` para configurar los servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ebee9-140">Called by the host before the `Configure` method to configure the app's services.</span></span>
* <span data-ttu-id="ebee9-141">Es donde se establecen por convención las [opciones de configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ebee9-141">Where [configuration options](xref:fundamentals/configuration/index) are set by convention.</span></span>

<span data-ttu-id="ebee9-142">El host puede configurar algunos servicios antes de que se llame a los métodos `Startup`.</span><span class="sxs-lookup"><span data-stu-id="ebee9-142">The host may configure some services before `Startup` methods are called.</span></span> <span data-ttu-id="ebee9-143">Para obtener más información, vea [El host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="ebee9-143">For more information, see [The host](xref:fundamentals/index#host).</span></span>

<span data-ttu-id="ebee9-144">Para las características que requieren una configuración sustancial, hay métodos de extensión `Add{Service}` en <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span><span class="sxs-lookup"><span data-stu-id="ebee9-144">For features that require substantial setup, there are `Add{Service}` extension methods on <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span></span> <span data-ttu-id="ebee9-145">Por ejemplo, **Add**DbContext, **Add**DefaultIdentity, **Add**EntityFrameworkStores y **Add**RazorPages:</span><span class="sxs-lookup"><span data-stu-id="ebee9-145">For example, **Add**DbContext, **Add**DefaultIdentity, **Add**EntityFrameworkStores, and **Add**RazorPages:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

::: moniker-end

<span data-ttu-id="ebee9-146">La adición de servicios al contenedor de servicios hace que estén disponibles en la aplicación y en el método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="ebee9-146">Adding services to the service container makes them available within the app and in the `Configure` method.</span></span> <span data-ttu-id="ebee9-147">Los servicios se resuelven a través de la [inserción de dependencias](xref:fundamentals/dependency-injection) o desde <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span><span class="sxs-lookup"><span data-stu-id="ebee9-147">The services are resolved via [dependency injection](xref:fundamentals/dependency-injection) or from <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ebee9-148">Vea [SetCompatibilityVersion](xref:mvc/compatibility-version) para obtener más información sobre `SetCompatibilityVersion`.</span><span class="sxs-lookup"><span data-stu-id="ebee9-148">See [SetCompatibilityVersion](xref:mvc/compatibility-version) for more information on `SetCompatibilityVersion`.</span></span>

::: moniker-end

## <a name="the-configure-method"></a><span data-ttu-id="ebee9-149">El método Configure</span><span class="sxs-lookup"><span data-stu-id="ebee9-149">The Configure method</span></span>

<span data-ttu-id="ebee9-150">El método <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> se usa para especificar la forma en que la aplicación responde a las solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="ebee9-150">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method is used to specify how the app responds to HTTP requests.</span></span> <span data-ttu-id="ebee9-151">La canalización de solicitudes se configura mediante la adición de componentes de [middleware](xref:fundamentals/middleware/index) a una instancia de <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ebee9-151">The request pipeline is configured by adding [middleware](xref:fundamentals/middleware/index) components to an <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance.</span></span> <span data-ttu-id="ebee9-152">`IApplicationBuilder` está disponible para el método `Configure`, pero no está registrado en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="ebee9-152">`IApplicationBuilder` is available to the `Configure` method, but it isn't registered in the service container.</span></span> <span data-ttu-id="ebee9-153">El hospedaje crea un elemento `IApplicationBuilder` y lo pasa directamente a `Configure`.</span><span class="sxs-lookup"><span data-stu-id="ebee9-153">Hosting creates an `IApplicationBuilder` and passes it directly to `Configure`.</span></span>

<span data-ttu-id="ebee9-154">Las [plantillas de ASP.NET Core](/dotnet/core/tools/dotnet-new) configuran la canalización con compatibilidad para lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ebee9-154">The [ASP.NET Core templates](/dotnet/core/tools/dotnet-new) configure the pipeline with support for:</span></span>

* [<span data-ttu-id="ebee9-155">Página de excepciones para el desarrollador</span><span class="sxs-lookup"><span data-stu-id="ebee9-155">Developer Exception Page</span></span>](xref:fundamentals/error-handling#developer-exception-page)
* [<span data-ttu-id="ebee9-156">Controlador de excepciones</span><span class="sxs-lookup"><span data-stu-id="ebee9-156">Exception handler</span></span>](xref:fundamentals/error-handling#exception-handler-page)
* [<span data-ttu-id="ebee9-157">Seguridad de transporte estricta de HTTP (HSTS)</span><span class="sxs-lookup"><span data-stu-id="ebee9-157">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [<span data-ttu-id="ebee9-158">Redireccionamiento de HTTPS</span><span class="sxs-lookup"><span data-stu-id="ebee9-158">HTTPS redirection</span></span>](xref:security/enforcing-ssl)
* [<span data-ttu-id="ebee9-159">Archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="ebee9-159">Static files</span></span>](xref:fundamentals/static-files)
* <span data-ttu-id="ebee9-160">ASP.NET Core [MVC](xref:mvc/overview) y [Razor Pages](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="ebee9-160">ASP.NET Core [MVC](xref:mvc/overview) and [Razor Pages](xref:razor-pages/index)</span></span>

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="ebee9-161">Reglamento General de Protección de Datos (GDPR)</span><span class="sxs-lookup"><span data-stu-id="ebee9-161">General Data Protection Regulation (GDPR)</span></span>](xref:security/gdpr)

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="ebee9-162">El ejemplo anterior es para [Razor Pages](xref:razor-pages/index); la versión para MVC es similar.</span><span class="sxs-lookup"><span data-stu-id="ebee9-162">The preceding sample is for [Razor Pages](xref:razor-pages/index); the MVC version is similar.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

::: moniker-end

<span data-ttu-id="ebee9-163">Cada método de extensión `Use` agrega uno o más componentes de middleware a la canalización de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="ebee9-163">Each `Use` extension method adds one or more middleware components to the request pipeline.</span></span> <span data-ttu-id="ebee9-164">Por ejemplo, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configura [middleware](xref:fundamentals/middleware/index) para proporcionar [archivos estáticos](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="ebee9-164">For instance, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configures [middleware](xref:fundamentals/middleware/index) to serve [static files](xref:fundamentals/static-files).</span></span>

<span data-ttu-id="ebee9-165">Cada componente de middleware de la canalización de solicitudes es responsable de invocar al siguiente componente de la canalización o de cortocircuitar la cadena en caso de ser necesario.</span><span class="sxs-lookup"><span data-stu-id="ebee9-165">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the chain, if appropriate.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ebee9-166">En la firma del método `Configure` se pueden especificar servicios adicionales, como `IWebHostEnvironment`, `ILoggerFactory`, o bien todo lo definido en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ebee9-166">Additional services, such as `IWebHostEnvironment`, `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="ebee9-167">Estos servicios adicionales se insertan si están disponibles.</span><span class="sxs-lookup"><span data-stu-id="ebee9-167">These services are injected if they're available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ebee9-168">En la firma del método `Configure` se pueden especificar servicios adicionales, como `IHostingEnvironment` e `ILoggerFactory`, o bien todo lo definido en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ebee9-168">Additional services, such as `IHostingEnvironment` and `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="ebee9-169">Estos servicios adicionales se insertan si están disponibles.</span><span class="sxs-lookup"><span data-stu-id="ebee9-169">These services are injected if they're available.</span></span>

::: moniker-end

<span data-ttu-id="ebee9-170">Para obtener más información sobre cómo usar `IApplicationBuilder` y el orden de procesamiento de middleware, consulte <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="ebee9-170">For more information on how to use `IApplicationBuilder` and the order of middleware processing, see <xref:fundamentals/middleware/index>.</span></span>

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a><span data-ttu-id="ebee9-171">Configuración de servicios sin inicio</span><span class="sxs-lookup"><span data-stu-id="ebee9-171">Configure services without Startup</span></span>

<span data-ttu-id="ebee9-172">Para configurar los servicios y la canalización de procesamiento de solicitudes sin usar una clase `Startup`, llame a los métodos de conveniencia `ConfigureServices` y `Configure` en el generador de host.</span><span class="sxs-lookup"><span data-stu-id="ebee9-172">To configure services and the request processing pipeline without using a `Startup` class, call `ConfigureServices` and `Configure` convenience methods on the host builder.</span></span> <span data-ttu-id="ebee9-173">Varias llamadas a `ConfigureServices` se anexan entre sí.</span><span class="sxs-lookup"><span data-stu-id="ebee9-173">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="ebee9-174">Si hay varias llamadas al método `Configure`, se usa la última llamada a `Configure`.</span><span class="sxs-lookup"><span data-stu-id="ebee9-174">If multiple `Configure` method calls exist, the last `Configure` call is used.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

::: moniker-end

## <a name="extend-startup-with-startup-filters"></a><span data-ttu-id="ebee9-175">Extensión del inicio con filtros de inicio</span><span class="sxs-lookup"><span data-stu-id="ebee9-175">Extend Startup with startup filters</span></span>

<span data-ttu-id="ebee9-176">Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:</span><span class="sxs-lookup"><span data-stu-id="ebee9-176">Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:</span></span>

* <span data-ttu-id="ebee9-177">Para configurar el middleware al principio o al final de la canalización de middleware [Configure](#the-configure-method) de una aplicación sin una llamada explícita a `Use{Middleware}`.</span><span class="sxs-lookup"><span data-stu-id="ebee9-177">To configure middleware at the beginning or end of an app's [Configure](#the-configure-method) middleware pipeline without an explicit call to `Use{Middleware}`.</span></span> <span data-ttu-id="ebee9-178">ASP.NET Core usa `IStartupFilter` para agregar los valores predeterminados al principio de la canalización sin que el creador de la aplicación tenga que registrar explícitamente el middleware predeterminado.</span><span class="sxs-lookup"><span data-stu-id="ebee9-178">`IStartupFilter` is used by ASP.NET Core to add defaults to the beginning of the pipeline without having to make the app author explicitly register the default middleware.</span></span> <span data-ttu-id="ebee9-179">`IStartupFilter` permite que otro componente llame a `Use{Middleware}` en nombre del creador de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ebee9-179">`IStartupFilter` allows a different component call `Use{Middleware}` on behalf of the app author.</span></span>
* <span data-ttu-id="ebee9-180">Para crear una canalización de métodos `Configure`.</span><span class="sxs-lookup"><span data-stu-id="ebee9-180">To create a pipeline of `Configure` methods.</span></span> <span data-ttu-id="ebee9-181">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) puede configurar middleware para que se ejecute antes o después del middleware agregado por las bibliotecas.</span><span class="sxs-lookup"><span data-stu-id="ebee9-181">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) can set a middleware to run before or after middleware added by libraries.</span></span>

<span data-ttu-id="ebee9-182">`IStartupFilter` implementa <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, que recibe y devuelve un elemento `Action<IApplicationBuilder>`.</span><span class="sxs-lookup"><span data-stu-id="ebee9-182">`IStartupFilter` implements <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, which receives and returns an `Action<IApplicationBuilder>`.</span></span> <span data-ttu-id="ebee9-183"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> define una clase para configurar la canalización de solicitudes de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="ebee9-183">An <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> defines a class to configure an app's request pipeline.</span></span> <span data-ttu-id="ebee9-184">Para más información, vea [Creación de una canalización de middleware con IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span><span class="sxs-lookup"><span data-stu-id="ebee9-184">For more information, see [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span></span>

<span data-ttu-id="ebee9-185">Cada instancia de `IStartupFilter` puede agregar uno o más middleware en la canalización de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="ebee9-185">Each `IStartupFilter` can add one or more middlewares in the request pipeline.</span></span> <span data-ttu-id="ebee9-186">Los filtros se invocan en el orden en que se agregaron al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="ebee9-186">The filters are invoked in the order they were added to the service container.</span></span> <span data-ttu-id="ebee9-187">Los filtros pueden agregar middleware antes o después de pasar el control al siguiente filtro, por lo que se anexan al principio o al final de la canalización de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ebee9-187">Filters may add middleware before or after passing control to the next filter, thus they append to the beginning or end of the app pipeline.</span></span>

<span data-ttu-id="ebee9-188">En el ejemplo siguiente, se muestra cómo registrar un componente de middleware con `IStartupFilter`.</span><span class="sxs-lookup"><span data-stu-id="ebee9-188">The following example demonstrates how to register a middleware with `IStartupFilter`.</span></span> <span data-ttu-id="ebee9-189">El componente de middleware `RequestSetOptionsMiddleware` establece un valor de opciones de un parámetro de cadena de consulta:</span><span class="sxs-lookup"><span data-stu-id="ebee9-189">The `RequestSetOptionsMiddleware` middleware sets an options value from a query string parameter:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

<span data-ttu-id="ebee9-190">`RequestSetOptionsMiddleware` está configurado en las clase `RequestSetOptionsStartupFilter`:</span><span class="sxs-lookup"><span data-stu-id="ebee9-190">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

<span data-ttu-id="ebee9-191">`RequestSetOptionsMiddleware` está configurado en las clase `RequestSetOptionsStartupFilter`:</span><span class="sxs-lookup"><span data-stu-id="ebee9-191">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

::: moniker-end

<span data-ttu-id="ebee9-192">`IStartupFilter` se registra en el contenedor de servicios en <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.</span><span class="sxs-lookup"><span data-stu-id="ebee9-192">The `IStartupFilter` is registered in the service container in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

::: moniker-end

<span data-ttu-id="ebee9-193">Cuando se proporciona un parámetro de cadena de consulta para `option`, el middleware procesa la asignación del valor antes de que el middleware de ASP.NET Core represente la respuesta.</span><span class="sxs-lookup"><span data-stu-id="ebee9-193">When a query string parameter for `option` is provided, the middleware processes the value assignment before the ASP.NET Core middleware renders the response.</span></span>

<span data-ttu-id="ebee9-194">El orden de ejecución de middleware se establece según el orden de registros de `IStartupFilter`:</span><span class="sxs-lookup"><span data-stu-id="ebee9-194">Middleware execution order is set by the order of `IStartupFilter` registrations:</span></span>

* <span data-ttu-id="ebee9-195">Varias implementaciones de `IStartupFilter` pueden interactuar con los mismos objetos.</span><span class="sxs-lookup"><span data-stu-id="ebee9-195">Multiple `IStartupFilter` implementations may interact with the same objects.</span></span> <span data-ttu-id="ebee9-196">Si el orden es importante, ordene los registros de servicio de `IStartupFilter` para que coincidan con el orden en que se deben ejecutar los middleware.</span><span class="sxs-lookup"><span data-stu-id="ebee9-196">If ordering is important, order their `IStartupFilter` service registrations to match the order that their middlewares should run.</span></span>
* <span data-ttu-id="ebee9-197">Las bibliotecas pueden agregar middleware con una o varias implementaciones de `IStartupFilter` que se ejecuten antes o después de otro middleware de aplicación registrado con `IStartupFilter`.</span><span class="sxs-lookup"><span data-stu-id="ebee9-197">Libraries may add middleware with one or more `IStartupFilter` implementations that run before or after other app middleware registered with `IStartupFilter`.</span></span> <span data-ttu-id="ebee9-198">Para invocar un middleware `IStartupFilter` antes de un middleware agregado por el elemento `IStartupFilter` de una biblioteca:</span><span class="sxs-lookup"><span data-stu-id="ebee9-198">To invoke an `IStartupFilter` middleware before a middleware added by a library's `IStartupFilter`:</span></span>

  * <span data-ttu-id="ebee9-199">Coloque el registro del servicio antes de que la biblioteca se agregue al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="ebee9-199">Position the service registration before the library is added to the service container.</span></span>
  * <span data-ttu-id="ebee9-200">Para la invocación posterior, coloque el registro del servicio después de que se agregue la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="ebee9-200">To invoke afterward, position the service registration after the library is added.</span></span>

## <a name="add-configuration-at-startup-from-an-external-assembly"></a><span data-ttu-id="ebee9-201">Agregar opciones de configuración en el inicio desde un ensamblado externo</span><span class="sxs-lookup"><span data-stu-id="ebee9-201">Add configuration at startup from an external assembly</span></span>

<span data-ttu-id="ebee9-202">Una implementación de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite agregar mejoras a una aplicación al iniciarla a partir de un ensamblado externo fuera de la clase `Startup` de esta.</span><span class="sxs-lookup"><span data-stu-id="ebee9-202">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="ebee9-203">Para más información, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ebee9-203">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ebee9-204">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="ebee9-204">Additional resources</span></span>

* [<span data-ttu-id="ebee9-205">El host</span><span class="sxs-lookup"><span data-stu-id="ebee9-205">The host</span></span>](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>
