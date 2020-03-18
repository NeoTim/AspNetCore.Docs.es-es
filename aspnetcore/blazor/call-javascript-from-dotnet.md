---
title: Llamada a funciones de JavaScript con métodos de .NET en Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo invocar funciones de JavaScript con métodos de .NET en aplicaciones de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/19/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 7a27b6f1be2ef296d5b2b2a4f566e0cdedbe6480
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647525"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-opno-locblazor"></a><span data-ttu-id="60343-103">Llamada a funciones de JavaScript con métodos de .NET en Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="60343-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="60343-104">Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="60343-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="60343-105">Una aplicación de Blazor puede invocar funciones de JavaScript desde métodos de .NET y viceversa.</span><span class="sxs-lookup"><span data-stu-id="60343-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="60343-106">Estos escenarios se denominan *interoperabilidad de JavaScript* (o *interoperabilidad de JS*).</span><span class="sxs-lookup"><span data-stu-id="60343-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="60343-107">En este artículo se describe cómo invocar funciones de JavaScript desde .NET.</span><span class="sxs-lookup"><span data-stu-id="60343-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="60343-108">Para más información sobre cómo llamar a métodos de .NET desde JavaScript, vea <xref:blazor/call-dotnet-from-javascript>.</span><span class="sxs-lookup"><span data-stu-id="60343-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="60343-109">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="60343-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="60343-110">Para llamar a JavaScript desde .NET, use la abstracción `IJSRuntime`.</span><span class="sxs-lookup"><span data-stu-id="60343-110">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="60343-111">Para emitir llamadas de interoperabilidad de JS, inserte la abstracción `IJSRuntime` en el componente.</span><span class="sxs-lookup"><span data-stu-id="60343-111">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="60343-112">El método `InvokeAsync<T>` toma un identificador de la función de JavaScript que queremos invocar junto con un número cualquiera de argumentos serializables con JSON.</span><span class="sxs-lookup"><span data-stu-id="60343-112">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="60343-113">El identificador de función es relativo al ámbito global (`window`).</span><span class="sxs-lookup"><span data-stu-id="60343-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="60343-114">Si quiere llamar a `window.someScope.someFunction`, el identificador es `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="60343-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="60343-115">No es necesario registrar la función para poder llamarla.</span><span class="sxs-lookup"><span data-stu-id="60343-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="60343-116">El tipo de valor devuelto `T` también debe ser serializable con JSON.</span><span class="sxs-lookup"><span data-stu-id="60343-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="60343-117">`T` debe coincidir con el tipo de .NET que mejor asignación tenga con el tipo de JSON devuelto.</span><span class="sxs-lookup"><span data-stu-id="60343-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="60343-118">En el caso de las aplicaciones de servidor Blazor que tienen habilitada la representación previa, no se puede llamar a JavaScript durante la representación previa inicial.</span><span class="sxs-lookup"><span data-stu-id="60343-118">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="60343-119">Las llamadas de interoperabilidad de JavaScript se deben aplazar hasta que se establezca la conexión con el explorador.</span><span class="sxs-lookup"><span data-stu-id="60343-119">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="60343-120">Para más información, vea la sección [Detección de cuándo se está obteniendo una representación previa de una aplicación de servidor Blazor](#detect-when-a-blazor-server-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="60343-120">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="60343-121">El siguiente ejemplo se basa en [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), un descodificador basado en JavaScript.</span><span class="sxs-lookup"><span data-stu-id="60343-121">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="60343-122">En él se explica cómo invocar una función de JavaScript desde un método de C#.</span><span class="sxs-lookup"><span data-stu-id="60343-122">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="60343-123">La función de JavaScript acepta una matriz de bytes procedente de un método de C#, la descodifica y devuelve el texto al componente para que lo muestre.</span><span class="sxs-lookup"><span data-stu-id="60343-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="60343-124">Dentro del elemento `<head>` de *wwwroot/index.html* (WebAssembly de Blazor) o *Pages/_Host.cshtml* (servidor Blazor), proporcione una función de JavaScript que use `TextDecoder` para descodificar una matriz que se ha pasado y devolver el valor descodificado:</span><span class="sxs-lookup"><span data-stu-id="60343-124">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="60343-125">El código de JavaScript, como el código que se muestra en el ejemplo anterior, también se puede cargar desde un archivo JavaScript ( *.js*) con una referencia al archivo de script:</span><span class="sxs-lookup"><span data-stu-id="60343-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="60343-126">El siguiente componente:</span><span class="sxs-lookup"><span data-stu-id="60343-126">The following component:</span></span>

* <span data-ttu-id="60343-127">Invoca la función de JavaScript `convertArray` mediante `JSRuntime` cuando se selecciona un botón de componente (**Convert Array**).</span><span class="sxs-lookup"><span data-stu-id="60343-127">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="60343-128">Después de llamar a la función de JavaScript, la matriz que se ha pasado se convierte en una cadena,</span><span class="sxs-lookup"><span data-stu-id="60343-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="60343-129">que se devuelve al componente para que la muestre.</span><span class="sxs-lookup"><span data-stu-id="60343-129">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="60343-130">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="60343-130">IJSRuntime</span></span>

<span data-ttu-id="60343-131">Para usar la abstracción `IJSRuntime`, adopte cualquiera de los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="60343-131">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="60343-132">Inyecte la abstracción `IJSRuntime` en el componente de Razor ( *.razor*):</span><span class="sxs-lookup"><span data-stu-id="60343-132">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="60343-133">Dentro del elemento `<head>` de *wwwroot/index.html* (WebAssembly de Blazor) o *Pages/_Host.cshtml* (servidor Blazor), proporcione una función de JavaScript que use `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="60343-133">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="60343-134">Se llama a la función con `IJSRuntime.InvokeVoidAsync` y no se devuelve un valor:</span><span class="sxs-lookup"><span data-stu-id="60343-134">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="60343-135">Inyecte la abstracción `IJSRuntime` en una clase ( *.cs*):</span><span class="sxs-lookup"><span data-stu-id="60343-135">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="60343-136">Dentro del elemento `<head>` de *wwwroot/index.html* (WebAssembly de Blazor) o *Pages/_Host.cshtml* (servidor Blazor), proporcione una función de JavaScript que use `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="60343-136">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="60343-137">Se llama a la función con `JSRuntime.InvokeAsync` y sí se devuelve un valor:</span><span class="sxs-lookup"><span data-stu-id="60343-137">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="60343-138">Para generar contenido dinámico con [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use el atributo `[Inject]`:</span><span class="sxs-lookup"><span data-stu-id="60343-138">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="60343-139">En la aplicación de ejemplo del lado cliente de este tema hay dos funciones de JavaScript disponibles para la aplicación que interactúan con el DOM para recibir los datos proporcionados por el usuario y mostrar un mensaje de bienvenida:</span><span class="sxs-lookup"><span data-stu-id="60343-139">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="60343-140">`showPrompt`: genera un mensaje para aceptar la entrada del usuario (el nombre del usuario) y devuelve dicho nombre al autor de la llamada.</span><span class="sxs-lookup"><span data-stu-id="60343-140">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="60343-141">`displayWelcome`: asigna un mensaje de bienvenida del autor de la llamada a un objeto DOM con un `id` de `welcome`.</span><span class="sxs-lookup"><span data-stu-id="60343-141">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="60343-142">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="60343-142">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="60343-143">Coloque la etiqueta `<script>` que hace referencia al archivo JavaScript en el archivo *wwwroot/index.html* (WebAssembly de Blazor) o *Pages/_Host.cshtml* (servidor Blazor).</span><span class="sxs-lookup"><span data-stu-id="60343-143">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="60343-144">*wwwroot/index.html* (WebAssembly de Blazor):</span><span class="sxs-lookup"><span data-stu-id="60343-144">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="60343-145">*Pages/_Host.cshtml* (servidor Blazor):</span><span class="sxs-lookup"><span data-stu-id="60343-145">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="60343-146">No coloque una etiqueta `<script>` en un archivo de componente, porque la etiqueta `<script>` no se puede actualizar dinámicamente.</span><span class="sxs-lookup"><span data-stu-id="60343-146">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="60343-147">Los métodos de .NET interoperan con las funciones de JavaScript en el archivo *exampleJsInterop.js* llamando a `IJSRuntime.InvokeAsync<T>`.</span><span class="sxs-lookup"><span data-stu-id="60343-147">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="60343-148">La abstracción `IJSRuntime` es asincrónica para dar cabida a escenarios de servidor Blazor.</span><span class="sxs-lookup"><span data-stu-id="60343-148">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="60343-149">Si la aplicación es una aplicación de WebAssembly de Blazor y quiere invocar una función de JavaScript sincrónicamente, conviértala a un tipo heredado `IJSInProcessRuntime` y llame a `Invoke<T>` en su lugar.</span><span class="sxs-lookup"><span data-stu-id="60343-149">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="60343-150">Se recomienda que la mayoría de las bibliotecas de interoperabilidad de JS usen API asincrónicas para garantizar que esas bibliotecas van a estar disponibles en todos los escenarios.</span><span class="sxs-lookup"><span data-stu-id="60343-150">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="60343-151">La aplicación de ejemplo incluye un componente para mostrar la interoperabilidad de JS.</span><span class="sxs-lookup"><span data-stu-id="60343-151">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="60343-152">El componente:</span><span class="sxs-lookup"><span data-stu-id="60343-152">The component:</span></span>

* <span data-ttu-id="60343-153">Recibe la entrada del usuario a través de un mensaje de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="60343-153">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="60343-154">Devuelve el texto al componente para que lo procese.</span><span class="sxs-lookup"><span data-stu-id="60343-154">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="60343-155">Llama a una segunda función de JavaScript que interactúa con el DOM para mostrar un mensaje de bienvenida.</span><span class="sxs-lookup"><span data-stu-id="60343-155">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="60343-156">*Pages/JSInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="60343-156">*Pages/JSInterop.razor*:</span></span>

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

1. <span data-ttu-id="60343-157">Cuando `TriggerJsPrompt` se ejecuta seleccionando el botón **Trigger JavaScript Prompt** del componente, se llama a la función `showPrompt` de JavaScript proporcionada en el archivo *wwwroot/exampleJsInterop.js*.</span><span class="sxs-lookup"><span data-stu-id="60343-157">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="60343-158">La función `showPrompt` acepta la entrada del usuario (el nombre del usuario), que se codifica en HTML y se devuelve al componente.</span><span class="sxs-lookup"><span data-stu-id="60343-158">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="60343-159">El componente almacena el nombre del usuario en una variable local, `name`.</span><span class="sxs-lookup"><span data-stu-id="60343-159">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="60343-160">La cadena almacenada en `name` se incorpora a un mensaje de bienvenida, que se pasa a una función de JavaScript, `displayWelcome`, que representa el mensaje de bienvenida en una etiqueta de encabezado.</span><span class="sxs-lookup"><span data-stu-id="60343-160">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="60343-161">Llamada a una función de JavaScript vacía</span><span class="sxs-lookup"><span data-stu-id="60343-161">Call a void JavaScript function</span></span>

<span data-ttu-id="60343-162">Las funciones de JavaScript que devuelven [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) o [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) se llaman con `IJSRuntime.InvokeVoidAsync`.</span><span class="sxs-lookup"><span data-stu-id="60343-162">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-opno-locblazor-server-app-is-prerendering"></a><span data-ttu-id="60343-163">Detección de cuándo se está obteniendo una representación previa de una aplicación de servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="60343-163">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="60343-164">Captura de referencias a elementos</span><span class="sxs-lookup"><span data-stu-id="60343-164">Capture references to elements</span></span>

<span data-ttu-id="60343-165">Algunos escenarios de interoperabilidad de JS requieren referencias a elementos HTML.</span><span class="sxs-lookup"><span data-stu-id="60343-165">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="60343-166">Por ejemplo, una biblioteca de interfaz de usuario puede requerir una referencia de elemento para inicializarse, o puede que necesite llamar a API de tipo comando en un elemento, como `focus` o `play`.</span><span class="sxs-lookup"><span data-stu-id="60343-166">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="60343-167">Use el siguiente método para capturar referencias a elementos HTML en un componente:</span><span class="sxs-lookup"><span data-stu-id="60343-167">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="60343-168">Agregue un atributo `@ref` al elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="60343-168">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="60343-169">Defina un campo de tipo `ElementReference` cuyo nombre coincida con el valor del atributo `@ref`.</span><span class="sxs-lookup"><span data-stu-id="60343-169">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="60343-170">En el siguiente ejemplo se muestra la captura de una referencia al elemento `<input>` `username`:</span><span class="sxs-lookup"><span data-stu-id="60343-170">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="60343-171">Use únicamente una referencia de elemento para mutar el contenido de un elemento vacío que no interactúa con Blazor.</span><span class="sxs-lookup"><span data-stu-id="60343-171">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="60343-172">Este escenario es útil cuando una API de terceros proporciona contenido al elemento.</span><span class="sxs-lookup"><span data-stu-id="60343-172">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="60343-173">Dado que Blazor no interactúa con el elemento, no existe ninguna posibilidad de que se produzca un conflicto entre la representación de Blazor del elemento y el DOM.</span><span class="sxs-lookup"><span data-stu-id="60343-173">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="60343-174">En el siguiente ejemplo, es *peligroso* mutar el contenido de la lista sin ordenar (`ul`) porque Blazor interactúa con el DOM para rellenar los elementos de lista de este elemento (`<li>`):</span><span class="sxs-lookup"><span data-stu-id="60343-174">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="60343-175">Si la interoperabilidad de JS muta el contenido del elemento `MyList` y Blazor intenta realizar comparaciones con ese elemento, las comparaciones no coincidirán con el DOM.</span><span class="sxs-lookup"><span data-stu-id="60343-175">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="60343-176">En lo que respecta al código .NET, un elemento `ElementReference` es un identificador opaco.</span><span class="sxs-lookup"><span data-stu-id="60343-176">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="60343-177">*Lo único* que se puede hacer con `ElementReference` es pasarlo a código de JavaScript a través de la interoperabilidad de JS.</span><span class="sxs-lookup"><span data-stu-id="60343-177">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="60343-178">Al hacerlo, el código del lado JavaScript recibe una instancia de `HTMLElement`, que puede usar con las API de DOM habituales.</span><span class="sxs-lookup"><span data-stu-id="60343-178">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="60343-179">Por ejemplo, el siguiente código define un método de extensión de .NET que permite establecer el foco en un elemento:</span><span class="sxs-lookup"><span data-stu-id="60343-179">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="60343-180">*exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="60343-180">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="60343-181">Para llamar a una función de JavaScript que no devuelve un valor, use `IJSRuntime.InvokeVoidAsync`.</span><span class="sxs-lookup"><span data-stu-id="60343-181">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="60343-182">El siguiente código establece el foco en la entrada de nombre de usuario llamando a la función de JavaScript anterior con el elemento `ElementReference` capturado:</span><span class="sxs-lookup"><span data-stu-id="60343-182">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="60343-183">Para usar un método de extensión, cree un método de extensión estático que reciba la instancia de `IJSRuntime`:</span><span class="sxs-lookup"><span data-stu-id="60343-183">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="60343-184">Se llama al método `Focus` directamente en el objeto.</span><span class="sxs-lookup"><span data-stu-id="60343-184">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="60343-185">En el siguiente ejemplo se da por hecho que el método `Focus` está disponible en el espacio de nombres `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="60343-185">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="60343-186">La variable `username` solo se rellena después de que el componente se represente.</span><span class="sxs-lookup"><span data-stu-id="60343-186">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="60343-187">Si se pasa un elemento `ElementReference` sin rellenar al código de JavaScript, el código de JavaScript recibe un valor `null`.</span><span class="sxs-lookup"><span data-stu-id="60343-187">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="60343-188">Para manipular referencias de elemento una vez finalizada la representación del componente (para establecer el foco inicial en un elemento), use los [métodos de ciclo de vida del componente OnAfterRenderAsync u OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="60343-188">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="60343-189">Cuando se trabaje con tipos genéricos y se devuelva un valor, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span><span class="sxs-lookup"><span data-stu-id="60343-189">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="60343-190">Se llama al método `GenericMethod` directamente en el objeto con un tipo.</span><span class="sxs-lookup"><span data-stu-id="60343-190">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="60343-191">En el siguiente ejemplo se da por hecho que el método `GenericMethod` está disponible en el espacio de nombres `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="60343-191">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="60343-192">Referencia a elementos en componentes</span><span class="sxs-lookup"><span data-stu-id="60343-192">Reference elements across components</span></span>

<span data-ttu-id="60343-193">La validez de un elemento `ElementReference` solo está garantizada en el método de `OnAfterRender` de un componente (y una referencia de elemento es un `struct`), por lo que una referencia de elemento no se puede pasar entre componentes.</span><span class="sxs-lookup"><span data-stu-id="60343-193">An `ElementReference` is only guaranteed valid in a component's `OnAfterRender` method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="60343-194">Para que un componente primario haga que una referencia de elemento esté disponible para otros componentes, el componente primario puede:</span><span class="sxs-lookup"><span data-stu-id="60343-194">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="60343-195">Permitir que los componentes secundarios registren devoluciones de llamada.</span><span class="sxs-lookup"><span data-stu-id="60343-195">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="60343-196">Invoca las devoluciones de llamada registradas durante el evento `OnAfterRender` con la referencia de elemento que se ha pasado.</span><span class="sxs-lookup"><span data-stu-id="60343-196">Invoke the registered callbacks during the `OnAfterRender` event with the passed element reference.</span></span> <span data-ttu-id="60343-197">Este método permite de forma indirecta que los componentes secundarios interactúen con la referencia del elemento primario.</span><span class="sxs-lookup"><span data-stu-id="60343-197">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="60343-198">En el siguiente ejemplo de WebAssembly de Blazor se ilustra el método.</span><span class="sxs-lookup"><span data-stu-id="60343-198">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="60343-199">En la etiqueta `<head>` de *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="60343-199">In the `<head>` of *wwwroot/index.html*:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="60343-200">En la etiqueta `<body>` de *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="60343-200">In the `<body>` of *wwwroot/index.html*:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="60343-201">*Pages/Index.razor* (componente primario):</span><span class="sxs-lookup"><span data-stu-id="60343-201">*Pages/Index.razor* (parent component):</span></span>

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="60343-202">*Pages/Index.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="60343-202">*Pages/Index.razor.cs*:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool _disposing;
        private IList<IObserver<ElementReference>> _subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference _title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnNext(_title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            _disposing = true;

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            _subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (_disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            _subscriptions.Add(observer);

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
                Self._subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="60343-203">*Shared/SurveyPrompt.razor* (componente secundario):</span><span class="sxs-lookup"><span data-stu-id="60343-203">*Shared/SurveyPrompt.razor* (child component):</span></span>

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

<span data-ttu-id="60343-204">*Shared/SurveyPrompt.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="60343-204">*Shared/SurveyPrompt.razor.cs*:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable _subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (_subscription != null)
            {
                _subscription.Dispose();
            }

            _subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            _subscription = null;
        }

        public void OnError(Exception error)
        {
            _subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            _subscription?.Dispose();
        }
    }
}
```

## <a name="harden-js-interop-calls"></a><span data-ttu-id="60343-205">Protección de las llamadas de interoperabilidad de JS</span><span class="sxs-lookup"><span data-stu-id="60343-205">Harden JS interop calls</span></span>

<span data-ttu-id="60343-206">La interoperabilidad de JS puede no funcionar debido a errores de red y debe tratarse como no confiable.</span><span class="sxs-lookup"><span data-stu-id="60343-206">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="60343-207">De forma predeterminada, una aplicación de servidor Blazor agota el tiempo de espera de las llamadas de interoperabilidad de JS en el servidor transcurrido un minuto.</span><span class="sxs-lookup"><span data-stu-id="60343-207">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="60343-208">Si una aplicación puede tolerar un tiempo de espera más restrictivo, como 10 segundos, establézcalo recurriendo a uno de los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="60343-208">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="60343-209">Globalmente en `Startup.ConfigureServices`, especifique el tiempo de espera:</span><span class="sxs-lookup"><span data-stu-id="60343-209">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="60343-210">Por cada invocación en el código de componente, una sola llamada puede especificar el tiempo de espera:</span><span class="sxs-lookup"><span data-stu-id="60343-210">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="60343-211">Para más información sobre el agotamiento de recursos, vea <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="60343-211">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="additional-resources"></a><span data-ttu-id="60343-212">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="60343-212">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="60343-213">Ejemplo de InteropComponent.razor (repositorio de GitHub dotnet/AspNetCore, rama de la versión 3.1)</span><span class="sxs-lookup"><span data-stu-id="60343-213">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="60343-214">[Realización de transferencias de gran cantidad de datos en aplicaciones de servidor Blazor](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="60343-214">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
