---
title: Configuración de ASP.NET Core Signalr
author: bradygaster
description: Aprenda a configurar aplicaciones de ASP.NET Core Signalr.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 08/05/2019
uid: signalr/configuration
ms.openlocfilehash: 156ffac83fbdf61fd88ad8acc307c2c701c46bca
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773929"
---
# <a name="aspnet-core-signalr-configuration"></a>Configuración de ASP.NET Core Signalr

## <a name="jsonmessagepack-serialization-options"></a>Opciones de serialización de JSON/MessagePack

ASP.NET Core Signalr admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html). Cada protocolo tiene opciones de configuración de serialización.

La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que se puede agregar después `Startup.ConfigureServices` de [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el método. El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto. La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese objeto es un objeto `JsonSerializerSettings` JSON.net que se puede usar para configurar la serialización de argumentos y valores devueltos. Consulte la [documentación de JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm) para obtener más información.

Por ejemplo, para configurar el serializador de modo que use nombres de propiedad "PascalCase", en lugar de los nombres predeterminados de "camelCase", use el código siguiente:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
        new DefaultContractResolver();
    });
```

En el cliente de .net, el `AddJsonProtocol` mismo método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.

### <a name="messagepack-serialization-options"></a>Opciones de serialización de MessagePack

La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Consulte [MessagePack en signalr](xref:signalr/messagepackhubprotocol) para obtener más detalles.

> [!NOTE]
> En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.

## <a name="configure-server-options"></a>Configurar opciones de servidor

En la tabla siguiente se describen las opciones para configurar los concentradores de Signalr:

::: moniker range=">= aspnetcore-3.0"

| Opción | Valor predeterminado | DESCRIPCIÓN |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 segundos | El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo. Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa. El valor recomendado es el doble `KeepAliveInterval` del valor.|
| `HandshakeTimeout` | 15 segundos | Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra. Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave. Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión. Al cambiar `KeepAliveInterval`, cambie la `ServerTimeout` /configuraciónenel cliente`serverTimeoutInMilliseconds` . El valor `ServerTimeout` recomendado / `KeepAliveInterval` es eldobledelvalor.`serverTimeoutInMilliseconds`  |
| `SupportedProtocols` | Todos los protocolos instalados | Protocolos admitidos por este centro. De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales. |
| `EnableDetailedErrors` | `false` | Si `true`es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador. El valor predeterminado `false`es, ya que estos mensajes de excepción pueden contener información confidencial. |
| `StreamBufferCapacity` | `10` | Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente. Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.|
| `MaximumReceiveMessageSize` | 32 KB | Tamaño máximo de un único mensaje de concentrador entrante. |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| Opción | Valor predeterminado | DESCRIPCIÓN |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 segundos | El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo. Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa. El valor recomendado es el doble `KeepAliveInterval` del valor.|
| `HandshakeTimeout` | 15 segundos | Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra. Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave. Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión. Al cambiar `KeepAliveInterval`, cambie la `ServerTimeout` /configuraciónenel cliente`serverTimeoutInMilliseconds` . El valor `ServerTimeout` recomendado / `KeepAliveInterval` es eldobledelvalor.`serverTimeoutInMilliseconds`  |
| `SupportedProtocols` | Todos los protocolos instalados | Protocolos admitidos por este centro. De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales. |
| `EnableDetailedErrors` | `false` | Si `true`es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador. El valor predeterminado `false`es, ya que estos mensajes de excepción pueden contener información confidencial. |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| Opción | Valor predeterminado | DESCRIPCIÓN |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 segundos | Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra. Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave. Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión. Al cambiar `KeepAliveInterval`, cambie la `ServerTimeout` /configuraciónenel cliente`serverTimeoutInMilliseconds` . El valor `ServerTimeout` recomendado / `KeepAliveInterval` es eldobledelvalor.`serverTimeoutInMilliseconds`  |
| `SupportedProtocols` | Todos los protocolos instalados | Protocolos admitidos por este centro. De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales. |
| `EnableDetailedErrors` | `false` | Si `true`es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador. El valor predeterminado `false`es, ya que estos mensajes de excepción pueden contener información confidencial. |

::: moniker-end

Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de `AddSignalR` opciones a `Startup.ConfigureServices`la llamada en.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Las opciones de un solo concentrador invalidan las `AddSignalR` opciones globales proporcionadas en <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>y se pueden configurar mediante:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opciones de configuración de HTTP avanzadas

::: moniker range=">= aspnetcore-3.0"

Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria. Estas opciones se configuran pasando un delegado [a\<MapHub T >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en. `Startup.Configure`

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```
::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria. Estas opciones se configuran pasando un delegado [a\<MapHub T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en. `Startup.Configure`

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

::: moniker-end

En la tabla siguiente se describen las opciones para configurar las opciones de HTTP avanzadas de ASP.NET Core Signalr:

::: moniker range=">= aspnetcore-3.0"

| Opción | Valor predeterminado | DESCRIPCIÓN |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva. Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria. |
| `AuthorizationData` | Los datos se recopilan `Authorize` automáticamente a partir de los atributos aplicados a la clase hub. | Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro. |
| `TransportMaxBufferSize` | 32 KB | Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión. El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria. |
| `Transports` | Todos los transportes están habilitados. | Una enumeración de marcas `HttpTransportType` de bits que puede restringir los transportes que un cliente puede utilizar para conectarse. |
| `LongPolling` | Véalo a continuación. | Opciones adicionales específicas del transporte de sondeo prolongado. |
| `WebSockets` | Véalo a continuación. | Opciones adicionales específicas del transporte de WebSockets. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Opción | Valor predeterminado | DESCRIPCIÓN |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Número máximo de bytes recibidos del cliente que el servidor almacena en búfer. Aumentar este valor permite que el servidor reciba mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria. |
| `AuthorizationData` | Los datos se recopilan `Authorize` automáticamente a partir de los atributos aplicados a la clase hub. | Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro. |
| `TransportMaxBufferSize` | 32 KB | Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer. Al aumentar este valor, el servidor puede enviar mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria. |
| `Transports` | Todos los transportes están habilitados. | Una enumeración de marcas `HttpTransportType` de bits que puede restringir los transportes que un cliente puede utilizar para conectarse. |
| `LongPolling` | Véalo a continuación. | Opciones adicionales específicas del transporte de sondeo prolongado. |
| `WebSockets` | Véalo a continuación. | Opciones adicionales específicas del transporte de WebSockets. |

::: moniker-end

El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante `LongPolling` la propiedad:

| Opción | Valor predeterminado | DESCRIPCIÓN |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 segundos | Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo. Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia. |

El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante `WebSockets` la propiedad:

| Opción | Valor predeterminado | DESCRIPCIÓN |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 segundos | Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión. |
| `SubProtocolSelector` | `null` | Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado. El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado. |

## <a name="configure-client-options"></a>Configurar opciones de cliente

Las opciones de cliente se pueden configurar `HubConnectionBuilder` en el tipo (disponible en los clientes de .net y JavaScript). También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como `HubConnection` en el propio.

### <a name="configure-logging"></a>Configurar el registro

El registro se configura en el cliente .net mediante `ConfigureLogging` el método. Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor. Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.

> [!NOTE]
> Para registrar los proveedores de registro, debe instalar los paquetes necesarios. Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.

Por ejemplo, para habilitar el registro de la consola `Microsoft.Extensions.Logging.Console` , instale el paquete NuGet. Llame al `AddConsole` método de extensión:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

En el cliente de JavaScript, existe `configureLogging` un método similar. Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir. Los registros se escriben en la ventana de la consola del explorador.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro. Esto resulta útil cuando se configura el registro de signalr en entornos donde no se tiene `LogLevel` acceso a las constantes.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

En la tabla siguiente se enumeran los niveles de registro disponibles. El valor que se proporciona `configureLogging` para establecer el nivel de registro **mínimo** que se registrará. Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla**.

| Cadena                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**o bien**`information` | `LogLevel.Information` |
| `warn`**o bien**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> Para deshabilitar el registro por `signalR.LogLevel.None` completo, `configureLogging` especifique en el método.

Para obtener más información sobre el registro, consulte la documentación sobre el [diagnóstico de signalr](xref:signalr/diagnostics).

El cliente de Signalr Java usa la biblioteca [SLF4J](https://www.slf4j.org/) para el registro. Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica. En el fragmento de código siguiente se muestra `java.util.logging` cómo usar con el cliente de signalr Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Esto se puede omitir sin ningún riesgo.

### <a name="configure-allowed-transports"></a>Configurar transportes permitidos

Los transportes utilizados por signalr se pueden configurar en la `WithUrl` llamada (`withUrl` en JavaScript). Una operación OR bit a bit de los `HttpTransportType` valores de se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados. Todos los transportes están habilitados de forma predeterminada.

Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

En el cliente de JavaScript, los transportes se configuran estableciendo `transport` el campo en el objeto de opciones `withUrl`proporcionado a:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

En esta versión del cliente de Java, WebSockets es el único transporte disponible.

::: moniker-end

::: moniker range="= aspnetcore-3.0"

En el cliente de Java, el transporte se selecciona con `withTransport` el método `HttpHubConnectionBuilder`en. De forma predeterminada, el cliente de Java usa el transporte de WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> El cliente de Signalr Java no admite todavía la reserva de transporte.

::: moniker-end

### <a name="configure-bearer-authentication"></a>Configurar la autenticación de portador

Para proporcionar datos de autenticación junto con solicitudes de signalr, `AccessTokenProvider` use la`accessTokenFactory` opción (en JavaScript) para especificar una función que devuelva el token de acceso deseado. En el cliente de .net, este token de acceso se pasa como un token de "autenticación de portador" de http `Authorization` (mediante el encabezado con `Bearer`un tipo de). En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets). En estos casos, el token de acceso se proporciona como un valor `access_token`de cadena de consulta.

En el cliente de .net, `AccessTokenProvider` se puede especificar la opción mediante el delegado de `WithUrl`opciones en:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

En el cliente de JavaScript, el token de acceso se configura estableciendo `accessTokenFactory` el campo en el objeto de `withUrl`opciones en:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

En el cliente de Signalr Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar un [RxJava](https://github.com/ReactiveX/RxJava) de una [única\<cadena>](https://reactivex.io/documentation/single.html). Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurar las opciones de tiempo de espera y mantenimiento de conexión

En el propio objeto están disponibles opciones adicionales para configurar el `HubConnection` comportamiento de tiempo de espera y mantenimiento de conexión:


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Opción | Valor predeterminado | DESCRIPCIÓN |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `Closed` el evento`onclose` (en JavaScript). Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera. El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping. |
| `HandshakeTimeout` | 15 segundos | Tiempo de espera para el protocolo de enlace inicial del servidor. Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `Closed` el evento`onclose` (en JavaScript). Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave. Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Determina el intervalo en el que el cliente envía mensajes de ping. Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado. |

En el cliente de .net, los valores de tiempo `TimeSpan` de espera se especifican como valores.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Opción | Valor predeterminado | DESCRIPCIÓN |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onclose` el evento. Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera. El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping. |
| `keepAliveIntervalInMilliseconds` | 15 segundos (15.000 milisegundos) | Determina el intervalo en el que el cliente envía mensajes de ping. Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Opción | Valor predeterminado | DESCRIPCIÓN |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onClose` el evento. Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera. El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping. |
| `withHandshakeResponseTimeout` | 15 segundos | Tiempo de espera para el protocolo de enlace inicial del servidor. Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `onClose` el evento. Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave. Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 segundos (15.000 milisegundos) | Determina el intervalo en el que el cliente envía mensajes de ping. Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado. |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Opción | Valor predeterminado | DESCRIPCIÓN |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `Closed` el evento`onclose` (en JavaScript). Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera. El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping. |
| `HandshakeTimeout` | 15 segundos | Tiempo de espera para el protocolo de enlace inicial del servidor. Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `Closed` el evento`onclose` (en JavaScript). Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave. Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

En el cliente de .net, los valores de tiempo `TimeSpan` de espera se especifican como valores.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Opción | Valor predeterminado | DESCRIPCIÓN |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onclose` el evento. Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera. El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Opción | Valor predeterminado | DESCRIPCIÓN |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onClose` el evento. Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera. El valor recomendado es un número al menos el doble del valor `KeepAliveInterval` del servidor, para permitir que llegue el tiempo para que lleguen los ping. |
| `withHandshakeResponseTimeout` | 15 segundos | Tiempo de espera para el protocolo de enlace inicial del servidor. Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `onClose` el evento. Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave. Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

::: moniker-end

---

### <a name="configure-additional-options"></a>Configurar opciones adicionales

Se pueden configurar opciones adicionales en el `WithUrl` método`withUrl` (en JavaScript) en `HubConnectionBuilder` `HttpHubConnectionBuilder` o en las diversas API de configuración de en el cliente de Java:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Opción de .NET |  Valor predeterminado | DESCRIPCIÓN |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `SkipNegotiation` | `false` | Establézcalo en `true` para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**. Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr. |
| `ClientCertificates` | Empty | Colección de certificados TLS que se enviarán a las solicitudes de autenticación. |
| `Cookies` | Empty | Colección de cookies HTTP que se enviarán con cada solicitud HTTP. |
| `Credentials` | Empty | Credenciales que se van a enviar con cada solicitud HTTP. |
| `CloseTimeout` | 5 segundos | Solo WebSockets. Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre. Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta. |
| `Headers` | Empty | Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP. No se usa para las conexiones WebSocket. Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro. Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia. **Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.** |
| `Proxy` | `null` | Proxy HTTP que se va a usar al enviar solicitudes HTTP. |
| `UseDefaultCredentials` | `false` | Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets. Esto habilita el uso de la autenticación de Windows. |
| `WebSocketConfiguration` | `null` | Delegado que se puede usar para configurar opciones adicionales de WebSocket. Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones. |

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Opción de JavaScript | Valor predeterminado | DESCRIPCIÓN |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `skipNegotiation` | `false` | Establézcalo en `true` para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**. Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Opción de Java | Valor predeterminado | DESCRIPCIÓN |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `shouldSkipNegotiate` | `false` | Establézcalo en `true` para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**. Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr. |
| `withHeader``withHeaders` | Empty | Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP. |

---

En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones `WithUrl`proporcionado a:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript `withUrl`proporcionado a:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

En el cliente de Java, estas opciones se pueden configurar con los métodos `HttpHubConnectionBuilder` del devuelto desde`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
