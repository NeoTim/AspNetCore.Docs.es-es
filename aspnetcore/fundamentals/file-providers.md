---
title: Proveedores de archivo en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo ASP.NET Core abstrae el acceso al sistema de archivos mediante el uso de proveedores de archivos.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/file-providers
ms.openlocfilehash: 1e243d31a1c6b1f6ac6c9f7966ce07ecb01ceae5
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106188"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="0a5a7-103">Proveedores de archivo en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a5a7-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="0a5a7-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="0a5a7-104">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0a5a7-105">ASP.NET Core abstrae el acceso al sistema de archivos mediante el uso de proveedores de archivos.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="0a5a7-106">Los proveedores de archivos se usan en el marco de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-106">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="0a5a7-107">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-107">For example:</span></span>

* <span data-ttu-id="0a5a7-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> expone la [raíz del contenido](xref:fundamentals/index#content-root) y la [raíz web](xref:fundamentals/index#web-root) de la aplicación como tipos `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="0a5a7-109">El [middleware de archivos estáticos](xref:fundamentals/static-files) usa proveedores de archivos para buscar archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-109">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="0a5a7-110">[Razor](xref:mvc/views/razor) usa proveedores de archivos para localizar páginas y vistas.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-110">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="0a5a7-111">Las herramientas de .NET Core usan proveedores de archivos y patrones globales para especificar los archivos que deben publicarse.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-111">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="0a5a7-112">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0a5a7-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="0a5a7-113">Interfaces de proveedor de archivos</span><span class="sxs-lookup"><span data-stu-id="0a5a7-113">File Provider interfaces</span></span>

<span data-ttu-id="0a5a7-114">La interfaz principal es <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-114">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="0a5a7-115">`IFileProvider` expone métodos para:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-115">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="0a5a7-116">Obtenga la información del archivo (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="0a5a7-116">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="0a5a7-117">Obtenga la información del directorio (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="0a5a7-117">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="0a5a7-118">Configure las notificaciones de cambio (mediante <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="0a5a7-118">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="0a5a7-119">`IFileInfo` proporciona métodos y propiedades para trabajar con archivos:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-119">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="0a5a7-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (en bytes)</span><span class="sxs-lookup"><span data-stu-id="0a5a7-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="0a5a7-121">Fecha <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified></span><span class="sxs-lookup"><span data-stu-id="0a5a7-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="0a5a7-122">Se puede leer en el archivo mediante el método <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-122">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="0a5a7-123">La aplicación de ejemplo *FileProviderSample* muestra cómo configurar un proveedor de archivos en `Startup.ConfigureServices` para su uso en toda la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0a5a7-123">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="0a5a7-124">Implementaciones del proveedor de archivos</span><span class="sxs-lookup"><span data-stu-id="0a5a7-124">File Provider implementations</span></span>

<span data-ttu-id="0a5a7-125">En la tabla siguiente se enumeran las implementaciones de `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-125">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="0a5a7-126">Implementación</span><span class="sxs-lookup"><span data-stu-id="0a5a7-126">Implementation</span></span> | <span data-ttu-id="0a5a7-127">Descripción</span><span class="sxs-lookup"><span data-stu-id="0a5a7-127">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="0a5a7-128">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="0a5a7-128">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="0a5a7-129">Se usa para proporcionar acceso combinado a archivos y directorios de uno o más proveedores.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-129">Used to provide combined access to files and directories from one or more other providers.</span></span> |
| [<span data-ttu-id="0a5a7-130">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="0a5a7-130">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="0a5a7-131">Se usa para tener acceso a archivos insertados en ensamblados.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-131">Used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="0a5a7-132">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="0a5a7-132">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="0a5a7-133">Se usa para tener acceso a los archivos físicos del sistema.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-133">Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="0a5a7-134">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="0a5a7-134">PhysicalFileProvider</span></span>

<span data-ttu-id="0a5a7-135"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> proporciona acceso al sistema de archivos físico.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-135">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="0a5a7-136">`PhysicalFileProvider` usa el tipo <xref:System.IO.File?displayProperty=fullName> (para el proveedor físico) y define el ámbito de todas las rutas de acceso a un directorio y sus elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-136">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="0a5a7-137">Esta definición de ámbito impide el acceso al sistema de archivos fuera del directorio especificado y sus elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-137">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="0a5a7-138">El escenario más común para crear y usar `PhysicalFileProvider` es solicitar `IFileProvider` en un constructor mediante la [inyección de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0a5a7-138">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="0a5a7-139">Al crear una instancia de este proveedor directamente, se requiere una ruta de acceso absoluta al directorio, que actúa como la ruta de acceso base para todas las solicitudes realizadas usando el proveedor.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-139">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="0a5a7-140">Los patrones globales no se admiten en la ruta de acceso al directorio.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-140">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="0a5a7-141">El código siguiente muestra cómo usar `PhysicalFileProvider` para obtener el contenido del directorio y la información del archivo:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-141">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="0a5a7-142">Tipos del ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-142">Types in the preceding example:</span></span>

* <span data-ttu-id="0a5a7-143">`provider` es `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-143">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="0a5a7-144">`contents` es `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-144">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="0a5a7-145">`fileInfo` es `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-145">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="0a5a7-146">El proveedor de archivos puede usarse para iterar por el directorio especificado por `applicationRoot` o llamar a `GetFileInfo` para obtener información de un archivo.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-146">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="0a5a7-147">No se pueden pasar patrones globales al método `GetFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-147">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="0a5a7-148">El proveedor de archivos no tiene acceso fuera del directorio `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-148">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="0a5a7-149">La aplicación de ejemplo *FileProviderSample* crea el proveedor en el método `Startup.ConfigureServices` con <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-149">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="0a5a7-150">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="0a5a7-150">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="0a5a7-151"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> se utiliza para acceder a archivos insertados dentro de ensamblados.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-151">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="0a5a7-152">`ManifestEmbeddedFileProvider` utiliza un manifiesto compilado en el ensamblado para reconstruir las rutas de acceso originales de los archivos incrustados.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-152">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="0a5a7-153">Para generar un manifiesto de los archivos incrustados:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-153">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="0a5a7-154">Agregue el paquete de NuGet [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) al proyecto.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-154">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="0a5a7-155">Establezca la propiedad `<GenerateEmbeddedFilesManifest>` en `true`.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-155">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="0a5a7-156">Especifique los archivos que desea incrustar con [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="0a5a7-156">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="0a5a7-157">Use [patrones globales](#glob-patterns) para especificar uno o varios archivos para incrustar en el ensamblado.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-157">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="0a5a7-158">La aplicación de ejemplo *FileProviderSample* crea `ManifestEmbeddedFileProvider` y pasa el ensamblado que se está ejecutando actualmente a su constructor.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-158">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="0a5a7-159">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-159">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="0a5a7-160">Las sobrecargas adicionales le permiten:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-160">Additional overloads allow you to:</span></span>

* <span data-ttu-id="0a5a7-161">Especificar una ruta de acceso de archivo relativa.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-161">Specify a relative file path.</span></span>
* <span data-ttu-id="0a5a7-162">Definir el ámbito de archivos a la fecha de la última modificación.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-162">Scope files to a last modified date.</span></span>
* <span data-ttu-id="0a5a7-163">Asignar nombre al recurso incrustado que contiene el manifiesto del archivo incrustado.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-163">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="0a5a7-164">Sobrecarga</span><span class="sxs-lookup"><span data-stu-id="0a5a7-164">Overload</span></span> | <span data-ttu-id="0a5a7-165">Descripción</span><span class="sxs-lookup"><span data-stu-id="0a5a7-165">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="0a5a7-166">Acepta parámetro de ruta de acceso relativa `root` opcional.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-166">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="0a5a7-167">Especifique `root` para definir el ámbito de las llamadas en <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> en aquellos recursos que se encuentran bajo las rutas de acceso proporcionadas.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-167">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="0a5a7-168">Acepta un parámetro de ruta de acceso relativa `root` opcional y un parámetro de fecha `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="0a5a7-168">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="0a5a7-169">La fecha `lastModified` define el ámbito de la última fecha de modificación para las instancias de <xref:Microsoft.Extensions.FileProviders.IFileInfo> que <xref:Microsoft.Extensions.FileProviders.IFileProvider> devuelve.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-169">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="0a5a7-170">Acepta una ruta de acceso relativa `root` opcional, una fecha `lastModified` y parámetros `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-170">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="0a5a7-171">`manifestName` representa el nombre del recurso incrustado que contiene el manifiesto.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-171">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="0a5a7-172">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="0a5a7-172">CompositeFileProvider</span></span>

<span data-ttu-id="0a5a7-173"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combina instancias de `IFileProvider`, y expone una única interfaz para trabajar con archivos de varios proveedores.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-173">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="0a5a7-174">Al crear `CompositeFileProvider`, se pasan una o varias instancias de `IFileProvider` a su constructor.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-174">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="0a5a7-175">En la aplicación de ejemplo *FileProviderSample*, `PhysicalFileProvider` y `ManifestEmbeddedFileProvider` proporcionan archivos a un `CompositeFileProvider` registrado en el contenedor de servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-175">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="0a5a7-176">El código siguiente se encuentra en el método `Startup.ConfigureServices` del proyecto:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-176">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="0a5a7-177">Observación de cambios</span><span class="sxs-lookup"><span data-stu-id="0a5a7-177">Watch for changes</span></span>

<span data-ttu-id="0a5a7-178">El método <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> proporciona un escenario para ver uno o más archivos o directorios para detectar cambios.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-178">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="0a5a7-179">El método `Watch` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-179">The `Watch` method:</span></span>

* <span data-ttu-id="0a5a7-180">Acepta una cadena de ruta de acceso a archivo, que puede usar [patrones globales](#glob-patterns) para especificar varios archivos.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-180">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="0a5a7-181">Devuelve un valor <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-181">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="0a5a7-182">El token de cambio resultante expone:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-182">The resulting change token exposes:</span></span>

* <span data-ttu-id="0a5a7-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: una propiedad que se puede inspeccionar para determinar si se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="0a5a7-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: se llama cuando se detectan cambios en la cadena de ruta de acceso especificada.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="0a5a7-185">Cada token de cambio solo llama a su devolución de llamada asociada en respuesta a un único cambio.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-185">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="0a5a7-186">Para habilitar la supervisión constante, puede usar <xref:System.Threading.Tasks.TaskCompletionSource`1> (como se muestra a continuación) o volver a crear instancias de `IChangeToken` en respuesta a los cambios.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-186">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="0a5a7-187">La aplicación de ejemplo *WatchConsole* escribe un mensaje cada vez que se modifica un archivo *.txt* en el directorio *TextFiles*:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-187">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="0a5a7-188">Algunos sistemas de archivos, como contenedores de Docker y recursos compartidos de red, no pueden enviar notificaciones de cambio de forma confiable.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-188">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="0a5a7-189">Establezca la variable de entorno `DOTNET_USE_POLLING_FILE_WATCHER` en `1` o `true` para sondear el sistema de archivos en busca de cambios cada cuatro segundos (no configurable).</span><span class="sxs-lookup"><span data-stu-id="0a5a7-189">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="0a5a7-190">Patrones globales</span><span class="sxs-lookup"><span data-stu-id="0a5a7-190">Glob patterns</span></span>

<span data-ttu-id="0a5a7-191">Las rutas de acceso del sistema de archivos utilizan patrones de caracteres comodín denominados *patrones globales*.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-191">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="0a5a7-192">Especifique grupos de archivos con estos patrones.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-192">Specify groups of files with these patterns.</span></span> <span data-ttu-id="0a5a7-193">Los dos caracteres comodín son `*` y `**`:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-193">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="0a5a7-194">Coincide con cualquier elemento del nivel de carpeta actual, con cualquier nombre de archivo o con cualquier extensión de archivo.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-194">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="0a5a7-195">Las coincidencias finalizan con los caracteres `/` y `.` en la ruta de acceso de archivo.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-195">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="0a5a7-196">Coincide con cualquier elemento de varios niveles de directorios.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-196">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="0a5a7-197">Puede usarse para coincidir de forma recursiva con muchos archivos dentro de una jerarquía de directorios.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-197">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="0a5a7-198">En la tabla siguiente se proporcionan ejemplos comunes de patrones globales.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-198">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="0a5a7-199">Modelo</span><span class="sxs-lookup"><span data-stu-id="0a5a7-199">Pattern</span></span>  |<span data-ttu-id="0a5a7-200">Descripción</span><span class="sxs-lookup"><span data-stu-id="0a5a7-200">Description</span></span>  |
|---------|---------|
|`directory/file.txt`|<span data-ttu-id="0a5a7-201">Coincide con un archivo concreto en un directorio específico.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-201">Matches a specific file in a specific directory.</span></span>|
|`directory/*.txt`|<span data-ttu-id="0a5a7-202">Coincide con todos los archivos que tengan la extensión *.txt* en un directorio específico.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-202">Matches all files with *.txt* extension in a specific directory.</span></span>|
|`directory/*/appsettings.json`|<span data-ttu-id="0a5a7-203">Coincide con todos los archivos *appsettings.json* que están en directorios exactamente un nivel por debajo de la carpeta *directorio*.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-203">Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.</span></span>|
|`directory/**/*.txt`|<span data-ttu-id="0a5a7-204">Coincide con todos los archivos con una extensión *.txt* y que se encuentran en cualquier lugar de la carpeta *directorio*.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-204">Matches all files with a *.txt* extension found anywhere under the *directory* folder.</span></span>|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0a5a7-205">ASP.NET Core abstrae el acceso al sistema de archivos mediante el uso de proveedores de archivos.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-205">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="0a5a7-206">Los proveedores de archivos se usan en el marco de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-206">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="0a5a7-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> expone la [raíz del contenido](xref:fundamentals/index#content-root) y la [raíz web](xref:fundamentals/index#web-root) de la aplicación como tipos `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="0a5a7-208">El [middleware de archivos estáticos](xref:fundamentals/static-files) usa proveedores de archivos para buscar archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-208">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="0a5a7-209">[Razor](xref:mvc/views/razor) usa proveedores de archivos para localizar páginas y vistas.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-209">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="0a5a7-210">Las herramientas de .NET Core usan proveedores de archivos y patrones globales para especificar los archivos que deben publicarse.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-210">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="0a5a7-211">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0a5a7-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="0a5a7-212">Interfaces de proveedor de archivos</span><span class="sxs-lookup"><span data-stu-id="0a5a7-212">File Provider interfaces</span></span>

<span data-ttu-id="0a5a7-213">La interfaz principal es <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-213">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="0a5a7-214">`IFileProvider` expone métodos para:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-214">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="0a5a7-215">Obtenga la información del archivo (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="0a5a7-215">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="0a5a7-216">Obtenga la información del directorio (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="0a5a7-216">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="0a5a7-217">Configure las notificaciones de cambio (mediante <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="0a5a7-217">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="0a5a7-218">`IFileInfo` proporciona métodos y propiedades para trabajar con archivos:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-218">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="0a5a7-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (en bytes)</span><span class="sxs-lookup"><span data-stu-id="0a5a7-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="0a5a7-220">Fecha <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified></span><span class="sxs-lookup"><span data-stu-id="0a5a7-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="0a5a7-221">Puede leer del archivo mediante el método [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*).</span><span class="sxs-lookup"><span data-stu-id="0a5a7-221">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="0a5a7-222">La aplicación de ejemplo muestra cómo configurar un proveedor de archivos en `Startup.ConfigureServices` para su uso en toda la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0a5a7-222">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="0a5a7-223">Implementaciones del proveedor de archivos</span><span class="sxs-lookup"><span data-stu-id="0a5a7-223">File Provider implementations</span></span>

<span data-ttu-id="0a5a7-224">Hay tres implementaciones de `IFileProvider` disponibles.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-224">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="0a5a7-225">Implementación</span><span class="sxs-lookup"><span data-stu-id="0a5a7-225">Implementation</span></span> | <span data-ttu-id="0a5a7-226">Descripción</span><span class="sxs-lookup"><span data-stu-id="0a5a7-226">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="0a5a7-227">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="0a5a7-227">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="0a5a7-228">El proveedor físico se utiliza para acceder a los archivos físicos del sistema.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-228">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="0a5a7-229">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="0a5a7-229">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="0a5a7-230">El proveedor insertado de manifiestos se utiliza para tener acceder a archivos insertados en ensamblados.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-230">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="0a5a7-231">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="0a5a7-231">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="0a5a7-232">El proveedor compuesto se utiliza para proporcionar acceso combinado a archivos y directorios de uno o más proveedores.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-232">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="0a5a7-233">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="0a5a7-233">PhysicalFileProvider</span></span>

<span data-ttu-id="0a5a7-234"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> proporciona acceso al sistema de archivos físico.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-234">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="0a5a7-235">`PhysicalFileProvider` usa el tipo <xref:System.IO.File?displayProperty=fullName> (para el proveedor físico) y define el ámbito de todas las rutas de acceso a un directorio y sus elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-235">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="0a5a7-236">Esta definición de ámbito impide el acceso al sistema de archivos fuera del directorio especificado y sus elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-236">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="0a5a7-237">El escenario más común para crear y usar `PhysicalFileProvider` es solicitar `IFileProvider` en un constructor mediante la [inyección de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0a5a7-237">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="0a5a7-238">Al crear una instancia de este proveedor directamente, se requiere una ruta de acceso del directorio, que actúa como la ruta de acceso base para todas las solicitudes realizadas usando el proveedor.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-238">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="0a5a7-239">El código siguiente muestra cómo crear `PhysicalFileProvider` y usarlo para obtener el contenido del directorio y la información del archivo:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-239">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="0a5a7-240">Tipos del ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-240">Types in the preceding example:</span></span>

* <span data-ttu-id="0a5a7-241">`provider` es `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-241">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="0a5a7-242">`contents` es `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-242">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="0a5a7-243">`fileInfo` es `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-243">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="0a5a7-244">El proveedor de archivos puede usarse para iterar por el directorio especificado por `applicationRoot` o llamar a `GetFileInfo` para obtener información de un archivo.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-244">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="0a5a7-245">El proveedor de archivos no tiene acceso fuera del directorio `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-245">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="0a5a7-246">La aplicación de ejemplo crea el proveedor en la clase `Startup.ConfigureServices` de la aplicación mediante [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="0a5a7-246">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="0a5a7-247">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="0a5a7-247">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="0a5a7-248"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> se utiliza para acceder a archivos insertados dentro de ensamblados.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-248">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="0a5a7-249">`ManifestEmbeddedFileProvider` utiliza un manifiesto compilado en el ensamblado para reconstruir las rutas de acceso originales de los archivos incrustados.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-249">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="0a5a7-250">Para generar un manifiesto de los archivos incrustados, establezca la propiedad `<GenerateEmbeddedFilesManifest>` en `true`.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-250">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="0a5a7-251">Especifique los archivos que desea incrustar con [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="0a5a7-251">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="0a5a7-252">Use [patrones globales](#glob-patterns) para especificar uno o varios archivos para incrustar en el ensamblado.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-252">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="0a5a7-253">La aplicación de ejemplo crea `ManifestEmbeddedFileProvider` y pasa el ensamblado que se está ejecutando actualmente a su constructor.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-253">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="0a5a7-254">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-254">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="0a5a7-255">Las sobrecargas adicionales le permiten:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-255">Additional overloads allow you to:</span></span>

* <span data-ttu-id="0a5a7-256">Especificar una ruta de acceso de archivo relativa.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-256">Specify a relative file path.</span></span>
* <span data-ttu-id="0a5a7-257">Definir el ámbito de archivos a la fecha de la última modificación.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-257">Scope files to a last modified date.</span></span>
* <span data-ttu-id="0a5a7-258">Asignar nombre al recurso incrustado que contiene el manifiesto del archivo incrustado.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-258">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="0a5a7-259">Sobrecarga</span><span class="sxs-lookup"><span data-stu-id="0a5a7-259">Overload</span></span> | <span data-ttu-id="0a5a7-260">Descripción</span><span class="sxs-lookup"><span data-stu-id="0a5a7-260">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="0a5a7-261">Acepta parámetro de ruta de acceso relativa `root` opcional.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-261">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="0a5a7-262">Especifique `root` para definir el ámbito de las llamadas en <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> en aquellos recursos que se encuentran bajo las rutas de acceso proporcionadas.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-262">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="0a5a7-263">Acepta un parámetro de ruta de acceso relativa `root` opcional y un parámetro de fecha `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="0a5a7-263">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="0a5a7-264">La fecha `lastModified` define el ámbito de la última fecha de modificación para las instancias de <xref:Microsoft.Extensions.FileProviders.IFileInfo> que <xref:Microsoft.Extensions.FileProviders.IFileProvider> devuelve.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-264">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="0a5a7-265">Acepta una ruta de acceso relativa `root` opcional, una fecha `lastModified` y parámetros `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-265">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="0a5a7-266">`manifestName` representa el nombre del recurso incrustado que contiene el manifiesto.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-266">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="0a5a7-267">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="0a5a7-267">CompositeFileProvider</span></span>

<span data-ttu-id="0a5a7-268"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combina instancias de `IFileProvider`, y expone una única interfaz para trabajar con archivos de varios proveedores.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-268">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="0a5a7-269">Al crear `CompositeFileProvider`, se pasan una o varias instancias de `IFileProvider` a su constructor.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-269">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="0a5a7-270">En la aplicación de ejemplo, `PhysicalFileProvider` y `ManifestEmbeddedFileProvider` proporcionan archivos a un `CompositeFileProvider` registrado en el contenedor de servicios de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-270">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="0a5a7-271">Observación de cambios</span><span class="sxs-lookup"><span data-stu-id="0a5a7-271">Watch for changes</span></span>

<span data-ttu-id="0a5a7-272">El método [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) proporciona un escenario para ver uno o más archivos o directorios para detectar cambios.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-272">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="0a5a7-273">`Watch` acepta una cadena de ruta de acceso, que puede usar [patrones globales](#glob-patterns) para especificar varios archivos.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-273">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="0a5a7-274">`Watch` devuelve un valor de <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-274">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="0a5a7-275">El token de cambio expone:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-275">The change token exposes:</span></span>

* <span data-ttu-id="0a5a7-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: una propiedad que se puede inspeccionar para determinar si se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="0a5a7-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: se llama cuando se detectan cambios en la cadena de ruta de acceso especificada.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="0a5a7-278">Cada token de cambio solo llama a su devolución de llamada asociada en respuesta a un único cambio.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-278">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="0a5a7-279">Para habilitar la supervisión constante, puede usar <xref:System.Threading.Tasks.TaskCompletionSource`1> (como se muestra a continuación) o volver a crear instancias de `IChangeToken` en respuesta a los cambios.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-279">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="0a5a7-280">En la aplicación de ejemplo, la aplicación de consola *WatchConsole* se configura para mostrar un mensaje cada vez que se modifica un archivo de texto:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-280">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="0a5a7-281">Algunos sistemas de archivos, como contenedores de Docker y recursos compartidos de red, no pueden enviar notificaciones de cambio de forma confiable.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-281">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="0a5a7-282">Establezca la variable de entorno `DOTNET_USE_POLLING_FILE_WATCHER` en `1` o `true` para sondear el sistema de archivos en busca de cambios cada cuatro segundos (no configurable).</span><span class="sxs-lookup"><span data-stu-id="0a5a7-282">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="0a5a7-283">Patrones globales</span><span class="sxs-lookup"><span data-stu-id="0a5a7-283">Glob patterns</span></span>

<span data-ttu-id="0a5a7-284">Las rutas de acceso del sistema de archivos utilizan patrones de caracteres comodín denominados *patrones globales*.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-284">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="0a5a7-285">Especifique grupos de archivos con estos patrones.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-285">Specify groups of files with these patterns.</span></span> <span data-ttu-id="0a5a7-286">Los dos caracteres comodín son `*` y `**`:</span><span class="sxs-lookup"><span data-stu-id="0a5a7-286">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="0a5a7-287">Coincide con cualquier elemento del nivel de carpeta actual, con cualquier nombre de archivo o con cualquier extensión de archivo.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-287">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="0a5a7-288">Las coincidencias finalizan con los caracteres `/` y `.` en la ruta de acceso de archivo.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-288">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="0a5a7-289">Coincide con cualquier elemento de varios niveles de directorios.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-289">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="0a5a7-290">Puede usarse para coincidir de forma recursiva con muchos archivos dentro de una jerarquía de directorios.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-290">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="0a5a7-291">**Ejemplos de patrones globales**</span><span class="sxs-lookup"><span data-stu-id="0a5a7-291">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="0a5a7-292">Coincide con un archivo concreto en un directorio específico.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-292">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="0a5a7-293">Coincide con todos los archivos que tengan la extensión *.txt* en un directorio específico.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-293">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="0a5a7-294">Coincide con todos los archivos `appsettings.json` que estén en directorios exactamente un nivel por debajo de la carpeta *directorio*.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-294">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="0a5a7-295">Coincide con todos los archivos que tengan la extensión *.txt* y se encuentren en cualquier lugar de la carpeta *directorio*.</span><span class="sxs-lookup"><span data-stu-id="0a5a7-295">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
