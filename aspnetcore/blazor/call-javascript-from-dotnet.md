---
title: Llamada a funciones de JavaScript con métodos de .NET en Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo invocar funciones de JavaScript con métodos de .NET en aplicaciones de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: de04992c3e3c7ce2dc73eee801484d5e3930fa3a
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102452"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a>Llamada a funciones de JavaScript con métodos de .NET en Blazor de ASP.NET Core

Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)

Una aplicación de Blazor puede invocar funciones de JavaScript desde métodos de .NET y viceversa. Estos escenarios se denominan *interoperabilidad de JavaScript* (o *interoperabilidad de JS*).

En este artículo se describe cómo invocar funciones de JavaScript desde .NET. Para más información sobre cómo llamar a métodos de .NET desde JavaScript, vea <xref:blazor/call-dotnet-from-javascript>.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

Para llamar a JavaScript desde .NET, use la abstracción <xref:Microsoft.JSInterop.IJSRuntime>. Para emitir llamadas de interoperabilidad de JS, inserte la abstracción <xref:Microsoft.JSInterop.IJSRuntime> en el componente. <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> toma un identificador de la función de JavaScript que se quiere invocar junto con un número cualquiera de argumentos serializables con JSON. El identificador de función es relativo al ámbito global (`window`). Si quiere llamar a `window.someScope.someFunction`, el identificador es `someScope.someFunction`. No es necesario registrar la función para poder llamarla. El tipo de valor devuelto `T` también debe ser serializable con JSON. `T` debe coincidir con el tipo de .NET que mejor asignación tenga con el tipo de JSON devuelto.

En el caso de las aplicaciones de servidor Blazor que tienen habilitada la representación previa, no se puede llamar a JavaScript durante la representación previa inicial. Las llamadas de interoperabilidad de JavaScript se deben aplazar hasta que se establezca la conexión con el explorador. Para más información, vea la sección [Detección de cuándo se está obteniendo una representación previa de una aplicación de servidor Blazor](#detect-when-a-blazor-server-app-is-prerendering).

El siguiente ejemplo se basa en [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), un descodificador basado en JavaScript. En el ejemplo se muestra cómo invocar una función de JavaScript desde un método de C# que descarga un requisito del código de desarrollador a una API de JavaScript existente. La función de JavaScript acepta una matriz de bytes procedente de un método de C#, la descodifica y devuelve el texto al componente para que lo muestre.

Dentro del elemento `<head>` de *wwwroot/index.html* (WebAssembly de Blazor) o *Pages/_Host.cshtml* (servidor Blazor), proporcione una función de JavaScript que use `TextDecoder` para descodificar una matriz que se ha pasado y devolver el valor descodificado:

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

El código de JavaScript, como el código que se muestra en el ejemplo anterior, también se puede cargar desde un archivo JavaScript ( *.js*) con una referencia al archivo de script:

```html
<script src="exampleJsInterop.js"></script>
```

El siguiente componente:

* Invoca la función de JavaScript `convertArray` mediante `JSRuntime` cuando se selecciona un botón de componente (**Convert Array**).
* Después de llamar a la función de JavaScript, la matriz que se ha pasado se convierte en una cadena, que se devuelve al componente para que la muestre.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

Para usar la abstracción <xref:Microsoft.JSInterop.IJSRuntime>, adopte cualquiera de los siguientes métodos:

* Inserte la abstracción <xref:Microsoft.JSInterop.IJSRuntime> en el componente de Razor ( *.razor*):

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  Dentro del elemento `<head>` de *wwwroot/index.html* (WebAssembly de Blazor) o *Pages/_Host.cshtml* (servidor Blazor), proporcione una función de JavaScript que use `handleTickerChanged`. Se llama a la función con <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> y no se devuelve un valor:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* Inyecte la abstracción <xref:Microsoft.JSInterop.IJSRuntime> en una clase ( *.cs*):

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  Dentro del elemento `<head>` de *wwwroot/index.html* (WebAssembly de Blazor) o *Pages/_Host.cshtml* (servidor Blazor), proporcione una función de JavaScript que use `handleTickerChanged`. Se llama a la función con `JSRuntime.InvokeAsync` y sí se devuelve un valor:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* Para generar contenido dinámico con [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use el atributo `[Inject]`:

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

En la aplicación de ejemplo del lado cliente de este tema hay dos funciones de JavaScript disponibles para la aplicación que interactúan con el DOM para recibir los datos proporcionados por el usuario y mostrar un mensaje de bienvenida:

* `showPrompt`: genera un mensaje para aceptar la entrada del usuario (el nombre del usuario) y devuelve dicho nombre al autor de la llamada.
* `displayWelcome`: asigna un mensaje de bienvenida del autor de la llamada a un objeto DOM con un `id` de `welcome`.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Coloque la etiqueta `<script>` que hace referencia al archivo JavaScript en el archivo *wwwroot/index.html* (WebAssembly de Blazor) o *Pages/_Host.cshtml* (servidor Blazor).

*wwwroot/index.html* (WebAssembly de Blazor):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

*Pages/_Host.cshtml* (servidor Blazor):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

No coloque una etiqueta `<script>` en un archivo de componente, porque la etiqueta `<script>` no se puede actualizar dinámicamente.

Los métodos de .NET interoperan con las funciones de JavaScript en el archivo *exampleJsInterop.js* llamando a <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.

La abstracción <xref:Microsoft.JSInterop.IJSRuntime> es asincrónica para dar cabida a escenarios de servidor Blazor. Si la aplicación es una aplicación de WebAssembly de Blazor y quiere invocar una función de JavaScript sincrónicamente, conviértala a un tipo heredado <xref:Microsoft.JSInterop.IJSInProcessRuntime> y llame a <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> en su lugar. Se recomienda que la mayoría de las bibliotecas de interoperabilidad de JS usen API asincrónicas para garantizar que esas bibliotecas van a estar disponibles en todos los escenarios.

La aplicación de ejemplo incluye un componente para mostrar la interoperabilidad de JS. El componente:

* Recibe la entrada del usuario a través de un mensaje de JavaScript.
* Devuelve el texto al componente para que lo procese.
* Llama a una segunda función de JavaScript que interactúa con el DOM para mostrar un mensaje de bienvenida.

*Pages/JSInterop.razor*:

```razor
@page "/JSInterop"
@using BlazorSample.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

1. Cuando `TriggerJsPrompt` se ejecuta seleccionando el botón **Trigger JavaScript Prompt** del componente, se llama a la función `showPrompt` de JavaScript proporcionada en el archivo *wwwroot/exampleJsInterop.js*.
1. La función `showPrompt` acepta la entrada del usuario (el nombre del usuario), que se codifica en HTML y se devuelve al componente. El componente almacena el nombre del usuario en una variable local, `name`.
1. La cadena almacenada en `name` se incorpora a un mensaje de bienvenida, que se pasa a una función de JavaScript, `displayWelcome`, que representa el mensaje de bienvenida en una etiqueta de encabezado.

## <a name="call-a-void-javascript-function"></a>Llamada a una función de JavaScript vacía

Las funciones de JavaScript que devuelven [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) o [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) se llaman con <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a>Detección de cuándo se está obteniendo una representación previa de una aplicación de servidor Blazor
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Captura de referencias a elementos

Algunos escenarios de interoperabilidad de JS requieren referencias a elementos HTML. Por ejemplo, una biblioteca de interfaz de usuario puede requerir una referencia de elemento para inicializarse, o puede que necesite llamar a API de tipo comando en un elemento, como `focus` o `play`.

Use el siguiente método para capturar referencias a elementos HTML en un componente:

* Agregue un atributo `@ref` al elemento HTML.
* Defina un campo de tipo <xref:Microsoft.AspNetCore.Components.ElementReference> cuyo nombre coincida con el valor del atributo `@ref`.

En el siguiente ejemplo se muestra la captura de una referencia al elemento `<input>` `username`:

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Use únicamente una referencia de elemento para mutar el contenido de un elemento vacío que no interactúa con Blazor. Este escenario es útil cuando una API de terceros proporciona contenido al elemento. Dado que Blazor no interactúa con el elemento, no existe ninguna posibilidad de que se produzca un conflicto entre la representación de Blazor del elemento y el DOM.
>
> En el siguiente ejemplo, es *peligroso* mutar el contenido de la lista sin ordenar (`ul`) porque Blazor interactúa con el DOM para rellenar los elementos de lista de este elemento (`<li>`):
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> Si la interoperabilidad de JS muta el contenido del elemento `MyList` y Blazor intenta realizar comparaciones con ese elemento, las comparaciones no coincidirán con el DOM.

En lo que respecta al código .NET, un elemento <xref:Microsoft.AspNetCore.Components.ElementReference> es un identificador opaco. *Lo único* que se puede hacer con <xref:Microsoft.AspNetCore.Components.ElementReference> es pasarlo a código de JavaScript a través de la interoperabilidad de JS. Al hacerlo, el código del lado JavaScript recibe una instancia de `HTMLElement`, que puede usar con las API de DOM habituales.

Por ejemplo, el siguiente código define un método de extensión de .NET que permite establecer el foco en un elemento:

*exampleJsInterop.js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Para llamar a una función de JavaScript que no devuelve un valor, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>. El siguiente código establece el foco en la entrada de nombre de usuario llamando a la función de JavaScript anterior con el elemento <xref:Microsoft.AspNetCore.Components.ElementReference> capturado:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Para usar un método de extensión, cree un método de extensión estático que reciba la instancia de <xref:Microsoft.JSInterop.IJSRuntime>:

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

Se llama al método `Focus` directamente en el objeto. En el siguiente ejemplo se da por hecho que el método `Focus` está disponible en el espacio de nombres `JsInteropClasses`:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> La variable `username` solo se rellena después de que el componente se represente. Si se pasa un elemento <xref:Microsoft.AspNetCore.Components.ElementReference> sin rellenar al código de JavaScript, el código de JavaScript recibe un valor `null`. Para manipular referencias de elemento una vez finalizada la representación del componente (para establecer el foco inicial en un elemento), use los [métodos de ciclo de vida del componente OnAfterRenderAsync u OnAfterRender](xref:blazor/components/lifecycle#after-component-render).

Cuando se trabaje con tipos genéricos y se devuelva un valor, use <xref:System.Threading.Tasks.ValueTask%601>:

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

Se llama al método `GenericMethod` directamente en el objeto con un tipo. En el siguiente ejemplo se da por hecho que el método `GenericMethod` está disponible en el espacio de nombres `JsInteropClasses`:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Referencia a elementos en componentes

La validez de un elemento <xref:Microsoft.AspNetCore.Components.ElementReference> solo está garantizada en el método de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> de un componente (y una referencia de elemento es un `struct`), por lo que una referencia de elemento no se puede pasar entre componentes.

Para que un componente primario haga que una referencia de elemento esté disponible para otros componentes, el componente primario puede:

* Permitir que los componentes secundarios registren devoluciones de llamada.
* Invoca las devoluciones de llamada registradas durante el evento <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> con la referencia de elemento que se ha pasado. Este método permite de forma indirecta que los componentes secundarios interactúen con la referencia del elemento primario.

En el siguiente ejemplo de WebAssembly de Blazor se ilustra el método.

En la etiqueta `<head>` de *wwwroot/index.html*:

```html
<style>
    .red { color: red }
</style>
```

En la etiqueta `<body>` de *wwwroot/index.html*:

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

*Pages/Index.razor* (componente primario):

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

*Pages/Index.razor.cs*:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

*Shared/SurveyPrompt.razor* (componente secundario):

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

*Shared/SurveyPrompt.razor.cs*:

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

## <a name="harden-js-interop-calls"></a>Protección de las llamadas de interoperabilidad de JS

La interoperabilidad de JS puede no funcionar debido a errores de red y debe tratarse como no confiable. De forma predeterminada, una aplicación de servidor Blazor agota el tiempo de espera de las llamadas de interoperabilidad de JS en el servidor transcurrido un minuto. Si una aplicación puede tolerar un tiempo de espera más restrictivo, establézcalo recurriendo a uno de los siguientes métodos:

* Globalmente en `Startup.ConfigureServices`, especifique el tiempo de espera:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Por cada invocación en el código de componente, una sola llamada puede especificar el tiempo de espera:

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Para más información sobre el agotamiento de recursos, vea <xref:blazor/security/server/threat-mitigation>.

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Evitar referencias de objetos circulares

Los objetos que contienen referencias circulares no se pueden serializar en el cliente para:

* Llamadas de método .NET.
* Llamadas de método JavaScript desde C# cuando el tipo de valor devuelto tiene referencias circulares.

Para más información, consulte los problemas siguientes:

* [Circular references are not supported, take two )](https://github.com/dotnet/aspnetcore/issues/20525) (No se admiten las referencias circulares, toma dos) (dotnet/aspnetcore #20525
* [Proposal: Add mechanism to handle circular references when serializing](https://github.com/dotnet/runtime/issues/30820) (Propuesta: agregar un mecanismo para controlar las referencias circulares al serializar) (dotnet/runtime #30820)

## <a name="additional-resources"></a>Recursos adicionales

* <xref:blazor/call-dotnet-from-javascript>
* [Ejemplo de InteropComponent.razor (repositorio de GitHub dotnet/AspNetCore, rama de la versión 3.1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Realización de transferencias de gran cantidad de datos en aplicaciones de servidor Blazor](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
