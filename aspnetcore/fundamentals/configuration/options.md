---
<span data-ttu-id="112f4-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="112f4-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="112f4-102">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="112f4-102">'Blazor'</span></span>
- <span data-ttu-id="112f4-103">"Identity"</span><span class="sxs-lookup"><span data-stu-id="112f4-103">'Identity'</span></span>
- <span data-ttu-id="112f4-104">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="112f4-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="112f4-105">"Razor"</span><span class="sxs-lookup"><span data-stu-id="112f4-105">'Razor'</span></span>
- <span data-ttu-id="112f4-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="112f4-106">'SignalR' uid:</span></span> 

--- 
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="112f4-107">Patrón de opciones en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="112f4-107">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="112f4-108">Por [Kirk Larkin](https://twitter.com/serpent5) y [Rick Anderson](https://twitter.com/RickAndMSFT).</span><span class="sxs-lookup"><span data-stu-id="112f4-108">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="112f4-109">El patrón de opciones usa clases para proporcionar acceso fuertemente tipado a grupos de configuraciones relacionadas.</span><span class="sxs-lookup"><span data-stu-id="112f4-109">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="112f4-110">Cuando los [valores de configuración](xref:fundamentals/configuration/index) están aislados por escenario en clases independientes, la aplicación se ajusta a dos principios de ingeniería de software importantes:</span><span class="sxs-lookup"><span data-stu-id="112f4-110">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="112f4-111">El [principio de segregación de interfaz (ISP) o encapsulación](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): los escenarios (clases) que dependen de valores de configuración dependen únicamente de los valores de configuración que usen.</span><span class="sxs-lookup"><span data-stu-id="112f4-111">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="112f4-112">[Separación de intereses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): los valores de configuración para distintos elementos de la aplicación no son dependientes entre sí ni están emparejados.</span><span class="sxs-lookup"><span data-stu-id="112f4-112">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="112f4-113">Las opciones también proporcionan un mecanismo para validar los datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="112f4-113">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="112f4-114">Para obtener más información, consulte la sección [Opciones de validación](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="112f4-114">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="112f4-115">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="112f4-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="112f4-116">Enlace de configuración jerárquica</span><span class="sxs-lookup"><span data-stu-id="112f4-116">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="112f4-117">Interfaces de opciones</span><span class="sxs-lookup"><span data-stu-id="112f4-117">Options interfaces</span></span>

<span data-ttu-id="112f4-118"><xref:Microsoft.Extensions.Options.IOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="112f4-118"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="112f4-119">***No*** admite:</span><span class="sxs-lookup"><span data-stu-id="112f4-119">Does ***not*** support:</span></span>
  * <span data-ttu-id="112f4-120">Lectura de los datos de configuración una vez iniciada la aplicación.</span><span class="sxs-lookup"><span data-stu-id="112f4-120">Reading of configuration data after the app has started.</span></span>
  * [<span data-ttu-id="112f4-121">Opciones con nombre</span><span class="sxs-lookup"><span data-stu-id="112f4-121">Named options</span></span>](#named)
* <span data-ttu-id="112f4-122">Se registra como [Singleton](xref:fundamentals/dependency-injection#singleton) y se puede insertar en cualquier [duración del servicio](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="112f4-122">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="112f4-123"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span><span class="sxs-lookup"><span data-stu-id="112f4-123"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="112f4-124">es útil en escenarios donde se deben volver a calcular las opciones en cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="112f4-124">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="112f4-125">Para obtener más información, consulte el apartado [Uso de IOptionsSnapshot para leer datos actualizados](#ios).</span><span class="sxs-lookup"><span data-stu-id="112f4-125">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="112f4-126">Se registra como [Con ámbito](xref:fundamentals/dependency-injection#scoped) y, por tanto, no se puede insertar en un servicio Singleton.</span><span class="sxs-lookup"><span data-stu-id="112f4-126">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="112f4-127">Admite [opciones con nombre](#named)</span><span class="sxs-lookup"><span data-stu-id="112f4-127">Supports [named options](#named)</span></span>

<span data-ttu-id="112f4-128"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="112f4-128"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="112f4-129">se usa para recuperar las opciones y administrar las notificaciones de las opciones para instancias de `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="112f4-129">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="112f4-130">Se registra como [Singleton](xref:fundamentals/dependency-injection#singleton) y se puede insertar en cualquier [duración del servicio](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="112f4-130">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="112f4-131">Es compatible con:</span><span class="sxs-lookup"><span data-stu-id="112f4-131">Supports:</span></span>
  * <span data-ttu-id="112f4-132">Notificaciones de cambios</span><span class="sxs-lookup"><span data-stu-id="112f4-132">Change notifications</span></span>
  * [<span data-ttu-id="112f4-133">Opciones con nombre</span><span class="sxs-lookup"><span data-stu-id="112f4-133">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
  * [<span data-ttu-id="112f4-134">Configuración que se puede recargar</span><span class="sxs-lookup"><span data-stu-id="112f4-134">Reloadable configuration</span></span>](#ios)
  * <span data-ttu-id="112f4-135">Invalidación de opciones de selección (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="112f4-135">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="112f4-136">Los escenarios [posteriores a la configuración](#options-post-configuration) permiten establecer o cambiar las opciones después de que finalice toda la configuración de <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="112f4-136">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="112f4-137"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> es responsable de crear nuevas instancias de opciones.</span><span class="sxs-lookup"><span data-stu-id="112f4-137"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="112f4-138">Tiene un solo método <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="112f4-138">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="112f4-139">La implementación predeterminada toma todas las instancias registradas de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> y <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>, y establece todas las configuraciones primero, seguidas de las configuraciones posteriores.</span><span class="sxs-lookup"><span data-stu-id="112f4-139">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="112f4-140">Distingue entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> y <xref:Microsoft.Extensions.Options.IConfigureOptions%601>, y solo llama a la interfaz adecuada.</span><span class="sxs-lookup"><span data-stu-id="112f4-140">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="112f4-141"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> se usa por <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para almacenar en caché las instancias de `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="112f4-141"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="112f4-142"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalida instancias de opciones en la supervisión para que se pueda volver a calcular el valor (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="112f4-142">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="112f4-143">Los valores se pueden introducir manualmente y mediante <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="112f4-143">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="112f4-144">Se usa el método <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> cuando todas las instancias con nombre se deben volver a crear a petición.</span><span class="sxs-lookup"><span data-stu-id="112f4-144">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="112f4-145">Uso de IOptionsSnapshot para leer datos actualizados</span><span class="sxs-lookup"><span data-stu-id="112f4-145">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="112f4-146">Al usar <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, cuando se accede a las opciones y se las almacena en caché durante la vigencia de la solicitud, se calculan una vez por solicitud.</span><span class="sxs-lookup"><span data-stu-id="112f4-146">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="112f4-147">Los cambios en la configuración se leen tras iniciarse la aplicación al usar proveedores de configuración que admiten la lectura de valores de configuración actualizados.</span><span class="sxs-lookup"><span data-stu-id="112f4-147">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="112f4-148">La diferencia entre `IOptionsMonitor` y `IOptionsSnapshot` es que:</span><span class="sxs-lookup"><span data-stu-id="112f4-148">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="112f4-149">`IOptionsMonitor` es un [servicio singleton](xref:fundamentals/dependency-injection#singleton) que recupera los valores de las opciones actuales en cualquier momento, lo que resulta especialmente útil en las dependencias singleton.</span><span class="sxs-lookup"><span data-stu-id="112f4-149">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="112f4-150">`IOptionsSnapshot` es un [servicio con ámbito](xref:fundamentals/dependency-injection#scoped) y proporciona una instantánea de las opciones en el momento en que se construye el objeto `IOptionsSnapshot<T>`.</span><span class="sxs-lookup"><span data-stu-id="112f4-150">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="112f4-151">Las instantáneas de opciones están diseñadas para usarlas con dependencias transitorias y con ámbito.</span><span class="sxs-lookup"><span data-stu-id="112f4-151">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="112f4-152">El código siguiente usa <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span><span class="sxs-lookup"><span data-stu-id="112f4-152">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="112f4-153">El código siguiente registra una instancia de configuración en la que se enlaza `MyOptions`:</span><span class="sxs-lookup"><span data-stu-id="112f4-153">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="112f4-154">En el código anterior, los cambios en el archivo de configuración de JSON producidos una vez iniciada la aplicación se leen.</span><span class="sxs-lookup"><span data-stu-id="112f4-154">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="112f4-155">IOptionsMonitor</span><span class="sxs-lookup"><span data-stu-id="112f4-155">IOptionsMonitor</span></span>

<span data-ttu-id="112f4-156">El código siguiente registra una instancia de configuración en la que se enlaza `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="112f4-156">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="112f4-157">En el ejemplo siguiente se usa <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="112f4-157">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="112f4-158">En el código anterior, de forma predeterminada, los cambios en el archivo de configuración de JSON producidos una vez iniciada la aplicación se leen.</span><span class="sxs-lookup"><span data-stu-id="112f4-158">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="112f4-159">Compatibilidad de opciones con nombre con IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="112f4-159">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="112f4-160">Opciones con nombre:</span><span class="sxs-lookup"><span data-stu-id="112f4-160">Named options:</span></span>

* <span data-ttu-id="112f4-161">son útiles cuando varias secciones de configuración se enlazan a las mismas propiedades.</span><span class="sxs-lookup"><span data-stu-id="112f4-161">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="112f4-162">Distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="112f4-162">Are case sensitive.</span></span>

<span data-ttu-id="112f4-163">Fíjese en el siguiente archivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="112f4-163">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="112f4-164">En lugar de crear dos clases para enlazar `TopItem:Month` y `TopItem:Year`, se usará la clase siguiente para cada sección:</span><span class="sxs-lookup"><span data-stu-id="112f4-164">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="112f4-165">El siguiente código configura las opciones con nombre:</span><span class="sxs-lookup"><span data-stu-id="112f4-165">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="112f4-166">En el código siguiente se muestran las opciones con nombre:</span><span class="sxs-lookup"><span data-stu-id="112f4-166">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="112f4-167">Todas las opciones son instancias con nombre.</span><span class="sxs-lookup"><span data-stu-id="112f4-167">All options are named instances.</span></span> <span data-ttu-id="112f4-168">Las instancias de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> se usan para seleccionar como destino la instancia de `Options.DefaultName`, que es `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="112f4-168"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="112f4-169"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> también implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="112f4-169"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="112f4-170">La implementación predeterminada de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> tiene lógica para usar cada una de forma adecuada.</span><span class="sxs-lookup"><span data-stu-id="112f4-170">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="112f4-171">La opción con nombre `null` se usa para seleccionar como destino todas las instancias con nombre, en lugar de una instancia con nombre determinada.</span><span class="sxs-lookup"><span data-stu-id="112f4-171">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="112f4-172"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> y <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> usan esta convención.</span><span class="sxs-lookup"><span data-stu-id="112f4-172"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="112f4-173">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="112f4-173">OptionsBuilder API</span></span>

<span data-ttu-id="112f4-174"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> se usa para configurar instancias `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="112f4-174"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="112f4-175">`OptionsBuilder` simplifica la creación de opciones con nombre, ya que es un único parámetro para la llamada `AddOptions<TOptions>(string optionsName)` inicial en lugar de aparecer en todas las llamadas posteriores.</span><span class="sxs-lookup"><span data-stu-id="112f4-175">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="112f4-176">La validación de opciones y las sobrecargas `ConfigureOptions` que aceptan las dependencias de servicio solo están disponibles mediante `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="112f4-176">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="112f4-177">`OptionsBuilder` se usa en la sección [Opciones de validación](#val).</span><span class="sxs-lookup"><span data-stu-id="112f4-177">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="112f4-178">Uso de servicios de DI para configurar opciones</span><span class="sxs-lookup"><span data-stu-id="112f4-178">Use DI services to configure options</span></span>

<span data-ttu-id="112f4-179">Hay dos formas de acceder a los servicios desde la inserción de dependencias durante la configuración de opciones:</span><span class="sxs-lookup"><span data-stu-id="112f4-179">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="112f4-180">Si se pasa un delegado de configuración a [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) en [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="112f4-180">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="112f4-181">`OptionsBuilder<TOptions>` proporciona sobrecargas de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) que permiten usar hasta cinco servicios para configurar las opciones:</span><span class="sxs-lookup"><span data-stu-id="112f4-181">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="112f4-182">Si se crea un tipo que implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601> o <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, y se registra como un servicio.</span><span class="sxs-lookup"><span data-stu-id="112f4-182">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="112f4-183">Se recomienda pasar un delegado de configuración a [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), ya que la creación de un servicio es más complicada.</span><span class="sxs-lookup"><span data-stu-id="112f4-183">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="112f4-184">La creación de un tipo es equivalente a lo que el marco hace cuando se llama a [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="112f4-184">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="112f4-185">La llamada a [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registra una interfaz <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> genérica y transitoria, con un constructor que acepta los tipos de servicio genéricos especificados.</span><span class="sxs-lookup"><span data-stu-id="112f4-185">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="112f4-186">Opciones de validación</span><span class="sxs-lookup"><span data-stu-id="112f4-186">Options validation</span></span>

<span data-ttu-id="112f4-187">Opciones de validación permite que se validen los valores de opción.</span><span class="sxs-lookup"><span data-stu-id="112f4-187">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="112f4-188">Fíjese en el siguiente archivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="112f4-188">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="112f4-189">La siguiente clase se enlaza a la sección de configuración `"MyConfig"` y aplica un par de reglas `DataAnnotations`:</span><span class="sxs-lookup"><span data-stu-id="112f4-189">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="112f4-190">El código siguiente llama a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> para obtener un elemento [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) que se enlaza a la clase `MyConfigOptions` y habilita la validación `DataAnnotations`:</span><span class="sxs-lookup"><span data-stu-id="112f4-190">The following code calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an  [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class and enables `DataAnnotations` validation:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="112f4-191">En el código siguiente se muestran los valores de configuración o los errores de validación:</span><span class="sxs-lookup"><span data-stu-id="112f4-191">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="112f4-192">El código siguiente aplica una regla de validación más compleja mediante un delegado:</span><span class="sxs-lookup"><span data-stu-id="112f4-192">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="112f4-193">IValidateOptions para la validación compleja</span><span class="sxs-lookup"><span data-stu-id="112f4-193">IValidateOptions for complex validation</span></span>

<span data-ttu-id="112f4-194">La siguiente clase implementa <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span><span class="sxs-lookup"><span data-stu-id="112f4-194">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="112f4-195">`IValidateOptions` permite mover el código de validación fuera de `StartUp` y dentro de una clase.</span><span class="sxs-lookup"><span data-stu-id="112f4-195">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="112f4-196">Con el código anterior, la validación se habilita en `Startup.ConfigureServices` con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="112f4-196">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/StartupValidation.cs?name=snippet)]

<!-- The following comment doesn't seem that useful 
Options validation doesn't guard against options modifications after the options instance is created. For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed. The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.

The `Validate` method accepts a `Func<TOptions, bool>`. To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:

* Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` validates:

* A specific named options instance.
* All options when `name` is `null`.

Return a `ValidateOptionsResult` from your implementation of the interface:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`. `Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.

-->

## <a name="options-post-configuration"></a><span data-ttu-id="112f4-197">Configuración posterior de las opciones</span><span class="sxs-lookup"><span data-stu-id="112f4-197">Options post-configuration</span></span>

<span data-ttu-id="112f4-198">Establezca la configuración posterior con <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="112f4-198">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="112f4-199">La configuración posterior se ejecuta una vez completada toda la configuración de <xref:Microsoft.Extensions.Options.IConfigureOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="112f4-199">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="112f4-200"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> está disponible para configurar posteriormente las opciones con nombre:</span><span class="sxs-lookup"><span data-stu-id="112f4-200"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="112f4-201">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> para configurar posteriormente todas las instancias de configuración:</span><span class="sxs-lookup"><span data-stu-id="112f4-201">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="112f4-202">Acceso a opciones durante el inicio</span><span class="sxs-lookup"><span data-stu-id="112f4-202">Accessing options during startup</span></span>

<span data-ttu-id="112f4-203"><xref:Microsoft.Extensions.Options.IOptions%601> y <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> puede usarse en `Startup.Configure`, ya que los servicios se compilan antes de que se ejecute el método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="112f4-203"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="112f4-204">No use <xref:Microsoft.Extensions.Options.IOptions%601> o <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="112f4-204">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="112f4-205">Puede que exista un estado incoherente de opciones debido al orden de los registros de servicio.</span><span class="sxs-lookup"><span data-stu-id="112f4-205">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="112f4-206">Paquete NuGet Options.ConfigurationExtensions</span><span class="sxs-lookup"><span data-stu-id="112f4-206">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="112f4-207">Se hace referencia implícita al paquete [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) en las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="112f4-207">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="112f4-208">El patrón de opciones usa clases para representar grupos de configuraciones relacionadas.</span><span class="sxs-lookup"><span data-stu-id="112f4-208">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="112f4-209">Cuando los [valores de configuración](xref:fundamentals/configuration/index) están aislados por escenario en clases independientes, la aplicación se ajusta a dos principios de ingeniería de software importantes:</span><span class="sxs-lookup"><span data-stu-id="112f4-209">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="112f4-210">El [principio de segregación de interfaz (ISP) o encapsulación](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): los escenarios (clases) que dependen de valores de configuración dependen únicamente de los valores de configuración que usen.</span><span class="sxs-lookup"><span data-stu-id="112f4-210">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="112f4-211">[Separación de intereses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): los valores de configuración para distintos elementos de la aplicación no son dependientes entre sí ni están emparejados.</span><span class="sxs-lookup"><span data-stu-id="112f4-211">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="112f4-212">Las opciones también proporcionan un mecanismo para validar los datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="112f4-212">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="112f4-213">Para obtener más información, consulte la sección [Opciones de validación](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="112f4-213">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="112f4-214">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="112f4-214">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="112f4-215">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="112f4-215">Prerequisites</span></span>

<span data-ttu-id="112f4-216">Haga referencia al [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) o agregue una referencia de paquete al paquete [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="112f4-216">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="112f4-217">Interfaces de opciones</span><span class="sxs-lookup"><span data-stu-id="112f4-217">Options interfaces</span></span>

<span data-ttu-id="112f4-218"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> se usa para recuperar las opciones y administrar las notificaciones de las opciones para instancias de `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="112f4-218"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="112f4-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> admite las siguientes situaciones:</span><span class="sxs-lookup"><span data-stu-id="112f4-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="112f4-220">Notificaciones de cambios</span><span class="sxs-lookup"><span data-stu-id="112f4-220">Change notifications</span></span>
* [<span data-ttu-id="112f4-221">Opciones con nombre</span><span class="sxs-lookup"><span data-stu-id="112f4-221">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="112f4-222">Configuración que se puede recargar</span><span class="sxs-lookup"><span data-stu-id="112f4-222">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="112f4-223">Invalidación de opciones de selección (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="112f4-223">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="112f4-224">Los escenarios [posteriores a la configuración](#options-post-configuration) le permiten establecer o cambiar las opciones después de que finalice toda la configuración de <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="112f4-224">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="112f4-225"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> es responsable de crear nuevas instancias de opciones.</span><span class="sxs-lookup"><span data-stu-id="112f4-225"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="112f4-226">Tiene un solo método <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="112f4-226">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="112f4-227">La implementación predeterminada toma todas las instancias registradas de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> y <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>, y establece todas las configuraciones primero, seguidas de las configuraciones posteriores.</span><span class="sxs-lookup"><span data-stu-id="112f4-227">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="112f4-228">Distingue entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> y <xref:Microsoft.Extensions.Options.IConfigureOptions%601>, y solo llama a la interfaz adecuada.</span><span class="sxs-lookup"><span data-stu-id="112f4-228">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="112f4-229"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> se usa por <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para almacenar en caché las instancias de `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="112f4-229"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="112f4-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalida instancias de opciones en la supervisión para que se pueda volver a calcular el valor (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="112f4-230">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="112f4-231">Los valores se pueden introducir manualmente y mediante <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="112f4-231">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="112f4-232">Se usa el método <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> cuando todas las instancias con nombre se deben volver a crear a petición.</span><span class="sxs-lookup"><span data-stu-id="112f4-232">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="112f4-233"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> es útil en escenarios donde se deben volver a calcular las opciones en cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="112f4-233"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="112f4-234">Para obtener más información, consulte la sección [Volver a cargar los datos de configuración con IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="112f4-234">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="112f4-235"><xref:Microsoft.Extensions.Options.IOptions%601> puede utilizarse para admitir las opciones.</span><span class="sxs-lookup"><span data-stu-id="112f4-235"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="112f4-236">Sin embargo, <xref:Microsoft.Extensions.Options.IOptions%601> no es compatible con los escenarios anteriores de <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="112f4-236">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="112f4-237">Aún puede usar <xref:Microsoft.Extensions.Options.IOptions%601> en marcos y bibliotecas existentes que ya usan la interfaz <xref:Microsoft.Extensions.Options.IOptions%601> y no requieren los escenarios proporcionados por <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="112f4-237">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="112f4-238">Configuración de opciones generales</span><span class="sxs-lookup"><span data-stu-id="112f4-238">General options configuration</span></span>

<span data-ttu-id="112f4-239">La configuración de opciones generales se muestra en el ejemplo 1 en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="112f4-239">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="112f4-240">Una clase de opciones debe ser no abstracta con un constructor público sin parámetros.</span><span class="sxs-lookup"><span data-stu-id="112f4-240">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="112f4-241">La siguiente clase, `MyOptions`, tiene dos propiedades: `Option1` y `Option2`.</span><span class="sxs-lookup"><span data-stu-id="112f4-241">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="112f4-242">Configurar los valores predeterminados es opcional, pero el constructor de clases en el ejemplo siguiente establece el valor predeterminado de `Option1`.</span><span class="sxs-lookup"><span data-stu-id="112f4-242">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="112f4-243">`Option2` tiene un valor predeterminado que se establece al inicializar la propiedad directamente (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="112f4-243">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="112f4-244">La clase `MyOptions` se agrega al contenedor de servicios con <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> y se enlaza a la configuración:</span><span class="sxs-lookup"><span data-stu-id="112f4-244">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="112f4-245">El siguiente modelo de página usa la [inserción de dependencias de constructor](xref:mvc/controllers/dependency-injection) con <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para acceder a la configuración (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="112f4-245">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="112f4-246">El archivo *appSettings.json* del ejemplo especifica valores para `option1` y `option2`:</span><span class="sxs-lookup"><span data-stu-id="112f4-246">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="112f4-247">Cuando se ejecuta la aplicación, el método `OnGet` del modelo de página devuelve una cadena que muestra los valores de la clase de opción:</span><span class="sxs-lookup"><span data-stu-id="112f4-247">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="112f4-248">Al usar una instancia de <xref:System.Configuration.ConfigurationBuilder> personalizada para cargar las opciones de configuración desde un archivo de configuración, confirme que la ruta de acceso base esté configurada correctamente:</span><span class="sxs-lookup"><span data-stu-id="112f4-248">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="112f4-249">Al cargar las opciones de configuración desde el archivo de configuración a través de <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, no es necesario establecer de forma explícita la ruta de acceso base.</span><span class="sxs-lookup"><span data-stu-id="112f4-249">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="112f4-250">Configurar opciones simples con un delegado</span><span class="sxs-lookup"><span data-stu-id="112f4-250">Configure simple options with a delegate</span></span>

<span data-ttu-id="112f4-251">La configuración de opciones simples con un delegado se muestra como ejemplo 2 en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="112f4-251">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="112f4-252">Use un delegado para establecer los valores de opciones.</span><span class="sxs-lookup"><span data-stu-id="112f4-252">Use a delegate to set options values.</span></span> <span data-ttu-id="112f4-253">La aplicación de ejemplo usa la clase `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="112f4-253">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="112f4-254">En el código siguiente, un segundo servicio <xref:Microsoft.Extensions.Options.IConfigureOptions%601> se agrega al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="112f4-254">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="112f4-255">Usa un delegado para configurar el enlace con `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="112f4-255">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="112f4-256">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="112f4-256">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="112f4-257">Puede agregar varios proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="112f4-257">You can add multiple configuration providers.</span></span> <span data-ttu-id="112f4-258">Los proveedores de configuración están disponibles en paquetes de NuGet y se aplican en el orden en que están registrados.</span><span class="sxs-lookup"><span data-stu-id="112f4-258">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="112f4-259">Para obtener más información, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="112f4-259">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="112f4-260">Cada llamada a <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> agrega un servicio <xref:Microsoft.Extensions.Options.IConfigureOptions%601> al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="112f4-260">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="112f4-261">En el ejemplo anterior, los valores de `Option1` y `Option2` se especifican en *appSettings.json*, pero los valores de `Option1` y `Option2` se reemplazan por el delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="112f4-261">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="112f4-262">Cuando se habilita más de un servicio de configuración, la última fuente de configuración especificada *gana* y establece el valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="112f4-262">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="112f4-263">Cuando se ejecuta la aplicación, el método `OnGet` del modelo de página devuelve una cadena que muestra los valores de la clase de opción:</span><span class="sxs-lookup"><span data-stu-id="112f4-263">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="112f4-264">Configuración de subopciones</span><span class="sxs-lookup"><span data-stu-id="112f4-264">Suboptions configuration</span></span>

<span data-ttu-id="112f4-265">La configuración de subopciones se muestra en el ejemplo 3 en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="112f4-265">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="112f4-266">Las aplicaciones deben crear clases de opciones que pertenezcan a grupos específicos de escenarios (clases) en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="112f4-266">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="112f4-267">Los elementos de la aplicación que requieran valores de configuración deben acceder solamente a los valores de configuración que usen.</span><span class="sxs-lookup"><span data-stu-id="112f4-267">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="112f4-268">Al enlazar opciones para la configuración, cada propiedad en el tipo de opciones se enlaza a una clave de configuración del formulario `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="112f4-268">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="112f4-269">Por ejemplo, la propiedad `MyOptions.Option1` se enlaza a la clave `Option1`, que se lee desde la propiedad `option1` en *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="112f4-269">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="112f4-270">En el código siguiente, se agrega un tercer servicio <xref:Microsoft.Extensions.Options.IConfigureOptions%601> al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="112f4-270">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="112f4-271">Enlaza `MySubOptions` a la sección `subsection` del archivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="112f4-271">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="112f4-272">El método `GetSection` requiere el espacio de nombres <xref:Microsoft.Extensions.Configuration?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="112f4-272">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="112f4-273">El archivo *appSettings.json* del ejemplo define un miembro `subsection` con las claves para `suboption1` y `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="112f4-273">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="112f4-274">La clase `MySubOptions` define propiedades, `SubOption1` y `SubOption2`, para mantener los valores de opciones (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="112f4-274">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="112f4-275">El método `OnGet` del modelo de página devuelve una cadena con los valores de opciones (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="112f4-275">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="112f4-276">Cuando se ejecuta la aplicación, el método `OnGet` devuelve una cadena que muestra los valores de clase de subopciones:</span><span class="sxs-lookup"><span data-stu-id="112f4-276">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="112f4-277">Inserción de opciones</span><span class="sxs-lookup"><span data-stu-id="112f4-277">Options injection</span></span>

<span data-ttu-id="112f4-278">La inserción de opciones se muestra en el ejemplo 4 en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="112f4-278">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="112f4-279">Inserte <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> en:</span><span class="sxs-lookup"><span data-stu-id="112f4-279">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="112f4-280">Una página de Razor o una vista de MVC con la directiva [`@inject`](xref:mvc/views/razor#inject) de Razor.</span><span class="sxs-lookup"><span data-stu-id="112f4-280">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="112f4-281">Un modelo de página o vista.</span><span class="sxs-lookup"><span data-stu-id="112f4-281">A page or view model.</span></span>

<span data-ttu-id="112f4-282">En el ejemplo siguiente de la aplicación de ejemplo, inserte <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> en un modelo de página (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="112f4-282">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="112f4-283">La aplicación de ejemplo muestra cómo insertar `IOptionsMonitor<MyOptions>` con una directiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="112f4-283">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="112f4-284">Cuando se ejecuta la aplicación, se muestran los valores de opciones en la página representada:</span><span class="sxs-lookup"><span data-stu-id="112f4-284">When the app is run, the options values are shown in the rendered page:</span></span>

![Los valores de opciones de Option1: value1_from_json y Option2: -1 se cargan desde el modelo y por inserción en la vista.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="112f4-286">Volver a cargar los datos de configuración con IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="112f4-286">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="112f4-287">El procedimiento de volver a cargar los datos de configuración con <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> se muestra en el ejemplo 5 en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="112f4-287">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="112f4-288">Al usar <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, cuando se accede a las opciones y se las almacena en caché durante la vigencia de la solicitud, se calculan una vez por solicitud.</span><span class="sxs-lookup"><span data-stu-id="112f4-288">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="112f4-289">La diferencia entre `IOptionsMonitor` y `IOptionsSnapshot` es que:</span><span class="sxs-lookup"><span data-stu-id="112f4-289">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="112f4-290">`IOptionsMonitor` es un [servicio singleton](xref:fundamentals/dependency-injection#singleton) que recupera los valores de las opciones actuales en cualquier momento, lo que resulta especialmente útil en las dependencias singleton.</span><span class="sxs-lookup"><span data-stu-id="112f4-290">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="112f4-291">`IOptionsSnapshot` es un [servicio con ámbito](xref:fundamentals/dependency-injection#scoped) y proporciona una instantánea de las opciones en el momento en que se construye el objeto `IOptionsSnapshot<T>`.</span><span class="sxs-lookup"><span data-stu-id="112f4-291">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="112f4-292">Las instantáneas de opciones están diseñadas para usarlas con dependencias transitorias y con ámbito.</span><span class="sxs-lookup"><span data-stu-id="112f4-292">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="112f4-293">En el ejemplo siguiente se muestra cómo se crea un nuevo <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> después de cambiar el archivo *appSettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="112f4-293">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="112f4-294">Varias solicitudes al servidor devuelven valores constantes proporcionados por el archivo *appSettings.json* hasta que se modifique el archivo y vuelva a cargarse la configuración.</span><span class="sxs-lookup"><span data-stu-id="112f4-294">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="112f4-295">En la siguiente imagen se muestran los valores `option1` y `option2` iniciales cargados desde el archivo *appSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="112f4-295">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="112f4-296">Cambie los valores del archivo *appSettings.json* a `value1_from_json UPDATED` y `200`.</span><span class="sxs-lookup"><span data-stu-id="112f4-296">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="112f4-297">Guarde el archivo *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="112f4-297">Save the *appsettings.json* file.</span></span> <span data-ttu-id="112f4-298">Actualice el explorador para ver qué valores de opciones se han actualizado:</span><span class="sxs-lookup"><span data-stu-id="112f4-298">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="112f4-299">Compatibilidad de opciones con nombre con IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="112f4-299">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="112f4-300">La compatibilidad de opciones con nombre con <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> se muestra en el ejemplo 6 de la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="112f4-300">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="112f4-301">La compatibilidad con las opciones con nombre permite a la aplicación distinguir entre las configuraciones de opciones con nombre.</span><span class="sxs-lookup"><span data-stu-id="112f4-301">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="112f4-302">En la aplicación de ejemplo, las opciones con nombre se declaran con [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), que llama al método de extensión [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="112f4-302">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="112f4-303">Las opciones con nombre distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="112f4-303">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="112f4-304">La aplicación de ejemplo accede a las opciones con nombre con <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="112f4-304">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="112f4-305">Al ejecutar la aplicación de ejemplo, se devuelven las opciones con nombre:</span><span class="sxs-lookup"><span data-stu-id="112f4-305">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="112f4-306">Se proporcionan valores de `named_options_1` a partir de la configuración, que se cargan desde el archivo *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="112f4-306">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="112f4-307">Los valores de `named_options_2` los proporciona:</span><span class="sxs-lookup"><span data-stu-id="112f4-307">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="112f4-308">El delegado `named_options_2` en `ConfigureServices` para `Option1`.</span><span class="sxs-lookup"><span data-stu-id="112f4-308">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="112f4-309">El valor predeterminado para `Option2` proporcionado por la clase `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="112f4-309">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="112f4-310">Configurar todas las opciones con el método ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="112f4-310">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="112f4-311">Configure todas las instancias de opciones con el método <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="112f4-311">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="112f4-312">El siguiente código configura `Option1` para todas las instancias de configuración con un valor común.</span><span class="sxs-lookup"><span data-stu-id="112f4-312">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="112f4-313">Agregue manualmente este código al método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="112f4-313">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="112f4-314">Al ejecutar la aplicación de ejemplo después de agregar el código se produce el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="112f4-314">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="112f4-315">Todas las opciones son instancias con nombre.</span><span class="sxs-lookup"><span data-stu-id="112f4-315">All options are named instances.</span></span> <span data-ttu-id="112f4-316">Las instancias de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existentes se usan para seleccionar como destino la instancia de `Options.DefaultName`, que es `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="112f4-316">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="112f4-317"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> también implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="112f4-317"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="112f4-318">La implementación predeterminada de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> tiene lógica para usar cada una de forma adecuada.</span><span class="sxs-lookup"><span data-stu-id="112f4-318">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="112f4-319">La opción con nombre `null` se usa para seleccionar como destino todas las instancias con nombre, en lugar de una instancia con nombre determinada (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> y <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> usan esta convención).</span><span class="sxs-lookup"><span data-stu-id="112f4-319">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="112f4-320">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="112f4-320">OptionsBuilder API</span></span>

<span data-ttu-id="112f4-321"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> se usa para configurar instancias `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="112f4-321"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="112f4-322">`OptionsBuilder` simplifica la creación de opciones con nombre, ya que es un único parámetro para la llamada `AddOptions<TOptions>(string optionsName)` inicial en lugar de aparecer en todas las llamadas posteriores.</span><span class="sxs-lookup"><span data-stu-id="112f4-322">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="112f4-323">La validación de opciones y las sobrecargas `ConfigureOptions` que aceptan las dependencias de servicio solo están disponibles mediante `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="112f4-323">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="112f4-324">Uso de servicios de DI para configurar opciones</span><span class="sxs-lookup"><span data-stu-id="112f4-324">Use DI services to configure options</span></span>

<span data-ttu-id="112f4-325">Hay dos formas de acceder a otros servicios desde la inserción de dependencias durante la configuración de opciones:</span><span class="sxs-lookup"><span data-stu-id="112f4-325">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="112f4-326">Si se pasa un delegado de configuración a [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) en [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="112f4-326">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="112f4-327">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) proporciona sobrecargas de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) que permiten usar hasta cinco servicios para configurar las opciones:</span><span class="sxs-lookup"><span data-stu-id="112f4-327">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="112f4-328">Si se crea un tipo propio que implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601> o <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, y se registrar como un servicio.</span><span class="sxs-lookup"><span data-stu-id="112f4-328">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="112f4-329">Se recomienda pasar un delegado de configuración a [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), ya que la creación de un servicio es más complicada.</span><span class="sxs-lookup"><span data-stu-id="112f4-329">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="112f4-330">La creación de un tipo propio es equivalente a lo que el marco hace de forma automática cuando se usa [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="112f4-330">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="112f4-331">La llamada a [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registra una interfaz <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> genérica y transitoria, con un constructor que acepta los tipos de servicio genéricos especificados.</span><span class="sxs-lookup"><span data-stu-id="112f4-331">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="112f4-332">Opciones de validación</span><span class="sxs-lookup"><span data-stu-id="112f4-332">Options validation</span></span>

<span data-ttu-id="112f4-333">Las opciones de validación permiten validar las opciones cuando se configuran.</span><span class="sxs-lookup"><span data-stu-id="112f4-333">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="112f4-334">Llame a `Validate` con un método de validación que devuelve `true` si las opciones son válidas y `false` si no lo son:</span><span class="sxs-lookup"><span data-stu-id="112f4-334">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="112f4-335">El ejemplo anterior establece la instancia de opciones con nombre en `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="112f4-335">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="112f4-336">La instancia predeterminada es `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="112f4-336">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="112f4-337">La validación se ejecuta cuando se crea la instancia de opciones.</span><span class="sxs-lookup"><span data-stu-id="112f4-337">Validation runs when the options instance is created.</span></span> <span data-ttu-id="112f4-338">Una instancia de opciones pasa seguro la validación la primera vez que se accede.</span><span class="sxs-lookup"><span data-stu-id="112f4-338">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="112f4-339">La validación de opciones no protege contra las modificaciones de opciones después de crearse la instancia de opciones.</span><span class="sxs-lookup"><span data-stu-id="112f4-339">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="112f4-340">Por ejemplo, las opciones `IOptionsSnapshot` se crean y validan una vez por solicitud al obtenerse acceso a estas por primera vez.</span><span class="sxs-lookup"><span data-stu-id="112f4-340">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="112f4-341">Las opciones `IOptionsSnapshot` no se validarán de nuevo en intentos de acceso posteriores *para la misma solicitud*.</span><span class="sxs-lookup"><span data-stu-id="112f4-341">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="112f4-342">El método `Validate` acepta una expresión `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="112f4-342">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="112f4-343">Para personalizar completamente la validación, implemente `IValidateOptions<TOptions>`, que permite:</span><span class="sxs-lookup"><span data-stu-id="112f4-343">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="112f4-344">Validación de varios tipos de opciones: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="112f4-344">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="112f4-345">Validación que depende de otro tipo de opción: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="112f4-345">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="112f4-346">`IValidateOptions` valida:</span><span class="sxs-lookup"><span data-stu-id="112f4-346">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="112f4-347">Una instancia de opciones con nombre específica.</span><span class="sxs-lookup"><span data-stu-id="112f4-347">A specific named options instance.</span></span>
* <span data-ttu-id="112f4-348">Todas las opciones cuando `name` es `null`.</span><span class="sxs-lookup"><span data-stu-id="112f4-348">All options when `name` is `null`.</span></span>

<span data-ttu-id="112f4-349">Devuelve `ValidateOptionsResult` de la implementación de la interfaz:</span><span class="sxs-lookup"><span data-stu-id="112f4-349">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="112f4-350">La validación basada en la anotación de datos está disponible en el paquete [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) llamando al método <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> en `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="112f4-350">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="112f4-351">`Microsoft.Extensions.Options.DataAnnotations` se incluye en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="112f4-351">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="112f4-352">La validación diligente (con respuesta rápida a errores en el inicio) se está teniendo en cuenta de cara a una versión futura.</span><span class="sxs-lookup"><span data-stu-id="112f4-352">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="112f4-353">Configuración posterior de las opciones</span><span class="sxs-lookup"><span data-stu-id="112f4-353">Options post-configuration</span></span>

<span data-ttu-id="112f4-354">Establezca la configuración posterior con <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="112f4-354">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="112f4-355">La configuración posterior se ejecuta una vez completada toda la configuración de <xref:Microsoft.Extensions.Options.IConfigureOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="112f4-355">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="112f4-356"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> está disponible para configurar posteriormente las opciones con nombre:</span><span class="sxs-lookup"><span data-stu-id="112f4-356"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="112f4-357">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> para configurar posteriormente todas las instancias de configuración:</span><span class="sxs-lookup"><span data-stu-id="112f4-357">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="112f4-358">Acceso a opciones durante el inicio</span><span class="sxs-lookup"><span data-stu-id="112f4-358">Accessing options during startup</span></span>

<span data-ttu-id="112f4-359"><xref:Microsoft.Extensions.Options.IOptions%601> y <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> puede usarse en `Startup.Configure`, ya que los servicios se compilan antes de que se ejecute el método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="112f4-359"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="112f4-360">No use <xref:Microsoft.Extensions.Options.IOptions%601> o <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="112f4-360">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="112f4-361">Puede que exista un estado incoherente de opciones debido al orden de los registros de servicio.</span><span class="sxs-lookup"><span data-stu-id="112f4-361">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="112f4-362">El patrón de opciones usa clases para representar grupos de configuraciones relacionadas.</span><span class="sxs-lookup"><span data-stu-id="112f4-362">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="112f4-363">Cuando los [valores de configuración](xref:fundamentals/configuration/index) están aislados por escenario en clases independientes, la aplicación se ajusta a dos principios de ingeniería de software importantes:</span><span class="sxs-lookup"><span data-stu-id="112f4-363">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="112f4-364">El [principio de segregación de interfaz (ISP) o encapsulación](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): los escenarios (clases) que dependen de valores de configuración dependen únicamente de los valores de configuración que usen.</span><span class="sxs-lookup"><span data-stu-id="112f4-364">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="112f4-365">[Separación de intereses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): los valores de configuración para distintos elementos de la aplicación no son dependientes entre sí ni están emparejados.</span><span class="sxs-lookup"><span data-stu-id="112f4-365">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="112f4-366">Las opciones también proporcionan un mecanismo para validar los datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="112f4-366">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="112f4-367">Para obtener más información, consulte la sección [Opciones de validación](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="112f4-367">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="112f4-368">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="112f4-368">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="112f4-369">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="112f4-369">Prerequisites</span></span>

<span data-ttu-id="112f4-370">Haga referencia al [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) o agregue una referencia de paquete al paquete [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="112f4-370">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="112f4-371">Interfaces de opciones</span><span class="sxs-lookup"><span data-stu-id="112f4-371">Options interfaces</span></span>

<span data-ttu-id="112f4-372"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> se usa para recuperar las opciones y administrar las notificaciones de las opciones para instancias de `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="112f4-372"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="112f4-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> admite las siguientes situaciones:</span><span class="sxs-lookup"><span data-stu-id="112f4-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="112f4-374">Notificaciones de cambios</span><span class="sxs-lookup"><span data-stu-id="112f4-374">Change notifications</span></span>
* [<span data-ttu-id="112f4-375">Opciones con nombre</span><span class="sxs-lookup"><span data-stu-id="112f4-375">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="112f4-376">Configuración que se puede recargar</span><span class="sxs-lookup"><span data-stu-id="112f4-376">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="112f4-377">Invalidación de opciones de selección (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="112f4-377">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="112f4-378">Los escenarios [posteriores a la configuración](#options-post-configuration) le permiten establecer o cambiar las opciones después de que finalice toda la configuración de <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="112f4-378">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="112f4-379"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> es responsable de crear nuevas instancias de opciones.</span><span class="sxs-lookup"><span data-stu-id="112f4-379"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="112f4-380">Tiene un solo método <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="112f4-380">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="112f4-381">La implementación predeterminada toma todas las instancias registradas de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> y <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>, y establece todas las configuraciones primero, seguidas de las configuraciones posteriores.</span><span class="sxs-lookup"><span data-stu-id="112f4-381">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="112f4-382">Distingue entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> y <xref:Microsoft.Extensions.Options.IConfigureOptions%601>, y solo llama a la interfaz adecuada.</span><span class="sxs-lookup"><span data-stu-id="112f4-382">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="112f4-383"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> se usa por <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para almacenar en caché las instancias de `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="112f4-383"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="112f4-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalida instancias de opciones en la supervisión para que se pueda volver a calcular el valor (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="112f4-384">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="112f4-385">Los valores se pueden introducir manualmente y mediante <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="112f4-385">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="112f4-386">Se usa el método <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> cuando todas las instancias con nombre se deben volver a crear a petición.</span><span class="sxs-lookup"><span data-stu-id="112f4-386">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="112f4-387"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> es útil en escenarios donde se deben volver a calcular las opciones en cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="112f4-387"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="112f4-388">Para obtener más información, consulte la sección [Volver a cargar los datos de configuración con IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="112f4-388">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="112f4-389"><xref:Microsoft.Extensions.Options.IOptions%601> puede utilizarse para admitir las opciones.</span><span class="sxs-lookup"><span data-stu-id="112f4-389"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="112f4-390">Sin embargo, <xref:Microsoft.Extensions.Options.IOptions%601> no es compatible con los escenarios anteriores de <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="112f4-390">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="112f4-391">Aún puede usar <xref:Microsoft.Extensions.Options.IOptions%601> en marcos y bibliotecas existentes que ya usan la interfaz <xref:Microsoft.Extensions.Options.IOptions%601> y no requieren los escenarios proporcionados por <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="112f4-391">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="112f4-392">Configuración de opciones generales</span><span class="sxs-lookup"><span data-stu-id="112f4-392">General options configuration</span></span>

<span data-ttu-id="112f4-393">La configuración de opciones generales se muestra en el ejemplo 1 en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="112f4-393">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="112f4-394">Una clase de opciones debe ser no abstracta con un constructor público sin parámetros.</span><span class="sxs-lookup"><span data-stu-id="112f4-394">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="112f4-395">La siguiente clase, `MyOptions`, tiene dos propiedades: `Option1` y `Option2`.</span><span class="sxs-lookup"><span data-stu-id="112f4-395">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="112f4-396">Configurar los valores predeterminados es opcional, pero el constructor de clases en el ejemplo siguiente establece el valor predeterminado de `Option1`.</span><span class="sxs-lookup"><span data-stu-id="112f4-396">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="112f4-397">`Option2` tiene un valor predeterminado que se establece al inicializar la propiedad directamente (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="112f4-397">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="112f4-398">La clase `MyOptions` se agrega al contenedor de servicios con <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> y se enlaza a la configuración:</span><span class="sxs-lookup"><span data-stu-id="112f4-398">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="112f4-399">El siguiente modelo de página usa la [inserción de dependencias de constructor](xref:mvc/controllers/dependency-injection) con <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para acceder a la configuración (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="112f4-399">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="112f4-400">El archivo *appSettings.json* del ejemplo especifica valores para `option1` y `option2`:</span><span class="sxs-lookup"><span data-stu-id="112f4-400">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="112f4-401">Cuando se ejecuta la aplicación, el método `OnGet` del modelo de página devuelve una cadena que muestra los valores de la clase de opción:</span><span class="sxs-lookup"><span data-stu-id="112f4-401">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="112f4-402">Al usar una instancia de <xref:System.Configuration.ConfigurationBuilder> personalizada para cargar las opciones de configuración desde un archivo de configuración, confirme que la ruta de acceso base esté configurada correctamente:</span><span class="sxs-lookup"><span data-stu-id="112f4-402">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="112f4-403">Al cargar las opciones de configuración desde el archivo de configuración a través de <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, no es necesario establecer de forma explícita la ruta de acceso base.</span><span class="sxs-lookup"><span data-stu-id="112f4-403">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="112f4-404">Configurar opciones simples con un delegado</span><span class="sxs-lookup"><span data-stu-id="112f4-404">Configure simple options with a delegate</span></span>

<span data-ttu-id="112f4-405">La configuración de opciones simples con un delegado se muestra como ejemplo 2 en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="112f4-405">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="112f4-406">Use un delegado para establecer los valores de opciones.</span><span class="sxs-lookup"><span data-stu-id="112f4-406">Use a delegate to set options values.</span></span> <span data-ttu-id="112f4-407">La aplicación de ejemplo usa la clase `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="112f4-407">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="112f4-408">En el código siguiente, un segundo servicio <xref:Microsoft.Extensions.Options.IConfigureOptions%601> se agrega al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="112f4-408">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="112f4-409">Usa un delegado para configurar el enlace con `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="112f4-409">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="112f4-410">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="112f4-410">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="112f4-411">Puede agregar varios proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="112f4-411">You can add multiple configuration providers.</span></span> <span data-ttu-id="112f4-412">Los proveedores de configuración están disponibles en paquetes de NuGet y se aplican en el orden en que están registrados.</span><span class="sxs-lookup"><span data-stu-id="112f4-412">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="112f4-413">Para obtener más información, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="112f4-413">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="112f4-414">Cada llamada a <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> agrega un servicio <xref:Microsoft.Extensions.Options.IConfigureOptions%601> al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="112f4-414">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="112f4-415">En el ejemplo anterior, los valores de `Option1` y `Option2` se especifican en *appSettings.json*, pero los valores de `Option1` y `Option2` se reemplazan por el delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="112f4-415">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="112f4-416">Cuando se habilita más de un servicio de configuración, la última fuente de configuración especificada *gana* y establece el valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="112f4-416">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="112f4-417">Cuando se ejecuta la aplicación, el método `OnGet` del modelo de página devuelve una cadena que muestra los valores de la clase de opción:</span><span class="sxs-lookup"><span data-stu-id="112f4-417">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="112f4-418">Configuración de subopciones</span><span class="sxs-lookup"><span data-stu-id="112f4-418">Suboptions configuration</span></span>

<span data-ttu-id="112f4-419">La configuración de subopciones se muestra en el ejemplo 3 en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="112f4-419">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="112f4-420">Las aplicaciones deben crear clases de opciones que pertenezcan a grupos específicos de escenarios (clases) en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="112f4-420">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="112f4-421">Los elementos de la aplicación que requieran valores de configuración deben acceder solamente a los valores de configuración que usen.</span><span class="sxs-lookup"><span data-stu-id="112f4-421">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="112f4-422">Al enlazar opciones para la configuración, cada propiedad en el tipo de opciones se enlaza a una clave de configuración del formulario `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="112f4-422">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="112f4-423">Por ejemplo, la propiedad `MyOptions.Option1` se enlaza a la clave `Option1`, que se lee desde la propiedad `option1` en *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="112f4-423">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="112f4-424">En el código siguiente, se agrega un tercer servicio <xref:Microsoft.Extensions.Options.IConfigureOptions%601> al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="112f4-424">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="112f4-425">Enlaza `MySubOptions` a la sección `subsection` del archivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="112f4-425">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="112f4-426">El método `GetSection` requiere el espacio de nombres <xref:Microsoft.Extensions.Configuration?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="112f4-426">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="112f4-427">El archivo *appSettings.json* del ejemplo define un miembro `subsection` con las claves para `suboption1` y `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="112f4-427">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="112f4-428">La clase `MySubOptions` define propiedades, `SubOption1` y `SubOption2`, para mantener los valores de opciones (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="112f4-428">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="112f4-429">El método `OnGet` del modelo de página devuelve una cadena con los valores de opciones (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="112f4-429">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="112f4-430">Cuando se ejecuta la aplicación, el método `OnGet` devuelve una cadena que muestra los valores de clase de subopciones:</span><span class="sxs-lookup"><span data-stu-id="112f4-430">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="112f4-431">Opciones proporcionadas por un modelo de vista o con inserción de vista directa</span><span class="sxs-lookup"><span data-stu-id="112f4-431">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="112f4-432">Las opciones proporcionadas por un modelo de vista o de inserción de vista directa se muestran en el ejemplo 4 en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="112f4-432">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="112f4-433">Las opciones se pueden suministrar en un modelo de vista o insertando <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directamente en una vista (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="112f4-433">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="112f4-434">La aplicación de ejemplo muestra cómo insertar `IOptionsMonitor<MyOptions>` con una directiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="112f4-434">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="112f4-435">Cuando se ejecuta la aplicación, se muestran los valores de opciones en la página representada:</span><span class="sxs-lookup"><span data-stu-id="112f4-435">When the app is run, the options values are shown in the rendered page:</span></span>

![Los valores de opciones de Option1: value1_from_json y Option2: -1 se cargan desde el modelo y por inserción en la vista.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="112f4-437">Volver a cargar los datos de configuración con IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="112f4-437">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="112f4-438">El procedimiento de volver a cargar los datos de configuración con <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> se muestra en el ejemplo 5 en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="112f4-438">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="112f4-439"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> admite volver a cargar opciones con la mínima sobrecarga de procesamiento.</span><span class="sxs-lookup"><span data-stu-id="112f4-439"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="112f4-440">Cuando se accede a las opciones y se las almacena en caché durante la vigencia de la solicitud, se calculan una vez por solicitud.</span><span class="sxs-lookup"><span data-stu-id="112f4-440">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="112f4-441">En el ejemplo siguiente se muestra cómo se crea un nuevo <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> después de cambiar el archivo *appSettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="112f4-441">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="112f4-442">Varias solicitudes al servidor devuelven valores constantes proporcionados por el archivo *appSettings.json* hasta que se modifique el archivo y vuelva a cargarse la configuración.</span><span class="sxs-lookup"><span data-stu-id="112f4-442">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="112f4-443">En la siguiente imagen se muestran los valores `option1` y `option2` iniciales cargados desde el archivo *appSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="112f4-443">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="112f4-444">Cambie los valores del archivo *appSettings.json* a `value1_from_json UPDATED` y `200`.</span><span class="sxs-lookup"><span data-stu-id="112f4-444">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="112f4-445">Guarde el archivo *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="112f4-445">Save the *appsettings.json* file.</span></span> <span data-ttu-id="112f4-446">Actualice el explorador para ver qué valores de opciones se han actualizado:</span><span class="sxs-lookup"><span data-stu-id="112f4-446">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="112f4-447">Compatibilidad de opciones con nombre con IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="112f4-447">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="112f4-448">La compatibilidad de opciones con nombre con <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> se muestra en el ejemplo 6 de la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="112f4-448">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="112f4-449">La compatibilidad con las opciones con nombre permite a la aplicación distinguir entre las configuraciones de opciones con nombre.</span><span class="sxs-lookup"><span data-stu-id="112f4-449">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="112f4-450">En la aplicación de ejemplo, las opciones con nombre se declaran con [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), que llama al método de extensión [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="112f4-450">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="112f4-451">Las opciones con nombre distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="112f4-451">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="112f4-452">La aplicación de ejemplo accede a las opciones con nombre con <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="112f4-452">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="112f4-453">Al ejecutar la aplicación de ejemplo, se devuelven las opciones con nombre:</span><span class="sxs-lookup"><span data-stu-id="112f4-453">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="112f4-454">Se proporcionan valores de `named_options_1` a partir de la configuración, que se cargan desde el archivo *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="112f4-454">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="112f4-455">Los valores de `named_options_2` los proporciona:</span><span class="sxs-lookup"><span data-stu-id="112f4-455">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="112f4-456">El delegado `named_options_2` en `ConfigureServices` para `Option1`.</span><span class="sxs-lookup"><span data-stu-id="112f4-456">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="112f4-457">El valor predeterminado para `Option2` proporcionado por la clase `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="112f4-457">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="112f4-458">Configurar todas las opciones con el método ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="112f4-458">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="112f4-459">Configure todas las instancias de opciones con el método <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="112f4-459">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="112f4-460">El siguiente código configura `Option1` para todas las instancias de configuración con un valor común.</span><span class="sxs-lookup"><span data-stu-id="112f4-460">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="112f4-461">Agregue manualmente este código al método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="112f4-461">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="112f4-462">Al ejecutar la aplicación de ejemplo después de agregar el código se produce el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="112f4-462">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="112f4-463">Todas las opciones son instancias con nombre.</span><span class="sxs-lookup"><span data-stu-id="112f4-463">All options are named instances.</span></span> <span data-ttu-id="112f4-464">Las instancias de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existentes se usan para seleccionar como destino la instancia de `Options.DefaultName`, que es `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="112f4-464">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="112f4-465"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> también implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="112f4-465"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="112f4-466">La implementación predeterminada de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> tiene lógica para usar cada una de forma adecuada.</span><span class="sxs-lookup"><span data-stu-id="112f4-466">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="112f4-467">La opción con nombre `null` se usa para seleccionar como destino todas las instancias con nombre, en lugar de una instancia con nombre determinada (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> y <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> usan esta convención).</span><span class="sxs-lookup"><span data-stu-id="112f4-467">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="112f4-468">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="112f4-468">OptionsBuilder API</span></span>

<span data-ttu-id="112f4-469"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> se usa para configurar instancias `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="112f4-469"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="112f4-470">`OptionsBuilder` simplifica la creación de opciones con nombre, ya que es un único parámetro para la llamada `AddOptions<TOptions>(string optionsName)` inicial en lugar de aparecer en todas las llamadas posteriores.</span><span class="sxs-lookup"><span data-stu-id="112f4-470">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="112f4-471">La validación de opciones y las sobrecargas `ConfigureOptions` que aceptan las dependencias de servicio solo están disponibles mediante `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="112f4-471">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="112f4-472">Uso de servicios de DI para configurar opciones</span><span class="sxs-lookup"><span data-stu-id="112f4-472">Use DI services to configure options</span></span>

<span data-ttu-id="112f4-473">Hay dos formas de acceder a otros servicios desde la inserción de dependencias durante la configuración de opciones:</span><span class="sxs-lookup"><span data-stu-id="112f4-473">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="112f4-474">Si se pasa un delegado de configuración a [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) en [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="112f4-474">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="112f4-475">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) proporciona sobrecargas de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) que permiten usar hasta cinco servicios para configurar las opciones:</span><span class="sxs-lookup"><span data-stu-id="112f4-475">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="112f4-476">Si se crea un tipo propio que implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601> o <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, y se registrar como un servicio.</span><span class="sxs-lookup"><span data-stu-id="112f4-476">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="112f4-477">Se recomienda pasar un delegado de configuración a [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), ya que la creación de un servicio es más complicada.</span><span class="sxs-lookup"><span data-stu-id="112f4-477">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="112f4-478">La creación de un tipo propio es equivalente a lo que el marco hace de forma automática cuando se usa [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="112f4-478">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="112f4-479">La llamada a [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registra una interfaz <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> genérica y transitoria, con un constructor que acepta los tipos de servicio genéricos especificados.</span><span class="sxs-lookup"><span data-stu-id="112f4-479">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="112f4-480">Configuración posterior de las opciones</span><span class="sxs-lookup"><span data-stu-id="112f4-480">Options post-configuration</span></span>

<span data-ttu-id="112f4-481">Establezca la configuración posterior con <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="112f4-481">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="112f4-482">La configuración posterior se ejecuta una vez completada toda la configuración de <xref:Microsoft.Extensions.Options.IConfigureOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="112f4-482">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="112f4-483"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> está disponible para configurar posteriormente las opciones con nombre:</span><span class="sxs-lookup"><span data-stu-id="112f4-483"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="112f4-484">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> para configurar posteriormente todas las instancias de configuración:</span><span class="sxs-lookup"><span data-stu-id="112f4-484">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="112f4-485">Acceso a opciones durante el inicio</span><span class="sxs-lookup"><span data-stu-id="112f4-485">Accessing options during startup</span></span>

<span data-ttu-id="112f4-486"><xref:Microsoft.Extensions.Options.IOptions%601> y <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> puede usarse en `Startup.Configure`, ya que los servicios se compilan antes de que se ejecute el método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="112f4-486"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="112f4-487">No use <xref:Microsoft.Extensions.Options.IOptions%601> o <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="112f4-487">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="112f4-488">Puede que exista un estado incoherente de opciones debido al orden de los registros de servicio.</span><span class="sxs-lookup"><span data-stu-id="112f4-488">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="112f4-489">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="112f4-489">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
