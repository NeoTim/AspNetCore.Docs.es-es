---
title: Detección de cambios con tokens de cambio en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo usar tokens de cambio para realizar el seguimiento de los cambios.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
uid: fundamentals/change-tokens
ms.openlocfilehash: bb30d7a4c7dc82200821c60a49c314b246562111
ms.sourcegitcommit: 3d082bd46e9e00a3297ea0314582b1ed2abfa830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2019
ms.locfileid: "72007214"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="b0183-103">Detección de cambios con tokens de cambio en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b0183-103">Detect changes with change tokens in ASP.NET Core</span></span>

<span data-ttu-id="b0183-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b0183-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b0183-105">Un *token de cambio* es un bloque de creación de bajo nivel y uso general que se usa para realizar el seguimiento de los cambios de estado.</span><span class="sxs-lookup"><span data-stu-id="b0183-105">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="b0183-106">[Vea o descargue el código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b0183-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="b0183-107">Interfaz IChangeToken</span><span class="sxs-lookup"><span data-stu-id="b0183-107">IChangeToken interface</span></span>

<span data-ttu-id="b0183-108"><xref:Microsoft.Extensions.Primitives.IChangeToken> propaga notificaciones que indican que se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="b0183-108"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="b0183-109">`IChangeToken` reside en el espacio de nombres <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="b0183-109">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="b0183-110">El paquete de NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) se proporciona implícitamente con las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b0183-110">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="b0183-111">`IChangeToken` tiene dos propiedades:</span><span class="sxs-lookup"><span data-stu-id="b0183-111">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="b0183-112"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indica si el token genera devoluciones de llamada de manera proactiva.</span><span class="sxs-lookup"><span data-stu-id="b0183-112"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="b0183-113">Si `ActiveChangedCallbacks` se establece en `false`, nunca se llama a una devolución de llamada y la aplicación debe sondear `HasChanged` en busca de cambios.</span><span class="sxs-lookup"><span data-stu-id="b0183-113">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="b0183-114">También es posible que un token nunca se cancele si no se producen cambios o si se elimina o deshabilita el agente de escucha de cambios subyacente.</span><span class="sxs-lookup"><span data-stu-id="b0183-114">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="b0183-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> recibe un valor que indica si se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="b0183-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="b0183-116">La interfaz `IChangeToken` incluye el método [RegisterChangeCallback(Acción\<Objeto>, Objeto)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*), que registra una devolución de llamada que se invoca cuando el token ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="b0183-116">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="b0183-117">`HasChanged` se debe establecer antes de que se invoque la devolución de llamada.</span><span class="sxs-lookup"><span data-stu-id="b0183-117">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="b0183-118">Clase ChangeToken</span><span class="sxs-lookup"><span data-stu-id="b0183-118">ChangeToken class</span></span>

<span data-ttu-id="b0183-119"><xref:Microsoft.Extensions.Primitives.ChangeToken> es una clase estática que se usa para propagar notificaciones que indican que se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="b0183-119"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="b0183-120">`ChangeToken` reside en el espacio de nombres <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="b0183-120">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="b0183-121">El paquete de NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) se proporciona implícitamente con las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b0183-121">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="b0183-122">El método [ChangeToken.OnChange(Func\<IChangeToken>, Acción)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) registra una `Action` que se llama cada vez que cambia el token:</span><span class="sxs-lookup"><span data-stu-id="b0183-122">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="b0183-123">`Func<IChangeToken>` genera el token.</span><span class="sxs-lookup"><span data-stu-id="b0183-123">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="b0183-124">Se llama a `Action` cuando cambia el token.</span><span class="sxs-lookup"><span data-stu-id="b0183-124">`Action` is called when the token changes.</span></span>

<span data-ttu-id="b0183-125">La sobrecarga [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Acción\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) toma un parámetro `TState` adicional que se pasa a la `Action` de consumidor de token.</span><span class="sxs-lookup"><span data-stu-id="b0183-125">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="b0183-126">`OnChange` devuelve un valor de <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="b0183-126">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="b0183-127">Al llamar a <xref:System.IDisposable.Dispose*> se detiene la escucha del token de futuras modificaciones y se liberan sus recursos.</span><span class="sxs-lookup"><span data-stu-id="b0183-127">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="b0183-128">Ejemplos de uso de tokens de cambio en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b0183-128">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="b0183-129">Los tokens de cambio se usan en áreas principales de ASP.NET Core para supervisar los cambios en los objetos:</span><span class="sxs-lookup"><span data-stu-id="b0183-129">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="b0183-130">Para supervisar los cambios en los archivos, el método <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> de <xref:Microsoft.Extensions.FileProviders.IFileProvider> crea un `IChangeToken` para los archivos especificados o la carpeta que se va a supervisar.</span><span class="sxs-lookup"><span data-stu-id="b0183-130">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="b0183-131">Se pueden agregar tokens `IChangeToken` a las entradas de caché para desencadenar expulsiones de caché al producirse un cambio.</span><span class="sxs-lookup"><span data-stu-id="b0183-131">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="b0183-132">Para los cambios de `TOptions`, la implementación predeterminada <xref:Microsoft.Extensions.Options.OptionsMonitor`1> de <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> tiene una sobrecarga que acepta una o varias instancias de <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>.</span><span class="sxs-lookup"><span data-stu-id="b0183-132">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="b0183-133">Cada instancia devuelve un `IChangeToken` para registrar una devolución de llamada de notificación de cambio a fin de realizar el seguimiento de los cambios en las opciones.</span><span class="sxs-lookup"><span data-stu-id="b0183-133">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="b0183-134">Supervisión de los cambios de configuración</span><span class="sxs-lookup"><span data-stu-id="b0183-134">Monitor for configuration changes</span></span>

<span data-ttu-id="b0183-135">De forma predeterminada, las plantillas de ASP.NET Core usan [archivos de configuración de JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* y *appsettings.Production.json*) para cargar parámetros de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b0183-135">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="b0183-136">Estos archivos se configuran mediante el método de extensión [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) en <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> que acepta un parámetro `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="b0183-136">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="b0183-137">`reloadOnChange` indica si la configuración se debe recargar en los cambios de archivo.</span><span class="sxs-lookup"><span data-stu-id="b0183-137">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="b0183-138">Esta configuración aparece en el método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> de <xref:Microsoft.Extensions.Hosting.Host>:</span><span class="sxs-lookup"><span data-stu-id="b0183-138">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="b0183-139">La configuración basada en archivo se presenta por medio de <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="b0183-139">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="b0183-140">`FileConfigurationSource` use <xref:Microsoft.Extensions.FileProviders.IFileProvider> para supervisar los archivos.</span><span class="sxs-lookup"><span data-stu-id="b0183-140">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="b0183-141"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> proporciona `IFileMonitor` de manera predeterminada, que usa <xref:System.IO.FileSystemWatcher> para supervisar los cambios del archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="b0183-141">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="b0183-142">En la aplicación de ejemplo se muestran dos implementaciones para supervisar los cambios de configuración.</span><span class="sxs-lookup"><span data-stu-id="b0183-142">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="b0183-143">Si se modifica cualquiera de los archivos *appsettings*, ambas implementaciones de supervisión de los archivos ejecutan un código personalizado&mdash;la aplicación de ejemplo escribe un mensaje en la consola.</span><span class="sxs-lookup"><span data-stu-id="b0183-143">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="b0183-144">El `FileSystemWatcher` de un archivo de configuración puede desencadenar varias devoluciones de llamada de token para un único cambio del archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="b0183-144">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="b0183-145">Para garantizar que el código personalizado se ejecute solo una vez cuando se desencadenan varias devoluciones de llamada de token, la implementación del ejemplo comprueba los hashes de archivo.</span><span class="sxs-lookup"><span data-stu-id="b0183-145">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="b0183-146">El ejemplo usa el algoritmo hash seguro 1.</span><span class="sxs-lookup"><span data-stu-id="b0183-146">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="b0183-147">Se implementa un reintento con una interrupción exponencial.</span><span class="sxs-lookup"><span data-stu-id="b0183-147">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="b0183-148">El reintento aparece porque se puede producir un bloqueo de archivos que impida temporalmente calcular un hash nuevo en un archivo.</span><span class="sxs-lookup"><span data-stu-id="b0183-148">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="b0183-149">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0183-149">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="b0183-150">Token de cambio de inicio simple</span><span class="sxs-lookup"><span data-stu-id="b0183-150">Simple startup change token</span></span>

<span data-ttu-id="b0183-151">Registre una devolución de llamada de `Action` del consumidor de token para las notificaciones de cambio en el token de recarga de configuración.</span><span class="sxs-lookup"><span data-stu-id="b0183-151">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="b0183-152">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b0183-152">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="b0183-153">`config.GetReloadToken()` proporciona el token.</span><span class="sxs-lookup"><span data-stu-id="b0183-153">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="b0183-154">La devolución de llamada es el método `InvokeChanged`:</span><span class="sxs-lookup"><span data-stu-id="b0183-154">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="b0183-155">El `state` de la devolución de llamada se usa para pasar el `IWebHostEnvironment`, que resulta útil para especificar el archivo de configuración *appsettings* que se va a supervisar (por ejemplo, *appsettings.Development.json* cuando se está en el entorno de desarrollo).</span><span class="sxs-lookup"><span data-stu-id="b0183-155">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="b0183-156">Se usa el hash de archivo para evitar que se ejecute varias veces la instrucción `WriteConsole` debido a varias devoluciones de llamada de token cuando el archivo de configuración solo ha cambiado una vez.</span><span class="sxs-lookup"><span data-stu-id="b0183-156">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="b0183-157">Este sistema se ejecuta siempre que la aplicación esté en ejecución y el usuario no lo puede deshabilitar.</span><span class="sxs-lookup"><span data-stu-id="b0183-157">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="b0183-158">Supervisión de los cambios de configuración como servicio</span><span class="sxs-lookup"><span data-stu-id="b0183-158">Monitor configuration changes as a service</span></span>

<span data-ttu-id="b0183-159">En el ejemplo se implementa lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b0183-159">The sample implements:</span></span>

* <span data-ttu-id="b0183-160">La supervisión del token de inicio básico.</span><span class="sxs-lookup"><span data-stu-id="b0183-160">Basic startup token monitoring.</span></span>
* <span data-ttu-id="b0183-161">La supervisión como servicio.</span><span class="sxs-lookup"><span data-stu-id="b0183-161">Monitoring as a service.</span></span>
* <span data-ttu-id="b0183-162">Un mecanismo para habilitar y deshabilitar la supervisión.</span><span class="sxs-lookup"><span data-stu-id="b0183-162">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="b0183-163">El ejemplo establece una interfaz `IConfigurationMonitor`.</span><span class="sxs-lookup"><span data-stu-id="b0183-163">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="b0183-164">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0183-164">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="b0183-165">El constructor de la clase implementada, `ConfigurationMonitor`, registra una devolución de llamada para las notificaciones de cambio:</span><span class="sxs-lookup"><span data-stu-id="b0183-165">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="b0183-166">`config.GetReloadToken()` proporciona el token.</span><span class="sxs-lookup"><span data-stu-id="b0183-166">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="b0183-167">`InvokeChanged` es el método de devolución de llamada.</span><span class="sxs-lookup"><span data-stu-id="b0183-167">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="b0183-168">El elemento `state` de esta instancia es una referencia a la instancia de `IConfigurationMonitor` que se usa para tener acceso al estado de supervisión.</span><span class="sxs-lookup"><span data-stu-id="b0183-168">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="b0183-169">Se usan dos propiedades:</span><span class="sxs-lookup"><span data-stu-id="b0183-169">Two properties are used:</span></span>

* <span data-ttu-id="b0183-170">`MonitoringEnabled` &ndash; indica si la devolución de llamada debe ejecutar su código personalizado.</span><span class="sxs-lookup"><span data-stu-id="b0183-170">`MonitoringEnabled` &ndash; Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="b0183-171">`CurrentState` &ndash; describe el estado de supervisión actual para su uso en la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="b0183-171">`CurrentState` &ndash; Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="b0183-172">El método `InvokeChanged` es similar al enfoque anterior, excepto en que:</span><span class="sxs-lookup"><span data-stu-id="b0183-172">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="b0183-173">No ejecuta su código, a menos que `MonitoringEnabled` sea `true`.</span><span class="sxs-lookup"><span data-stu-id="b0183-173">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="b0183-174">Genera el `state` actual en su salida de `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="b0183-174">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="b0183-175">Una instancia de `ConfigurationMonitor` se registra como servicio en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b0183-175">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="b0183-176">En la página Index se ofrece al usuario el control sobre la supervisión de la configuración.</span><span class="sxs-lookup"><span data-stu-id="b0183-176">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="b0183-177">La instancia de `IConfigurationMonitor` se inserta en `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="b0183-177">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="b0183-178">*Páginas/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0183-178">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="b0183-179">El monitor de configuración (`_monitor`) se usa para habilitar o deshabilitar la supervisión y establecer el estado actual de los comentarios de la interfaz de usuario:</span><span class="sxs-lookup"><span data-stu-id="b0183-179">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="b0183-180">Cuando se desencadena `OnPostStartMonitoring`, se habilita la supervisión y se borra el estado actual.</span><span class="sxs-lookup"><span data-stu-id="b0183-180">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="b0183-181">Cuando se desencadena `OnPostStopMonitoring`, se deshabilita la supervisión y se establece el estado para reflejar que no se está realizando la supervisión.</span><span class="sxs-lookup"><span data-stu-id="b0183-181">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="b0183-182">Los botones de la interfaz de usuario habilitan y deshabilitan la supervisión.</span><span class="sxs-lookup"><span data-stu-id="b0183-182">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="b0183-183">*Páginas/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b0183-183">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="b0183-184">Supervisión de los cambios de archivos en caché</span><span class="sxs-lookup"><span data-stu-id="b0183-184">Monitor cached file changes</span></span>

<span data-ttu-id="b0183-185">El contenido de los archivos se puede almacenar en caché en memoria mediante <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="b0183-185">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="b0183-186">El almacenamiento en caché en memoria se describe en el tema [Cache in-memory](xref:performance/caching/memory) (Almacenamiento en caché en memoria).</span><span class="sxs-lookup"><span data-stu-id="b0183-186">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="b0183-187">Sin realizar pasos adicionales, como la implementación que se describe a continuación, si los datos de origen cambian, se devuelven datos *obsoletos* (no actualizados) de la caché.</span><span class="sxs-lookup"><span data-stu-id="b0183-187">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="b0183-188">Por ejemplo, si no se tiene en cuenta el estado de un archivo de origen en caché cuando se renueva un período de [vencimiento variable](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration), se pueden crear datos de archivo en caché obsoletos.</span><span class="sxs-lookup"><span data-stu-id="b0183-188">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="b0183-189">En cada solicitud de los datos se renueva el período de vencimiento variable, pero el archivo nunca se vuelve a cargar en la caché.</span><span class="sxs-lookup"><span data-stu-id="b0183-189">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="b0183-190">Las características de la aplicación que usen el contenido en caché del archivo están sujetas a la posible recepción de contenido obsoleto.</span><span class="sxs-lookup"><span data-stu-id="b0183-190">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="b0183-191">El uso de tokens de cambio en un escenario de almacenamiento en caché de archivos evita la presencia de contenido de archivos obsoletos en la caché.</span><span class="sxs-lookup"><span data-stu-id="b0183-191">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="b0183-192">En la aplicación de ejemplo se muestra una implementación del enfoque.</span><span class="sxs-lookup"><span data-stu-id="b0183-192">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="b0183-193">En el ejemplo se usa `GetFileContent` para:</span><span class="sxs-lookup"><span data-stu-id="b0183-193">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="b0183-194">Devolver el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="b0183-194">Return file content.</span></span>
* <span data-ttu-id="b0183-195">Implementar un algoritmo de reintento con interrupción exponencial para casos en los que un bloqueo de archivo impide temporalmente leer un archivo.</span><span class="sxs-lookup"><span data-stu-id="b0183-195">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="b0183-196">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0183-196">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="b0183-197">Se crea un `FileService` para administrar las búsquedas de archivos en caché.</span><span class="sxs-lookup"><span data-stu-id="b0183-197">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="b0183-198">La llamada al método `GetFileContent` del servicio intenta obtener el contenido de archivo de la caché en memoria y devolverlo al autor de la llamada (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="b0183-198">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="b0183-199">Si el contenido en caché no se encuentra mediante la clave de caché, se realizan las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="b0183-199">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="b0183-200">El contenido del archivo se obtiene mediante `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="b0183-200">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="b0183-201">Se obtiene un token de cambio del proveedor de archivos con [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="b0183-201">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="b0183-202">La devolución de llamada del token se desencadena cuando se modifica el archivo.</span><span class="sxs-lookup"><span data-stu-id="b0183-202">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="b0183-203">El contenido del archivo se almacena en caché con un período de [vencimiento variable](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration).</span><span class="sxs-lookup"><span data-stu-id="b0183-203">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="b0183-204">El token de cambio se adjunta con [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) para expulsar la entrada de caché si el archivo cambia mientras está almacenado en caché.</span><span class="sxs-lookup"><span data-stu-id="b0183-204">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="b0183-205">En el ejemplo siguiente, los archivos se almacenan en la [raíz del contenido](xref:fundamentals/index#content-root) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b0183-205">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="b0183-206">`IWebHostEnvironment.ContentRootFileProvider` se usa para obtener un <xref:Microsoft.Extensions.FileProviders.IFileProvider> que apunte a `IWebHostEnvironment.ContentRootPath` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b0183-206">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="b0183-207">La `filePath` se obtiene con [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="b0183-207">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="b0183-208">El `FileService` se registra en el contenedor de servicios junto con el servicio de almacenamiento en caché.</span><span class="sxs-lookup"><span data-stu-id="b0183-208">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="b0183-209">En `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b0183-209">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="b0183-210">El modelo de página carga el contenido del archivo mediante el servicio.</span><span class="sxs-lookup"><span data-stu-id="b0183-210">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="b0183-211">En el método `OnGet` de la página de índice (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0183-211">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="b0183-212">Clase CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="b0183-212">CompositeChangeToken class</span></span>

<span data-ttu-id="b0183-213">Para representar una o varias instancias de `IChangeToken` en un solo objeto, use la clase <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="b0183-213">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="b0183-214">En el token compuesto, `HasChanged` notifica `true` si algún token representado `HasChanged` es `true`.</span><span class="sxs-lookup"><span data-stu-id="b0183-214">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="b0183-215">En el token compuesto, `ActiveChangeCallbacks` notifica `true` si algún token representado `ActiveChangeCallbacks` es `true`.</span><span class="sxs-lookup"><span data-stu-id="b0183-215">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="b0183-216">Si se producen varios eventos de cambio simultáneos, la devolución de llamada de cambio compuesto se invoca una vez.</span><span class="sxs-lookup"><span data-stu-id="b0183-216">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b0183-217">Un *token de cambio* es un bloque de creación de bajo nivel y uso general que se usa para realizar el seguimiento de los cambios de estado.</span><span class="sxs-lookup"><span data-stu-id="b0183-217">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="b0183-218">[Vea o descargue el código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b0183-218">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="b0183-219">Interfaz IChangeToken</span><span class="sxs-lookup"><span data-stu-id="b0183-219">IChangeToken interface</span></span>

<span data-ttu-id="b0183-220"><xref:Microsoft.Extensions.Primitives.IChangeToken> propaga notificaciones que indican que se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="b0183-220"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="b0183-221">`IChangeToken` reside en el espacio de nombres <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="b0183-221">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="b0183-222">En el caso de las aplicaciones que no usan el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), cree una referencia al paquete NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="b0183-222">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="b0183-223">`IChangeToken` tiene dos propiedades:</span><span class="sxs-lookup"><span data-stu-id="b0183-223">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="b0183-224"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indica si el token genera devoluciones de llamada de manera proactiva.</span><span class="sxs-lookup"><span data-stu-id="b0183-224"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="b0183-225">Si `ActiveChangedCallbacks` se establece en `false`, nunca se llama a una devolución de llamada y la aplicación debe sondear `HasChanged` en busca de cambios.</span><span class="sxs-lookup"><span data-stu-id="b0183-225">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="b0183-226">También es posible que un token nunca se cancele si no se producen cambios o si se elimina o deshabilita el agente de escucha de cambios subyacente.</span><span class="sxs-lookup"><span data-stu-id="b0183-226">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="b0183-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> recibe un valor que indica si se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="b0183-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="b0183-228">La interfaz `IChangeToken` incluye el método [RegisterChangeCallback(Acción\<Objeto>, Objeto)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*), que registra una devolución de llamada que se invoca cuando el token ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="b0183-228">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="b0183-229">`HasChanged` se debe establecer antes de que se invoque la devolución de llamada.</span><span class="sxs-lookup"><span data-stu-id="b0183-229">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="b0183-230">Clase ChangeToken</span><span class="sxs-lookup"><span data-stu-id="b0183-230">ChangeToken class</span></span>

<span data-ttu-id="b0183-231"><xref:Microsoft.Extensions.Primitives.ChangeToken> es una clase estática que se usa para propagar notificaciones que indican que se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="b0183-231"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="b0183-232">`ChangeToken` reside en el espacio de nombres <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="b0183-232">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="b0183-233">En el caso de las aplicaciones que no usan el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), cree una referencia al paquete NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="b0183-233">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="b0183-234">El método [ChangeToken.OnChange(Func\<IChangeToken>, Acción)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) registra una `Action` que se llama cada vez que cambia el token:</span><span class="sxs-lookup"><span data-stu-id="b0183-234">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="b0183-235">`Func<IChangeToken>` genera el token.</span><span class="sxs-lookup"><span data-stu-id="b0183-235">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="b0183-236">Se llama a `Action` cuando cambia el token.</span><span class="sxs-lookup"><span data-stu-id="b0183-236">`Action` is called when the token changes.</span></span>

<span data-ttu-id="b0183-237">La sobrecarga [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Acción\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) toma un parámetro `TState` adicional que se pasa a la `Action` de consumidor de token.</span><span class="sxs-lookup"><span data-stu-id="b0183-237">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="b0183-238">`OnChange` devuelve un valor de <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="b0183-238">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="b0183-239">Al llamar a <xref:System.IDisposable.Dispose*> se detiene la escucha del token de futuras modificaciones y se liberan sus recursos.</span><span class="sxs-lookup"><span data-stu-id="b0183-239">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="b0183-240">Ejemplos de uso de tokens de cambio en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b0183-240">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="b0183-241">Los tokens de cambio se usan en áreas principales de ASP.NET Core para supervisar los cambios en los objetos:</span><span class="sxs-lookup"><span data-stu-id="b0183-241">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="b0183-242">Para supervisar los cambios en los archivos, el método <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> de <xref:Microsoft.Extensions.FileProviders.IFileProvider> crea un `IChangeToken` para los archivos especificados o la carpeta que se va a supervisar.</span><span class="sxs-lookup"><span data-stu-id="b0183-242">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="b0183-243">Se pueden agregar tokens `IChangeToken` a las entradas de caché para desencadenar expulsiones de caché al producirse un cambio.</span><span class="sxs-lookup"><span data-stu-id="b0183-243">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="b0183-244">Para los cambios de `TOptions`, la implementación predeterminada <xref:Microsoft.Extensions.Options.OptionsMonitor`1> de <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> tiene una sobrecarga que acepta una o varias instancias de <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>.</span><span class="sxs-lookup"><span data-stu-id="b0183-244">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="b0183-245">Cada instancia devuelve un `IChangeToken` para registrar una devolución de llamada de notificación de cambio a fin de realizar el seguimiento de los cambios en las opciones.</span><span class="sxs-lookup"><span data-stu-id="b0183-245">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="b0183-246">Supervisión de los cambios de configuración</span><span class="sxs-lookup"><span data-stu-id="b0183-246">Monitor for configuration changes</span></span>

<span data-ttu-id="b0183-247">De forma predeterminada, las plantillas de ASP.NET Core usan [archivos de configuración de JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* y *appsettings.Production.json*) para cargar parámetros de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b0183-247">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="b0183-248">Estos archivos se configuran mediante el método de extensión [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) en <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> que acepta un parámetro `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="b0183-248">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="b0183-249">`reloadOnChange` indica si la configuración se debe recargar en los cambios de archivo.</span><span class="sxs-lookup"><span data-stu-id="b0183-249">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="b0183-250">Esta configuración aparece en el método <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> de <xref:Microsoft.AspNetCore.WebHost>:</span><span class="sxs-lookup"><span data-stu-id="b0183-250">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="b0183-251">La configuración basada en archivo se presenta por medio de <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="b0183-251">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="b0183-252">`FileConfigurationSource` use <xref:Microsoft.Extensions.FileProviders.IFileProvider> para supervisar los archivos.</span><span class="sxs-lookup"><span data-stu-id="b0183-252">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="b0183-253"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> proporciona `IFileMonitor` de manera predeterminada, que usa <xref:System.IO.FileSystemWatcher> para supervisar los cambios del archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="b0183-253">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="b0183-254">En la aplicación de ejemplo se muestran dos implementaciones para supervisar los cambios de configuración.</span><span class="sxs-lookup"><span data-stu-id="b0183-254">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="b0183-255">Si se modifica cualquiera de los archivos *appsettings*, ambas implementaciones de supervisión de los archivos ejecutan un código personalizado&mdash;la aplicación de ejemplo escribe un mensaje en la consola.</span><span class="sxs-lookup"><span data-stu-id="b0183-255">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="b0183-256">El `FileSystemWatcher` de un archivo de configuración puede desencadenar varias devoluciones de llamada de token para un único cambio del archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="b0183-256">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="b0183-257">Para garantizar que el código personalizado se ejecute solo una vez cuando se desencadenan varias devoluciones de llamada de token, la implementación del ejemplo comprueba los hashes de archivo.</span><span class="sxs-lookup"><span data-stu-id="b0183-257">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="b0183-258">El ejemplo usa el algoritmo hash seguro 1.</span><span class="sxs-lookup"><span data-stu-id="b0183-258">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="b0183-259">Se implementa un reintento con una interrupción exponencial.</span><span class="sxs-lookup"><span data-stu-id="b0183-259">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="b0183-260">El reintento aparece porque se puede producir un bloqueo de archivos que impida temporalmente calcular un hash nuevo en un archivo.</span><span class="sxs-lookup"><span data-stu-id="b0183-260">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="b0183-261">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0183-261">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="b0183-262">Token de cambio de inicio simple</span><span class="sxs-lookup"><span data-stu-id="b0183-262">Simple startup change token</span></span>

<span data-ttu-id="b0183-263">Registre una devolución de llamada de `Action` del consumidor de token para las notificaciones de cambio en el token de recarga de configuración.</span><span class="sxs-lookup"><span data-stu-id="b0183-263">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="b0183-264">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b0183-264">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="b0183-265">`config.GetReloadToken()` proporciona el token.</span><span class="sxs-lookup"><span data-stu-id="b0183-265">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="b0183-266">La devolución de llamada es el método `InvokeChanged`:</span><span class="sxs-lookup"><span data-stu-id="b0183-266">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="b0183-267">El `state` de la devolución de llamada se usa para pasar el `IHostingEnvironment`, que resulta útil para especificar el archivo de configuración *appsettings* que se va a supervisar (por ejemplo, *appsettings.Development.json* cuando se está en el entorno de desarrollo).</span><span class="sxs-lookup"><span data-stu-id="b0183-267">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="b0183-268">Se usa el hash de archivo para evitar que se ejecute varias veces la instrucción `WriteConsole` debido a varias devoluciones de llamada de token cuando el archivo de configuración solo ha cambiado una vez.</span><span class="sxs-lookup"><span data-stu-id="b0183-268">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="b0183-269">Este sistema se ejecuta siempre que la aplicación esté en ejecución y el usuario no lo puede deshabilitar.</span><span class="sxs-lookup"><span data-stu-id="b0183-269">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="b0183-270">Supervisión de los cambios de configuración como servicio</span><span class="sxs-lookup"><span data-stu-id="b0183-270">Monitor configuration changes as a service</span></span>

<span data-ttu-id="b0183-271">En el ejemplo se implementa lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b0183-271">The sample implements:</span></span>

* <span data-ttu-id="b0183-272">La supervisión del token de inicio básico.</span><span class="sxs-lookup"><span data-stu-id="b0183-272">Basic startup token monitoring.</span></span>
* <span data-ttu-id="b0183-273">La supervisión como servicio.</span><span class="sxs-lookup"><span data-stu-id="b0183-273">Monitoring as a service.</span></span>
* <span data-ttu-id="b0183-274">Un mecanismo para habilitar y deshabilitar la supervisión.</span><span class="sxs-lookup"><span data-stu-id="b0183-274">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="b0183-275">El ejemplo establece una interfaz `IConfigurationMonitor`.</span><span class="sxs-lookup"><span data-stu-id="b0183-275">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="b0183-276">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0183-276">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="b0183-277">El constructor de la clase implementada, `ConfigurationMonitor`, registra una devolución de llamada para las notificaciones de cambio:</span><span class="sxs-lookup"><span data-stu-id="b0183-277">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="b0183-278">`config.GetReloadToken()` proporciona el token.</span><span class="sxs-lookup"><span data-stu-id="b0183-278">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="b0183-279">`InvokeChanged` es el método de devolución de llamada.</span><span class="sxs-lookup"><span data-stu-id="b0183-279">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="b0183-280">El elemento `state` de esta instancia es una referencia a la instancia de `IConfigurationMonitor` que se usa para tener acceso al estado de supervisión.</span><span class="sxs-lookup"><span data-stu-id="b0183-280">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="b0183-281">Se usan dos propiedades:</span><span class="sxs-lookup"><span data-stu-id="b0183-281">Two properties are used:</span></span>

* <span data-ttu-id="b0183-282">`MonitoringEnabled` &ndash; indica si la devolución de llamada debe ejecutar su código personalizado.</span><span class="sxs-lookup"><span data-stu-id="b0183-282">`MonitoringEnabled` &ndash; Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="b0183-283">`CurrentState` &ndash; describe el estado de supervisión actual para su uso en la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="b0183-283">`CurrentState` &ndash; Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="b0183-284">El método `InvokeChanged` es similar al enfoque anterior, excepto en que:</span><span class="sxs-lookup"><span data-stu-id="b0183-284">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="b0183-285">No ejecuta su código, a menos que `MonitoringEnabled` sea `true`.</span><span class="sxs-lookup"><span data-stu-id="b0183-285">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="b0183-286">Genera el `state` actual en su salida de `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="b0183-286">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="b0183-287">Una instancia de `ConfigurationMonitor` se registra como servicio en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b0183-287">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="b0183-288">En la página Index se ofrece al usuario el control sobre la supervisión de la configuración.</span><span class="sxs-lookup"><span data-stu-id="b0183-288">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="b0183-289">La instancia de `IConfigurationMonitor` se inserta en `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="b0183-289">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="b0183-290">*Páginas/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0183-290">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="b0183-291">El monitor de configuración (`_monitor`) se usa para habilitar o deshabilitar la supervisión y establecer el estado actual de los comentarios de la interfaz de usuario:</span><span class="sxs-lookup"><span data-stu-id="b0183-291">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="b0183-292">Cuando se desencadena `OnPostStartMonitoring`, se habilita la supervisión y se borra el estado actual.</span><span class="sxs-lookup"><span data-stu-id="b0183-292">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="b0183-293">Cuando se desencadena `OnPostStopMonitoring`, se deshabilita la supervisión y se establece el estado para reflejar que no se está realizando la supervisión.</span><span class="sxs-lookup"><span data-stu-id="b0183-293">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="b0183-294">Los botones de la interfaz de usuario habilitan y deshabilitan la supervisión.</span><span class="sxs-lookup"><span data-stu-id="b0183-294">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="b0183-295">*Páginas/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b0183-295">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="b0183-296">Supervisión de los cambios de archivos en caché</span><span class="sxs-lookup"><span data-stu-id="b0183-296">Monitor cached file changes</span></span>

<span data-ttu-id="b0183-297">El contenido de los archivos se puede almacenar en caché en memoria mediante <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="b0183-297">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="b0183-298">El almacenamiento en caché en memoria se describe en el tema [Cache in-memory](xref:performance/caching/memory) (Almacenamiento en caché en memoria).</span><span class="sxs-lookup"><span data-stu-id="b0183-298">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="b0183-299">Sin realizar pasos adicionales, como la implementación que se describe a continuación, si los datos de origen cambian, se devuelven datos *obsoletos* (no actualizados) de la caché.</span><span class="sxs-lookup"><span data-stu-id="b0183-299">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="b0183-300">Por ejemplo, si no se tiene en cuenta el estado de un archivo de origen en caché cuando se renueva un período de [vencimiento variable](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration), se pueden crear datos de archivo en caché obsoletos.</span><span class="sxs-lookup"><span data-stu-id="b0183-300">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="b0183-301">En cada solicitud de los datos se renueva el período de vencimiento variable, pero el archivo nunca se vuelve a cargar en la caché.</span><span class="sxs-lookup"><span data-stu-id="b0183-301">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="b0183-302">Las características de la aplicación que usen el contenido en caché del archivo están sujetas a la posible recepción de contenido obsoleto.</span><span class="sxs-lookup"><span data-stu-id="b0183-302">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="b0183-303">El uso de tokens de cambio en un escenario de almacenamiento en caché de archivos evita la presencia de contenido de archivos obsoletos en la caché.</span><span class="sxs-lookup"><span data-stu-id="b0183-303">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="b0183-304">En la aplicación de ejemplo se muestra una implementación del enfoque.</span><span class="sxs-lookup"><span data-stu-id="b0183-304">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="b0183-305">En el ejemplo se usa `GetFileContent` para:</span><span class="sxs-lookup"><span data-stu-id="b0183-305">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="b0183-306">Devolver el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="b0183-306">Return file content.</span></span>
* <span data-ttu-id="b0183-307">Implementar un algoritmo de reintento con interrupción exponencial para casos en los que un bloqueo de archivo impide temporalmente leer un archivo.</span><span class="sxs-lookup"><span data-stu-id="b0183-307">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="b0183-308">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0183-308">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="b0183-309">Se crea un `FileService` para administrar las búsquedas de archivos en caché.</span><span class="sxs-lookup"><span data-stu-id="b0183-309">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="b0183-310">La llamada al método `GetFileContent` del servicio intenta obtener el contenido de archivo de la caché en memoria y devolverlo al autor de la llamada (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="b0183-310">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="b0183-311">Si el contenido en caché no se encuentra mediante la clave de caché, se realizan las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="b0183-311">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="b0183-312">El contenido del archivo se obtiene mediante `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="b0183-312">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="b0183-313">Se obtiene un token de cambio del proveedor de archivos con [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="b0183-313">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="b0183-314">La devolución de llamada del token se desencadena cuando se modifica el archivo.</span><span class="sxs-lookup"><span data-stu-id="b0183-314">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="b0183-315">El contenido del archivo se almacena en caché con un período de [vencimiento variable](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration).</span><span class="sxs-lookup"><span data-stu-id="b0183-315">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="b0183-316">El token de cambio se adjunta con [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) para expulsar la entrada de caché si el archivo cambia mientras está almacenado en caché.</span><span class="sxs-lookup"><span data-stu-id="b0183-316">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="b0183-317">En el ejemplo siguiente, los archivos se almacenan en la [raíz del contenido](xref:fundamentals/index#content-root) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b0183-317">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="b0183-318">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) se usa para obtener un elemento <xref:Microsoft.Extensions.FileProviders.IFileProvider> que apunta a la <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b0183-318">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="b0183-319">La `filePath` se obtiene con [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="b0183-319">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="b0183-320">El `FileService` se registra en el contenedor de servicios junto con el servicio de almacenamiento en caché.</span><span class="sxs-lookup"><span data-stu-id="b0183-320">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="b0183-321">En `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b0183-321">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="b0183-322">El modelo de página carga el contenido del archivo mediante el servicio.</span><span class="sxs-lookup"><span data-stu-id="b0183-322">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="b0183-323">En el método `OnGet` de la página de índice (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0183-323">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="b0183-324">Clase CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="b0183-324">CompositeChangeToken class</span></span>

<span data-ttu-id="b0183-325">Para representar una o varias instancias de `IChangeToken` en un solo objeto, use la clase <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="b0183-325">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="b0183-326">En el token compuesto, `HasChanged` notifica `true` si algún token representado `HasChanged` es `true`.</span><span class="sxs-lookup"><span data-stu-id="b0183-326">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="b0183-327">En el token compuesto, `ActiveChangeCallbacks` notifica `true` si algún token representado `ActiveChangeCallbacks` es `true`.</span><span class="sxs-lookup"><span data-stu-id="b0183-327">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="b0183-328">Si se producen varios eventos de cambio simultáneos, la devolución de llamada de cambio compuesto se invoca una vez.</span><span class="sxs-lookup"><span data-stu-id="b0183-328">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b0183-329">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="b0183-329">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
