---
title: Diferencias entre SignalR y ASP.net CoreSignalR
author: bradygaster
description: Diferencias entre SignalR y ASP.net CoreSignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: d63e9077ba23c3783d7ce1ceb9c9683bcb0d3a29
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408895"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a>Diferencias entre ASP.NET SignalR y ASP.net CoreSignalR

ASP.NET Core SignalR no es compatible con clientes o servidores de ASP.net SignalR . En este artículo se detallan las características que se han quitado o cambiado en ASP.NET Core SignalR .

## <a name="how-to-identify-the-signalr-version"></a>Cómo identificar la SignalR versión

::: moniker range=">= aspnetcore-3.0"

|                      | ASP.NETSignalR | ASP.NET CoreSignalR |
| -------------------- | --------------- | -------------------- |
| Paquete NuGet de servidor | [Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | Ninguno. Se incluye en el marco de trabajo compartido [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) . |
| Paquetes NuGet de cliente | [Microsoft. AspNet. SignalR . Nº](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. AspNet. SignalR . ASPX](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. AspNetCore. SignalR . Nº](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| Paquete NPM de cliente de JavaScript | [signalr](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| Cliente de Java | [Repositorio de github](https://github.com/SignalR/java-client) (desusado)  | Paquete Maven [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| Tipo de aplicación de servidor | ASP.NET (System. Web) o Self-host OWIN | ASP.NET Core |
| Plataformas de servidor admitidas | .NET Framework 4,5 o posterior | .NET Core 3,0 o posterior |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | ASP.NETSignalR | ASP.NET CoreSignalR |
| -------------------- | --------------- | -------------------- |
| Paquete NuGet de servidor | [Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.net Core)<br>[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| Paquetes NuGet de cliente | [Microsoft. AspNet. SignalR . Nº](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. AspNet. SignalR . ASPX](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. AspNetCore. SignalR . Nº](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| Paquete NPM de cliente de JavaScript | [signalr](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| Cliente de Java | [Repositorio de github](https://github.com/SignalR/java-client) (desusado)  | Paquete Maven [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| Tipo de aplicación de servidor | ASP.NET (System. Web) o Self-host OWIN | ASP.NET Core |
| Plataformas de servidor admitidas | .NET Framework 4,5 o posterior | .NET Framework 4.6.1 o versiones posteriores<br>.NET Core 2,1 o posterior |

::: moniker-end

## <a name="feature-differences"></a>Diferencias de características

### <a name="automatic-reconnects"></a>Reconexiones automáticas

::: moniker range=">= aspnetcore-3.0"

En ASP.NET SignalR :

* De forma predeterminada, SignalR intenta volver a conectarse al servidor si se quita la conexión. 

En ASP.NET Core SignalR :

* Las reconexiones automáticas se incluyen en el [cliente de .net](xref:signalr/dotnet-client#automatically-reconnect) y en el [cliente de JavaScript](xref:signalr/javascript-client#automatically-reconnect):

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Antes de ASP.NET Core 3,0, SignalR no es compatible con las reconexiones automáticas. Si el cliente está desconectado, el usuario debe iniciar explícitamente una nueva conexión para volver a conectarse. En ASP.NET SignalR , SignalR intenta volver a conectarse al servidor si se quita la conexión.

::: moniker-end

### <a name="protocol-support"></a>Compatibilidad con protocolos

ASP.NET Core SignalR admite JSON, así como un nuevo protocolo binario basado en [MessagePack](xref:signalr/messagepackhubprotocol). Además, se pueden crear protocolos personalizados.

### <a name="transports"></a>Transportes

El transporte de tramas Forever no se admite en ASP.NET Core SignalR .

## <a name="differences-on-the-server"></a>Diferencias en el servidor

Las SignalR bibliotecas del lado servidor de ASP.net Core se incluyen en [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), que se usa en la plantilla de **aplicación Web ASP.net Core** para los Razor proyectos de y MVC.

ASP.NET Core SignalR es un middleware ASP.net Core. Se debe configurar llamando a <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> en `Startup.ConfigureServices` .

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

Para configurar el enrutamiento, asigne rutas a los concentradores dentro de la <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> llamada al método en el `Startup.Configure` método.

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Para configurar el enrutamiento, asigne rutas a los concentradores dentro de la <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> llamada al método en el `Startup.Configure` método.

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a>Sesiones permanentes

El modelo ampliación para ASP.NET SignalR permite a los clientes volver a conectarse y enviar mensajes a cualquier servidor de la granja. En ASP.NET Core SignalR , el cliente debe interactuar con el mismo servidor mientras dure la conexión. En el caso de ampliación con Redis, eso significa que se requieren sesiones permanentes. En el caso de ampliación con el [ SignalR servicio de Azure](/azure/azure-signalr/), las sesiones permanentes no son necesarias porque el servicio administra las conexiones a los clientes.

### <a name="single-hub-per-connection"></a>Un solo concentrador por conexión

En ASP.NET Core SignalR , se ha simplificado el modelo de conexión. Las conexiones se realizan directamente en un solo concentrador, en lugar de usar una sola conexión para compartir el acceso a varios centros.

### <a name="streaming"></a>Streaming

ASP.NET Core SignalR ahora admite el [streaming de datos](xref:signalr/streaming) desde el concentrador al cliente.

### <a name="state"></a>State

Se ha quitado la capacidad de pasar el estado arbitrario entre los clientes y el concentrador (a menudo denominado `HubState` ), así como la compatibilidad con los mensajes de progreso. En este momento no hay ningún homólogo de servidores proxy de concentrador.

### <a name="persistentconnection-removal"></a>Eliminación de PersistentConnection

En ASP.NET Core SignalR , se ha quitado la clase [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) .

### <a name="globalhost"></a>Host global

ASP.NET Core tiene la inserción de dependencias (DI) integrada en el marco de trabajo. Los servicios pueden usar DI para acceder a [HubContext](xref:signalr/hubcontext). El `GlobalHost` objeto que se usa en ASP.net SignalR para obtener `HubContext` no existe en ASP.net Core SignalR .

### <a name="hubpipeline"></a>HubPipeline

ASP.NET Core SignalR no es compatible con los `HubPipeline` módulos de.

## <a name="differences-on-the-client"></a>Diferencias en el cliente

### <a name="typescript"></a>TypeScript

El SignalR cliente de ASP.net Core está escrito en [TypeScript](https://www.typescriptlang.org/). Puede escribir en JavaScript o TypeScript cuando se usa el [cliente de JavaScript](xref:signalr/javascript-client).

### <a name="the-javascript-client-is-hosted-at-npm"></a>El cliente de JavaScript se hospeda en NPM

::: moniker range=">= aspnetcore-3.0"

En las versiones de ASP.NET, el cliente de JavaScript se obtuvo a través de un paquete NuGet en Visual Studio. En las versiones ASP.NET Core, el [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) paquete NPM contiene las bibliotecas de JavaScript. Este paquete no está incluido en la plantilla de **aplicación Web de ASP.net Core** . Use NPM para obtener e instalar el `@microsoft/signalr` paquete NPM.

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

En las versiones de ASP.NET, el cliente de JavaScript se obtuvo a través de un paquete NuGet en Visual Studio. En las versiones ASP.NET Core, el [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) paquete NPM contiene las bibliotecas de JavaScript. Este paquete no está incluido en la plantilla de **aplicación Web de ASP.net Core** . Use NPM para obtener e instalar el `@aspnet/signalr` paquete NPM.

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a>jQuery

La dependencia de jQuery se ha quitado, pero los proyectos todavía pueden usar jQuery.

### <a name="internet-explorer-support"></a>Compatibilidad con Internet Explorer

ASP.NET Core SignalR requiere Microsoft Internet Explorer 11 o posterior (ASP.net SignalR compatible con Microsoft Internet Explorer 8 y versiones posteriores).

### <a name="javascript-client-method-syntax"></a>Sintaxis del método de cliente JavaScript

::: moniker range=">= aspnetcore-3.0"

La sintaxis de JavaScript ha cambiado con respecto a la versión de ASP.NET de SignalR . En lugar de usar el `$connection` objeto, cree una conexión mediante la API de [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) .

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Use el método on para especificar los métodos [de](/javascript/api/@microsoft/signalr/HubConnection#on) cliente a los que puede llamar el concentrador.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

La sintaxis de JavaScript ha cambiado con respecto a la versión de ASP.NET de SignalR . En lugar de usar el `$connection` objeto, cree una conexión mediante la API de [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) .

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Use el método on para especificar los métodos [de](/javascript/api/@aspnet/signalr/HubConnection#on) cliente a los que puede llamar el concentrador.

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

Después de crear el método de cliente, inicie la conexión del concentrador. Encadenar un método [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) para registrar o controlar los errores.

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a>Servidores proxy de concentrador

::: moniker range=">= aspnetcore-3.0"

Los proxies de concentrador ya no se generan automáticamente. En su lugar, el nombre del método se pasa a la API de [invocación](/javascript/api/@microsoft/signalr/hubconnection#invoke) como una cadena.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Los proxies de concentrador ya no se generan automáticamente. En su lugar, el nombre del método se pasa a la API de [invocación](/javascript/api/@aspnet/signalr/hubconnection#invoke) como una cadena.

::: moniker-end

### <a name="net-and-other-clients"></a>.NET y otros clientes

[Microsoft. AspNetCore. SignalR . ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)El paquete NuGet de cliente contiene las bibliotecas de cliente de .net para ASP.net Core SignalR .

Utilice <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> para crear y compilar una instancia de una conexión a un concentrador.

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>Diferencias de ampliación

ASP.NET SignalR admite SQL Server y Redis. ASP.NET Core SignalR admite el SignalR servicio de Azure y Redis.

### <a name="aspnet"></a>ASP.NET

* [SignalRAmpliación con Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [SignalRAmpliación con Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [SignalRAmpliación con SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>ASP.NET Core

* [Servicio de Azure SignalR](/azure/azure-signalr/)
* [Backplane de Redis](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>Recursos adicionales

* [Concentradores](xref:signalr/hubs)
* [Cliente de JavaScript](xref:signalr/javascript-client)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Plataformas compatibles](xref:signalr/supported-platforms)
