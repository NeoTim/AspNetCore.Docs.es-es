---
title: Creación y uso de componentes de Razor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo crear y usar componentes de Razor, incluido cómo enlazar a datos, controlar eventos y administrar los ciclos de vida de los componentes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: e444ebfef5143a6c33ed2d122933903ad3a4f4a7
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648041"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="6802c-103">Creación y uso de componentes de Razor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6802c-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="6802c-104">Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="6802c-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="6802c-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6802c-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6802c-106">Las aplicaciones Blazor se crean usando *componentes*.</span><span class="sxs-lookup"><span data-stu-id="6802c-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="6802c-107">Un componente es un fragmento independiente de la interfaz de usuario, como una página, un cuadro de diálogo o un formulario.</span><span class="sxs-lookup"><span data-stu-id="6802c-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="6802c-108">Los componentes incluyen el marcado HTML y la lógica de procesamiento necesarios para insertar datos o responder a eventos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="6802c-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="6802c-109">Además, son flexibles y ligeros,</span><span class="sxs-lookup"><span data-stu-id="6802c-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="6802c-110">y se pueden anidar, reutilizar y compartir entre proyectos.</span><span class="sxs-lookup"><span data-stu-id="6802c-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="6802c-111">Clases de componentes</span><span class="sxs-lookup"><span data-stu-id="6802c-111">Component classes</span></span>

<span data-ttu-id="6802c-112">Los componentes se implementan en archivos de componentes de [Razor](xref:mvc/views/razor) ( *.razor*) usando una combinación de C# y marcado HTML.</span><span class="sxs-lookup"><span data-stu-id="6802c-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="6802c-113">Un componente en Blazor se conoce formalmente como *componente de Razor*.</span><span class="sxs-lookup"><span data-stu-id="6802c-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="6802c-114">El nombre de un componente debe empezar por mayúsculas.</span><span class="sxs-lookup"><span data-stu-id="6802c-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="6802c-115">Por ejemplo, *MyCoolComponent.razor* es válido, mientras que *myCoolComponent.razor* no.</span><span class="sxs-lookup"><span data-stu-id="6802c-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="6802c-116">La interfaz de usuario de un componente se define mediante HTML.</span><span class="sxs-lookup"><span data-stu-id="6802c-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="6802c-117">La lógica de la representación dinámica (por ejemplo, bucles, instrucciones condicionales, expresiones) se agrega mediante una sintaxis de C# insertada denominada [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="6802c-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="6802c-118">Cuando una aplicación se compila, el marcado HTML y la lógica de representación de C# se convierten en una clase de componente.</span><span class="sxs-lookup"><span data-stu-id="6802c-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="6802c-119">El nombre de la clase generada coincide con el nombre del archivo.</span><span class="sxs-lookup"><span data-stu-id="6802c-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="6802c-120">Los miembros de la clase de componente se definen en un bloque `@code`.</span><span class="sxs-lookup"><span data-stu-id="6802c-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="6802c-121">En el bloque `@code`, el estado del componente (propiedades, campos) se especifica con métodos de control de eventos o con objeto de definir otra lógica de componente.</span><span class="sxs-lookup"><span data-stu-id="6802c-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="6802c-122">Se permite emplear más de un bloque `@code`.</span><span class="sxs-lookup"><span data-stu-id="6802c-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="6802c-123">Los miembros de un componente se pueden usar como parte de la lógica de representación del componente mediante expresiones de C# que comienzan por `@`.</span><span class="sxs-lookup"><span data-stu-id="6802c-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="6802c-124">Por ejemplo, un campo de C# se representa anteponiendo el prefijo `@` al nombre del campo.</span><span class="sxs-lookup"><span data-stu-id="6802c-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="6802c-125">En el siguiente ejemplo se evalúa y representa lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="6802c-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="6802c-126">`_headingFontStyle` del valor de la propiedad CSS de `font-style`</span><span class="sxs-lookup"><span data-stu-id="6802c-126">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="6802c-127">`_headingText` del contenido del elemento `<h1>`</span><span class="sxs-lookup"><span data-stu-id="6802c-127">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="6802c-128">Una vez que el componente se ha representado inicialmente, este vuelve a generar su árbol de representación en respuesta a eventos.</span><span class="sxs-lookup"><span data-stu-id="6802c-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="6802c-129"> compara el nuevo árbol de representación con el anterior y aplica las modificaciones necesarias al explorador de Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="6802c-129"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="6802c-130">Los componentes son clases de C# normales, y se pueden colocar en cualquier parte dentro de un proyecto.</span><span class="sxs-lookup"><span data-stu-id="6802c-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="6802c-131">Los componentes que generan páginas web suelen residir en la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="6802c-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="6802c-132">Los componentes que no son de página colocan con frecuencia en la carpeta *Shared* o en una carpeta personalizada agregada al proyecto.</span><span class="sxs-lookup"><span data-stu-id="6802c-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="6802c-133">Por lo general, el espacio de nombres de un componente se deriva del espacio de nombres raíz de la aplicación y de la ubicación del componente (carpeta) dentro de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6802c-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="6802c-134">Así, si el espacio de nombres raíz de la aplicación es `BlazorApp` y el componente `Counter` reside en la carpeta *Pages*:</span><span class="sxs-lookup"><span data-stu-id="6802c-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="6802c-135">El espacio de nombres del componente `Counter` es `BlazorApp.Pages`.</span><span class="sxs-lookup"><span data-stu-id="6802c-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="6802c-136">El nombre de tipo completo del componente es `BlazorApp.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="6802c-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="6802c-137">Para más información, vea la sección [Importación de componentes](#import-components).</span><span class="sxs-lookup"><span data-stu-id="6802c-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="6802c-138">Para usar una carpeta personalizada, agregue el espacio de nombres de la carpeta personalizada al componente primario o al archivo *_Imports.razor* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6802c-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="6802c-139">Por ejemplo, el siguiente espacio de nombres hace que los componentes incluidos en una carpeta *Components* estén disponibles cuando el espacio de nombres raíz de la aplicación es `BlazorApp`:</span><span class="sxs-lookup"><span data-stu-id="6802c-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a><span data-ttu-id="6802c-140">Recursos estáticos</span><span class="sxs-lookup"><span data-stu-id="6802c-140">Static assets</span></span>

Blazor<span data-ttu-id="6802c-141"> sigue la convención de las aplicaciones de ASP.NET Core consistente en colocar los activos estáticos en la [carpeta de raíz web (wwwroot)](xref:fundamentals/index#web-root) del proyecto.</span><span class="sxs-lookup"><span data-stu-id="6802c-141"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="6802c-142">Use una ruta de acceso relativa base (`/`) para hacer referencia a la raíz web de un activo estático.</span><span class="sxs-lookup"><span data-stu-id="6802c-142">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="6802c-143">En el siguiente ejemplo, *logo.png* se encuentra físicamente en la carpeta *{RAÍZ DEL PROYECTO}/wwwroot/images*:</span><span class="sxs-lookup"><span data-stu-id="6802c-143">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="6802c-144">Los componentes de Razor **no** admiten la notación de tilde de la ñ-barra diagonal (`~/`).</span><span class="sxs-lookup"><span data-stu-id="6802c-144">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="6802c-145">Para más información sobre cómo establecer la ruta de acceso base de una aplicación, vea <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="6802c-145">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="6802c-146">Las aplicaciones auxiliares de etiquetas no se admiten en los componentes</span><span class="sxs-lookup"><span data-stu-id="6802c-146">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="6802c-147">Las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) no se pueden usar en los componentes de Razor (archivos *.razor*).</span><span class="sxs-lookup"><span data-stu-id="6802c-147">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="6802c-148">Para proporcionar una funcionalidad similar a la de las aplicaciones auxiliares de etiquetas en Blazor, cree un componente con la misma funcionalidad que la aplicación auxiliar de etiquetas y use el componente en su lugar.</span><span class="sxs-lookup"><span data-stu-id="6802c-148">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="6802c-149">Uso de componentes</span><span class="sxs-lookup"><span data-stu-id="6802c-149">Use components</span></span>

<span data-ttu-id="6802c-150">Para que los componentes puedan incluir otros componentes, hay que declararlos usando la sintaxis de elementos HTML.</span><span class="sxs-lookup"><span data-stu-id="6802c-150">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="6802c-151">El marcado para utilizar un componente se parece a una etiqueta HTML en la que el nombre de la etiqueta es el tipo de componente.</span><span class="sxs-lookup"><span data-stu-id="6802c-151">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="6802c-152">El enlace de atributos distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="6802c-152">Attribute binding is case sensitive.</span></span> <span data-ttu-id="6802c-153">Por ejemplo, `@bind` no es válido, pero `@Bind` sí.</span><span class="sxs-lookup"><span data-stu-id="6802c-153">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="6802c-154">El siguiente marcado en *Index.razor* representa una instancia de `HeadingComponent`:</span><span class="sxs-lookup"><span data-stu-id="6802c-154">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="6802c-155">*Components/HeadingComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="6802c-155">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="6802c-156">Si un componente contiene un elemento HTML cuyo nombre empieza por mayúsculas y no coincide con un nombre de componente, se emite una advertencia que indica que el elemento tiene un nombre inesperado.</span><span class="sxs-lookup"><span data-stu-id="6802c-156">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="6802c-157">Si se agrega una directiva `@using` relativa al espacio de nombres del componente, esto permite que el componente esté disponible, lo que resuelve la advertencia.</span><span class="sxs-lookup"><span data-stu-id="6802c-157">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="6802c-158">Enrutamiento</span><span class="sxs-lookup"><span data-stu-id="6802c-158">Routing</span></span>

<span data-ttu-id="6802c-159">El enrutamiento en Blazor se consigue proporcionando una plantilla de ruta a cada componente accesible en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6802c-159">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="6802c-160">Cuando se compila un archivo de Razor con una directiva `@page`, la clase generada recibe un elemento <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>, donde se especifica la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="6802c-160">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="6802c-161">En tiempo de ejecución, el enrutador busca las clases de componentes con un atributo `RouteAttribute`, y representa el componente que tenga una plantilla de ruta que coincida con la dirección URL solicitada.</span><span class="sxs-lookup"><span data-stu-id="6802c-161">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="6802c-162">Para obtener más información, vea <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="6802c-162">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="6802c-163">Parámetros</span><span class="sxs-lookup"><span data-stu-id="6802c-163">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="6802c-164">Parámetros de ruta</span><span class="sxs-lookup"><span data-stu-id="6802c-164">Route parameters</span></span>

<span data-ttu-id="6802c-165">Los componentes pueden recibir parámetros de ruta de la plantilla de ruta proporcionada en la directiva `@page`.</span><span class="sxs-lookup"><span data-stu-id="6802c-165">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="6802c-166">El enrutador usa parámetros de ruta para rellenar los parámetros de componente correspondientes con el mismo nombre.</span><span class="sxs-lookup"><span data-stu-id="6802c-166">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="6802c-167">*Pages/RouteParameter.razor*:</span><span class="sxs-lookup"><span data-stu-id="6802c-167">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="6802c-168">Los parámetros opcionales no se admiten, por lo que en el ejemplo anterior se aplican dos directivas `@page`.</span><span class="sxs-lookup"><span data-stu-id="6802c-168">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="6802c-169">La primera permite navegar al componente sin un parámetro,</span><span class="sxs-lookup"><span data-stu-id="6802c-169">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="6802c-170">mientras que la segunda recibe el parámetro de ruta `{text}` y asigna el valor a la propiedad `Text`.</span><span class="sxs-lookup"><span data-stu-id="6802c-170">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="6802c-171">Los componentes de Razor ( *.razor*) **no** admiten la sintaxis de parámetro *catch-all* (`*`/`**`), que captura la ruta de acceso a lo largo de varios límites de carpeta.</span><span class="sxs-lookup"><span data-stu-id="6802c-171">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="6802c-172">Parámetros del componente</span><span class="sxs-lookup"><span data-stu-id="6802c-172">Component parameters</span></span>

<span data-ttu-id="6802c-173">Los componentes pueden tener *parámetros de componente*, que se definen por medio de propiedades públicas en la clase del componente con el atributo `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="6802c-173">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="6802c-174">Use atributos para especificar argumentos para un componente en el marcado.</span><span class="sxs-lookup"><span data-stu-id="6802c-174">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="6802c-175">*Components/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="6802c-175">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="6802c-176">En el siguiente ejemplo de la aplicación de muestra, `ParentComponent` establece el valor de la propiedad `Title` de `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="6802c-176">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="6802c-177">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="6802c-177">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="6802c-178">Contenido secundario</span><span class="sxs-lookup"><span data-stu-id="6802c-178">Child content</span></span>

<span data-ttu-id="6802c-179">Los componentes pueden definir el contenido de otro componente.</span><span class="sxs-lookup"><span data-stu-id="6802c-179">Components can set the content of another component.</span></span> <span data-ttu-id="6802c-180">El componente que asigna proporciona el contenido entre las etiquetas que especifican el componente receptor.</span><span class="sxs-lookup"><span data-stu-id="6802c-180">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="6802c-181">En el siguiente ejemplo, `ChildComponent` tiene una propiedad `ChildContent` que representa un elemento `RenderFragment`, que representa a su vez un segmento de interfaz de usuario que se va a representar.</span><span class="sxs-lookup"><span data-stu-id="6802c-181">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="6802c-182">El valor de `ChildContent` se coloca en el marcado del componente donde el contenido debe representarse.</span><span class="sxs-lookup"><span data-stu-id="6802c-182">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="6802c-183">El valor de `ChildContent` se recibe desde el componente primario, y se representa dentro del elemento `panel-body` del panel de arranque.</span><span class="sxs-lookup"><span data-stu-id="6802c-183">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="6802c-184">*Components/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="6802c-184">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="6802c-185">La propiedad que recibe el contenido de `RenderFragment` debe denominarse `ChildContent` por convención.</span><span class="sxs-lookup"><span data-stu-id="6802c-185">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="6802c-186">El elemento `ParentComponent` de la aplicación de muestra puede proporcionar contenido para representar el elemento `ChildComponent` colocando el contenido dentro de las etiquetas `<ChildComponent>`.</span><span class="sxs-lookup"><span data-stu-id="6802c-186">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="6802c-187">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="6802c-187">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="6802c-188">Expansión de atributos y parámetros arbitrarios</span><span class="sxs-lookup"><span data-stu-id="6802c-188">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="6802c-189">Los componentes pueden capturar y representar más atributos aparte de los parámetros declarados del componente.</span><span class="sxs-lookup"><span data-stu-id="6802c-189">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="6802c-190">Estos atributos extra se pueden capturar en un diccionario y, después, *expandirse* en un elemento cuando el componente se represente por medio de la directiva de Razor [`@attributes`](xref:mvc/views/razor#attributes).</span><span class="sxs-lookup"><span data-stu-id="6802c-190">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="6802c-191">Este escenario es útil cuando se define un componente que genera un elemento de marcado que admite diversas personalizaciones.</span><span class="sxs-lookup"><span data-stu-id="6802c-191">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="6802c-192">Por ejemplo, definir atributos por separado para un elemento `<input>` que admite muchos parámetros puede ser un engorro.</span><span class="sxs-lookup"><span data-stu-id="6802c-192">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="6802c-193">En el siguiente ejemplo, el primer elemento `<input>` (`id="useIndividualParams"`) usa parámetros de componente individuales, mientras que el segundo elemento `<input>` (`id="useAttributesDict"`) usa la expansión de atributos:</span><span class="sxs-lookup"><span data-stu-id="6802c-193">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="6802c-194">El tipo del parámetro debe implementar `IEnumerable<KeyValuePair<string, object>>` con claves de cadena.</span><span class="sxs-lookup"><span data-stu-id="6802c-194">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="6802c-195">El uso de `IReadOnlyDictionary<string, object>` también es una opción en este escenario.</span><span class="sxs-lookup"><span data-stu-id="6802c-195">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="6802c-196">Los elementos `<input>` representados con ambos métodos son idénticos:</span><span class="sxs-lookup"><span data-stu-id="6802c-196">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="6802c-197">Para aceptar atributos arbitrarios, defina un parámetro de componente usando el atributo `[Parameter]` con la propiedad `CaptureUnmatchedValues` establecida en `true`:</span><span class="sxs-lookup"><span data-stu-id="6802c-197">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="6802c-198">La propiedad `CaptureUnmatchedValues` en `[Parameter]` permite que el parámetro coincida con todos los atributos que no coinciden con ningún otro parámetro.</span><span class="sxs-lookup"><span data-stu-id="6802c-198">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="6802c-199">Un componente solamente puede definir un parámetro con `CaptureUnmatchedValues`.</span><span class="sxs-lookup"><span data-stu-id="6802c-199">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="6802c-200">El tipo de propiedad que se usa con `CaptureUnmatchedValues` se debe poder asignar desde `Dictionary<string, object>` con claves de cadena.</span><span class="sxs-lookup"><span data-stu-id="6802c-200">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="6802c-201">`IEnumerable<KeyValuePair<string, object>>` o `IReadOnlyDictionary<string, object>` también son opciones en este escenario.</span><span class="sxs-lookup"><span data-stu-id="6802c-201">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="6802c-202">La posición de `@attributes` relativa a la posición de los atributos de elemento es importante.</span><span class="sxs-lookup"><span data-stu-id="6802c-202">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="6802c-203">Cuando `@attributes` se expande en el elemento, los atributos se procesan de derecha a izquierda (de último a primero).</span><span class="sxs-lookup"><span data-stu-id="6802c-203">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="6802c-204">Veamos el siguiente ejemplo de un componente que usa un componente de `Child`:</span><span class="sxs-lookup"><span data-stu-id="6802c-204">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="6802c-205">*ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="6802c-205">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="6802c-206">*ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="6802c-206">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="6802c-207">El atributo `extra` del componente de `Child` se establece a la derecha de `@attributes`.</span><span class="sxs-lookup"><span data-stu-id="6802c-207">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="6802c-208">El elemento `<div>` representado del componente `Parent` contiene `extra="5"` cuando se pasa a través del atributo adicional, ya que los atributos se procesan de derecha a izquierda (de último a primero):</span><span class="sxs-lookup"><span data-stu-id="6802c-208">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="6802c-209">En el siguiente ejemplo, el orden de `extra` y `@attributes` se invierte en el elemento `<div>` del componente `Child`:</span><span class="sxs-lookup"><span data-stu-id="6802c-209">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="6802c-210">*ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="6802c-210">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="6802c-211">*ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="6802c-211">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="6802c-212">El elemento `<div>` representado en el componente `Parent` contiene `extra="10"` cuando se pasa a través del atributo adicional:</span><span class="sxs-lookup"><span data-stu-id="6802c-212">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="6802c-213">Captura de referencias a componentes</span><span class="sxs-lookup"><span data-stu-id="6802c-213">Capture references to components</span></span>

<span data-ttu-id="6802c-214">Las referencias de componentes son una forma de hacer referencia a una instancia de componente para poder emitir comandos a dicha instancia, como `Show` o `Reset`.</span><span class="sxs-lookup"><span data-stu-id="6802c-214">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="6802c-215">Para capturar una referencia de componente:</span><span class="sxs-lookup"><span data-stu-id="6802c-215">To capture a component reference:</span></span>

* <span data-ttu-id="6802c-216">Agregue un atributo [`@ref`](xref:mvc/views/razor#ref) al componente secundario.</span><span class="sxs-lookup"><span data-stu-id="6802c-216">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="6802c-217">Defina un campo con el mismo tipo que el componente secundario.</span><span class="sxs-lookup"><span data-stu-id="6802c-217">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="_loginDialog" ... />

@code {
    private MyLoginDialog _loginDialog;

    private void OnSomething()
    {
        _loginDialog.Show();
    }
}
```

<span data-ttu-id="6802c-218">Cuando el componente se represente, el campo `_loginDialog` se rellena con la instancia del componente secundario `MyLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="6802c-218">When the component is rendered, the `_loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="6802c-219">Tras ello, se pueden invocar métodos de .NET en la instancia del componente.</span><span class="sxs-lookup"><span data-stu-id="6802c-219">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6802c-220">La variable `_loginDialog` solo se rellena después de que el componente se represente, y su salida incluye el elemento `MyLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="6802c-220">The `_loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="6802c-221">Hasta ese momento, no hay nada a lo que hacer referencia.</span><span class="sxs-lookup"><span data-stu-id="6802c-221">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="6802c-222">Para manipular las referencias de componente una vez finalizada la representación del componente, use los [métodos OnAfterRenderAsync u OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="6802c-222">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="6802c-223">Si bien la captura de referencias de componentes emplea una sintaxis similar a la de la [captura de referencias de elementos](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), no es una característica de interoperabilidad de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6802c-223">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="6802c-224">Las referencias de componentes no se pasan a código de JavaScript; se usan única y exclusivamente en código de .NET.</span><span class="sxs-lookup"><span data-stu-id="6802c-224">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="6802c-225">**No** use referencias de componentes para mutar el estado de los componentes secundarios.</span><span class="sxs-lookup"><span data-stu-id="6802c-225">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="6802c-226">En su lugar, use parámetros declarativos normales para pasar datos a componentes secundarios.</span><span class="sxs-lookup"><span data-stu-id="6802c-226">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="6802c-227">El uso de parámetros declarativos normales da como resultado componentes secundarios que se representarán automáticamente justo cuando corresponda.</span><span class="sxs-lookup"><span data-stu-id="6802c-227">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="6802c-228">Invocación de métodos de componentes externamente para actualizar el estado</span><span class="sxs-lookup"><span data-stu-id="6802c-228">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="6802c-229"> usa un elemento `SynchronizationContext` para aplicar un único subproceso lógico de ejecución.</span><span class="sxs-lookup"><span data-stu-id="6802c-229"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="6802c-230">Los [métodos de ciclo de vida](xref:blazor/lifecycle) de un componente y todas las devoluciones de llamada de eventos que Blazor genere se ejecutan en dicho elemento `SynchronizationContext`.</span><span class="sxs-lookup"><span data-stu-id="6802c-230">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="6802c-231">En caso de que un componente deba actualizarse en función de un evento externo, como un temporizador u otras notificaciones, use el método `InvokeAsync`, que enviará al elemento `SynchronizationContext` de Blazor.</span><span class="sxs-lookup"><span data-stu-id="6802c-231">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="6802c-232">Consideremos, por ejemplo, un *servicio de notificador* capaz de notificar el estado actualizado a cualquier componente de escucha:</span><span class="sxs-lookup"><span data-stu-id="6802c-232">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

<span data-ttu-id="6802c-233">Registre el elemento `NotifierService` como un singleton:</span><span class="sxs-lookup"><span data-stu-id="6802c-233">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="6802c-234">En WebAssembly de Blazor, registre el servicio en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="6802c-234">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="6802c-235">En el servidor Blazor, registre el servicio en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6802c-235">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="6802c-236">Use el elemento `NotifierService` para actualizar un componente:</span><span class="sxs-lookup"><span data-stu-id="6802c-236">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @_lastNotification.key = @_lastNotification.value</p>

@code {
    private (string key, int value) _lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            _lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="6802c-237">En el ejemplo anterior, `NotifierService` invoca el método de `OnNotify` del componente fuera del elemento `SynchronizationContext` de Blazor.</span><span class="sxs-lookup"><span data-stu-id="6802c-237">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="6802c-238">`InvokeAsync` se utiliza para cambiar al contexto correcto y poner una representación en cola.</span><span class="sxs-lookup"><span data-stu-id="6802c-238">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="6802c-239">Uso de \@key para controlar la conservación de elementos y componentes</span><span class="sxs-lookup"><span data-stu-id="6802c-239">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="6802c-240">Cuando se representa una lista de elementos o componentes, y esos elementos o componentes cambian posteriormente, el algoritmo de comparación de Blazor debe decidir cuáles de los elementos o componentes anteriores se pueden conservar y cómo asignar objetos de modelo a ellos.</span><span class="sxs-lookup"><span data-stu-id="6802c-240">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="6802c-241">Normalmente, este proceso es automático y se puede pasar por alto, pero hay casos en los que puede que queramos controlarlo.</span><span class="sxs-lookup"><span data-stu-id="6802c-241">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="6802c-242">Considere el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="6802c-242">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="6802c-243">El contenido de la colección `People` puede cambiar porque se inserten, eliminen o reordenen entradas.</span><span class="sxs-lookup"><span data-stu-id="6802c-243">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="6802c-244">Cuando el componente se representa, el componente `<DetailsEditor>` puede cambiar para recibir diferentes valores de parámetro `Details`.</span><span class="sxs-lookup"><span data-stu-id="6802c-244">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="6802c-245">Esto puede hacer que se genere una nueva representación más compleja de lo esperado.</span><span class="sxs-lookup"><span data-stu-id="6802c-245">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="6802c-246">En algunos casos, la nueva representación puede producir diferencias de comportamiento visibles, como la pérdida de foco de un elemento.</span><span class="sxs-lookup"><span data-stu-id="6802c-246">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="6802c-247">El proceso de asignación se puede controlar con el atributo de directiva `@key`.</span><span class="sxs-lookup"><span data-stu-id="6802c-247">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="6802c-248">`@key` hace que el algoritmo de comparación de componentes garantice la conservación de elementos o componentes en función del valor de la clave:</span><span class="sxs-lookup"><span data-stu-id="6802c-248">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="6802c-249">Cuando la colección `People` cambia, el algoritmo de comparación mantiene la asociación entre las instancias de `<DetailsEditor>` y las instancias de `person`:</span><span class="sxs-lookup"><span data-stu-id="6802c-249">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="6802c-250">Si un elemento `Person` se elimina de la lista `People`, solo se quitará de la interfaz de usuario la instancia de `<DetailsEditor>` correspondiente.</span><span class="sxs-lookup"><span data-stu-id="6802c-250">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="6802c-251">Las demás instancias permanecerán inalteradas.</span><span class="sxs-lookup"><span data-stu-id="6802c-251">Other instances are left unchanged.</span></span>
* <span data-ttu-id="6802c-252">Si se inserta un elemento `Person` en una posición dentro de la lista, se insertará una nueva instancia de `<DetailsEditor>` en la posición correspondiente.</span><span class="sxs-lookup"><span data-stu-id="6802c-252">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="6802c-253">Las demás instancias permanecerán inalteradas.</span><span class="sxs-lookup"><span data-stu-id="6802c-253">Other instances are left unchanged.</span></span>
* <span data-ttu-id="6802c-254">Si las entradas `Person` se reordenan, las instancias de `<DetailsEditor>` correspondientes se conservarán y reordenarán en la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="6802c-254">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="6802c-255">En algunos escenarios, el uso de `@key` reduce la complejidad de la nueva representación y evita posibles problemas con las partes con estado del DOM que cambia, como la posición del foco.</span><span class="sxs-lookup"><span data-stu-id="6802c-255">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6802c-256">Las claves son locales de cada componente o elemento contenedor.</span><span class="sxs-lookup"><span data-stu-id="6802c-256">Keys are local to each container element or component.</span></span> <span data-ttu-id="6802c-257">Las claves no se comparan globalmente en todo el documento.</span><span class="sxs-lookup"><span data-stu-id="6802c-257">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="6802c-258">Cuándo usar \@key</span><span class="sxs-lookup"><span data-stu-id="6802c-258">When to use \@key</span></span>

<span data-ttu-id="6802c-259">Normalmente, usar `@key` tiene sentido cada vez que una lista se represente (por ejemplo, en un bloque `@foreach`) y haya un valor adecuado para definir el elemento `@key`.</span><span class="sxs-lookup"><span data-stu-id="6802c-259">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="6802c-260">`@key` también se puede usar para impedir que Blazor conserve un subárbol de componente o elemento cuando un objeto cambie:</span><span class="sxs-lookup"><span data-stu-id="6802c-260">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="6802c-261">Si `@currentPerson` cambia, la directiva de atributo `@key` fuerza a Blazor a descartar el elemento `<div>` entero y sus descendientes, y vuelve a generar el subárbol dentro de la interfaz de usuario con nuevos elementos y componentes.</span><span class="sxs-lookup"><span data-stu-id="6802c-261">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="6802c-262">Esto puede ser útil si debemos asegurarnos de que no se conserva ningún estado de interfaz de usuario cuando `@currentPerson` cambie.</span><span class="sxs-lookup"><span data-stu-id="6802c-262">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="6802c-263">Cuándo no usar \@key</span><span class="sxs-lookup"><span data-stu-id="6802c-263">When not to use \@key</span></span>

<span data-ttu-id="6802c-264">Las comparaciones con `@key` repercuten en el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="6802c-264">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="6802c-265">El rendimiento no se ve especialmente afectado, pero pese a ello debemos especificar `@key` únicamente cuando controlar las reglas de conservación de componentes o elementos suponga un beneficio para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6802c-265">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="6802c-266">Aun cuando `@key` no se use, Blazor conserva las instancias de componentes y elemento secundarios lo máximo posible.</span><span class="sxs-lookup"><span data-stu-id="6802c-266">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="6802c-267">La única ventaja de utilizar `@key` es el control sobre *cómo* se asignan instancias de modelo a las instancias de componente conservadas, en lugar del algoritmo de comparación, que selecciona la asignación.</span><span class="sxs-lookup"><span data-stu-id="6802c-267">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="6802c-268">Qué valores usar en \@key</span><span class="sxs-lookup"><span data-stu-id="6802c-268">What values to use for \@key</span></span>

<span data-ttu-id="6802c-269">Por lo general, lo lógico es proporcionar uno de los siguientes tipos de valor en `@key`:</span><span class="sxs-lookup"><span data-stu-id="6802c-269">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="6802c-270">Instancias de objetos de modelo (una instancia de `Person`, como en el ejemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="6802c-270">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="6802c-271">Esto garantiza la conservación en función de la igualdad de las referencias de objetos.</span><span class="sxs-lookup"><span data-stu-id="6802c-271">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="6802c-272">Identificadores únicos (por ejemplo, los valores de clave principal de tipo `int`, `string` o `Guid`).</span><span class="sxs-lookup"><span data-stu-id="6802c-272">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="6802c-273">Asegúrese de que los valores usados en `@key` no entran en conflicto.</span><span class="sxs-lookup"><span data-stu-id="6802c-273">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="6802c-274">Si se detectan valores en conflicto en el mismo elemento primario, Blazor produce una excepción porque no puede asignar de forma determinista elementos o componentes antiguos a nuevos elementos o componentes.</span><span class="sxs-lookup"><span data-stu-id="6802c-274">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="6802c-275">Use exclusivamente valores distintos, como instancias de objeto o valores de clave principal.</span><span class="sxs-lookup"><span data-stu-id="6802c-275">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="partial-class-support"></a><span data-ttu-id="6802c-276">Compatibilidad parcial de clases</span><span class="sxs-lookup"><span data-stu-id="6802c-276">Partial class support</span></span>

<span data-ttu-id="6802c-277">Los componentes de Razor se generan como clases parciales.</span><span class="sxs-lookup"><span data-stu-id="6802c-277">Razor components are generated as partial classes.</span></span> <span data-ttu-id="6802c-278">Los componentes de Razor se crean mediante cualquiera de los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="6802c-278">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="6802c-279">Se define código de C# en un bloque [`@code`](xref:mvc/views/razor#code) con marcado HTML y código de Razor en un mismo archivo.</span><span class="sxs-lookup"><span data-stu-id="6802c-279">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="6802c-280">Las plantillas de Blazor definen sus propios componentes de Razor con este método.</span><span class="sxs-lookup"><span data-stu-id="6802c-280">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="6802c-281">El código de C# se coloca en un archivo de código subyacente definido como una clase parcial.</span><span class="sxs-lookup"><span data-stu-id="6802c-281">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="6802c-282">En el siguiente ejemplo se muestra el componente `Counter` predeterminado con un bloque `@code` en una aplicación generada a partir de una plantilla de Blazor.</span><span class="sxs-lookup"><span data-stu-id="6802c-282">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="6802c-283">El marcado HTML, el código de Razor y el código de C# se encuentran en el mismo archivo:</span><span class="sxs-lookup"><span data-stu-id="6802c-283">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="6802c-284">*Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="6802c-284">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int _currentCount = 0;

    void IncrementCount()
    {
        _currentCount++;
    }
}
```

<span data-ttu-id="6802c-285">El componente `Counter` también se puede crear con un archivo de código subyacente con una clase parcial:</span><span class="sxs-lookup"><span data-stu-id="6802c-285">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="6802c-286">*Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="6802c-286">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="6802c-287">*Counter.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="6802c-287">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int _currentCount = 0;

        void IncrementCount()
        {
            _currentCount++;
        }
    }
}
```

<span data-ttu-id="6802c-288">Agregue los espacios de nombres que sean necesarios al archivo de clase parcial.</span><span class="sxs-lookup"><span data-stu-id="6802c-288">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="6802c-289">Los espacios de nombres típicos utilizados por los componentes de Razor son estos:</span><span class="sxs-lookup"><span data-stu-id="6802c-289">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="6802c-290">Especificación de una clase base</span><span class="sxs-lookup"><span data-stu-id="6802c-290">Specify a base class</span></span>

<span data-ttu-id="6802c-291">La directiva [`@inherits`](xref:mvc/views/razor#inherits) se puede usar para especificar la clase base de un componente.</span><span class="sxs-lookup"><span data-stu-id="6802c-291">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="6802c-292">En el siguiente ejemplo se muestra cómo un componente puede heredar una clase base, `BlazorRocksBase`, para proporcionar las propiedades y los métodos del componente.</span><span class="sxs-lookup"><span data-stu-id="6802c-292">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="6802c-293">La clase base debe derivar de `ComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="6802c-293">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="6802c-294">*Pages/BlazorRocks.razor*:</span><span class="sxs-lookup"><span data-stu-id="6802c-294">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="6802c-295">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="6802c-295">*BlazorRocksBase.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

## <a name="specify-an-attribute"></a><span data-ttu-id="6802c-296">Especificación de un atributo</span><span class="sxs-lookup"><span data-stu-id="6802c-296">Specify an attribute</span></span>

<span data-ttu-id="6802c-297">En los componentes de Razor se pueden especificar atributos mediante la directiva [`@attribute`](xref:mvc/views/razor#attribute).</span><span class="sxs-lookup"><span data-stu-id="6802c-297">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="6802c-298">En el siguiente ejemplo se aplica el atributo `[Authorize]` a la clase del componente:</span><span class="sxs-lookup"><span data-stu-id="6802c-298">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="6802c-299">Importación de componentes</span><span class="sxs-lookup"><span data-stu-id="6802c-299">Import components</span></span>

<span data-ttu-id="6802c-300">El espacio de nombres de un componente creado con Razor se basa en lo siguiente (por orden de prioridad):</span><span class="sxs-lookup"><span data-stu-id="6802c-300">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="6802c-301">Designación de [`@namespace`](xref:mvc/views/razor#namespace) en el marcado del archivo de Razor ( *.razor*) (`@namespace BlazorSample.MyNamespace`).</span><span class="sxs-lookup"><span data-stu-id="6802c-301">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="6802c-302">Elemento `RootNamespace` del proyecto en el archivo de proyecto (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="6802c-302">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="6802c-303">Nombre del proyecto, tomado del nombre de archivo del archivo de proyecto ( *.csproj*) y la ruta de acceso de la raíz del proyecto al componente.</span><span class="sxs-lookup"><span data-stu-id="6802c-303">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="6802c-304">Por ejemplo, el marco de trabajo resuelve *{RAÍZ DEL PROYECTO}/Pages/Index.razor* (*BlazorSample.csproj*) en el espacio de nombres `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="6802c-304">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="6802c-305">Los componentes de C# siguen las reglas de los enlaces de nombres.</span><span class="sxs-lookup"><span data-stu-id="6802c-305">Components follow C# name binding rules.</span></span> <span data-ttu-id="6802c-306">En el caso del componente `Index` de este ejemplo, los componentes en el ámbito serían todos los componentes:</span><span class="sxs-lookup"><span data-stu-id="6802c-306">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="6802c-307">En la misma carpeta, *Pages*.</span><span class="sxs-lookup"><span data-stu-id="6802c-307">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="6802c-308">Los componentes en la raíz del proyecto que no especifiquen explícitamente un espacio de nombres diferente.</span><span class="sxs-lookup"><span data-stu-id="6802c-308">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="6802c-309">Los componentes definidos en un espacio de nombres diferente se incluyen en el ámbito usando la directiva [`@using`](xref:mvc/views/razor#using) de Razor.</span><span class="sxs-lookup"><span data-stu-id="6802c-309">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="6802c-310">Si existe otro componente, `NavMenu.razor`, en la carpeta *BlazorSample/Shared/* , dicho componente se puede usar en `Index.razor` con la siguiente instrucción `@using`:</span><span class="sxs-lookup"><span data-stu-id="6802c-310">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="6802c-311">También se puede hacer referencia a los componentes mediante sus nombres completos, lo cual no requiere el uso de la directiva [`@using`](xref:mvc/views/razor#using):</span><span class="sxs-lookup"><span data-stu-id="6802c-311">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="6802c-312">La calificación `global::` no se puede usar.</span><span class="sxs-lookup"><span data-stu-id="6802c-312">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="6802c-313">No se pueden importar componentes con instrucciones `using` con alias (por ejemplo, `@using Foo = Bar`).</span><span class="sxs-lookup"><span data-stu-id="6802c-313">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="6802c-314">No se pueden usar nombres parciales.</span><span class="sxs-lookup"><span data-stu-id="6802c-314">Partially qualified names aren't supported.</span></span> <span data-ttu-id="6802c-315">Por ejemplo, no se puede agregar `@using BlazorSample` y hacer referencia a `NavMenu.razor` con `<Shared.NavMenu></Shared.NavMenu>`.</span><span class="sxs-lookup"><span data-stu-id="6802c-315">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="6802c-316">Atributos de elementos HTML condicionales</span><span class="sxs-lookup"><span data-stu-id="6802c-316">Conditional HTML element attributes</span></span>

<span data-ttu-id="6802c-317">Los atributos de elementos HTML se representan condicionalmente en función del valor de .NET.</span><span class="sxs-lookup"><span data-stu-id="6802c-317">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="6802c-318">Si el valor es `false` o `null`, el atributo no se representa.</span><span class="sxs-lookup"><span data-stu-id="6802c-318">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="6802c-319">Si el valor es `true`, el atributo se representa minimizado.</span><span class="sxs-lookup"><span data-stu-id="6802c-319">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="6802c-320">En el siguiente ejemplo, `IsCompleted` determina si `checked` se representa en el marcado del elemento:</span><span class="sxs-lookup"><span data-stu-id="6802c-320">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="6802c-321">Si `IsCompleted` es `true`, la casilla se representa así:</span><span class="sxs-lookup"><span data-stu-id="6802c-321">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="6802c-322">Si `IsCompleted` es `false`, la casilla se representa así:</span><span class="sxs-lookup"><span data-stu-id="6802c-322">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="6802c-323">Para obtener más información, vea <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="6802c-323">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="6802c-324">Algunos atributos HTML, como [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), no funcionan correctamente cuando el tipo de .NET es `bool`.</span><span class="sxs-lookup"><span data-stu-id="6802c-324">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="6802c-325">En esos casos, use un tipo `string` en lugar de `bool`.</span><span class="sxs-lookup"><span data-stu-id="6802c-325">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="6802c-326">HTML sin formato</span><span class="sxs-lookup"><span data-stu-id="6802c-326">Raw HTML</span></span>

<span data-ttu-id="6802c-327">Normalmente, las cadenas se representan mediante nodos de texto DOM, lo que significa que cualquier marcado que esas cadenas puedan contener se omite y se trata como texto literal.</span><span class="sxs-lookup"><span data-stu-id="6802c-327">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="6802c-328">Para representar HTML sin formato, encapsule el contenido HTML en un valor `MarkupString`.</span><span class="sxs-lookup"><span data-stu-id="6802c-328">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="6802c-329">El valor se analiza como HTML o SVG y se inserta en el DOM.</span><span class="sxs-lookup"><span data-stu-id="6802c-329">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="6802c-330">La representación de HTML sin formato creado a partir de un origen que no es de confianza entraña un **riesgo de seguridad** y debe evitarse.</span><span class="sxs-lookup"><span data-stu-id="6802c-330">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="6802c-331">En el siguiente ejemplo se describe cómo usar el tipo `MarkupString` para agregar un bloque de contenido HTML estático a la salida representada de un componente:</span><span class="sxs-lookup"><span data-stu-id="6802c-331">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="6802c-332">Valores y parámetros en cascada</span><span class="sxs-lookup"><span data-stu-id="6802c-332">Cascading values and parameters</span></span>

<span data-ttu-id="6802c-333">En algunos escenarios, no es conveniente que los datos fluyan desde un componente antecesor a un componente descendiente usando [parámetros de componente](#component-parameters), sobre todo si hay varios niveles de componentes.</span><span class="sxs-lookup"><span data-stu-id="6802c-333">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="6802c-334">Los valores y parámetros en cascada ponen fin a este problema, ya que constituyen un método cómodo para que un componente antecesor proporcione un valor a todos sus componentes descendientes.</span><span class="sxs-lookup"><span data-stu-id="6802c-334">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="6802c-335">Los valores y parámetros en cascada también sirven para que los componentes se coordinen.</span><span class="sxs-lookup"><span data-stu-id="6802c-335">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="6802c-336">Ejemplo de Theme</span><span class="sxs-lookup"><span data-stu-id="6802c-336">Theme example</span></span>

<span data-ttu-id="6802c-337">En el siguiente ejemplo de la aplicación de muestra, la clase `ThemeInfo` especifica la información de tema que va a fluir hacia abajo en la jerarquía de componentes para que todos los botones de una parte determinada de la aplicación compartan el mismo estilo.</span><span class="sxs-lookup"><span data-stu-id="6802c-337">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="6802c-338">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="6802c-338">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="6802c-339">Un componente antecesor puede proporcionar un valor en cascada mediante el componente CascadingValue.</span><span class="sxs-lookup"><span data-stu-id="6802c-339">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="6802c-340">El componente `CascadingValue` encapsula un subárbol de la jerarquía de componentes y proporciona un mismo valor para todos los componentes de ese subárbol.</span><span class="sxs-lookup"><span data-stu-id="6802c-340">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="6802c-341">Por ejemplo, en la aplicación de muestra se especifica información de tema (`ThemeInfo`) en uno de los diseños de la aplicación como un parámetro en cascada de todos los componentes que componen el cuerpo del diseño de la propiedad `@Body`.</span><span class="sxs-lookup"><span data-stu-id="6802c-341">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="6802c-342">A `ButtonClass` se le asigna un valor `btn-success` en el componente de diseño.</span><span class="sxs-lookup"><span data-stu-id="6802c-342">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="6802c-343">Cualquier componente descendiente puede usar esta propiedad a lo largo del objeto en cascada `ThemeInfo`.</span><span class="sxs-lookup"><span data-stu-id="6802c-343">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="6802c-344">Componente `CascadingValuesParametersLayout`:</span><span class="sxs-lookup"><span data-stu-id="6802c-344">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="_theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo _theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="6802c-345">Para usar valores en cascada, los componentes declaran parámetros en cascada mediante el atributo `[CascadingParameter]`.</span><span class="sxs-lookup"><span data-stu-id="6802c-345">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="6802c-346">Los valores en cascada se enlazan a los parámetros en cascada según el tipo.</span><span class="sxs-lookup"><span data-stu-id="6802c-346">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="6802c-347">En la aplicación de muestra, el componente `CascadingValuesParametersTheme` enlaza el valor en cascada `ThemeInfo` a un parámetro en cascada.</span><span class="sxs-lookup"><span data-stu-id="6802c-347">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="6802c-348">El parámetro se usa para establecer la clase CSS para uno de los botones que el componente muestra.</span><span class="sxs-lookup"><span data-stu-id="6802c-348">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="6802c-349">Componente `CascadingValuesParametersTheme`:</span><span class="sxs-lookup"><span data-stu-id="6802c-349">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @_currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int _currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        _currentCount++;
    }
}
```

<span data-ttu-id="6802c-350">Para poner en cascada varios valores del mismo tipo en un mismo subárbol, proporcione una cadena `Name` única en cada componente `CascadingValue` y su `CascadingParameter` correspondiente.</span><span class="sxs-lookup"><span data-stu-id="6802c-350">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="6802c-351">En el siguiente ejemplo, dos componentes `CascadingValue` en cascada son instancias distintas de `MyCascadingType` por su nombre:</span><span class="sxs-lookup"><span data-stu-id="6802c-351">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@_parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType _parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="6802c-352">En un componente descendiente, los parámetros en cascada reciben sus valores de los valores en cascada correspondientes del componente antecesor por su nombre:</span><span class="sxs-lookup"><span data-stu-id="6802c-352">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="6802c-353">Ejemplo de TabSet</span><span class="sxs-lookup"><span data-stu-id="6802c-353">TabSet example</span></span>

<span data-ttu-id="6802c-354">Los parámetros en cascada también permiten que los componentes colaboren a lo largo de la jerarquía de componentes.</span><span class="sxs-lookup"><span data-stu-id="6802c-354">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="6802c-355">Veamos el siguiente ejemplo de *TabSet* en la aplicación de muestra.</span><span class="sxs-lookup"><span data-stu-id="6802c-355">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="6802c-356">La aplicación de muestra tiene una interfaz `ITab` que las pestañas implementan:</span><span class="sxs-lookup"><span data-stu-id="6802c-356">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="6802c-357">El componente `CascadingValuesParametersTabSet` usa el componente `TabSet`, que contiene varios componentes `Tab`:</span><span class="sxs-lookup"><span data-stu-id="6802c-357">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

<span data-ttu-id="6802c-358">Los componentes `Tab` secundarios no se pasan explícitamente como parámetros a `TabSet`,</span><span class="sxs-lookup"><span data-stu-id="6802c-358">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="6802c-359">sino que forman parte del contenido secundario de `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="6802c-359">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="6802c-360">Pese a ello, `TabSet` sigue necesitando saber de cada componente `Tab` para poder representar los encabezados y la pestaña activa. Para permitir esta coordinación sin requerir código extra, el componente `TabSet` *puede proporcionarse a sí mismo como un valor en cascada* que, luego, van a seleccionar los componentes `Tab` descendientes.</span><span class="sxs-lookup"><span data-stu-id="6802c-360">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="6802c-361">Componente `TabSet`:</span><span class="sxs-lookup"><span data-stu-id="6802c-361">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="6802c-362">Los componentes `Tab` descendientes capturan el elemento `TabSet` contenedor como un parámetro en cascada, por lo que los componentes `Tab` se agregan a sí mismo a `TabSet` y coordinan en qué pestaña está activo.</span><span class="sxs-lookup"><span data-stu-id="6802c-362">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="6802c-363">Componente `Tab`:</span><span class="sxs-lookup"><span data-stu-id="6802c-363">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="6802c-364">Plantillas de Razor</span><span class="sxs-lookup"><span data-stu-id="6802c-364">Razor templates</span></span>

<span data-ttu-id="6802c-365">Se pueden definir fragmentos de representación usando la sintaxis de plantilla de Razor.</span><span class="sxs-lookup"><span data-stu-id="6802c-365">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="6802c-366">Las plantillas de Razor son un modo de definir un fragmento de interfaz de usuario y asumen el siguiente formato:</span><span class="sxs-lookup"><span data-stu-id="6802c-366">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="6802c-367">En el siguiente ejemplo se muestra cómo especificar los valores `RenderFragment` y `RenderFragment<T>` y las plantillas de representación directamente en un componente.</span><span class="sxs-lookup"><span data-stu-id="6802c-367">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="6802c-368">Los fragmentos de representación también se pueden pasar como argumentos a [componentes con plantilla](xref:blazor/templated-components).</span><span class="sxs-lookup"><span data-stu-id="6802c-368">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

```razor
@_timeTemplate

@_petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment _timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> _petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="6802c-369">Salida representada del código anterior:</span><span class="sxs-lookup"><span data-stu-id="6802c-369">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="6802c-370">Imágenes con formato Scalable Vector Graphics (SVG)</span><span class="sxs-lookup"><span data-stu-id="6802c-370">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="6802c-371">Como Blazor representa HTML, se pueden usar imágenes compatibles con el explorador, incluidas imágenes con formato Scalable Vector Graphics (SVG) ( *.svg*), mediante la etiqueta `<img>`:</span><span class="sxs-lookup"><span data-stu-id="6802c-371">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="6802c-372">Del mismo modo, se pueden usar imágenes SVG en las reglas de CSS de un archivo de hoja de estilos ( *.css*):</span><span class="sxs-lookup"><span data-stu-id="6802c-372">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="6802c-373">Pero no todos los escenarios admiten el uso de marcado SVG en línea.</span><span class="sxs-lookup"><span data-stu-id="6802c-373">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="6802c-374">Si se coloca una etiqueta `<svg>` directamente en un archivo de componente ( *.razor*), se puede usar la representación de imágenes básica, pero muchos escenarios avanzados no estarán admitidos.</span><span class="sxs-lookup"><span data-stu-id="6802c-374">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="6802c-375">Por ejemplo, actualmente las etiquetas `<use>` no se cumplen, y `@bind` no se puede usar con algunas etiquetas SVG.</span><span class="sxs-lookup"><span data-stu-id="6802c-375">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="6802c-376">Esperamos abordar estas limitaciones en una versión futura.</span><span class="sxs-lookup"><span data-stu-id="6802c-376">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6802c-377">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="6802c-377">Additional resources</span></span>

* <span data-ttu-id="6802c-378"><xref:security/blazor/server>: incluye instrucciones sobre cómo crear aplicaciones de servidor Blazor que deben afrontar situaciones de agotamiento de recursos.</span><span class="sxs-lookup"><span data-stu-id="6802c-378"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
