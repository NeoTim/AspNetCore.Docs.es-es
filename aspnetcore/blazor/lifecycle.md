---
title: Ciclo de vida de ASP.NET Core Blazor
author: guardrex
description: Obtenga información sobre cómo usar los métodos de ciclo de vida de los componentes Razor en aplicaciones ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: 831f575afa6ce11d06c016d43ecd1bb59d09eab6
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218913"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="ae732-103">Ciclo de vida de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="ae732-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="ae732-104">Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ae732-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="ae732-105">El marco de Blazor incluye métodos de ciclo de vida sincrónicos y asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="ae732-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="ae732-106">Invalide los métodos de ciclo de vida para realizar operaciones adicionales en los componentes durante la inicialización y representación de componentes.</span><span class="sxs-lookup"><span data-stu-id="ae732-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="ae732-107">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="ae732-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="ae732-108">Métodos de inicialización de componentes</span><span class="sxs-lookup"><span data-stu-id="ae732-108">Component initialization methods</span></span>

<span data-ttu-id="ae732-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> se invocan cuando se inicializa el componente después de haber recibido los parámetros iniciales de su componente primario.</span><span class="sxs-lookup"><span data-stu-id="ae732-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="ae732-110">Utilice `OnInitializedAsync` cuando el componente realice una operación asincrónica y deba actualizarse al completarse la operación.</span><span class="sxs-lookup"><span data-stu-id="ae732-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="ae732-111">En el caso de una operación sincrónica, invalide `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="ae732-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="ae732-112">Para realizar una operación asincrónica, invalide `OnInitializedAsync` y use la palabra clave `await` en la operación:</span><span class="sxs-lookup"><span data-stu-id="ae732-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="ae732-113">Las aplicaciones Blazor Server que [representan previamente su contenido](xref:blazor/hosting-model-configuration#render-mode) llaman a `OnInitializedAsync` **_dos veces_** :</span><span class="sxs-lookup"><span data-stu-id="ae732-113">Blazor Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="ae732-114">Una primera vez cuando el componente se representa inicialmente de forma estática como parte de la página.</span><span class="sxs-lookup"><span data-stu-id="ae732-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="ae732-115">Una segunda vez cuando el explorador establece una conexión de vuelta al servidor.</span><span class="sxs-lookup"><span data-stu-id="ae732-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="ae732-116">Para evitar que el código de desarrollador en `OnInitializedAsync` se ejecute dos veces, vea la sección [Reconexión con estado después de la representación previa](#stateful-reconnection-after-prerendering).</span><span class="sxs-lookup"><span data-stu-id="ae732-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="ae732-117">Durante la representación previa de una aplicación de Blazor Server, no es posible realizar ciertas acciones (como llamar a JavaScript), ya que no se ha establecido una conexión con el explorador.</span><span class="sxs-lookup"><span data-stu-id="ae732-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="ae732-118">Es posible que los componentes tengan que representarse de forma diferente cuando se representen previamente.</span><span class="sxs-lookup"><span data-stu-id="ae732-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="ae732-119">Para obtener más información, vea la sección [Detección de cuándo se está obteniendo una representación previa de una aplicación](#detect-when-the-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="ae732-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="ae732-120">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="ae732-120">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="ae732-121">Para obtener más información, vea la sección [Eliminación de componentes con IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="ae732-121">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="ae732-122">Antes de establecer los parámetros</span><span class="sxs-lookup"><span data-stu-id="ae732-122">Before parameters are set</span></span>

<span data-ttu-id="ae732-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> establece los parámetros que proporciona el elemento primario del componente en el árbol de representación:</span><span class="sxs-lookup"><span data-stu-id="ae732-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="ae732-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contiene todo el conjunto de valores de parámetro cada vez que se llama a `SetParametersAsync`.</span><span class="sxs-lookup"><span data-stu-id="ae732-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="ae732-125">La implementación predeterminada de `SetParametersAsync` establece el valor de cada propiedad con el atributo `[Parameter]` o `[CascadingParameter]`, que tiene un valor correspondiente en `ParameterView`.</span><span class="sxs-lookup"><span data-stu-id="ae732-125">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="ae732-126">Los parámetros que no tienen un valor correspondiente en `ParameterView` se dejan sin cambios.</span><span class="sxs-lookup"><span data-stu-id="ae732-126">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="ae732-127">Si no se invoca `base.SetParametersAync`, el código personalizado puede interpretar el valor de los parámetros entrantes de cualquier manera que sea necesaria.</span><span class="sxs-lookup"><span data-stu-id="ae732-127">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="ae732-128">Por ejemplo, no hay ningún requisito para asignar los parámetros entrantes a las propiedades de la clase.</span><span class="sxs-lookup"><span data-stu-id="ae732-128">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="ae732-129">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="ae732-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="ae732-130">Para obtener más información, vea la sección [Eliminación de componentes con IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="ae732-130">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="ae732-131">Después de establecer los parámetros</span><span class="sxs-lookup"><span data-stu-id="ae732-131">After parameters are set</span></span>

<span data-ttu-id="ae732-132">Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*>:</span><span class="sxs-lookup"><span data-stu-id="ae732-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="ae732-133">Cuando el componente se inicializa y ha recibido su primer conjunto de parámetros de su componente primario.</span><span class="sxs-lookup"><span data-stu-id="ae732-133">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="ae732-134">Cuando el componente primario vuelve a representarse y proporciona lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ae732-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="ae732-135">Solo se conocen tipos inmutables primitivos, de los que se ha cambiado por lo menos un parámetro.</span><span class="sxs-lookup"><span data-stu-id="ae732-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="ae732-136">Cualquier parámetro de tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="ae732-136">Any complex-typed parameters.</span></span> <span data-ttu-id="ae732-137">El marco no puede saber si los valores de un parámetro de tipo complejo se han transformado internamente, por lo que trata el conjunto de parámetros como modificado.</span><span class="sxs-lookup"><span data-stu-id="ae732-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="ae732-138">El trabajo asincrónico al aplicar parámetros y valores de propiedad debe producirse durante el evento de ciclo de vida de `OnParametersSetAsync`.</span><span class="sxs-lookup"><span data-stu-id="ae732-138">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="ae732-139">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="ae732-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="ae732-140">Para obtener más información, vea la sección [Eliminación de componentes con IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="ae732-140">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="ae732-141">Después de representar el componente</span><span class="sxs-lookup"><span data-stu-id="ae732-141">After component render</span></span>

<span data-ttu-id="ae732-142">Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> una vez que un componente haya terminado la representación.</span><span class="sxs-lookup"><span data-stu-id="ae732-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="ae732-143">En este momento, se rellenan las referencias a elementos y componentes.</span><span class="sxs-lookup"><span data-stu-id="ae732-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="ae732-144">Use esta fase para realizar pasos de inicialización adicionales mediante el contenido representado, como la activación de bibliotecas de JavaScript de terceros que operan en los elementos DOM representados.</span><span class="sxs-lookup"><span data-stu-id="ae732-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="ae732-145">El parámetro `firstRender` para `OnAfterRenderAsync` y `OnAfterRender`:</span><span class="sxs-lookup"><span data-stu-id="ae732-145">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="ae732-146">Se establece en `true` la primera vez que se representa la instancia del componente.</span><span class="sxs-lookup"><span data-stu-id="ae732-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="ae732-147">Se puede utilizar para garantizar que el trabajo de inicialización solo se realiza una vez.</span><span class="sxs-lookup"><span data-stu-id="ae732-147">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="ae732-148">El trabajo asincrónico inmediatamente después de la representación debe producirse durante el evento de ciclo de vida de `OnAfterRenderAsync`.</span><span class="sxs-lookup"><span data-stu-id="ae732-148">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="ae732-149">Incluso si se devuelve un elemento <xref:System.Threading.Tasks.Task> desde `OnAfterRenderAsync`, el marco no programa un ciclo de representación adicional para el componente una vez que la tarea se completa.</span><span class="sxs-lookup"><span data-stu-id="ae732-149">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="ae732-150">Esto se hace para evitar un bucle de representación infinito.</span><span class="sxs-lookup"><span data-stu-id="ae732-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="ae732-151">Es distinto del resto de métodos de ciclo de vida, los cuales programan un ciclo de representación adicional una vez se completa la tarea devuelta.</span><span class="sxs-lookup"><span data-stu-id="ae732-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="ae732-152">*No se llama a `OnAfterRender` y `OnAfterRenderAsync` al representarse previamente en el servidor.*</span><span class="sxs-lookup"><span data-stu-id="ae732-152">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="ae732-153">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="ae732-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="ae732-154">Para obtener más información, vea la sección [Eliminación de componentes con IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="ae732-154">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="ae732-155">Supresión de la actualización de la interfaz de usuario</span><span class="sxs-lookup"><span data-stu-id="ae732-155">Suppress UI refreshing</span></span>

<span data-ttu-id="ae732-156">Invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> para suprimir la actualización de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="ae732-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="ae732-157">Si la implementación devuelve `true`, la interfaz de usuario se actualiza:</span><span class="sxs-lookup"><span data-stu-id="ae732-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="ae732-158">Se llama a `ShouldRender` cada vez que se representa el componente.</span><span class="sxs-lookup"><span data-stu-id="ae732-158">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="ae732-159">Aunque se invalide `ShouldRender`, el componente siempre se representa inicialmente.</span><span class="sxs-lookup"><span data-stu-id="ae732-159">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="ae732-160">Cambios de estado</span><span class="sxs-lookup"><span data-stu-id="ae732-160">State changes</span></span>

<span data-ttu-id="ae732-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifica al componente que su estado ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="ae732-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="ae732-162">Cuando es aplicable, la llamada a `StateHasChanged` hace que el componente se represente.</span><span class="sxs-lookup"><span data-stu-id="ae732-162">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="ae732-163">Control de acciones asincrónicas incompletas en la representación</span><span class="sxs-lookup"><span data-stu-id="ae732-163">Handle incomplete async actions at render</span></span>

<span data-ttu-id="ae732-164">Es posible que las acciones asincrónicas realizadas en eventos de ciclo de vida no se hayan completado antes de que se represente el componente.</span><span class="sxs-lookup"><span data-stu-id="ae732-164">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="ae732-165">Los objetos podrían ser `null` o rellenarse de forma incompleta con datos mientras se ejecuta el método de ciclo de vida.</span><span class="sxs-lookup"><span data-stu-id="ae732-165">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="ae732-166">Proporcione lógica de representación para confirmar que los objetos se inicializan.</span><span class="sxs-lookup"><span data-stu-id="ae732-166">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="ae732-167">Represente los elementos de la interfaz de usuario del marcador de posición (por ejemplo, un mensaje de carga) mientras los objetos sean `null`.</span><span class="sxs-lookup"><span data-stu-id="ae732-167">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="ae732-168">En el componente `FetchData` de las plantillas de Blazor, `OnInitializedAsync` se invalida para recibir de forma asincrónica datos de previsión (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="ae732-168">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="ae732-169">Cuando `forecasts` es `null`, se muestra al usuario un mensaje de carga.</span><span class="sxs-lookup"><span data-stu-id="ae732-169">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="ae732-170">Una vez que se completa el elemento `Task` que devuelve `OnInitializedAsync`, el componente se representará con el estado actualizado.</span><span class="sxs-lookup"><span data-stu-id="ae732-170">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="ae732-171">*Pages/FetchData.razor* en la plantilla de Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="ae732-171">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="ae732-172">Eliminación de componentes con IDisposable</span><span class="sxs-lookup"><span data-stu-id="ae732-172">Component disposal with IDisposable</span></span>

<span data-ttu-id="ae732-173">Si un componente implementa <xref:System.IDisposable>, se llama al [método Dispose](/dotnet/standard/garbage-collection/implementing-dispose) cuando se quita el componente de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="ae732-173">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="ae732-174">El componente siguiente utiliza `@implements IDisposable` y el método `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="ae732-174">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="ae732-175">No se admite la llamada a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> en `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="ae732-175">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="ae732-176">`StateHasChanged` se puede invocar como parte de la desactivación del representador, por lo que no se admite la solicitud de actualizaciones de la interfaz de usuario en ese momento.</span><span class="sxs-lookup"><span data-stu-id="ae732-176">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="ae732-177">Cancele la suscripción de los controladores de eventos de .NET.</span><span class="sxs-lookup"><span data-stu-id="ae732-177">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="ae732-178">En los ejemplos de [formulario de Blazor](xref:blazor/forms-validation) siguientes se muestra cómo desenlazar un controlador de eventos en el método `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="ae732-178">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="ae732-179">Enfoque de campo privado y expresión lambda</span><span class="sxs-lookup"><span data-stu-id="ae732-179">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="ae732-180">Enfoque de método privado</span><span class="sxs-lookup"><span data-stu-id="ae732-180">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="ae732-181">Control de errores</span><span class="sxs-lookup"><span data-stu-id="ae732-181">Handle errors</span></span>

<span data-ttu-id="ae732-182">Para obtener información sobre cómo controlar los errores durante la ejecución del método de ciclo de vida, vea <xref:blazor/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="ae732-182">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="ae732-183">Reconexión con estado después de la representación previa</span><span class="sxs-lookup"><span data-stu-id="ae732-183">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="ae732-184">En una aplicación Blazor Server, cuando `RenderMode` es `ServerPrerendered`, el componente se representa inicialmente de forma estática como parte de la página.</span><span class="sxs-lookup"><span data-stu-id="ae732-184">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="ae732-185">Una vez que el explorador vuelve a establecer una conexión con el servidor, el componente se representa *otra vez* y el componente ahora es interactivo.</span><span class="sxs-lookup"><span data-stu-id="ae732-185">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="ae732-186">Si el método de ciclo de vida [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) para inicializar el componente está presente, el método se ejecuta *dos veces*:</span><span class="sxs-lookup"><span data-stu-id="ae732-186">If the [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="ae732-187">Cuando el componente se representa previamente de forma estática.</span><span class="sxs-lookup"><span data-stu-id="ae732-187">When the component is prerendered statically.</span></span>
* <span data-ttu-id="ae732-188">Después de establecerse la conexión con el servidor.</span><span class="sxs-lookup"><span data-stu-id="ae732-188">After the server connection has been established.</span></span>

<span data-ttu-id="ae732-189">Esto puede dar como resultado un cambio evidente en los datos mostrados en la interfaz de usuario cuando el componente se representa finalmente.</span><span class="sxs-lookup"><span data-stu-id="ae732-189">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="ae732-190">Para evitar el escenario de representación doble en una aplicación Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="ae732-190">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="ae732-191">Pase un identificador que se pueda usar para copiar en caché el estado durante la representación previa y recuperar el estado después de reiniciarse la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ae732-191">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="ae732-192">Use el identificador durante la representación previa para guardar el estado del componente.</span><span class="sxs-lookup"><span data-stu-id="ae732-192">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="ae732-193">Use el identificador después de la representación previa para recuperar el estado copiado en caché.</span><span class="sxs-lookup"><span data-stu-id="ae732-193">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="ae732-194">En el código siguiente se muestra un elemento `WeatherForecastService` actualizado en una aplicación Blazor Server basada en plantillas que evita la representación doble:</span><span class="sxs-lookup"><span data-stu-id="ae732-194">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="ae732-195">Para obtener más información sobre `RenderMode`, vea <xref:blazor/hosting-model-configuration#render-mode>.</span><span class="sxs-lookup"><span data-stu-id="ae732-195">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="ae732-196">Detección de cuándo se está obteniendo una representación previa de la aplicación</span><span class="sxs-lookup"><span data-stu-id="ae732-196">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]
