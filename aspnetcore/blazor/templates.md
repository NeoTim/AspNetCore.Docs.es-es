---
title: Plantillas de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre las plantillas de aplicación de Blazor de ASP.NET Core y la estructura de proyecto de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 705fa32ee72221b3c18653e9f3495b9cd61e9ad1
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967433"
---
# <a name="aspnet-core-blazor-templates"></a><span data-ttu-id="d3105-103">Plantillas de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d3105-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="d3105-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d3105-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="d3105-105">El marco Blazor proporciona plantillas que permiten desarrollar aplicaciones para cada uno de los modelos de hospedaje de Blazor:</span><span class="sxs-lookup"><span data-stu-id="d3105-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* <span data-ttu-id="d3105-106">WebAssembly de Blazor (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="d3105-106">Blazor WebAssembly (`blazorwasm`)</span></span>
* <span data-ttu-id="d3105-107">Servidor Blazor (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="d3105-107">Blazor Server (`blazorserver`)</span></span>

<span data-ttu-id="d3105-108">Para más información sobre los modelos de hospedaje de Blazor, vea <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="d3105-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="d3105-109">Para obtener instrucciones paso a paso sobre cómo crear una aplicación Blazor a partir de una plantilla, vea <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="d3105-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

<span data-ttu-id="d3105-110">Las opciones de plantilla están disponibles si se pasa la opción `--help` al comando [dotnet new](/dotnet/core/tools/dotnet-new) de la CLI:</span><span class="sxs-lookup"><span data-stu-id="d3105-110">Template options are available by passing the `--help` option to the [dotnet new](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a><span data-ttu-id="d3105-111">Estructura de proyecto de Blazor</span><span class="sxs-lookup"><span data-stu-id="d3105-111">Blazor project structure</span></span>

<span data-ttu-id="d3105-112">Los siguientes archivos y carpetas componen una aplicación Blazor generada a partir de una plantilla de Blazor:</span><span class="sxs-lookup"><span data-stu-id="d3105-112">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="d3105-113">*Program.cs*: punto de entrada de la aplicación que instala lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="d3105-113">*Program.cs* &ndash; The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="d3105-114">[Host](xref:fundamentals/host/generic-host) de ASP.NET Core (servidor Blazor)</span><span class="sxs-lookup"><span data-stu-id="d3105-114">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="d3105-115">Host de WebAssembly (WebAssembly de Blazor): el código de este archivo es único en las aplicaciones creadas a partir de la plantilla de WebAssembly de Blazor (`blazorwasm`).</span><span class="sxs-lookup"><span data-stu-id="d3105-115">WebAssembly host (Blazor WebAssembly) &ndash; The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="d3105-116">El componente `App`, que es el componente raíz de la aplicación, se especifica como el elemento DOM `app` en el método `Add`.</span><span class="sxs-lookup"><span data-stu-id="d3105-116">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="d3105-117">Los servicios se pueden configurar con el método `ConfigureServices` en el generador del host (por ejemplo, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span><span class="sxs-lookup"><span data-stu-id="d3105-117">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="d3105-118">La configuración se puede proporcionar a través del generador del host (`builder.Configuration`).</span><span class="sxs-lookup"><span data-stu-id="d3105-118">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="d3105-119">*Startup.cs* (servidor Blazor): contiene la lógica de inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d3105-119">*Startup.cs* (Blazor Server) &ndash; Contains the app's startup logic.</span></span> <span data-ttu-id="d3105-120">La clase `Startup` define dos métodos:</span><span class="sxs-lookup"><span data-stu-id="d3105-120">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="d3105-121">`ConfigureServices`: configura los servicios de [inserción de dependencias](xref:fundamentals/dependency-injection) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d3105-121">`ConfigureServices` &ndash; Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="d3105-122">En las aplicaciones del servidor Blazor, los servicios se agregan llamando a <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, y el elemento `WeatherForecastService` se agrega al contenedor de servicios para que lo use el componente `FetchData` de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="d3105-122">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="d3105-123">`Configure`: configura la canalización de administración de solicitudes de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="d3105-123">`Configure` &ndash; Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="d3105-124">Se llama a <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> para configurar un punto de conexión para la conexión en tiempo real con el explorador.</span><span class="sxs-lookup"><span data-stu-id="d3105-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="d3105-125">La conexión se crea con [SignalR](xref:signalr/introduction), que es un marco para agregar funcionalidad web a las aplicaciones en tiempo real.</span><span class="sxs-lookup"><span data-stu-id="d3105-125">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="d3105-126">Se llama a [MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) para configurar la página raíz de la aplicación (*Pages/_Host.cshtml*) y habilitar la navegación.</span><span class="sxs-lookup"><span data-stu-id="d3105-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="d3105-127">*wwwroot/index.html* (WebAssembly de Blazor): página raíz de la aplicación implementada como una página HTML:</span><span class="sxs-lookup"><span data-stu-id="d3105-127">*wwwroot/index.html* (Blazor WebAssembly) &ndash; The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="d3105-128">Cuando se solicita inicialmente cualquier página de la aplicación, esta página se representa y se devuelve en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="d3105-128">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="d3105-129">La página especifica dónde se va a representar el componente `App` raíz.</span><span class="sxs-lookup"><span data-stu-id="d3105-129">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="d3105-130">El componente `App` (*App.Razor*) se especifica como el elemento DOM `app` del método `AddComponent` en `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="d3105-130">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="d3105-131">Se carga el archivo JavaScript `_framework/blazor.webassembly.js`, que hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="d3105-131">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="d3105-132">Descarga el runtime de .NET, la aplicación y las dependencias de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d3105-132">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="d3105-133">Inicializa el runtime para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d3105-133">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="d3105-134">*App.razor*: componente raíz de la aplicación que configura el enrutamiento del lado cliente mediante el componente <xref:Microsoft.AspNetCore.Components.Routing.Router>.</span><span class="sxs-lookup"><span data-stu-id="d3105-134">*App.razor* &ndash; The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="d3105-135">El componente `Router` intercepta la navegación del explorador y representa la página que coincide con la dirección solicitada.</span><span class="sxs-lookup"><span data-stu-id="d3105-135">The `Router` component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="d3105-136">Carpeta *Pages* &ndash; Contiene las páginas o componentes enrutables ( *.razor*) que conforman la aplicación Blazor y la página raíz de Razor de una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="d3105-136">*Pages* folder &ndash; Contains the routable components/pages (*.razor*) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="d3105-137">La ruta de cada página se especifica usando la directiva [`@page`](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="d3105-137">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="d3105-138">En la plantilla se incluye lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="d3105-138">The template includes the following:</span></span>
  * <span data-ttu-id="d3105-139">*_Host.cshtml* (Blazor Server) &ndash; Página raíz de la aplicación implementada como página de Razor:</span><span class="sxs-lookup"><span data-stu-id="d3105-139">*_Host.cshtml* (Blazor Server) &ndash; The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="d3105-140">Cuando se solicita inicialmente cualquier página de la aplicación, esta página se representa y se devuelve en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="d3105-140">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="d3105-141">Se carga el archivo JavaScript `_framework/blazor.server.js`, que configura la conexión de SignalR en tiempo real entre el explorador y el servidor.</span><span class="sxs-lookup"><span data-stu-id="d3105-141">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="d3105-142">La página Host especifica dónde se va a representar el componente `App` raíz (*App.razor*).</span><span class="sxs-lookup"><span data-stu-id="d3105-142">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>
  * <span data-ttu-id="d3105-143">`Counter` (*Counter.razor*): implementa la página Counter (Contador).</span><span class="sxs-lookup"><span data-stu-id="d3105-143">`Counter` (*Counter.razor*) &ndash; Implements the Counter page.</span></span>
  * <span data-ttu-id="d3105-144">`Error` (*Error.razor*, solo aplicación de servidor Blazor): se representa cuando se produce una excepción no controlada en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d3105-144">`Error` (*Error.razor*, Blazor Server app only) &ndash; Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="d3105-145">`FetchData` (*FetchData.razor*): implementa la página Fetch data (Recuperar datos).</span><span class="sxs-lookup"><span data-stu-id="d3105-145">`FetchData` (*FetchData.razor*) &ndash; Implements the Fetch data page.</span></span>
  * <span data-ttu-id="d3105-146">`Index` (*Index.razor*): implementa la página principal.</span><span class="sxs-lookup"><span data-stu-id="d3105-146">`Index` (*Index.razor*) &ndash; Implements the Home page.</span></span>

* <span data-ttu-id="d3105-147">Carpeta *Shared*: contiene otros componentes de interfaz de usuario ( *.razor*) que la aplicación usa:</span><span class="sxs-lookup"><span data-stu-id="d3105-147">*Shared* folder &ndash; Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="d3105-148">`MainLayout` (*MainLayout.razor*): componente de diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d3105-148">`MainLayout` (*MainLayout.razor*) &ndash; The app's layout component.</span></span>
  * <span data-ttu-id="d3105-149">`NavMenu` (*NavMenu.razor*): implementa la navegación de barra lateral.</span><span class="sxs-lookup"><span data-stu-id="d3105-149">`NavMenu` (*NavMenu.razor*) &ndash; Implements sidebar navigation.</span></span> <span data-ttu-id="d3105-150">Incluye el [componente NavLink](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), que representa los vínculos de navegación a otros componentes de Razor.</span><span class="sxs-lookup"><span data-stu-id="d3105-150">Includes the [NavLink component](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="d3105-151">El componente `NavLink` indica automáticamente un estado seleccionado cuando su componente se carga, lo que ayuda al usuario a saber qué componente se está mostrando actualmente.</span><span class="sxs-lookup"><span data-stu-id="d3105-151">The `NavLink` component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="d3105-152">*_Imports.razor* &ndash; Engloba las directivas de Razor comunes que se van a incluir en los componentes de la aplicación ( *.razor*), como las directivas [`@using`](xref:mvc/views/razor#using) de los espacios de nombres.</span><span class="sxs-lookup"><span data-stu-id="d3105-152">*_Imports.razor* &ndash; Includes common Razor directives to include in the app's components (*.razor*), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="d3105-153">Carpeta *Data* (servidor Blazor): contiene la clase `WeatherForecast` y la implementación de `WeatherForecastService`, que proporcionan datos meteorológicos de ejemplo al componente `FetchData` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d3105-153">*Data* folder (Blazor Server) &ndash; Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="d3105-154">*wwwroot*: carpeta de [raíz web](xref:fundamentals/index#web-root) de la aplicación que contiene los activos estáticos públicos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d3105-154">*wwwroot* &ndash; The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="d3105-155">*appsettings.json* (servidor Blazor): opciones de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d3105-155">*appsettings.json* (Blazor Server) &ndash; Configuration settings for the app.</span></span>
