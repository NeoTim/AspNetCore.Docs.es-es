---
title: Creación de un servidor y un cliente gRPC en ASP.NET Core
author: juntaoluo
description: En este tutorial se le mostrará cómo crear un servicio gRPC y un cliente gRPC en ASP.NET Core. Aprenda a crear un proyecto de servicio gRPC, edite un archivo proto y agregue una llamada de streaming dúplex.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 8/26/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 7f80ead06f00037ae51b35d40dff9bc7f99bc5d8
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310579"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a>Tutorial: Crear un servidor y un cliente gRPC en ASP.NET Core

Por [John Luo](https://github.com/juntaoluo)

En este tutorial se muestra cómo crear un cliente [gRPC](https://grpc.io/docs/guides/) de .NET Core y un servidor gRPC de ASP.NET Core.

Al final tendrá un cliente gRPC que se comunica con el servicio Greeter de gRPC.

[Vea o descargue el código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).

En este tutorial ha:

> [!div class="checklist"]
> * Crear un servicio gRPC.
> * Crear un cliente gRPC.
> * Probar el servicio cliente gRPC con el servicio gRPC Greeter.

## <a name="prerequisites"></a>Requisitos previos

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a>Crear un servicio gRPC

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Inicie Visual Studio y seleccione **Crear un proyecto**. Alternativamente, en el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.
* En el cuadro de diálogo **Crear un proyecto**, seleccione **Servicio gRPC** y seleccione **Siguiente**:

  ![Cuadro de diálogo **Crear un proyecto**](~/tutorials/grpc/grpc-start/static/cnp.png)

* Llame al proyecto **GrpcGreeter**. Es importante asignarle el nombre *GrpcGreeter* para que los espacios de nombres coincidan al copiar y pegar el código.
* Seleccione **Crear**.
* En el cuadro de diálogo **Crear un servicio gRPC**:
  * Se selecciona la plantilla **Servicio gRPC**.
  * Seleccione **Crear**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Cambie los directorios (`cd`) a una carpeta que contenga el proyecto.
* Ejecute los comandos siguientes:

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * El comando `dotnet new` crea un nuevo servicio gRPC en la carpeta *GrpcGreeter*.
  * El comando `code` abre la carpeta *GrpcGreeter* en una nueva instancia de Visual Studio Code.

  Se muestra un cuadro de diálogo con el texto **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).
* Seleccione **Sí**.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Desde un terminal, ejecute estos comandos:

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

Los comandos anteriores utilizan la [CLI de .NET Core](/dotnet/core/tools/dotnet) para crear un servicio gRPC.

### <a name="open-the-project"></a>Abrir el proyecto

En Visual Studio, seleccione **Archivo** > **Abrir** y seleccione el archivo *GrpcGreeter.sln*.

---

### <a name="run-the-service"></a>Ejecutar el servicio

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Presione `Ctrl+F5` para ejecutar el servicio gRPC sin el depurador.

  Visual Studio ejecuta el servicio en un símbolo del sistema.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ejecute el proyecto *GrpcGreeter* de gRPC Greeter desde la línea de comandos mediante `dotnet run`.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Ejecute el proyecto *GrpcGreeter* de gRPC Greeter desde la línea de comandos mediante `dotnet run`.

---

Los registros muestran que el servicio está escuchando en `https://localhost:5001`.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> La plantilla gRPC está configurada para usar [Seguridad de la capa de transporte (TLS)](https://tools.ietf.org/html/rfc5246). Los clientes de gRPC necesitan usar HTTPS para llamar al servidor.
>
> macOS no admite gRPC de ASP.NET Core con TLS. Se requiere configuración adicional para ejecutar correctamente servicios gRPC en macOS. Para obtener más información, vea [No se puede iniciar la aplicación gRPC de ASP.NET Core en macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

### <a name="examine-the-project-files"></a>Examen de los archivo del proyecto

Archivos de proyecto de *GrpcGreeter*:

* *greet.proto* &ndash; el archivo *Protos/greet.proto* define el gRPC `Greeter` y se usa para generar los recursos de servidor gRPC. Para obtener más información, vea [Introducción a gRPC](xref:grpc/index).
* Carpeta *Servicios*: contiene la implementación del servicio `Greeter`.
* *appSettings.json* &ndash; contiene datos de configuración, como el protocolo usado por Kestrel. Para más información, consulte <xref:fundamentals/configuration/index>.
* *Program.cs* &ndash; contiene el punto de entrada para el servicio gRPC. Para más información, consulte <xref:fundamentals/host/generic-host>.
* *Startup.cs* &ndash; contiene código que configura el comportamiento de la aplicación. Para obtener más información, vea [Inicio de la aplicación](xref:fundamentals/startup).

## <a name="create-the-grpc-client-in-a-net-console-app"></a>Creación del cliente gRPC en una aplicación de consola de .NET

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Abra una segunda instancia de Visual Studio y seleccione **Crear un proyecto**.
* En el cuadro de diálogo **Crear un proyecto**, seleccione **Aplicación de consola (.NET Core)** y elija **Siguiente**.
* En el cuadro de texto **Nombre**, escriba **GrpcGreeterClient** y seleccione **Crear**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Cambie los directorios (`cd`) a una carpeta que contenga el proyecto.
* Ejecute los comandos siguientes:

  ```console
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Siga las instrucciones de [Creación de una solución completa de .NET Core en macOS con Visual Studio para Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) para crear una aplicación de consola con el nombre *GrpcGreeterClient*.

---

### <a name="add-required-packages"></a>Adición de paquetes necesarios

El proyecto de cliente gRPC requiere los siguientes paquetes:

* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), que contiene el cliente de .NET Core.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), que contiene API de mensajes protobuf para C#.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), que contiene compatibilidad con herramientas de C# para archivos protobuf. El paquete de herramientas no es necesario en el runtime, de modo que la dependencia se marca con `PrivateAssets="All"`.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Instale los paquetes con la Consola del Administrador de paquetes (PMC) o mediante Administrar paquetes NuGet.

#### <a name="pmc-option-to-install-packages"></a>Opción de PMC para instalar paquetes

* En Visual Studio, seleccione **Herramientas** > **Administrador de paquetes de NuGet** > **Consola del Administrador de paquetes**.
* En la ventana **Consola del Administrador de paquetes**, ejecute `cd GrpcGreeterClient` para cambiar los directorios a la carpeta que contiene los archivos *GrpcGreeterClient.csproj*.
* Ejecute los comandos siguientes:

  ```powershell
  Install-Package Grpc.Net.Client -prerelease
  Install-Package Google.Protobuf -prerelease
  Install-Package Grpc.Tools -prerelease
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>Administración de la opción Paquetes NuGet para instalar paquetes

* Haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** > **Administrar paquetes NuGet**.
* Seleccione la pestaña **Examinar**.
* Escriba **Grpc.Net.Client** en el cuadro de búsqueda.
* Seleccione el paquete **Grpc.Net.Client** en la pestaña **Examinar** y haga clic en **Instalar**.
* Repita el proceso para `Google.Protobuf` y `Grpc.Tools`.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ejecute los siguientes comandos en el **terminal integrado**:

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Haga clic con el botón derecho en la carpeta **Paquetes**, en **Panel de solución** > **Agregar paquetes**.
* Escriba **Grpc.Net.Client** en el cuadro de búsqueda.
* Seleccione el paquete **Grpc.Net.Client** en el panel de resultados y seleccione **Agregar paquete**.
* Repita el proceso para `Google.Protobuf` y `Grpc.Tools`.

---

### <a name="add-greetproto"></a>Adición de greet.proto

* Cree una carpeta *Protos* en el proyecto de cliente gRPC.
* Copie el archivo *Protos\greet.proto* del servicio gRPC Greeter en el proyecto de cliente gRPC.
* Edite el archivo de proyecto *GrpcGreeterClient.csproj*:

  # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

  Haga clic con el botón derecho en el proyecto y seleccione **Editar archivo del proyecto**.

  # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  Seleccione el archivo *GrpcGreeterClient.csproj*.

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

  Haga clic con el botón derecho en el proyecto y seleccione **Herramientas** > **Editar archivo**.

  ---

* Agregue un grupo de elementos con un elemento `<Protobuf>` que hace referencia al archivo *greet.proto*:

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Creación del cliente de Greeter

Compile el proyecto para crear los tipos en el espacio de nombres `GrpcGreeter`. El proceso de compilación genera automáticamente los tipos `GrpcGreeter`.

Actualice el archivo *Program.cs* del cliente gRPC con el código siguiente:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

*Program.cs* contiene el punto de entrada y la lógica para el cliente gRPC.

El cliente de Greeter se crea mediante lo siguiente:

* Creación de una instancia de `HttpClient` que contiene la información para crear la conexión al servicio gRPC.
* Uso de `HttpClient` para construir un canal gRPC y el cliente de Greeter:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

El cliente de Greeter realiza una llamada al método `SayHello` asincrónico. Se muestra el resultado de la llamada a `SayHello`:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Prueba del cliente gRPC con el servicio gRPC Greeter

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* En el servicio Greeter, presione `Ctrl+F5` para iniciar el servidor sin el depurador.
* En el proyecto `GrpcGreeterClient`, presione `Ctrl+F5` para iniciar el cliente sin el depurador.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Inicie el servicio Greeter.
* Inicie el cliente.


# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Inicie el servicio Greeter.
* Inicie el cliente.

---

El cliente envía un saludo al servicio con un mensaje que contiene su nombre *GreeterClient*. El servicio envía el mensaje "Hello GreeterClient" como respuesta. La respuesta "Hello GreeterClient" se muestra en el símbolo del sistema:

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

El servicio gRPC registra los detalles de la llamada correcta en los registros escritos en el símbolo del sistema:

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
> El código de este artículo requiere el certificado de desarrollo de .NET Core HTTPS para proteger el servicio gRPC. Si se produce un error con el mensaje `The remote certificate is invalid according to the validation procedure.` en el cliente, el certificado de desarrollo no es de confianza. Para instrucciones sobre cómo corregir este problema, consulte [Confiar en el certificado de desarrollo de ASP.NET Core HTTPS en Windows y macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).

### <a name="next-steps"></a>Pasos siguientes

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
