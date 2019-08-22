---
title: Creación de un servidor y un cliente gRPC en ASP.NET Core
author: juntaoluo
description: En este tutorial se le mostrará cómo crear un servicio gRPC y un cliente gRPC en ASP.NET Core. Aprenda a crear un proyecto de servicio gRPC, edite un archivo proto y agregue una llamada de streaming dúplex.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 08/07/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 496f659bd51e2404a936bea8aad77e674e1a285d
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022494"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="0151d-104">Tutorial: Crear un servidor y un cliente gRPC en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0151d-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="0151d-105">Por [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="0151d-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="0151d-106">En este tutorial se muestra cómo crear un cliente [gRPC](https://grpc.io/docs/guides/) de .NET Core y un servidor gRPC de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0151d-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="0151d-107">Al final tendrá un cliente gRPC que se comunica con el servicio Greeter de gRPC.</span><span class="sxs-lookup"><span data-stu-id="0151d-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="0151d-108">[Vea o descargue el código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="0151d-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="0151d-109">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="0151d-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0151d-110">Crear un servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="0151d-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="0151d-111">Crear un cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="0151d-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="0151d-112">Probar el servicio cliente gRPC con el servicio gRPC Greeter.</span><span class="sxs-lookup"><span data-stu-id="0151d-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0151d-113">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="0151d-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0151d-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0151d-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0151d-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0151d-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0151d-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0151d-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="0151d-117">Crear un servicio gRPC</span><span class="sxs-lookup"><span data-stu-id="0151d-117">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0151d-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0151d-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0151d-119">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="0151d-119">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0151d-120">En el cuadro de diálogo **Crear un proyecto nuevo**, seleccione **Aplicación web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="0151d-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="0151d-121">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="0151d-121">Select **Next**</span></span>
* <span data-ttu-id="0151d-122">Llame al proyecto **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="0151d-122">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="0151d-123">Es importante asignarle el nombre *GrpcGreeter* para que los espacios de nombres coincidan al copiar y pegar el código.</span><span class="sxs-lookup"><span data-stu-id="0151d-123">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="0151d-124">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="0151d-124">Select **Create**</span></span>
* <span data-ttu-id="0151d-125">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**:</span><span class="sxs-lookup"><span data-stu-id="0151d-125">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="0151d-126">Seleccione **.NET Core** y **ASP.NET Core 3.0** en los menús desplegables.</span><span class="sxs-lookup"><span data-stu-id="0151d-126">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="0151d-127">Seleccione la plantilla **Servicio gRPC**.</span><span class="sxs-lookup"><span data-stu-id="0151d-127">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="0151d-128">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="0151d-128">Select **Create**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0151d-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0151d-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0151d-130">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="0151d-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0151d-131">Cambie los directorios (`cd`) a una carpeta que contenga el proyecto.</span><span class="sxs-lookup"><span data-stu-id="0151d-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="0151d-132">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="0151d-132">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="0151d-133">El comando `dotnet new` crea un nuevo servicio gRPC en la carpeta *GrpcGreeter*.</span><span class="sxs-lookup"><span data-stu-id="0151d-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="0151d-134">El comando `code` abre la carpeta *GrpcGreeter* en una nueva instancia de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="0151d-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="0151d-135">Se muestra un cuadro de diálogo con el texto **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).</span><span class="sxs-lookup"><span data-stu-id="0151d-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="0151d-136">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="0151d-136">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0151d-137">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0151d-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0151d-138">Desde un terminal, ejecute estos comandos:</span><span class="sxs-lookup"><span data-stu-id="0151d-138">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="0151d-139">Los comandos anteriores utilizan la [CLI de .NET Core](/dotnet/core/tools/dotnet) para crear un servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="0151d-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="0151d-140">Abrir el proyecto</span><span class="sxs-lookup"><span data-stu-id="0151d-140">Open the project</span></span>

<span data-ttu-id="0151d-141">En Visual Studio, seleccione **Archivo > Abrir** y elija el archivo *GrpcGreeter.sln*.</span><span class="sxs-lookup"><span data-stu-id="0151d-141">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a><span data-ttu-id="0151d-142">Ejecutar el servicio</span><span class="sxs-lookup"><span data-stu-id="0151d-142">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0151d-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0151d-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0151d-144">Presione `Ctrl+F5` para ejecutar el servicio gRPC sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="0151d-144">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="0151d-145">Visual Studio ejecuta el servicio en un símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="0151d-145">Visual Studio runs the service in a command prompt.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="0151d-146">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0151d-146">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0151d-147">Ejecute el proyecto *GrpcGreeter* de gRPC Greeter desde la línea de comandos mediante `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="0151d-147">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="0151d-148">Los registros muestran que el servicio está escuchando en `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="0151d-148">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="0151d-149">La plantilla gRPC está configurada para usar [Seguridad de la capa de transporte (TLS)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="0151d-149">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="0151d-150">Los clientes de gRPC necesitan usar HTTPS para llamar al servidor.</span><span class="sxs-lookup"><span data-stu-id="0151d-150">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="0151d-151">macOS no admite gRPC de ASP.NET Core con TLS.</span><span class="sxs-lookup"><span data-stu-id="0151d-151">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="0151d-152">Se requiere configuración adicional para ejecutar correctamente servicios gRPC en macOS.</span><span class="sxs-lookup"><span data-stu-id="0151d-152">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="0151d-153">Para obtener más información, vea [No se puede iniciar la aplicación gRPC de ASP.NET Core en macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="0151d-153">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="0151d-154">Examen de los archivo del proyecto</span><span class="sxs-lookup"><span data-stu-id="0151d-154">Examine the project files</span></span>

<span data-ttu-id="0151d-155">Archivos de proyecto de *GrpcGreeter*:</span><span class="sxs-lookup"><span data-stu-id="0151d-155">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="0151d-156">*greet.proto*: El archivo *Protos/greet.proto* define el gRPC `Greeter` y se usa para generar los recursos de servidor gRPC.</span><span class="sxs-lookup"><span data-stu-id="0151d-156">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="0151d-157">Para obtener más información, vea [Introducción a gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="0151d-157">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="0151d-158">Carpeta *Servicios*: contiene la implementación del servicio `Greeter`.</span><span class="sxs-lookup"><span data-stu-id="0151d-158">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="0151d-159">*appSettings.json*: contiene datos de configuración, como el protocolo usado por Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0151d-159">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="0151d-160">Para más información, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="0151d-160">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="0151d-161">*Program.cs*: contiene el punto de entrada para el servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="0151d-161">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="0151d-162">Para más información, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="0151d-162">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="0151d-163">*Startup.cs*: Contiene código que configura el comportamiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0151d-163">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="0151d-164">Para obtener más información, vea [Inicio de la aplicación](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="0151d-164">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="0151d-165">Creación del cliente gRPC en una aplicación de consola de .NET</span><span class="sxs-lookup"><span data-stu-id="0151d-165">Create the gRPC client in a .NET console app</span></span>

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0151d-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0151d-166">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0151d-167">Abra una segunda instancia de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0151d-167">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="0151d-168">Seleccione **Archivo** > **Nuevo** > **Proyecto** de la barra de menús.</span><span class="sxs-lookup"><span data-stu-id="0151d-168">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="0151d-169">En el cuadro de diálogo **Crear un nuevo proyecto**, seleccione **Aplicación de consola (.NET Core)** .</span><span class="sxs-lookup"><span data-stu-id="0151d-169">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="0151d-170">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="0151d-170">Select **Next**</span></span>
* <span data-ttu-id="0151d-171">En el cuadro de texto **Nombre**, escriba "GrpcGreeterClient".</span><span class="sxs-lookup"><span data-stu-id="0151d-171">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="0151d-172">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="0151d-172">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0151d-173">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0151d-173">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0151d-174">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="0151d-174">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0151d-175">Cambie los directorios (`cd`) a una carpeta que contenga el proyecto.</span><span class="sxs-lookup"><span data-stu-id="0151d-175">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="0151d-176">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="0151d-176">Run the following commands:</span></span>

```console
dotnet new console -o GrpcGreeterClient
code -r GrpcGreeterClient
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0151d-177">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0151d-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0151d-178">Siga las instrucciones que se indican [aquí](/dotnet/core/tutorials/using-on-mac-vs-full-solution) para crear una aplicación de consola con el nombre *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="0151d-178">Follow the instructions [here](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

<!-- End of VS tabs -->

---

### <a name="add-required-packages"></a><span data-ttu-id="0151d-179">Adición de paquetes necesarios</span><span class="sxs-lookup"><span data-stu-id="0151d-179">Add required packages</span></span>

<span data-ttu-id="0151d-180">El proyecto de cliente gRPC requiere los siguientes paquetes:</span><span class="sxs-lookup"><span data-stu-id="0151d-180">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="0151d-181">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), que contiene el cliente de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="0151d-181">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="0151d-182">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), que contiene API de mensajes protobuf para C#.</span><span class="sxs-lookup"><span data-stu-id="0151d-182">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="0151d-183">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), que contiene compatibilidad con herramientas de C# para archivos protobuf.</span><span class="sxs-lookup"><span data-stu-id="0151d-183">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="0151d-184">El paquete de herramientas no es necesario en el runtime, de modo que la dependencia se marca con `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="0151d-184">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0151d-185">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0151d-185">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0151d-186">Instale los paquetes con la Consola del Administrador de paquetes (PMC) o mediante Administrar paquetes NuGet.</span><span class="sxs-lookup"><span data-stu-id="0151d-186">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="0151d-187">Opción de PMC para instalar paquetes</span><span class="sxs-lookup"><span data-stu-id="0151d-187">PMC option to install packages</span></span>

* <span data-ttu-id="0151d-188">En Visual Studio, seleccione **Herramientas** > **Administrador de paquetes de NuGet** > **Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="0151d-188">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="0151d-189">En la ventana de la **Consola del Administrador de paquetes**, desplácese al directorio en el que se encuentra el archivo *GrpcGreeterClient.csproj*.</span><span class="sxs-lookup"><span data-stu-id="0151d-189">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="0151d-190">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="0151d-190">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="0151d-191">Administración de la opción Paquetes NuGet para instalar paquetes</span><span class="sxs-lookup"><span data-stu-id="0151d-191">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="0151d-192">Haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** > **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="0151d-192">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="0151d-193">Seleccione la pestaña **Examinar**.</span><span class="sxs-lookup"><span data-stu-id="0151d-193">Select the **Browse** tab.</span></span>
* <span data-ttu-id="0151d-194">Escriba **Grpc.Net.Client** en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="0151d-194">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="0151d-195">Seleccione el paquete **Grpc.Net.Client** en la pestaña **Examinar** y haga clic en **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="0151d-195">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="0151d-196">Repita el proceso para `Google.Protobuf` y `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="0151d-196">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0151d-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0151d-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0151d-198">Ejecute los siguientes comandos en el **terminal integrado**:</span><span class="sxs-lookup"><span data-stu-id="0151d-198">Run the following commands from the **Integrated Terminal**:</span></span>

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0151d-199">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0151d-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0151d-200">Haga clic con el botón derecho en la carpeta **Paquetes**, en **Panel de solución** > **Agregar paquetes**.</span><span class="sxs-lookup"><span data-stu-id="0151d-200">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="0151d-201">Escriba **Grpc.Net.Client** en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="0151d-201">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="0151d-202">Seleccione el paquete **Grpc.Net.Client** en el panel de resultados y seleccione **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="0151d-202">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="0151d-203">Repita el proceso para `Google.Protobuf` y `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="0151d-203">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="0151d-204">Adición de greet.proto</span><span class="sxs-lookup"><span data-stu-id="0151d-204">Add greet.proto</span></span>

* <span data-ttu-id="0151d-205">Cree una carpeta **Protos** en el proyecto de cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="0151d-205">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="0151d-206">Copie el archivo **Protos\greet.proto** del servicio gRPC Greeter en el proyecto de cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="0151d-206">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="0151d-207">Edite el archivo de proyecto *GrpcGreeterClient.csproj*:</span><span class="sxs-lookup"><span data-stu-id="0151d-207">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0151d-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0151d-208">Visual Studio</span></span>](#tab/visual-studio) 

  <span data-ttu-id="0151d-209">Haga clic con el botón derecho en el proyecto y seleccione **Editar archivo del proyecto**.</span><span class="sxs-lookup"><span data-stu-id="0151d-209">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0151d-210">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0151d-210">Visual Studio Code</span></span>](#tab/visual-studio-code) 

  <span data-ttu-id="0151d-211">Seleccione el archivo *GrpcGreeterClient.csproj*.</span><span class="sxs-lookup"><span data-stu-id="0151d-211">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0151d-212">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0151d-212">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="0151d-213">Haga clic con el botón derecho en el proyecto y seleccione **Herramientas > Editar archivo**.</span><span class="sxs-lookup"><span data-stu-id="0151d-213">Right-click the project and select **Tools > Edit File**.</span></span>

  ---

* <span data-ttu-id="0151d-214">Agregue un grupo de elementos con un elemento `<Protobuf>` que hace referencia al archivo **greet.proto**:</span><span class="sxs-lookup"><span data-stu-id="0151d-214">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```XML
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="0151d-215">Creación del cliente de Greeter</span><span class="sxs-lookup"><span data-stu-id="0151d-215">Create the Greeter client</span></span>

<span data-ttu-id="0151d-216">Compile el proyecto para crear los tipos en el espacio de nombres `GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="0151d-216">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="0151d-217">El proceso de compilación genera automáticamente los tipos `GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="0151d-217">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="0151d-218">Actualice el archivo *Program.cs* del cliente gRPC con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="0151d-218">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="0151d-219">*Program.cs* contiene el punto de entrada y la lógica para el cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="0151d-219">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="0151d-220">El cliente de Greeter se crea mediante lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="0151d-220">The Greeter client is created by:</span></span>

* <span data-ttu-id="0151d-221">Creación de una instancia de `HttpClient` que contiene la información para crear la conexión al servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="0151d-221">Instantiating an `HttpClient` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="0151d-222">Uso de `HttpClient` para construir el cliente de Greeter:</span><span class="sxs-lookup"><span data-stu-id="0151d-222">Using the `HttpClient` to construct the Greeter client:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="0151d-223">El cliente de Greeter realiza una llamada al método `SayHello` asincrónico.</span><span class="sxs-lookup"><span data-stu-id="0151d-223">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="0151d-224">Se muestra el resultado de la llamada a `SayHello`:</span><span class="sxs-lookup"><span data-stu-id="0151d-224">The result of the `SayHello` call is displayed:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-9)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="0151d-225">Prueba del cliente gRPC con el servicio gRPC Greeter</span><span class="sxs-lookup"><span data-stu-id="0151d-225">Test the gRPC client with the gRPC Greeter service</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0151d-226">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0151d-226">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0151d-227">En el servicio Greeter, presione `Ctrl+F5` para iniciar el servidor sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="0151d-227">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="0151d-228">En el proyecto `GrpcGreeterClient`, presione `Ctrl+F5` para iniciar el cliente sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="0151d-228">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

### <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="0151d-229">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0151d-229">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0151d-230">Inicie el servicio Greeter.</span><span class="sxs-lookup"><span data-stu-id="0151d-230">Start the Greeter service.</span></span>
* <span data-ttu-id="0151d-231">Inicie el cliente.</span><span class="sxs-lookup"><span data-stu-id="0151d-231">Start the client.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="0151d-232">El cliente envía un saludo al servicio con un mensaje que contiene su nombre "GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="0151d-232">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="0151d-233">El servicio envía el mensaje "Hello GreeterClient" como respuesta.</span><span class="sxs-lookup"><span data-stu-id="0151d-233">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="0151d-234">La respuesta "Hello GreeterClient" se muestra en el símbolo del sistema:</span><span class="sxs-lookup"><span data-stu-id="0151d-234">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="0151d-235">El servicio gRPC registra los detalles de la llamada correcta en los registros escritos en el símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="0151d-235">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

### <a name="next-steps"></a><span data-ttu-id="0151d-236">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="0151d-236">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
