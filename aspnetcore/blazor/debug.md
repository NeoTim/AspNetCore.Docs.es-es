---
title: Depuración de WebAssembly de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo depurar aplicaciones Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 75db5d5e69cb200ebf3bd1dc1e0afed0300214cc
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242776"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a>Depuración de WebAssembly de Blazor en ASP.NET Core

[Daniel Roth](https://github.com/danroth27)

Las aplicaciones de Blazor WebAssembly se pueden depurar con las herramientas de desarrollo del explorador en exploradores basados en Chromium (Edge/Chrome). También puede depurar la aplicación con Visual Studio o Visual Studio Code.

Entre los escenarios disponibles se incluyen los siguientes:

* Establecimiento y eliminación de puntos de interrupción.
* Ejecute la aplicación con compatibilidad de depuración en Visual Studio y Visual Studio Code (compatibilidad con <kbd>F5</kbd>).
* Un solo paso (<kbd>F10</kbd>) por el código.
* Reanude la ejecución de código con <kbd>F8</kbd> en un explorador o con <kbd>F5</kbd> en Visual Studio o Visual Studio Code.
* En la pantalla *Variables locales*, observe los valores de las variables locales.
* Vea la pila de llamadas, incluidas las cadenas de llamadas que van desde JavaScript hasta .NET y desde .NET a JavaScript.

Por ahora, *no puede*:

* Interrumpir las operaciones ante excepciones no controladas.
* Alcanzar puntos de interrupción durante el inicio de la aplicación.

Seguiremos mejorando la experiencia de depuración en las próximas versiones.

## <a name="prerequisites"></a>Requisitos previos

La depuración requiere cualquiera de los exploradores siguientes:

* Microsoft Edge (versión 80 o posterior)
* Google Chrome (versión 70 o posterior)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Habilitación de la depuración para Visual Studio y Visual Studio Code

Para habilitar la depuración de una aplicación WebAssembly de Blazor existente, actualice el archivo `launchSettings.json` del proyecto de inicio para incluir la propiedad `inspectUri` siguiente en cada perfil de inicio:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Una vez que lo actualice, el archivo `launchSettings.json` debe ser similar a este ejemplo:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

La propiedad `inspectUri`:

* Permite que el IDE detecte que la aplicación es una aplicación de Blazor WebAssembly.
* Indica a la infraestructura de depuración de scripts que se conecte al explorador a través del proxy de depuración de Blazor.

El marco de trabajo proporciona los valores de marcador de posición para el protocolo WebSocket (`wsProtocol`), el host (`url.hostname`), el puerto (`url.port`) y el identificador URI del inspector en el explorador iniciado (`browserInspectUri`).

## <a name="visual-studio"></a>Programa para la mejora

Para depurar una aplicación de Blazor WebAssembly en Visual Studio:

1. Cree una nueva aplicación de Blazor WebAssembly hospedada por ASP.NET Core.
1. Presione <kbd>F5</kbd> para ejecutar la aplicación en el depurador.
1. Establezca un punto de interrupción en `Pages/Counter.razor` en el método `IncrementCount`.
1. Vaya a la pestaña **`Counter`** y seleccione el botón para alcanzar el punto de interrupción:

   ![Depuración del contador](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Revise el valor del campo `currentCount` en la ventana de variables locales:

   ![Vista de las variables locales](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Presione <kbd>F5</kbd> para continuar la ejecución.

Al depurar la aplicación de Blazor WebAssembly, también puede depurar el código del servidor:

1. Establezca un punto de interrupción en la página `Pages/FetchData.razor` de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.
1. Establezca un punto de interrupción en `WeatherForecastController` en el método de acción `Get`.
1. Vaya a la pestaña **`Fetch Data`** para alcanzar el primer punto de recuperación en el componente `FetchData` justo antes de que emita una solicitud HTTP al servidor:

   ![Depuración de captura de datos](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Presione <kbd>F5</kbd> para continuar la ejecución y, luego, alcance el punto de interrupción en el servidor en `WeatherForecastController`:

   ![Depuración del servidor](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Vuelva a presionar <kbd>F5</kbd> para permitir que la ejecución continúe y vea la tabla de pronóstico meteorológico representada.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

Para depurar una aplicación de Blazor WebAssembly en Visual Studio Code:
 
Instale la [extensión de C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) y la extensión del [depurador de JavaScript (nocturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` establecido en `true`.

![Extensiones](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![Depurador JS en versión preliminar](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a>Depuración de aplicaciones de Blazor WebAssembly independientes

1. Abra la aplicación de Blazor WebAssembly independiente en VS Code.

   Si recibe la notificación siguiente que indica que se requiere más información para habilitar la depuración:
   
   * Confirme que tiene instaladas las extensiones correctas.
   * Confirme que está habilitada la depuración de JavaScript en versión preliminar.
   * Recargue la ventana.

   ![Se requiere configuración adicional](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. Para iniciar la depuración, use el método abreviado de teclado <kbd>F5</kbd> o el elemento de menú.

1. Cuando se le pida, seleccione la opción **Blazor WebAssembly Debug** (Depuración de Blazor WebAssembly) para iniciar la depuración.

   ![Lista de opciones de depuración disponibles](index/_static/blazor-vscode-debugtypes.png)

1. Se inicia la aplicación independiente y se abre un explorador de depuración.

1. Establezca el punto de interrupción en el método `IncrementCount` en el componente `Counter` y seleccione el botón para alcanzar el punto de interrupción:

   ![Depuración del contenedor en VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a>Depuración de aplicaciones de Blazor WebAssembly hospedadas

1. Abra la aplicación de Blazor WebAssembly hospedada en VS Code.

1. Si no hay ninguna configuración de inicio establecida para el proyecto, aparece la notificación siguiente. Seleccione **Sí**.

   ![Agregar los recursos necesarios](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. En la ventana de selección, seleccione el proyecto *Servidor* dentro de la solución hospedada.

Se genera un archivo `launch.json` con la configuración de inicio para iniciar el depurador.

### <a name="attach-to-an-existing-debugging-session"></a>Adjuntar a una sesión de depuración existente

Para adjuntar a una aplicación Blazor en ejecución, cree un archivo `launch.json` con la configuración siguiente:

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> Solo se permite adjuntar a una sesión de depuración en el caso de las aplicaciones independientes. Para usar la depuración de pila completa, debe iniciar la aplicación desde VS Code.

### <a name="launch-configuration-options"></a>Opciones de configuración de inicio

Estas opciones de configuración de inicio son compatibles con el tipo de depuración `blazorwasm`.

| Opción    | Descripción |
| --------- | ----------- |
| `request` | Use `launch` para iniciar y adjuntar una sesión de depuración a una aplicación de Blazor WebAssembly o `attach` para adjuntar una sesión de depuración a una aplicación que ya está en ejecución. |
| `url`     | La dirección URL que se va a abrir en el explorador durante la depuración. Tiene como valor predeterminado `https://localhost:5001`. |
| `browser` | El explorador que se va a iniciar para la sesión de depuración. Se establece en `edge` o `chrome`. Tiene como valor predeterminado `chrome`. |
| `trace`   | Se usa para generar registros desde el depurador de JS. Se establece en `true` para generar registros. |
| `hosted`  | Se debe establecer en `true` si se inicia y depura una aplicación de Blazor WebAssembly hospedada. |
| `webRoot` | Especifica la ruta de acceso absoluta del servidor web. Se debe establecer si una aplicación se envía desde una subruta. |
| `timeout` | El número de milisegundos que se esperarán para adjuntar la sesión de depuración. El valor predeterminado es 30 000 milisegundos (30 segundos). |
| `program` | Una referencia al archivo ejecutable para ejecutar el servidor de la aplicación hospedada. Se debe establecer si `hosted` es `true`. |
| `cwd`     | El directorio de trabajo en el que se va a iniciar la aplicación. Se debe establecer si `hosted` es `true`. |
| `env`     | Las variables de entorno que se van a proporcionar al proceso iniciado. Solo se aplica si `hosted` está establecido en `true`. |

### <a name="example-launch-configurations"></a>Configuraciones de inicio de ejemplo

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a>Inicie y depure una aplicación de Blazor WebAssembly independiente

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a>Adjuntar a una aplicación en ejecución en una dirección URL especificada

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a>Inicie y depure una aplicación de Blazor WebAssembly hospedada

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a>Depuración en el explorador

1. Ejecute una compilación de depuración de la aplicación en el entorno de desarrollo.

1. Presione <kbd>Mayús</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.

1. El explorador se debe ejecutar con la depuración remota habilitada. Si la depuración remota está deshabilitada, se genera una página de error en la que se indica que **no se puede encontrar la pestaña del explorador depurable**. La página de error contiene instrucciones para ejecutar el explorador con el puerto de depuración abierto para que el proxy de depuración de Blazor se pueda conectar a la aplicación. *Cierre todas las instancias del explorador* y reinicie el explorador, tal como se indica.

Una vez que el explorador se ejecute con la depuración remota habilitada, el método abreviado de teclado de depuración abre una nueva pestaña del depurador. Tras unos instantes, en la pestaña **Orígenes** se mostrará una lista de los ensamblados .NET en la aplicación. Expanda cada ensamblado y busque los archivos de origen `.cs`/`.razor` disponibles para la depuración. Establezca puntos de interrupción, vuelva a la pestaña de la aplicación y los puntos de interrupción se alcanzarán cuando se ejecute el código. Una vez que se haya alcanzado un punto de interrupción, examine el código una vez (<kbd>F10</kbd>) o reanude (<kbd>F8</kbd>) la ejecución del código con normalidad.

Blazor proporciona un proxy de depuración que implementa el [protocolo Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) y aumenta el protocolo con información específica de .NET. Cuando se presiona el método abreviado de teclado de depuración, Blazor apunta a Chrome DevTools en el proxy. El proxy se conecta a la ventana del explorador que se quiere depurar (de ahí la necesidad de habilitar la depuración remota).

## <a name="browser-source-maps"></a>Mapas de origen del explorador

Los mapas de origen del explorador permiten al explorador volver a asignar los archivos compilados a sus archivos de código fuente originales y se suelen usar para la depuración del lado cliente. Sin embargo, en la actualidad Blazor no asigna C# directamente a JavaScript o WASM. En su lugar, Blazor realiza la interpretación de IL en el explorador, por lo que los mapas de origen no son pertinentes.

## <a name="troubleshoot"></a>Solucionar problemas

Si se encuentra con errores, las sugerencias siguientes pueden ser útiles:

* En la pestaña **Depurador**, abra las herramientas para desarrolladores en el explorador. En la consola, ejecute `localStorage.clear()` para quitar los puntos de interrupción.
* Confirme que instaló el certificado de desarrollo HTTPS de ASP.NET Core y que es de confianza. Para obtener más información, vea <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
