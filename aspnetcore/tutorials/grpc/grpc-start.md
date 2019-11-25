---
title: Creación de un servidor y un cliente gRPC en ASP.NET Core
author: juntaoluo
description: En este tutorial se le mostrará cómo crear un servicio gRPC y un cliente gRPC en ASP.NET Core. Aprenda a crear un proyecto de servicio gRPC, edite un archivo proto y agregue una llamada de streaming dúplex.
ms.author: johluo
ms.date: 11/12/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: e5373d9abb9a770132e756843dbd15534dbe3356
ms.sourcegitcommit: 231780c8d7848943e5e9fd55e93f437f7e5a371d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2019
ms.locfileid: "74116105"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="4b2eb-104">Tutorial: Crear un servidor y un cliente gRPC en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b2eb-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="4b2eb-105">Por [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="4b2eb-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="4b2eb-106">En este tutorial se muestra cómo crear un cliente [gRPC](https://grpc.io/docs/guides/) de .NET Core y un servidor gRPC de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="4b2eb-107">Al final tendrá un cliente gRPC que se comunica con el servicio Greeter de gRPC.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="4b2eb-108">[Vea o descargue el código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4b2eb-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="4b2eb-109">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="4b2eb-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4b2eb-110">Crear un servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="4b2eb-111">Crear un cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="4b2eb-112">Probar el servicio cliente gRPC con el servicio gRPC Greeter.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4b2eb-113">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="4b2eb-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4b2eb-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b2eb-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4b2eb-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b2eb-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4b2eb-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4b2eb-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="4b2eb-117">Crear un servicio gRPC</span><span class="sxs-lookup"><span data-stu-id="4b2eb-117">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4b2eb-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b2eb-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4b2eb-119">Inicie Visual Studio y seleccione **Crear un proyecto**.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-119">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="4b2eb-120">Alternativamente, en el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-120">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4b2eb-121">En el cuadro de diálogo **Crear un proyecto**, seleccione **Servicio gRPC** y elija **Siguiente**:</span><span class="sxs-lookup"><span data-stu-id="4b2eb-121">In the **Create a new project** dialog, select **gRPC Service** and select **Next**:</span></span>

  ![Cuadro de diálogo **Crear un proyecto**](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="4b2eb-123">Llame al proyecto **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-123">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="4b2eb-124">Es importante asignarle el nombre *GrpcGreeter* para que los espacios de nombres coincidan al copiar y pegar el código.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-124">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="4b2eb-125">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-125">Select **Create**.</span></span>
* <span data-ttu-id="4b2eb-126">En el cuadro de diálogo **Crear un servicio gRPC**:</span><span class="sxs-lookup"><span data-stu-id="4b2eb-126">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="4b2eb-127">Se selecciona la plantilla **Servicio gRPC**.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-127">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="4b2eb-128">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-128">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4b2eb-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b2eb-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4b2eb-130">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4b2eb-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4b2eb-131">Cambie los directorios (`cd`) a una carpeta que contenga el proyecto.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="4b2eb-132">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="4b2eb-132">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="4b2eb-133">El comando `dotnet new` crea un nuevo servicio gRPC en la carpeta *GrpcGreeter*.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="4b2eb-134">El comando `code` abre la carpeta *GrpcGreeter* en una nueva instancia de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="4b2eb-135">Se muestra un cuadro de diálogo con el texto **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).</span><span class="sxs-lookup"><span data-stu-id="4b2eb-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="4b2eb-136">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-136">Select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4b2eb-137">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4b2eb-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4b2eb-138">Desde un terminal, ejecute estos comandos:</span><span class="sxs-lookup"><span data-stu-id="4b2eb-138">From a terminal, run the following commands:</span></span>

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

<span data-ttu-id="4b2eb-139">Los comandos anteriores utilizan la [CLI de .NET Core](/dotnet/core/tools/dotnet) para crear un servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="4b2eb-140">Abrir el proyecto</span><span class="sxs-lookup"><span data-stu-id="4b2eb-140">Open the project</span></span>

<span data-ttu-id="4b2eb-141">En Visual Studio, seleccione **Archivo** > **Abrir** y seleccione el archivo *GrpcGreeter.csproj*.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-141">From Visual Studio, select **File** > **Open**, and then select the *GrpcGreeter.csproj* file.</span></span>

---

### <a name="run-the-service"></a><span data-ttu-id="4b2eb-142">Ejecutar el servicio</span><span class="sxs-lookup"><span data-stu-id="4b2eb-142">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="4b2eb-143">Los registros muestran que el servicio está escuchando en `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-143">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="4b2eb-144">La plantilla gRPC está configurada para usar [Seguridad de la capa de transporte (TLS)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="4b2eb-144">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="4b2eb-145">Los clientes de gRPC necesitan usar HTTPS para llamar al servidor.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-145">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="4b2eb-146">macOS no admite gRPC de ASP.NET Core con TLS.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-146">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="4b2eb-147">Se requiere configuración adicional para ejecutar correctamente servicios gRPC en macOS.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-147">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="4b2eb-148">Para obtener más información, vea [No se puede iniciar la aplicación gRPC de ASP.NET Core en macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="4b2eb-148">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="4b2eb-149">Examen de los archivo del proyecto</span><span class="sxs-lookup"><span data-stu-id="4b2eb-149">Examine the project files</span></span>

<span data-ttu-id="4b2eb-150">Archivos de proyecto de *GrpcGreeter*:</span><span class="sxs-lookup"><span data-stu-id="4b2eb-150">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="4b2eb-151">*greet.proto* &ndash; el archivo *Protos/greet.proto* define el gRPC `Greeter` y se usa para generar los recursos de servidor gRPC.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-151">*greet.proto* &ndash; The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="4b2eb-152">Para obtener más información, vea [Introducción a gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="4b2eb-152">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="4b2eb-153">Carpeta *Servicios*: contiene la implementación del servicio `Greeter`.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-153">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="4b2eb-154">*appSettings.json* &ndash; contiene datos de configuración, como el protocolo usado por Kestrel.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-154">*appSettings.json* &ndash; Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="4b2eb-155">Para más información, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="4b2eb-156">*Program.cs* &ndash; contiene el punto de entrada para el servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-156">*Program.cs* &ndash; Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="4b2eb-157">Para más información, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-157">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="4b2eb-158">*Startup.cs* &ndash; contiene código que configura el comportamiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-158">*Startup.cs* &ndash; Contains code that configures app behavior.</span></span> <span data-ttu-id="4b2eb-159">Para obtener más información, vea [Inicio de la aplicación](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="4b2eb-159">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="4b2eb-160">Creación del cliente gRPC en una aplicación de consola de .NET</span><span class="sxs-lookup"><span data-stu-id="4b2eb-160">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4b2eb-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b2eb-161">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4b2eb-162">Abra una segunda instancia de Visual Studio y seleccione **Crear un proyecto**.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-162">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="4b2eb-163">En el cuadro de diálogo **Crear un proyecto**, seleccione **Aplicación de consola (.NET Core)** y elija **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-163">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="4b2eb-164">En el cuadro de texto **Nombre**, escriba **GrpcGreeterClient** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-164">In the **Name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4b2eb-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b2eb-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4b2eb-166">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4b2eb-166">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4b2eb-167">Cambie los directorios (`cd`) a una carpeta que contenga el proyecto.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-167">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="4b2eb-168">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="4b2eb-168">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4b2eb-169">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4b2eb-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4b2eb-170">Siga las instrucciones de [Creación de una solución completa de .NET Core en macOS con Visual Studio para Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) para crear una aplicación de consola con el nombre *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-170">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="4b2eb-171">Adición de paquetes necesarios</span><span class="sxs-lookup"><span data-stu-id="4b2eb-171">Add required packages</span></span>

<span data-ttu-id="4b2eb-172">El proyecto de cliente gRPC requiere los siguientes paquetes:</span><span class="sxs-lookup"><span data-stu-id="4b2eb-172">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="4b2eb-173">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), que contiene el cliente de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-173">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="4b2eb-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), que contiene API de mensajes protobuf para C#.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="4b2eb-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), que contiene compatibilidad con herramientas de C# para archivos protobuf.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="4b2eb-176">El paquete de herramientas no es necesario en el runtime, de modo que la dependencia se marca con `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-176">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4b2eb-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b2eb-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4b2eb-178">Instale los paquetes con la Consola del Administrador de paquetes (PMC) o mediante Administrar paquetes NuGet.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-178">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="4b2eb-179">Opción de PMC para instalar paquetes</span><span class="sxs-lookup"><span data-stu-id="4b2eb-179">PMC option to install packages</span></span>

* <span data-ttu-id="4b2eb-180">En Visual Studio, seleccione **Herramientas** > **Administrador de paquetes de NuGet** > **Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-180">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="4b2eb-181">En la ventana **Consola del Administrador de paquetes**, ejecute `cd GrpcGreeterClient` para cambiar los directorios a la carpeta que contiene los archivos *GrpcGreeterClient.csproj*.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-181">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="4b2eb-182">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="4b2eb-182">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="4b2eb-183">Administración de la opción Paquetes NuGet para instalar paquetes</span><span class="sxs-lookup"><span data-stu-id="4b2eb-183">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="4b2eb-184">Haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** > **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-184">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="4b2eb-185">Seleccione la pestaña **Examinar**.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-185">Select the **Browse** tab.</span></span>
* <span data-ttu-id="4b2eb-186">Escriba **Grpc.Net.Client** en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-186">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="4b2eb-187">Seleccione el paquete **Grpc.Net.Client** en la pestaña **Examinar** y haga clic en **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-187">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="4b2eb-188">Repita el proceso para `Google.Protobuf` y `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-188">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4b2eb-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b2eb-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4b2eb-190">Ejecute los siguientes comandos en el **terminal integrado**:</span><span class="sxs-lookup"><span data-stu-id="4b2eb-190">Run the following commands from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4b2eb-191">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4b2eb-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4b2eb-192">Haga clic con el botón derecho en la carpeta **Paquetes**, en **Panel de solución** > **Agregar paquetes**.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-192">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="4b2eb-193">Escriba **Grpc.Net.Client** en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-193">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="4b2eb-194">Seleccione el paquete **Grpc.Net.Client** en el panel de resultados y seleccione **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-194">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="4b2eb-195">Repita el proceso para `Google.Protobuf` y `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-195">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="4b2eb-196">Adición de greet.proto</span><span class="sxs-lookup"><span data-stu-id="4b2eb-196">Add greet.proto</span></span>

* <span data-ttu-id="4b2eb-197">Cree una carpeta *Protos* en el proyecto de cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-197">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="4b2eb-198">Copie el archivo *Protos\greet.proto* del servicio gRPC Greeter en el proyecto de cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-198">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="4b2eb-199">Edite el archivo de proyecto *GrpcGreeterClient.csproj*:</span><span class="sxs-lookup"><span data-stu-id="4b2eb-199">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4b2eb-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b2eb-200">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="4b2eb-201">Haga clic con el botón derecho en el proyecto y seleccione **Editar archivo del proyecto**.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-201">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4b2eb-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b2eb-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="4b2eb-203">Seleccione el archivo *GrpcGreeterClient.csproj*.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-203">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4b2eb-204">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4b2eb-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="4b2eb-205">Haga clic con el botón derecho en el proyecto y seleccione **Herramientas** > **Editar archivo**.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-205">Right-click the project and select **Tools** > **Edit File**.</span></span>

  ---

* <span data-ttu-id="4b2eb-206">Agregue un grupo de elementos con un elemento `<Protobuf>` que hace referencia al archivo *greet.proto*:</span><span class="sxs-lookup"><span data-stu-id="4b2eb-206">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="4b2eb-207">Creación del cliente de Greeter</span><span class="sxs-lookup"><span data-stu-id="4b2eb-207">Create the Greeter client</span></span>

<span data-ttu-id="4b2eb-208">Compile el proyecto para crear los tipos en el espacio de nombres `GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-208">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="4b2eb-209">El proceso de compilación genera automáticamente los tipos `GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-209">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="4b2eb-210">Actualice el archivo *Program.cs* del cliente gRPC con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4b2eb-210">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="4b2eb-211">*Program.cs* contiene el punto de entrada y la lógica para el cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-211">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="4b2eb-212">El cliente de Greeter se crea mediante lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="4b2eb-212">The Greeter client is created by:</span></span>

* <span data-ttu-id="4b2eb-213">Creación de una instancia de `GrpcChannel` que contiene la información para crear la conexión al servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-213">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="4b2eb-214">Uso de `GrpcChannel` para construir el cliente de Greeter:</span><span class="sxs-lookup"><span data-stu-id="4b2eb-214">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="4b2eb-215">El cliente de Greeter realiza una llamada al método `SayHello` asincrónico.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-215">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="4b2eb-216">Se muestra el resultado de la llamada a `SayHello`:</span><span class="sxs-lookup"><span data-stu-id="4b2eb-216">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="4b2eb-217">Prueba del cliente gRPC con el servicio gRPC Greeter</span><span class="sxs-lookup"><span data-stu-id="4b2eb-217">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4b2eb-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b2eb-218">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4b2eb-219">En el servicio Greeter, presione `Ctrl+F5` para iniciar el servidor sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-219">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="4b2eb-220">En el proyecto `GrpcGreeterClient`, presione `Ctrl+F5` para iniciar el cliente sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-220">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4b2eb-221">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b2eb-221">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4b2eb-222">Inicie el servicio Greeter.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-222">Start the Greeter service.</span></span>
* <span data-ttu-id="4b2eb-223">Inicie el cliente.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-223">Start the client.</span></span>


# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4b2eb-224">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4b2eb-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4b2eb-225">Inicie el servicio Greeter.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-225">Start the Greeter service.</span></span>
* <span data-ttu-id="4b2eb-226">Inicie el cliente.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-226">Start the client.</span></span>

---

<span data-ttu-id="4b2eb-227">El cliente envía un saludo al servicio con un mensaje que contiene su nombre *GreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-227">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="4b2eb-228">El servicio envía el mensaje "Hello GreeterClient" como respuesta.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-228">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="4b2eb-229">La respuesta "Hello GreeterClient" se muestra en el símbolo del sistema:</span><span class="sxs-lookup"><span data-stu-id="4b2eb-229">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="4b2eb-230">El servicio gRPC registra los detalles de la llamada correcta en los registros escritos en el símbolo del sistema:</span><span class="sxs-lookup"><span data-stu-id="4b2eb-230">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

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

> [!NOTE]
> <span data-ttu-id="4b2eb-231">El código de este artículo requiere el certificado de desarrollo de .NET Core HTTPS para proteger el servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-231">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="4b2eb-232">Si se produce un error con el mensaje `The remote certificate is invalid according to the validation procedure.` en el cliente, el certificado de desarrollo no es de confianza.</span><span class="sxs-lookup"><span data-stu-id="4b2eb-232">If the client fails with the message `The remote certificate is invalid according to the validation procedure.`, the development certificate is not trusted.</span></span> <span data-ttu-id="4b2eb-233">Para instrucciones sobre cómo corregir este problema, consulte [Confiar en el certificado de desarrollo de ASP.NET Core HTTPS en Windows y macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="4b2eb-233">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="4b2eb-234">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="4b2eb-234">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
