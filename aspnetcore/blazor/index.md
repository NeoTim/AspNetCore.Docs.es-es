---
title: Introducción a Blazor en ASP.NET Core
author: guardrex
description: Explore Blazor en ASP.NET Core, una forma de compilar la interfaz de usuario web interactiva del lado cliente con .NET en una aplicación ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 09/05/2019
uid: blazor/index
ms.openlocfilehash: 767ec8f106bebb92cf13a10eb63fab4905715d3d
ms.sourcegitcommit: 092061c4f6ef46ed2165fa84de6273d3786fb97e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2019
ms.locfileid: "70964103"
---
# <a name="introduction-to-blazor"></a><span data-ttu-id="1fbf8-103">Introducción a Blazor</span><span class="sxs-lookup"><span data-stu-id="1fbf8-103">Introduction to Blazor</span></span>

<span data-ttu-id="1fbf8-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1fbf8-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1fbf8-105">*Le damos la bienvenida a Blazor*.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-105">*Welcome to Blazor!*</span></span>

<span data-ttu-id="1fbf8-106">Blazor es un marco de trabajo para la creación de interfaces de usuario web interactivas del lado cliente con .NET:</span><span class="sxs-lookup"><span data-stu-id="1fbf8-106">Blazor is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="1fbf8-107">Cree interfaces de usuario completamente interactivas con C# en lugar de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="1fbf8-108">Comparta la lógica de aplicación del lado cliente y servidor escrita con .NET.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="1fbf8-109">Represente la interfaz de usuario como HTML y CSS para la compatibilidad con todos los exploradores, incluidos los móviles.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="1fbf8-110">El uso de .NET para el desarrollo web en el lado cliente ofrece las siguientes ventajas:</span><span class="sxs-lookup"><span data-stu-id="1fbf8-110">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="1fbf8-111">Please use suggested translation and if uppercase wanted, please use uppercase in source as well, in this kind of sentences.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-111">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="1fbf8-112">Aprovechamiento del ecosistema y las bibliotecas .NET existentes.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-112">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="1fbf8-113">Uso compartido de la lógica de aplicación en el servidor y el cliente.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-113">Share app logic across server and client.</span></span>
* <span data-ttu-id="1fbf8-114">Beneficios de rendimiento, confiabilidad y seguridad de .NET.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-114">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="1fbf8-115">Use suggested translation</span><span class="sxs-lookup"><span data-stu-id="1fbf8-115">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="1fbf8-116">Compile sobre un conjunto común de lenguajes, marcos y herramientas que son estables, completos y fáciles de usar.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-116">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="1fbf8-117">Componentes</span><span class="sxs-lookup"><span data-stu-id="1fbf8-117">Components</span></span>

<span data-ttu-id="1fbf8-118">Las aplicaciones de Blazor se basan en *componentes*.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-118">Blazor apps are based on *components*.</span></span> <span data-ttu-id="1fbf8-119">Un componente en Blazor es un elemento de la interfaz de usuario, como una página, un cuadro de diálogo o un formulario de entrada de datos.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-119">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="1fbf8-120">Los componentes son clases de .NET compiladas en ensamblados de .NET que:</span><span class="sxs-lookup"><span data-stu-id="1fbf8-120">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="1fbf8-121">Definen la lógica de representación de la interfaz de usuario flexible.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-121">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="1fbf8-122">Controlan acciones del usuario.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-122">Handle user events.</span></span>
* <span data-ttu-id="1fbf8-123">Se pueden anidar y reutilizar.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-123">Can be nested and reused.</span></span>
* <span data-ttu-id="1fbf8-124">Se pueden compartir y distribuir como [bibliotecas de clases de Razor](xref:razor-pages/ui-class) o [paquetes de NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="1fbf8-124">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="1fbf8-125">La clase del componente se escribe normalmente en forma de una página de marcado de [Razor](xref:mvc/views/razor) con una extensión de archivo *.razor*.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-125">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a *.razor* file extension.</span></span> <span data-ttu-id="1fbf8-126">Se hace referencia a los componentes de Blazor como *componentes de Razor*.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-126">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="1fbf8-127">Razor es una sintaxis para combinar marcado HTML con código de C# diseñada para aumentar la productividad del desarrollador.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-127">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="1fbf8-128">Razor le permite cambiar entre marcado HTML y C# en el mismo archivo gracias a la compatibilidad con [IntelliSense](/visualstudio/ide/using-intellisense).</span><span class="sxs-lookup"><span data-stu-id="1fbf8-128">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="1fbf8-129">Razor Pages y MVC también usan Razor.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-129">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="1fbf8-130">A diferencia de Razor Pages y MVC, que se compilan en torno a un modelo de solicitud y respuesta, los componentes se usan específicamente en la composición y la lógica de la interfaz de usuario del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-130">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="1fbf8-131">El siguiente marcado de Razor muestra un componente (*Dialog.razor*), que se puede anidar dentro de otro componente:</span><span class="sxs-lookup"><span data-stu-id="1fbf8-131">The following Razor markup demonstrates a component (*Dialog.razor*), which can be nested within another component:</span></span>

```cshtml
<div>
    <h1>@Title</h1>

    @ChildContent

    <button @onclick="OnYes">Yes!</button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button was selected.");
    }
}
```

<span data-ttu-id="1fbf8-132">El contenido del cuerpo (`ChildContent`) y el título (`Title`) del cuadro de diálogo los proporciona el componente que usa este componente en su interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-132">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="1fbf8-133">`OnYes` es un método de C# desencadenado por el evento `onclick` del botón.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-133">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

<span data-ttu-id="1fbf8-134">Blazor usa etiquetas HTML naturales para la composición de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-134">Blazor uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="1fbf8-135">Los elementos HTML especifican componentes y los atributos de la etiqueta pasan valores a las propiedades del componente.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-135">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="1fbf8-136">En el ejemplo siguiente, el componente `Index` utiliza el componente `Dialog`.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-136">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="1fbf8-137">`ChildContent` y `Title` se establecen mediante los atributos y el contenido del elemento `<Dialog>`.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-137">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="1fbf8-138">*Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="1fbf8-138">*Index.razor*:</span></span>

```cshtml
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="1fbf8-139">El cuadro de diálogo se representa cuando se accede al elemento principal (*Index.razor*) en un explorador:</span><span class="sxs-lookup"><span data-stu-id="1fbf8-139">The dialog is rendered when the parent (*Index.razor*) is accessed in a browser:</span></span>

![Componente Dialog representado en el explorador](index/_static/dialog.png)

<span data-ttu-id="1fbf8-141">Cuando este componente se usa en la aplicación, IntelliSense en [Visual Studio](/visualstudio/ide/using-intellisense) y [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) acelera el desarrollo con la finalización de parámetros y sintaxis.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-141">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="1fbf8-142">Los componentes se representan en una representación en memoria de la especificación Document Object Model (DOM) del explorador llamada *árbol de representación*, que se usa para actualizar la interfaz de usuario de una manera eficaz y flexible.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-142">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="1fbf8-143">WebAssembly de Blazor</span><span class="sxs-lookup"><span data-stu-id="1fbf8-143">Blazor WebAssembly</span></span>

<span data-ttu-id="1fbf8-144">WebAssembly de Blazor es un marco de trabajo de aplicaciones de página única para la creación de aplicaciones web interactivas en el lado cliente con. NET.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-144">Blazor WebAssembly is a single-page app framework for building interactive client-side web apps with .NET.</span></span> <span data-ttu-id="1fbf8-145">WebAssembly de Blazor usa estándares web abiertos sin complementos ni transpilación de código y funciona en todos los exploradores web modernos, incluidos los exploradores para dispositivos móviles.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-145">Blazor WebAssembly uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="1fbf8-146">La ejecución de código .NET dentro de exploradores web se consigue mediante [WebAssembly](https://webassembly.org) (abreviado como *wasm*).</span><span class="sxs-lookup"><span data-stu-id="1fbf8-146">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="1fbf8-147">WebAssembly es un formato de código de bytes compacto optimizado para descargas rápidas y una velocidad de ejecución máxima.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-147">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="1fbf8-148">WebAssembly es un estándar web abierto y se admite en exploradores web sin complementos.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-148">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="1fbf8-149">El código de WebAssembly puede acceder a toda la funcionalidad del explorador mediante la *interoperabilidad de JavaScript* *.*</span><span class="sxs-lookup"><span data-stu-id="1fbf8-149">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="1fbf8-150">El código de .NET que se ejecuta a través de WebAssembly en el explorador se ejecuta a su vez en el espacio aislado de JavaScript del explorador con las protecciones que proporciona dicho espacio aislado contra acciones malintencionadas en la máquina cliente.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-150">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![WebAssembly de Blazor ejecuta código de .NET en el explorador con WebAssembly.](index/_static/blazor-webassembly.png)

<span data-ttu-id="1fbf8-152">Cuando se compila y ejecuta una aplicación WebAssembly de Blazor en un explorador:</span><span class="sxs-lookup"><span data-stu-id="1fbf8-152">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="1fbf8-153">Los archivos de código de C# y los archivos de Razor se compilan en ensamblados de .NET.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-153">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="1fbf8-154">Los ensamblados y el entorno de ejecución de .NET se descargan en el explorador.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-154">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="1fbf8-155">WebAssembly de Blazor arranca el entorno de ejecución .NET y lo configura para cargar los ensamblados de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-155">Blazor WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="1fbf8-156">El entorno de ejecución de WebAssembly de Blazor emplea la interoperabilidad de JavaScript para gestionar la manipulación de DOM y las llamadas API del explorador.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-156">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="1fbf8-157">El tamaño de la aplicación publicada, su *tamaño de carga*, es un factor de rendimiento crítico para la facilidad de uso de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-157">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="1fbf8-158">Una aplicación grande tarda un tiempo relativamente largo en descargarse en un explorador, lo que repercute en la experiencia del usuario.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-158">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> <span data-ttu-id="1fbf8-159">WebAssembly de Blazor optimiza el tamaño de carga para reducir los tiempos de descarga:</span><span class="sxs-lookup"><span data-stu-id="1fbf8-159">Blazor WebAssembly optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="1fbf8-160">Se ha quitado el código sin usar de la aplicación cuando se publica mediante el [enlazador del lenguaje intermedio (IL)](xref:host-and-deploy/blazor/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="1fbf8-160">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>
* <span data-ttu-id="1fbf8-161">Las respuestas HTTP se comprimen.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-161">HTTP responses are compressed.</span></span>
* <span data-ttu-id="1fbf8-162">El entorno de ejecución .NET y los ensamblados se almacenan en caché en el explorador.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-162">The .NET runtime and assemblies are cached in the browser.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="1fbf8-163">Servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="1fbf8-163">Blazor Server</span></span>

<span data-ttu-id="1fbf8-164">Blazor separa la lógica de representación de componentes del modo en que se aplican las actualizaciones de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-164">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="1fbf8-165">El servidor Blazor ofrece compatibilidad con el hospedaje de componentes de Razor en el servidor en una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-165">Blazor Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="1fbf8-166">Las actualizaciones de la interfaz de usuario se controlan mediante una conexión de [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="1fbf8-166">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="1fbf8-167">El entorno de ejecución controla el envío de eventos de la interfaz de usuario del explorador al servidor y, después de ejecutar los componentes, vuelve a aplicar al explorador las actualizaciones de la interfaz de usuario enviadas por el servidor.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-167">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="1fbf8-168">La conexión usada por el servidor Blazor para comunicarse con el explorador también se emplea para controlar las llamadas de interoperabilidad de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-168">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![El servidor Blazor ejecuta código de .NET en el servidor e interactúa con Document Object Model en el cliente mediante una conexión de SignalR.](index/_static/blazor-server.png)

## <a name="javascript-interop"></a><span data-ttu-id="1fbf8-170">Interoperabilidad de JavaScript</span><span class="sxs-lookup"><span data-stu-id="1fbf8-170">JavaScript interop</span></span>

<span data-ttu-id="1fbf8-171">En el caso de aplicaciones que necesitan bibliotecas de JavaScript de terceros y acceso a las API de explorador, los componentes interoperan con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-171">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="1fbf8-172">Los componentes pueden usar cualquier biblioteca o API que pueda utilizar JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-172">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="1fbf8-173">El código de C# puede llamar a código JavaScript y, a su vez, el código JavaScript puede llamar a código de C#.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-173">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="1fbf8-174">Para más información, consulte <xref:blazor/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-174">For more information, see <xref:blazor/javascript-interop>.</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="1fbf8-175">Uso compartido de código y .NET Standard</span><span class="sxs-lookup"><span data-stu-id="1fbf8-175">Code sharing and .NET Standard</span></span>

<span data-ttu-id="1fbf8-176">Blazor implementa [.NET Standard 2.0](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="1fbf8-176">Blazor implements [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span> <span data-ttu-id="1fbf8-177">.NET Standard es una especificación formal de las API de .NET comunes entre las implementaciones de .NET.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-177">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="1fbf8-178">Las bibliotecas de clase .NET Standard pueden compartirse a través de diferentes plataformas .NET, como Blazor, .NET Framework, .NET Core, Xamarin, Mono y Unity.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-178">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="1fbf8-179">Las API que no pueden aplicarse dentro de un explorador web (por ejemplo, para acceder al sistema de archivos, abrir un socket y ejecutar subprocesos) generan una excepción <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="1fbf8-179">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1fbf8-180">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="1fbf8-180">Additional resources</span></span>

* [<span data-ttu-id="1fbf8-181">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="1fbf8-181">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* [<span data-ttu-id="1fbf8-182">Guía de C#</span><span class="sxs-lookup"><span data-stu-id="1fbf8-182">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="1fbf8-183">HTML</span><span class="sxs-lookup"><span data-stu-id="1fbf8-183">HTML</span></span>](https://www.w3.org/html/)
