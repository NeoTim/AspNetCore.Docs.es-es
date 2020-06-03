---
<span data-ttu-id="ebb25-101">title: "Inserción de dependencias de ASP.NET Core Blazor" author: description: "Vea cómo las aplicaciones Blazor pueden insertar servicios en los componentes".</span><span class="sxs-lookup"><span data-stu-id="ebb25-101">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="ebb25-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebb25-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebb25-103">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-103">'Blazor'</span></span>
- <span data-ttu-id="ebb25-104">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ebb25-104">'Identity'</span></span>
- <span data-ttu-id="ebb25-105">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ebb25-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebb25-106">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-106">'Razor'</span></span>
- <span data-ttu-id="ebb25-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebb25-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="ebb25-108">Inserción de dependencias de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ebb25-108">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="ebb25-109">Por [Rainer Stropek](https://www.timecockpit.com) y [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="ebb25-109">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

Blazor<span data-ttu-id="ebb25-110"> admite la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ebb25-110"> supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ebb25-111">Las aplicaciones pueden usar servicios integrados mediante su inserción en componentes.</span><span class="sxs-lookup"><span data-stu-id="ebb25-111">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="ebb25-112">Las aplicaciones también pueden definir y registrar servicios personalizados y hacer que estén disponibles en toda la aplicación a través de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ebb25-112">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="ebb25-113">La inserción de dependencias es una técnica para acceder a los servicios configurados en una ubicación central.</span><span class="sxs-lookup"><span data-stu-id="ebb25-113">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="ebb25-114">Esto puede ser útil en aplicaciones Blazor para:</span><span class="sxs-lookup"><span data-stu-id="ebb25-114">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="ebb25-115">Compartir una única instancia de una clase de servicio entre varios componentes, lo que se conoce como servicio *singleton*.</span><span class="sxs-lookup"><span data-stu-id="ebb25-115">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="ebb25-116">Desacoplar componentes de clases de servicio concretas mediante abstracciones de referencia.</span><span class="sxs-lookup"><span data-stu-id="ebb25-116">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="ebb25-117">Por ejemplo, considere una interfaz `IDataAccess` para acceder a los datos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ebb25-117">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="ebb25-118">La interfaz se implementa mediante una clase `DataAccess` concreta y se registra como un servicio en el contenedor de servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ebb25-118">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="ebb25-119">Cuando un componente usa la inserción de dependencias para recibir una implementación de `IDataAccess`, el componente no se acopla al tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="ebb25-119">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="ebb25-120">La implementación se puede intercambiar, posiblemente para una implementación ficticia en pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="ebb25-120">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="ebb25-121">Servicios predeterminados</span><span class="sxs-lookup"><span data-stu-id="ebb25-121">Default services</span></span>

<span data-ttu-id="ebb25-122">Los servicios predeterminados se agregan de forma automática a la colección de servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ebb25-122">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="ebb25-123">web de Office</span><span class="sxs-lookup"><span data-stu-id="ebb25-123">Service</span></span> | <span data-ttu-id="ebb25-124">Período de duración</span><span class="sxs-lookup"><span data-stu-id="ebb25-124">Lifetime</span></span> | <span data-ttu-id="ebb25-125">Descripción</span><span class="sxs-lookup"><span data-stu-id="ebb25-125">Description</span></span> |
| ---
<span data-ttu-id="ebb25-126">title: "Inserción de dependencias de ASP.NET Core Blazor" author: description: "Vea cómo las aplicaciones Blazor pueden insertar servicios en los componentes".</span><span class="sxs-lookup"><span data-stu-id="ebb25-126">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="ebb25-127">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebb25-127">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebb25-128">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-128">'Blazor'</span></span>
- <span data-ttu-id="ebb25-129">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ebb25-129">'Identity'</span></span>
- <span data-ttu-id="ebb25-130">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ebb25-130">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebb25-131">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-131">'Razor'</span></span>
- <span data-ttu-id="ebb25-132">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebb25-132">'SignalR' uid:</span></span> 

<span data-ttu-id="ebb25-133">---- | --- title: "Inserción de dependencias de ASP.NET Core Blazor" author: description: "Vea cómo las aplicaciones Blazor pueden insertar servicios en los componentes".</span><span class="sxs-lookup"><span data-stu-id="ebb25-133">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="ebb25-134">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebb25-134">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebb25-135">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-135">'Blazor'</span></span>
- <span data-ttu-id="ebb25-136">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ebb25-136">'Identity'</span></span>
- <span data-ttu-id="ebb25-137">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ebb25-137">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebb25-138">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-138">'Razor'</span></span>
- <span data-ttu-id="ebb25-139">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebb25-139">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebb25-140">title: "Inserción de dependencias de ASP.NET Core Blazor" author: description: "Vea cómo las aplicaciones Blazor pueden insertar servicios en los componentes".</span><span class="sxs-lookup"><span data-stu-id="ebb25-140">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="ebb25-141">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebb25-141">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebb25-142">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-142">'Blazor'</span></span>
- <span data-ttu-id="ebb25-143">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ebb25-143">'Identity'</span></span>
- <span data-ttu-id="ebb25-144">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ebb25-144">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebb25-145">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-145">'Razor'</span></span>
- <span data-ttu-id="ebb25-146">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebb25-146">'SignalR' uid:</span></span> 

<span data-ttu-id="ebb25-147">---- | --- title: "Inserción de dependencias de ASP.NET Core Blazor" author: description: "Vea cómo las aplicaciones Blazor pueden insertar servicios en los componentes".</span><span class="sxs-lookup"><span data-stu-id="ebb25-147">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="ebb25-148">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebb25-148">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebb25-149">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-149">'Blazor'</span></span>
- <span data-ttu-id="ebb25-150">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ebb25-150">'Identity'</span></span>
- <span data-ttu-id="ebb25-151">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ebb25-151">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebb25-152">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-152">'Razor'</span></span>
- <span data-ttu-id="ebb25-153">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebb25-153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebb25-154">title: "Inserción de dependencias de ASP.NET Core Blazor" author: description: "Vea cómo las aplicaciones Blazor pueden insertar servicios en los componentes".</span><span class="sxs-lookup"><span data-stu-id="ebb25-154">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="ebb25-155">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebb25-155">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebb25-156">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-156">'Blazor'</span></span>
- <span data-ttu-id="ebb25-157">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ebb25-157">'Identity'</span></span>
- <span data-ttu-id="ebb25-158">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ebb25-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebb25-159">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-159">'Razor'</span></span>
- <span data-ttu-id="ebb25-160">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebb25-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebb25-161">title: "Inserción de dependencias de ASP.NET Core Blazor" author: description: "Vea cómo las aplicaciones Blazor pueden insertar servicios en los componentes".</span><span class="sxs-lookup"><span data-stu-id="ebb25-161">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="ebb25-162">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebb25-162">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebb25-163">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-163">'Blazor'</span></span>
- <span data-ttu-id="ebb25-164">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ebb25-164">'Identity'</span></span>
- <span data-ttu-id="ebb25-165">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ebb25-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebb25-166">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-166">'Razor'</span></span>
- <span data-ttu-id="ebb25-167">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebb25-167">'SignalR' uid:</span></span> 

<span data-ttu-id="ebb25-168">------ | | <xref:System.Net.Http.HttpClient> | Transient | Proporciona métodos para enviar solicitudes HTTP y recibir respuestas HTTP de un recurso identificado por un URI.</span><span class="sxs-lookup"><span data-stu-id="ebb25-168">------ | | <xref:System.Net.Http.HttpClient> | Transient | Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="ebb25-169">La instancia <xref:System.Net.Http.HttpClient> en una aplicación Blazor WebAssembly usa el explorador para administrar el tráfico HTTP en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="ebb25-169">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="ebb25-170">Las aplicaciones Blazor Server no incluyen un objeto <xref:System.Net.Http.HttpClient> configurado como servicio de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="ebb25-170">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="ebb25-171">Proporcione un objeto <xref:System.Net.Http.HttpClient> a una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="ebb25-171">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span><br><br><span data-ttu-id="ebb25-172">Para obtener más información, vea <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="ebb25-172">For more information, see <xref:blazor/call-web-api>.</span></span> <span data-ttu-id="ebb25-173">| | <xref:Microsoft.JSInterop.IJSRuntime> | Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="ebb25-173">| | <xref:Microsoft.JSInterop.IJSRuntime> | Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="ebb25-174">Scoped (Blazor Server) | Representa una instancia de un entorno de ejecución de JavaScript en la que se envían las llamadas de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ebb25-174">Scoped (Blazor Server) | Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="ebb25-175">Para obtener más información, vea <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="ebb25-175">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> <span data-ttu-id="ebb25-176">| | <xref:Microsoft.AspNetCore.Components.NavigationManager> | Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="ebb25-176">| | <xref:Microsoft.AspNetCore.Components.NavigationManager> | Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="ebb25-177">Scoped (Blazor Server) | Contiene asistentes para trabajar con URI y el estado de navegación.</span><span class="sxs-lookup"><span data-stu-id="ebb25-177">Scoped (Blazor Server) | Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="ebb25-178">Para obtener más información, vea [Asistentes de URI y estado de navegación](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="ebb25-178">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="ebb25-179">Un proveedor de servicios personalizado no proporciona automáticamente los servicios predeterminados que aparecen en la tabla.</span><span class="sxs-lookup"><span data-stu-id="ebb25-179">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="ebb25-180">Si usa un proveedor de servicios personalizado y necesita cualquiera de los servicios que se muestran en la tabla, agregue los servicios necesarios al nuevo proveedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="ebb25-180">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="ebb25-181">Adición de servicios a una aplicación</span><span class="sxs-lookup"><span data-stu-id="ebb25-181">Add services to an app</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="ebb25-182"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="ebb25-182"> WebAssembly</span></span>

<span data-ttu-id="ebb25-183">Configure los servicios de la colección de servicios de la aplicación en el método `Main` de *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="ebb25-183">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="ebb25-184">En el ejemplo siguiente, la implementación de `MyDependency` se registra para `IMyDependency`:</span><span class="sxs-lookup"><span data-stu-id="ebb25-184">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="ebb25-185">Una vez que se ha compilado el host, se puede acceder a los servicios desde el ámbito de inserción de dependencias raíz antes de que se representen los componentes.</span><span class="sxs-lookup"><span data-stu-id="ebb25-185">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="ebb25-186">Esto puede ser útil para ejecutar la lógica de inicialización antes de representar el contenido:</span><span class="sxs-lookup"><span data-stu-id="ebb25-186">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="ebb25-187">El host también proporciona una instancia de configuración central para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ebb25-187">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="ebb25-188">En función del ejemplo anterior, la dirección URL del servicio meteorológico se pasa de un origen de configuración predeterminado (por ejemplo, *appsettings.json*) a `InitializeWeatherAsync`:</span><span class="sxs-lookup"><span data-stu-id="ebb25-188">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### <a name="blazor-server"></a><span data-ttu-id="ebb25-189">Servidor de Blazor</span><span class="sxs-lookup"><span data-stu-id="ebb25-189">Blazor Server</span></span>

<span data-ttu-id="ebb25-190">Después de crear una aplicación, examine el método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ebb25-190">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="ebb25-191">Al método <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> se le pasa una interfaz <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, que es una lista de objetos de descriptor de servicio (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span><span class="sxs-lookup"><span data-stu-id="ebb25-191">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="ebb25-192">Para agregar los servicios, se proporcionan descriptores de servicio a la colección de servicios.</span><span class="sxs-lookup"><span data-stu-id="ebb25-192">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="ebb25-193">En el ejemplo siguiente se muestra el concepto con la interfaz `IDataAccess` y su implementación concreta de `DataAccess`:</span><span class="sxs-lookup"><span data-stu-id="ebb25-193">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="ebb25-194">Duración del servicio</span><span class="sxs-lookup"><span data-stu-id="ebb25-194">Service lifetime</span></span>

<span data-ttu-id="ebb25-195">Los servicios se pueden configurar con las duraciones que se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="ebb25-195">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="ebb25-196">Período de duración</span><span class="sxs-lookup"><span data-stu-id="ebb25-196">Lifetime</span></span> | <span data-ttu-id="ebb25-197">Descripción</span><span class="sxs-lookup"><span data-stu-id="ebb25-197">Description</span></span> |
| ---
<span data-ttu-id="ebb25-198">title: "Inserción de dependencias de ASP.NET Core Blazor" author: description: "Vea cómo las aplicaciones Blazor pueden insertar servicios en los componentes".</span><span class="sxs-lookup"><span data-stu-id="ebb25-198">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="ebb25-199">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebb25-199">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebb25-200">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-200">'Blazor'</span></span>
- <span data-ttu-id="ebb25-201">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ebb25-201">'Identity'</span></span>
- <span data-ttu-id="ebb25-202">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ebb25-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebb25-203">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-203">'Razor'</span></span>
- <span data-ttu-id="ebb25-204">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebb25-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebb25-205">title: "Inserción de dependencias de ASP.NET Core Blazor" author: description: "Vea cómo las aplicaciones Blazor pueden insertar servicios en los componentes".</span><span class="sxs-lookup"><span data-stu-id="ebb25-205">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="ebb25-206">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebb25-206">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebb25-207">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-207">'Blazor'</span></span>
- <span data-ttu-id="ebb25-208">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ebb25-208">'Identity'</span></span>
- <span data-ttu-id="ebb25-209">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ebb25-209">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebb25-210">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-210">'Razor'</span></span>
- <span data-ttu-id="ebb25-211">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebb25-211">'SignalR' uid:</span></span> 

<span data-ttu-id="ebb25-212">---- | --- title: "Inserción de dependencias de ASP.NET Core Blazor" author: description: "Vea cómo las aplicaciones Blazor pueden insertar servicios en los componentes".</span><span class="sxs-lookup"><span data-stu-id="ebb25-212">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="ebb25-213">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebb25-213">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebb25-214">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-214">'Blazor'</span></span>
- <span data-ttu-id="ebb25-215">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ebb25-215">'Identity'</span></span>
- <span data-ttu-id="ebb25-216">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ebb25-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebb25-217">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-217">'Razor'</span></span>
- <span data-ttu-id="ebb25-218">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebb25-218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebb25-219">title: "Inserción de dependencias de ASP.NET Core Blazor" author: description: "Vea cómo las aplicaciones Blazor pueden insertar servicios en los componentes".</span><span class="sxs-lookup"><span data-stu-id="ebb25-219">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="ebb25-220">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebb25-220">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebb25-221">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-221">'Blazor'</span></span>
- <span data-ttu-id="ebb25-222">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ebb25-222">'Identity'</span></span>
- <span data-ttu-id="ebb25-223">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ebb25-223">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebb25-224">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-224">'Razor'</span></span>
- <span data-ttu-id="ebb25-225">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebb25-225">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebb25-226">title: "Inserción de dependencias de ASP.NET Core Blazor" author: description: "Vea cómo las aplicaciones Blazor pueden insertar servicios en los componentes".</span><span class="sxs-lookup"><span data-stu-id="ebb25-226">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="ebb25-227">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebb25-227">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebb25-228">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-228">'Blazor'</span></span>
- <span data-ttu-id="ebb25-229">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ebb25-229">'Identity'</span></span>
- <span data-ttu-id="ebb25-230">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ebb25-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebb25-231">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ebb25-231">'Razor'</span></span>
- <span data-ttu-id="ebb25-232">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebb25-232">'SignalR' uid:</span></span> 

<span data-ttu-id="ebb25-233">------ | | Las aplicaciones <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor WebAssembly no tienen actualmente un concepto de ámbitos de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ebb25-233">------ | | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="ebb25-234">Los servicios registrados con `Scoped` se comportan como servicios `Singleton`.</span><span class="sxs-lookup"><span data-stu-id="ebb25-234">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="ebb25-235">Pero el modelo de hospedaje del servidor Blazor admite la duración `Scoped`.</span><span class="sxs-lookup"><span data-stu-id="ebb25-235">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="ebb25-236">En las aplicaciones de servidor Blazor, el ámbito del registro de un servicio con ámbito es la *conexión*.</span><span class="sxs-lookup"><span data-stu-id="ebb25-236">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="ebb25-237">Por este motivo, se prefiere el uso de servicios con ámbito para los servicios que deben tener el ámbito del usuario actual, aunque la intención actual sea ejecutar el lado cliente en el explorador.</span><span class="sxs-lookup"><span data-stu-id="ebb25-237">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> <span data-ttu-id="ebb25-238">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | La inserción de dependencias crea una *sola instancia* del servicio.</span><span class="sxs-lookup"><span data-stu-id="ebb25-238">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI creates a *single instance* of the service.</span></span> <span data-ttu-id="ebb25-239">Todos los componentes que requieren un servicio `Singleton` reciben una instancia del mismo servicio.</span><span class="sxs-lookup"><span data-stu-id="ebb25-239">All components requiring a `Singleton` service receive an instance of the same service.</span></span> <span data-ttu-id="ebb25-240">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Cada vez que un componente obtiene una instancia de un servicio `Transient` del contenedor de servicios, recibe una *nueva instancia* del servicio.</span><span class="sxs-lookup"><span data-stu-id="ebb25-240">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="ebb25-241">El sistema de inserción de dependencias se basa en el sistema de inserción de dependencias de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ebb25-241">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="ebb25-242">Para obtener más información, vea <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="ebb25-242">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="ebb25-243">Solicitud de un servicio en un componente</span><span class="sxs-lookup"><span data-stu-id="ebb25-243">Request a service in a component</span></span>

<span data-ttu-id="ebb25-244">Una vez que se han agregado los servicios a la colección de servicios, insértelos en los componentes mediante la directiva [\@inject](xref:mvc/views/razor#inject) de Razor.</span><span class="sxs-lookup"><span data-stu-id="ebb25-244">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="ebb25-245">[`@inject`](xref:mvc/views/razor#inject) tiene dos parámetros:</span><span class="sxs-lookup"><span data-stu-id="ebb25-245">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="ebb25-246">Tipo: el tipo de servicio que se va a insertar.</span><span class="sxs-lookup"><span data-stu-id="ebb25-246">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="ebb25-247">Propiedad: el nombre de la propiedad que recibe el servicio de aplicación insertado.</span><span class="sxs-lookup"><span data-stu-id="ebb25-247">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="ebb25-248">La propiedad no requiere la creación manual.</span><span class="sxs-lookup"><span data-stu-id="ebb25-248">The property doesn't require manual creation.</span></span> <span data-ttu-id="ebb25-249">El compilador crea la propiedad.</span><span class="sxs-lookup"><span data-stu-id="ebb25-249">The compiler creates the property.</span></span>

<span data-ttu-id="ebb25-250">Para obtener más información, vea <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="ebb25-250">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="ebb25-251">Use varias instrucciones [`@inject`](xref:mvc/views/razor#inject) para insertar distintos servicios.</span><span class="sxs-lookup"><span data-stu-id="ebb25-251">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="ebb25-252">En el ejemplo siguiente se muestra cómo utilizar [`@inject`](xref:mvc/views/razor#inject).</span><span class="sxs-lookup"><span data-stu-id="ebb25-252">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="ebb25-253">El servicio que implementa `Services.IDataAccess` se inserta en la propiedad `DataRepository` del componente.</span><span class="sxs-lookup"><span data-stu-id="ebb25-253">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="ebb25-254">Observe cómo el código solo usa la abstracción de `IDataAccess`:</span><span class="sxs-lookup"><span data-stu-id="ebb25-254">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="ebb25-255">De forma interna, la propiedad generada (`DataRepository`) usa el atributo [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute).</span><span class="sxs-lookup"><span data-stu-id="ebb25-255">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="ebb25-256">Normalmente, este atributo no se usa de manera directa.</span><span class="sxs-lookup"><span data-stu-id="ebb25-256">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="ebb25-257">Si se necesita una clase base para los componentes y las propiedades insertadas también son necesarias para la clase base, agregue manualmente el atributo [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute):</span><span class="sxs-lookup"><span data-stu-id="ebb25-257">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="ebb25-258">En los componentes derivados de la clase base, la directiva [`@inject`](xref:mvc/views/razor#inject) no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="ebb25-258">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="ebb25-259">Es suficiente con el objeto <xref:Microsoft.AspNetCore.Components.InjectAttribute> de la clase base:</span><span class="sxs-lookup"><span data-stu-id="ebb25-259">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="ebb25-260">Uso de la inserción de dependencias en servicios</span><span class="sxs-lookup"><span data-stu-id="ebb25-260">Use DI in services</span></span>

<span data-ttu-id="ebb25-261">Es posible que los servicios complejos requieran servicios adicionales.</span><span class="sxs-lookup"><span data-stu-id="ebb25-261">Complex services might require additional services.</span></span> <span data-ttu-id="ebb25-262">En el ejemplo anterior, `DataAccess` podría requerir el servicio predeterminado <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="ebb25-262">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="ebb25-263">[`@inject`](xref:mvc/views/razor#inject) (o el atributo [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)) no está disponible para su uso en los servicios.</span><span class="sxs-lookup"><span data-stu-id="ebb25-263">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="ebb25-264">En su lugar se debe usar la *inserción de constructores*.</span><span class="sxs-lookup"><span data-stu-id="ebb25-264">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="ebb25-265">Los servicios necesarios se agregan mediante la adición de parámetros al constructor del servicio.</span><span class="sxs-lookup"><span data-stu-id="ebb25-265">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="ebb25-266">Cuando la inserción de dependencias crea el servicio, reconoce los servicios que requiere en el constructor y los proporciona en consecuencia.</span><span class="sxs-lookup"><span data-stu-id="ebb25-266">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

<span data-ttu-id="ebb25-267">Requisitos previos para la inserción de constructores:</span><span class="sxs-lookup"><span data-stu-id="ebb25-267">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="ebb25-268">Debe existir un constructor cuyos argumentos se puedan cumplir mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ebb25-268">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="ebb25-269">Se permiten parámetros adicionales que no estén incluidos en la inserción de dependencias si especifican valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="ebb25-269">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="ebb25-270">El constructor aplicable debe ser *público*.</span><span class="sxs-lookup"><span data-stu-id="ebb25-270">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="ebb25-271">Debe existir un constructor aplicable.</span><span class="sxs-lookup"><span data-stu-id="ebb25-271">One applicable constructor must exist.</span></span> <span data-ttu-id="ebb25-272">En caso de ambigüedad, la inserción de dependencias inicia una excepción.</span><span class="sxs-lookup"><span data-stu-id="ebb25-272">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="ebb25-273">Clases de componentes base de utilidad para administrar un ámbito de inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="ebb25-273">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="ebb25-274">En las aplicaciones ASP.NET Core, el ámbito de los servicios con ámbito suele ser el de la solicitud actual.</span><span class="sxs-lookup"><span data-stu-id="ebb25-274">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="ebb25-275">Una vez que se ha completado la solicitud, el sistema de inserción de dependencias elimina todos los servicios con ámbito o transitorios.</span><span class="sxs-lookup"><span data-stu-id="ebb25-275">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="ebb25-276">En las aplicaciones de servidor Blazor, el ámbito de la solicitud dura lo mismo que la conexión de cliente, lo que puede dar lugar a que los servicios transitorios y con ámbito duren mucho más de lo esperado.</span><span class="sxs-lookup"><span data-stu-id="ebb25-276">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="ebb25-277">En las aplicaciones WebAssembly de Blazor, los servicios registrados con una duración con ámbito se tratan como singleton, por lo que viven más que los servicios con ámbito de aplicaciones ASP.NET Core típicas.</span><span class="sxs-lookup"><span data-stu-id="ebb25-277">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="ebb25-278">Un enfoque que limita la duración de un servicio en las aplicaciones Blazor es el uso del tipo <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="ebb25-278">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="ebb25-279"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> es un tipo abstracto derivado de <xref:Microsoft.AspNetCore.Components.ComponentBase> que crea un ámbito de inserción de dependencias que se corresponde a la duración del componente.</span><span class="sxs-lookup"><span data-stu-id="ebb25-279"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="ebb25-280">Con este ámbito, es posible usar los servicios de inserción de dependencias con una duración con ámbito y hacer que duren lo mismo que el componente.</span><span class="sxs-lookup"><span data-stu-id="ebb25-280">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="ebb25-281">Cuando el componente se destruye, también se eliminan los servicios del proveedor de servicios con ámbito del componente.</span><span class="sxs-lookup"><span data-stu-id="ebb25-281">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="ebb25-282">Esto puede ser útil para servicios que:</span><span class="sxs-lookup"><span data-stu-id="ebb25-282">This can be useful for services that:</span></span>

* <span data-ttu-id="ebb25-283">Se deban reutilizar dentro de un componente, ya que la duración transitoria no es apropiada.</span><span class="sxs-lookup"><span data-stu-id="ebb25-283">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="ebb25-284">No se deban compartir entre componentes, ya que la duración de singleton no es apropiada.</span><span class="sxs-lookup"><span data-stu-id="ebb25-284">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="ebb25-285">Hay dos versiones del tipo <xref:Microsoft.AspNetCore.Components.OwningComponentBase> disponibles:</span><span class="sxs-lookup"><span data-stu-id="ebb25-285">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="ebb25-286"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> es un elemento secundario abstracto y descartable del tipo <xref:Microsoft.AspNetCore.Components.ComponentBase> con una propiedad <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> protegida de tipo <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="ebb25-286"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="ebb25-287">Este proveedor se puede usar para resolver los servicios cuyo ámbito es la duración del componente.</span><span class="sxs-lookup"><span data-stu-id="ebb25-287">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="ebb25-288">Los servicios de inserción de dependencias insertados en el componente mediante [`@inject`](xref:mvc/views/razor#inject) o el atributo[`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) no se crean en el ámbito del componente.</span><span class="sxs-lookup"><span data-stu-id="ebb25-288">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="ebb25-289">Para usar el ámbito del componente, los servicios se deben resolver mediante <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> o <xref:System.IServiceProvider.GetService%2A>.</span><span class="sxs-lookup"><span data-stu-id="ebb25-289">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="ebb25-290">Los servicios que se resuelvan mediante el proveedor de <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> reciben sus dependencias desde ese mismo ámbito.</span><span class="sxs-lookup"><span data-stu-id="ebb25-290">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <span data-ttu-id="ebb25-291"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> deriva de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> y agrega una propiedad <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> que devuelve una instancia de `T` desde el proveedor de inserción de dependencias con ámbito.</span><span class="sxs-lookup"><span data-stu-id="ebb25-291"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="ebb25-292">Este tipo es una manera cómoda de acceder a los servicios con ámbito sin usar una instancia de <xref:System.IServiceProvider> cuando hay un servicio principal que la aplicación necesita del contenedor de inserción de dependencias que usa el ámbito del componente.</span><span class="sxs-lookup"><span data-stu-id="ebb25-292">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="ebb25-293">La propiedad <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> está disponible, por lo que la aplicación puede obtener servicios de otros tipos, si es necesario.</span><span class="sxs-lookup"><span data-stu-id="ebb25-293">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="ebb25-294">Uso de DbContext de Entity Framework desde la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="ebb25-294">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="ebb25-295">Un tipo de servicio común que se puede recuperar desde la inserción de dependencias en aplicaciones web es el objeto <xref:Microsoft.EntityFrameworkCore.DbContext> de Entity Framework (EF).</span><span class="sxs-lookup"><span data-stu-id="ebb25-295">One common service type to retrieve from DI in web apps is Entity Framework (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> objects.</span></span> <span data-ttu-id="ebb25-296">El registro de servicios de EF mediante <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> agrega el objeto <xref:Microsoft.EntityFrameworkCore.DbContext> como un servicio con ámbito de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="ebb25-296">Registering EF services using <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> adds the <xref:Microsoft.EntityFrameworkCore.DbContext> as a scoped service by default.</span></span> <span data-ttu-id="ebb25-297">El registro como un servicio con ámbito puede dar lugar a problemas en aplicaciones Blazor porque hace que las instancias de <xref:Microsoft.EntityFrameworkCore.DbContext> sean de larga duración y se compartan a través de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ebb25-297">Registering as a scoped service can lead to problems in Blazor apps because it causes <xref:Microsoft.EntityFrameworkCore.DbContext> instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="ebb25-298"><xref:Microsoft.EntityFrameworkCore.DbContext> no es seguro para subprocesos y no se debe usar de manera simultánea.</span><span class="sxs-lookup"><span data-stu-id="ebb25-298"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="ebb25-299">En función de la aplicación, el uso de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> para limitar el ámbito de un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> a un componente único *puede* resolver el problema.</span><span class="sxs-lookup"><span data-stu-id="ebb25-299">Depending on the app, using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> to limit the scope of a <xref:Microsoft.EntityFrameworkCore.DbContext> to a single component *may* solve the issue.</span></span> <span data-ttu-id="ebb25-300">Si un componente no usa un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> en paralelo, es suficiente con la derivación del componente de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> y la recuperación del objeto <xref:Microsoft.EntityFrameworkCore.DbContext> de <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> porque garantiza lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ebb25-300">If a component doesn't use a <xref:Microsoft.EntityFrameworkCore.DbContext> in parallel, deriving the component from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and retrieving the <xref:Microsoft.EntityFrameworkCore.DbContext> from <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> is sufficient because it ensures that:</span></span>

* <span data-ttu-id="ebb25-301">Los componentes independientes no comparten un objeto <xref:Microsoft.EntityFrameworkCore.DbContext>.</span><span class="sxs-lookup"><span data-stu-id="ebb25-301">Separate components don't share a <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span>
* <span data-ttu-id="ebb25-302">El objeto <xref:Microsoft.EntityFrameworkCore.DbContext> dura tanto como el componente que depende de él.</span><span class="sxs-lookup"><span data-stu-id="ebb25-302">The <xref:Microsoft.EntityFrameworkCore.DbContext> lives only as long as the component depending on it.</span></span>

<span data-ttu-id="ebb25-303">Si es posible que un único componente use un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> de forma simultánea (por ejemplo, cada vez que un usuario selecciona un botón), incluso el uso de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> no evita problemas con las operaciones de EF simultáneas.</span><span class="sxs-lookup"><span data-stu-id="ebb25-303">If a single component might use a <xref:Microsoft.EntityFrameworkCore.DbContext> concurrently (for example, every time a user selects a button), even using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="ebb25-304">En ese caso, use otro objeto <xref:Microsoft.EntityFrameworkCore.DbContext> para cada operación de EF lógica.</span><span class="sxs-lookup"><span data-stu-id="ebb25-304">In that case, use a different <xref:Microsoft.EntityFrameworkCore.DbContext> for each logical EF operation.</span></span> <span data-ttu-id="ebb25-305">Use uno de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="ebb25-305">Use either of the following approaches:</span></span>

* <span data-ttu-id="ebb25-306">Cree el objeto <xref:Microsoft.EntityFrameworkCore.DbContext> directamente con <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> como argumento, que se puede recuperar mediante la inserción de dependencias y es seguro para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="ebb25-306">Create the <xref:Microsoft.EntityFrameworkCore.DbContext> directly using <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> as an argument, which can be retrieved from DI and is thread safe.</span></span>

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = new AppDbContext(DbContextOptions))
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

* <span data-ttu-id="ebb25-307">Registre el objeto <xref:Microsoft.EntityFrameworkCore.DbContext> en el contenedor de servicios con una duración transitoria:</span><span class="sxs-lookup"><span data-stu-id="ebb25-307">Register the <xref:Microsoft.EntityFrameworkCore.DbContext> in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="ebb25-308">Al registrar el contexto, use <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="ebb25-308">When registering the context, use <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="ebb25-309">El método de extensión <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> toma dos parámetros opcionales de tipo <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime>.</span><span class="sxs-lookup"><span data-stu-id="ebb25-309">The <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method takes two optional parameters of type <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime>.</span></span> <span data-ttu-id="ebb25-310">Para usar este enfoque, solo el parámetro `contextLifetime` tiene que ser <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="ebb25-310">To use this approach, only the `contextLifetime` parameter needs to be <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="ebb25-311">`optionsLifetime` puede conservar su valor predeterminado de <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="ebb25-311">`optionsLifetime` can keep its default value of <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType>.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="ebb25-312">El objeto <xref:Microsoft.EntityFrameworkCore.DbContext> transitorio se puede insertar de la forma habitual (mediante [`@inject`](xref:mvc/views/razor#inject)) en componentes que no ejecutarán varias operaciones de EF en paralelo.</span><span class="sxs-lookup"><span data-stu-id="ebb25-312">The transient <xref:Microsoft.EntityFrameworkCore.DbContext> can be injected as normal (using [`@inject`](xref:mvc/views/razor#inject)) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="ebb25-313">Los que puedan realizar varias operaciones de EF simultáneamente pueden solicitar objetos <xref:Microsoft.EntityFrameworkCore.DbContext> independientes para cada operación paralela mediante <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A>.</span><span class="sxs-lookup"><span data-stu-id="ebb25-313">Those that may perform multiple EF operations simultaneously can request separate <xref:Microsoft.EntityFrameworkCore.DbContext> objects for each parallel operation using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A>.</span></span>

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = ServiceProvider.GetRequiredService<AppDbContext>())
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="ebb25-314">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="ebb25-314">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
