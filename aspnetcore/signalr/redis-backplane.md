---
title: Backplane de Redis para SignalR el escalado horizontal de ASP.net Core
author: bradygaster
description: Aprenda a configurar un backplane de Redis para habilitar el escalado horizontal de una SignalR aplicación ASP.net Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/redis-backplane
ms.openlocfilehash: 58c1ff2c9334e75535f6e5f0f418976176822724
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408479"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-signalr-scale-out"></a><span data-ttu-id="b7e93-103">Configuración de un backplane de Redis para ASP.NET Core SignalR la escalabilidad horizontal</span><span class="sxs-lookup"><span data-stu-id="b7e93-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="b7e93-104">Por [Andrew Stanton-enfermera](https://twitter.com/anurse), [Brady transgastor](https://twitter.com/bradygaster)y [Tom Dykstra](https://github.com/tdykstra),</span><span class="sxs-lookup"><span data-stu-id="b7e93-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="b7e93-105">En este artículo se explican los SignalR aspectos específicos de la configuración de un servidor de [Redis](https://redis.io/) para usar para escalar horizontalmente una SignalR aplicación ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="b7e93-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="b7e93-106">Configuración de un backplane de Redis</span><span class="sxs-lookup"><span data-stu-id="b7e93-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="b7e93-107">Implementar un servidor de Redis.</span><span class="sxs-lookup"><span data-stu-id="b7e93-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="b7e93-108">Para su uso en producción, se recomienda un backplane de Redis solo cuando se ejecuta en el mismo centro de datos que la SignalR aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7e93-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="b7e93-109">De lo contrario, la latencia de red degrada el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="b7e93-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="b7e93-110">Si la SignalR aplicación se ejecuta en la nube de Azure, se recomienda el SignalR servicio de Azure en lugar de un backplane de Redis.</span><span class="sxs-lookup"><span data-stu-id="b7e93-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="b7e93-111">Puede usar el servicio de Azure Redis Cache para entornos de desarrollo y pruebas.</span><span class="sxs-lookup"><span data-stu-id="b7e93-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="b7e93-112">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="b7e93-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="b7e93-113">Documentación de Redis</span><span class="sxs-lookup"><span data-stu-id="b7e93-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="b7e93-114">Documentación de Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="b7e93-114">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="b7e93-115">En la SignalR aplicación, instale el `Microsoft.AspNetCore.SignalR.Redis` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="b7e93-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span>
* <span data-ttu-id="b7e93-116">En el `Startup.ConfigureServices` método, llame a `AddRedis` después de `AddSignalR` :</span><span class="sxs-lookup"><span data-stu-id="b7e93-116">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="b7e93-117">Configure las opciones según sea necesario:</span><span class="sxs-lookup"><span data-stu-id="b7e93-117">Configure options as needed:</span></span>
 
  <span data-ttu-id="b7e93-118">La mayoría de las opciones se pueden establecer en la cadena de conexión o en el objeto [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="b7e93-118">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="b7e93-119">Las opciones especificadas en `ConfigurationOptions` invalidan las definidas en la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="b7e93-119">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="b7e93-120">En el ejemplo siguiente se muestra cómo establecer opciones en el `ConfigurationOptions` objeto.</span><span class="sxs-lookup"><span data-stu-id="b7e93-120">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="b7e93-121">En este ejemplo se agrega un prefijo de canal para que varias aplicaciones puedan compartir la misma instancia de Redis, tal y como se explica en el paso siguiente.</span><span class="sxs-lookup"><span data-stu-id="b7e93-121">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="b7e93-122">En el código anterior, `options.Configuration` se inicializa con lo que se haya especificado en la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="b7e93-122">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="b7e93-123">En la SignalR aplicación, instale uno de los siguientes paquetes NuGet:</span><span class="sxs-lookup"><span data-stu-id="b7e93-123">In the SignalR app, install one of the following NuGet packages:</span></span>

  * <span data-ttu-id="b7e93-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis`-Depende de StackExchange. Redis 2. X.X.</span><span class="sxs-lookup"><span data-stu-id="b7e93-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` - Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="b7e93-125">Este es el paquete recomendado para ASP.NET Core 2,2 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="b7e93-125">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * <span data-ttu-id="b7e93-126">`Microsoft.AspNetCore.SignalR.Redis`-Depende de StackExchange. Redis 1. X.X.</span><span class="sxs-lookup"><span data-stu-id="b7e93-126">`Microsoft.AspNetCore.SignalR.Redis` - Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="b7e93-127">Este paquete no está incluido en ASP.NET Core 3,0 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="b7e93-127">This package isn't included in ASP.NET Core 3.0 and later.</span></span>

* <span data-ttu-id="b7e93-128">En el `Startup.ConfigureServices` método, llame a <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> :</span><span class="sxs-lookup"><span data-stu-id="b7e93-128">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 <span data-ttu-id="b7e93-129">Al utilizar `Microsoft.AspNetCore.SignalR.Redis` , llame a <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> .</span><span class="sxs-lookup"><span data-stu-id="b7e93-129">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

* <span data-ttu-id="b7e93-130">Configure las opciones según sea necesario:</span><span class="sxs-lookup"><span data-stu-id="b7e93-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="b7e93-131">La mayoría de las opciones se pueden establecer en la cadena de conexión o en el objeto [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="b7e93-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="b7e93-132">Las opciones especificadas en `ConfigurationOptions` invalidan las definidas en la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="b7e93-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="b7e93-133">En el ejemplo siguiente se muestra cómo establecer opciones en el `ConfigurationOptions` objeto.</span><span class="sxs-lookup"><span data-stu-id="b7e93-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="b7e93-134">En este ejemplo se agrega un prefijo de canal para que varias aplicaciones puedan compartir la misma instancia de Redis, tal y como se explica en el paso siguiente.</span><span class="sxs-lookup"><span data-stu-id="b7e93-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 <span data-ttu-id="b7e93-135">Al utilizar `Microsoft.AspNetCore.SignalR.Redis` , llame a <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> .</span><span class="sxs-lookup"><span data-stu-id="b7e93-135">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

  <span data-ttu-id="b7e93-136">En el código anterior, `options.Configuration` se inicializa con lo que se haya especificado en la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="b7e93-136">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="b7e93-137">Para obtener información sobre las opciones de Redis, consulte la [documentación de StackExchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="b7e93-137">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="b7e93-138">En la SignalR aplicación, instale el siguiente paquete de NuGet:</span><span class="sxs-lookup"><span data-stu-id="b7e93-138">In the SignalR app, install the following NuGet package:</span></span>

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* <span data-ttu-id="b7e93-139">En el `Startup.ConfigureServices` método, llame a <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> :</span><span class="sxs-lookup"><span data-stu-id="b7e93-139">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* <span data-ttu-id="b7e93-140">Configure las opciones según sea necesario:</span><span class="sxs-lookup"><span data-stu-id="b7e93-140">Configure options as needed:</span></span>
 
  <span data-ttu-id="b7e93-141">La mayoría de las opciones se pueden establecer en la cadena de conexión o en el objeto [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="b7e93-141">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="b7e93-142">Las opciones especificadas en `ConfigurationOptions` invalidan las definidas en la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="b7e93-142">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="b7e93-143">En el ejemplo siguiente se muestra cómo establecer opciones en el `ConfigurationOptions` objeto.</span><span class="sxs-lookup"><span data-stu-id="b7e93-143">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="b7e93-144">En este ejemplo se agrega un prefijo de canal para que varias aplicaciones puedan compartir la misma instancia de Redis, tal y como se explica en el paso siguiente.</span><span class="sxs-lookup"><span data-stu-id="b7e93-144">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="b7e93-145">En el código anterior, `options.Configuration` se inicializa con lo que se haya especificado en la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="b7e93-145">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="b7e93-146">Para obtener información sobre las opciones de Redis, consulte la [documentación de StackExchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="b7e93-146">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="b7e93-147">Si usa un servidor de Redis para varias SignalR aplicaciones, use un prefijo de canal diferente para cada SignalR aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7e93-147">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="b7e93-148">La configuración de un prefijo de canal aísla una SignalR aplicación de otras que usan prefijos de canal diferentes.</span><span class="sxs-lookup"><span data-stu-id="b7e93-148">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="b7e93-149">Si no asigna prefijos diferentes, un mensaje enviado desde una aplicación a todos sus clientes irá a todos los clientes de todas las aplicaciones que usan el servidor de Redis como un backplane.</span><span class="sxs-lookup"><span data-stu-id="b7e93-149">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="b7e93-150">Configure el software de equilibrio de carga de la granja de servidores para sesiones permanentes.</span><span class="sxs-lookup"><span data-stu-id="b7e93-150">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="b7e93-151">Estos son algunos ejemplos de documentación sobre cómo hacerlo:</span><span class="sxs-lookup"><span data-stu-id="b7e93-151">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="b7e93-152">IIS</span><span class="sxs-lookup"><span data-stu-id="b7e93-152">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="b7e93-153">HAProxy</span><span class="sxs-lookup"><span data-stu-id="b7e93-153">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="b7e93-154">Nginx</span><span class="sxs-lookup"><span data-stu-id="b7e93-154">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="b7e93-155">pfSense</span><span class="sxs-lookup"><span data-stu-id="b7e93-155">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="b7e93-156">Errores del servidor de Redis</span><span class="sxs-lookup"><span data-stu-id="b7e93-156">Redis server errors</span></span>

<span data-ttu-id="b7e93-157">Cuando un servidor de Redis deja de funcionar, SignalR produce excepciones que indican que los mensajes no se entregarán.</span><span class="sxs-lookup"><span data-stu-id="b7e93-157">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="b7e93-158">Algunos mensajes de excepción típicos:</span><span class="sxs-lookup"><span data-stu-id="b7e93-158">Some typical exception messages:</span></span>

* <span data-ttu-id="b7e93-159">*Error al escribir el mensaje*</span><span class="sxs-lookup"><span data-stu-id="b7e93-159">*Failed writing message*</span></span>
* <span data-ttu-id="b7e93-160">*No se pudo invocar el método de concentrador ' MethodName '*</span><span class="sxs-lookup"><span data-stu-id="b7e93-160">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="b7e93-161">*Error en la conexión a Redis*</span><span class="sxs-lookup"><span data-stu-id="b7e93-161">*Connection to Redis failed*</span></span>

SignalR<span data-ttu-id="b7e93-162">no almacena en búfer los mensajes para enviarlos cuando el servidor vuelve a copia de seguridad.</span><span class="sxs-lookup"><span data-stu-id="b7e93-162"> doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="b7e93-163">Los mensajes enviados mientras el servidor de Redis está inactivo se pierden.</span><span class="sxs-lookup"><span data-stu-id="b7e93-163">Any messages sent while the Redis server is down are lost.</span></span>

SignalR<span data-ttu-id="b7e93-164">se vuelve a conectar automáticamente cuando el servidor de Redis está disponible de nuevo.</span><span class="sxs-lookup"><span data-stu-id="b7e93-164"> automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="b7e93-165">Comportamiento personalizado para errores de conexión</span><span class="sxs-lookup"><span data-stu-id="b7e93-165">Custom behavior for connection failures</span></span>

<span data-ttu-id="b7e93-166">Este es un ejemplo que muestra cómo controlar los eventos de error de conexión de Redis.</span><span class="sxs-lookup"><span data-stu-id="b7e93-166">Here's an example that shows how to handle Redis connection failure events.</span></span>

::: moniker range="= aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

::: moniker range="> aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddMessagePackProtocol()
        .AddStackExchangeRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

## <a name="redis-clustering"></a><span data-ttu-id="b7e93-167">Agrupación en clústeres de Redis</span><span class="sxs-lookup"><span data-stu-id="b7e93-167">Redis Clustering</span></span>

<span data-ttu-id="b7e93-168">La [agrupación en clústeres de Redis](https://redis.io/topics/cluster-spec) es un método para lograr alta disponibilidad mediante el uso de varios servidores de Redis.</span><span class="sxs-lookup"><span data-stu-id="b7e93-168">[Redis Clustering](https://redis.io/topics/cluster-spec) is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="b7e93-169">La agrupación en clústeres no se admite oficialmente, pero podría funcionar.</span><span class="sxs-lookup"><span data-stu-id="b7e93-169">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b7e93-170">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="b7e93-170">Next steps</span></span>

<span data-ttu-id="b7e93-171">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="b7e93-171">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="b7e93-172">Documentación de Redis</span><span class="sxs-lookup"><span data-stu-id="b7e93-172">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="b7e93-173">Documentación de Redis StackExchange</span><span class="sxs-lookup"><span data-stu-id="b7e93-173">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="b7e93-174">Documentación de Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="b7e93-174">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)
