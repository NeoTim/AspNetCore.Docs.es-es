---
title: Cliente JavaScript de ASP.NET Core Signalr
author: bradygaster
description: Información general de ASP.NET Core cliente JavaScript de Signalr.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/28/2019
uid: signalr/javascript-client
ms.openlocfilehash: f314e1fe0ef0ea73a28b332404a09f2956524132
ms.sourcegitcommit: f30b18442ed12831c7e86b0db249183ccd749f59
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2019
ms.locfileid: "68412384"
---
# <a name="aspnet-core-signalr-javascript-client"></a>Cliente JavaScript de ASP.NET Core Signalr

Por [Rachel Appel](https://twitter.com/rachelappel)

La biblioteca de cliente de JavaScript de ASP.NET Core Signalr permite a los desarrolladores llamar a código de concentrador de servidor.

[Vea o descargue el código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="install-the-signalr-client-package"></a>Instalar el paquete de cliente de Signalr

La biblioteca de cliente de Signalr JavaScript se entrega como un paquete [NPM](https://www.npmjs.com/) . Si usa Visual Studio, ejecute `npm install` desde la consola del **Administrador de paquetes** mientras se encuentra en la carpeta raíz. Para obtener Visual Studio Code, ejecute el comando desde el **terminal integrado**.

::: moniker range=">= aspnetcore-3.0"

  ```console
  npm init -y
  npm install @microsoft/signalr
  ```

NPM instala el contenido del paquete en el *node_modules\\@microsoft\signalr\dist\browser* carpeta. Cree una nueva carpeta denominada *signalr* en la *carpeta\\wwwroot lib* . Copie el archivo *signalr. js* en la carpeta *wwwroot\lib\signalr* .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

NPM instala el contenido del paquete en el *node_modules\\@aspnet\signalr\dist\browser* carpeta. Cree una nueva carpeta denominada *signalr* en la *carpeta\\wwwroot lib* . Copie el archivo *signalr. js* en la carpeta *wwwroot\lib\signalr* .

::: moniker-end

## <a name="use-the-signalr-javascript-client"></a>Uso del cliente de Signalr JavaScript

Haga referencia al cliente de signalr JavaScript `<script>` en el elemento.

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a>Conexión a un concentrador

El código siguiente crea e inicia una conexión. El nombre del centro no distingue mayúsculas de minúsculas.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>Conexiones entre orígenes

Normalmente, los exploradores cargan conexiones desde el mismo dominio que la página solicitada. Sin embargo, hay ocasiones en las que se requiere una conexión a otro dominio.

Para evitar que un sitio malintencionado Lea datos confidenciales de otro sitio, [las conexiones entre orígenes](xref:security/cors) están deshabilitadas de forma predeterminada. Para permitir una solicitud entre orígenes, habilítela en la `Startup` clase.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Llamar a métodos de Hub desde el cliente

Los clientes de JavaScript llaman a métodos públicos en los concentradores a través del método [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) de [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection). El `invoke` método acepta dos argumentos:

* Nombre del método de concentrador. En el ejemplo siguiente, el nombre del método en el concentrador es `SendMessage`.
* Cualquier argumento definido en el método de concentrador. En el ejemplo siguiente, el nombre del argumento `message`es. El código de ejemplo utiliza la sintaxis de la función de flecha que se admite en las versiones actuales de todos los exploradores principales, excepto Internet Explorer.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Si usa Azure Signalr Service en modo sin *servidor*, no puede llamar a métodos de concentrador desde un cliente. Para obtener más información, consulte la [documentación del servicio signalr](/azure/azure-signalr/signalr-concept-serverless-development-config).

El `invoke` método devuelve un [compromiso](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)de JavaScript. `Promise` Se resuelve con el valor devuelto (si existe) cuando el método en el servidor devuelve. Si el método en el servidor produce un error, `Promise` se rechaza con el mensaje de error. Use los `then` métodos `catch` y en el `Promise` propio para controlar estos casos (o `await` la sintaxis).

El `send` método devuelve un JavaScript `Promise`. `Promise` Se resuelve cuando el mensaje se ha enviado al servidor. Si se produce un error al enviar el mensaje, `Promise` se rechaza con el mensaje de error. Use los `then` métodos `catch` y en el `Promise` propio para controlar estos casos (o `await` la sintaxis).

> [!NOTE]
> El `send` uso de no espera hasta que el servidor haya recibido el mensaje. Por lo tanto, no es posible devolver datos ni errores del servidor.

## <a name="call-client-methods-from-hub"></a>Llamar a métodos de cliente desde el concentrador

Para recibir mensajes desde el concentrador, defina un método mediante el método [on](/javascript/api/%40aspnet/signalr/hubconnection#on) de `HubConnection`la.

* Nombre del método de cliente JavaScript. En el ejemplo siguiente, el nombre del método `ReceiveMessage`es.
* Argumentos que el concentrador pasa al método. En el ejemplo siguiente, el valor del argumento `message`es.

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

El código anterior en `connection.on` se ejecuta cuando el código del lado servidor lo llama mediante el método [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) .

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

Signalr determina el método de cliente al que se debe llamar haciendo coincidir el nombre `SendAsync` del `connection.on`método y los argumentos definidos en y.

> [!NOTE]
> Como procedimiento recomendado, llame al método [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) en el `HubConnection` después `on`de. Esto garantiza que los controladores se registren antes de que se reciban los mensajes.

## <a name="error-handling-and-logging"></a>Control de errores y registro

Encadenar un `catch` método al final `start` del método para controlar los errores del lado cliente. Use `console.error` para generar errores en la consola del explorador.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

Configure el seguimiento del registro del lado cliente pasando un registrador y un tipo de evento al registro cuando se establece la conexión. Los mensajes se registran con el nivel de registro especificado y superior. Los niveles de registro disponibles son los siguientes:

* `signalR.LogLevel.Error`&ndash; Mensajes de error. Solo `Error` registra mensajes.
* `signalR.LogLevel.Warning`&ndash; Mensajes de advertencia sobre posibles errores. Registros `Warning` y`Error` mensajes.
* `signalR.LogLevel.Information`&ndash; Mensajes de estado sin errores. Registra `Information`mensajes `Warning`, y `Error` .
* `signalR.LogLevel.Trace`&ndash; Mensajes de seguimiento. Registra todo, incluidos los datos transportados entre el concentrador y el cliente.

Use el método [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) en [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) para configurar el nivel de registro. Los mensajes se registran en la consola del explorador.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>Volver a conectar clientes

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Volver a conectar automáticamente

El cliente de JavaScript para signalr puede configurarse para que se vuelva `withAutomaticReconnect` a conectar automáticamente mediante el método en [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder). No se volverá a conectar automáticamente de forma predeterminada.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Sin parámetros, `withAutomaticReconnect()` configura el cliente para que espere 0, 2, 10 y 30 segundos, respectivamente, antes de intentar cada intento de reconexión, deteniéndose después de cuatro intentos erróneos.

Antes de iniciar cualquier intento de reconexión `HubConnection` , cambiará `HubConnectionState.Reconnecting` al estado y activará `onreconnecting` sus devoluciones de llamada en lugar de `Disconnected` pasar al estado y desencadenar sus `onclose` devoluciones de llamada como `HubConnection`sin la reconexión automática configurada. Esto proporciona una oportunidad para advertir a los usuarios de que se ha perdido la conexión y deshabilitar los elementos de la interfaz de usuario.

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Si el cliente `HubConnection` se vuelve `Connected` a conectar correctamente dentro de los cuatro primeros intentos, volverá a pasar al estado y activará `onreconnected` sus devoluciones de llamada. Esto proporciona una oportunidad para informar a los usuarios de que se ha restablecido la conexión.

Dado que la conexión es completamente nueva en el servidor, se `connectionId` proporcionará un nuevo a `onreconnected` la devolución de llamada.

> [!WARNING]
> El `onreconnected` parámetro de `connectionId` la devolución de llamada no se definirá si se configuró para omitir la `HubConnection` [negociación](xref:signalr/configuration#configure-client-options).

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`no configurará el para reintentar errores de inicio inicial, por lo que los `HubConnection` errores de inicio deben controlarse manualmente:

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

Si el cliente no `HubConnection` se vuelve `Disconnected` a conectar correctamente en los cuatro primeros intentos, cambiará al estado y activará sus devoluciones de llamada [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) . Esto proporciona una oportunidad para informar a los usuarios de que la conexión se ha perdido permanentemente y recomienda actualizar la página:

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Con el fin de configurar un número personalizado de intentos de reconexión antes de desconectar o cambiar el tiempo de reconexión, `withAutomaticReconnect` acepta una matriz de números que representan el retraso en milisegundos que se debe esperar antes de iniciar cada intento de reconexión.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

En el ejemplo anterior se configura para `HubConnection` iniciar el intento de reconexión inmediatamente después de la pérdida de la conexión. Esto también se aplica a la configuración predeterminada.

Si se produce un error en el primer intento de reconexión, el segundo intento de reconexión también se iniciará inmediatamente en lugar de esperar 2 segundos como lo haría en la configuración predeterminada.

Si se produce un error en el segundo intento de reconexión, el tercer intento de reconexión se iniciará en 10 segundos, lo que volverá a ser como la configuración predeterminada.

Después, el comportamiento personalizado difiere de nuevo del comportamiento predeterminado al detenerse después de que se produzca un error en el tercer intento de reconexión en lugar de intentar un intento de reconexión más en otros 30 segundos como lo haría en la configuración predeterminada.

Si desea tener un mayor control sobre la temporización y el número de intentos de reconexión `withAutomaticReconnect` automática, acepta un objeto `IRetryPolicy` que implementa la interfaz, que tiene un `nextRetryDelayInMilliseconds`único método denominado.

`nextRetryDelayInMilliseconds`toma un único argumento con el tipo `RetryContext`. `previousRetryCount` `elapsedMilliseconds` Tienetres`retryReason` propiedades: `number` , y que son ,y,respectivamente.`Error` `number` `RetryContext` Antes del primer intento de reconexión, `previousRetryCount` y `elapsedMilliseconds` será cero, y `retryReason` será el error que provocó la pérdida de la conexión. Después de cada intento de reintento erróneo, `previousRetryCount` se incrementará en uno, `elapsedMilliseconds` se actualizará para reflejar la cantidad de tiempo empleado en la reconexión hasta el momento en milisegundos y el `retryReason` será el error que provocó el último intento de reconexión a puedan.

`nextRetryDelayInMilliseconds`debe devolver un número que represente el número de milisegundos que se va a esperar antes del siguiente `null` intento de `HubConnection` reconexión o si debe dejar de volver a conectarse.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
          if (retryContext.elapsedMilliseconds < 60000) {
            // If we've been reconnecting for less than 60 seconds so far,
            // wait between 0 and 10 seconds before the next reconnect attempt.
            return Math.random() * 10000;
          } else {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
          }
        })
    .build();
```

Como alternativa, puede escribir código que volverá a conectar el cliente manualmente, tal como se muestra en [reconexión manual](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Volver a conectar manualmente

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Antes de 3,0, el cliente de JavaScript para Signalr no se vuelve a conectar automáticamente. Debe escribir código que volverá a conectar el cliente manualmente.

::: moniker-end

En el código siguiente se muestra un enfoque típico de reconexión manual:

1. Una función (en este caso, la `start` función) se crea para iniciar la conexión.
1. Llame a `start` la función en el controlador `onclose` de eventos de la conexión.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Una implementación del mundo real usaría una interrupción exponencial o reintentará un número especificado de veces antes de abandonarlo.

## <a name="additional-resources"></a>Recursos adicionales

* [Referencia de API de JavaScript](/javascript/api/?view=signalr-js-latest)
* [Tutorial de JavaScript](xref:tutorials/signalr)
* [Tutorial de WebPack y TypeScript](xref:tutorials/signalr-typescript-webpack)
* [Concentradores](xref:signalr/hubs)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Publicar en Azure](xref:signalr/publish-to-azure-web-app)
* [Solicitudes entre orígenes (CORS)](xref:security/cors)
* [Documentación sin servidor del servicio Azure Signalr](/azure/azure-signalr/signalr-concept-serverless-development-config)
