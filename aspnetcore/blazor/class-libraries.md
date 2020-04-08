---
title: Bibliotecas de clases de componentes de Razor de ASP.NET Core
author: guardrex
description: Descubra cómo se pueden incluir componentes en aplicaciones de Blazor desde una biblioteca de componentes externa.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: f2cc57638922bd1f6ab036adb2ed37209d14c5b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218771"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>Bibliotecas de clases de componentes de Razor de ASP.NET Core

Por [Simon Timms](https://github.com/stimms)

Los componentes se pueden compartir entre proyectos en una [biblioteca de clases de Razor (RCL)](xref:razor-pages/ui-class). Se puede incluir una *biblioteca de clases de componentes de Razor* desde:

* Otro proyecto de la solución.
* Un paquete NuGet.
* Una biblioteca de .NET a la que se hace referencia.

Del mismo modo que los componentes son tipos normales de .NET, los componentes proporcionados por una RCL son ensamblados .NET normales.

## <a name="create-an-rcl"></a>Creación de una RCL

Siga las instrucciones del artículo <xref:blazor/get-started> para configurar su entorno para Blazor.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Cree un nuevo proyecto.
1. Seleccione **Biblioteca de clases de Razor**. Seleccione **Siguiente**.
1. En el cuadro de diálogo **Crear una biblioteca de clases de Razor**, seleccione **Crear**.
1. Proporcione un nombre para el proyecto en el campo **Nombre del proyecto** o acepte el predeterminado. En los ejemplos de este tema se usa el nombre de proyecto `MyComponentLib1`. Seleccione **Crear**.
1. Agregue la RCL a una solución:
   1. Haga clic con el botón derecho en la solución. Seleccione **Agregar** > **Proyecto existente**.
   1. Navegue hasta el archivo del proyecto de la RCL.
   1. Seleccione el archivo de proyecto de la RCL ( *.csproj*).
1. Agregue una referencia a la RCL desde la aplicación:
   1. Haga clic con el botón derecho en el proyecto de la aplicación. Seleccione **Agregar** > **Referencia**.
   1. Seleccione el proyecto de la RCL. Seleccione **Aceptar**.

> [!NOTE]
> Si la casilla **Admitir páginas y vistas** está activada al generar la RCL desde la plantilla, agregue también un archivo *_Imports.razor* a la raíz del proyecto generado con el siguiente contenido para habilitar la creación de componentes de Razor:
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Agregue manualmente el archivo a la raíz del proyecto generado.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

1. Use la plantilla **Biblioteca de clases de Razor** (`razorclasslib`) con el comando [dotnet new](/dotnet/core/tools/dotnet-new) en un shell de comandos. En el ejemplo siguiente, se crea una RCL llamada `MyComponentLib1`. La carpeta que contiene `MyComponentLib1` se crea automáticamente cuando se ejecuta el comando:

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > Si se usa el modificador `-s|--support-pages-and-views` al generar la biblioteca de clases de Razor desde la plantilla, agregue también un archivo *_Imports.razor* a la raíz del proyecto generado con el siguiente contenido para habilitar la creación de componentes de Razor:
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Agregue manualmente el archivo a la raíz del proyecto generado.

1. Para agregar la biblioteca a un proyecto existente, use el comando [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) en un shell de comandos. En el siguiente ejemplo, se agrega la RCL a la aplicación. Ejecute el siguiente comando desde la carpeta de proyecto de la aplicación con la ruta de acceso a la biblioteca:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Consumo de un componente de biblioteca

Para consumir los componentes definidos en una biblioteca de otro proyecto, siga cualquiera de los métodos siguientes:

* Use el nombre de tipo completo con el espacio de nombres.
* Use la directiva [\@using](xref:mvc/views/razor#using) de Razor. Los componentes individuales se pueden agregar por nombre.

En los ejemplos siguientes, `MyComponentLib1` es una biblioteca de componentes que contiene un componente `SalesReport`.

Se puede hacer referencia al componente `SalesReport` usando su nombre de tipo completo con el espacio de nombres:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

También se puede hacer referencia al componente si la biblioteca se incluye en el ámbito con una directiva `@using`:

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

Incluya la directiva `@using MyComponentLib1` en el archivo de nivel superior *_Import.razor* para que los componentes de la biblioteca estén disponibles para un proyecto completo. Agregue la directiva a un archivo *_Import.razor* en cualquier nivel para aplicar el espacio de nombres a una sola página o a un conjunto de páginas dentro de una carpeta.

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Creación de una biblioteca de clases de componentes de Razor con recursos estáticos

Una RCL puede incluir recursos estáticos. Los recursos estáticos están disponibles para cualquier aplicación que use la biblioteca. Para obtener más información, consulta <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="build-pack-and-ship-to-nuget"></a>Compilación, empaquetado y envío de bibliotecas a NuGet

Dado que las bibliotecas de componentes son bibliotecas estándar de .NET, se empaquetan y se envían a NuGet de la misma manera que cualquier otra biblioteca. El empaquetado se realiza mediante el comando [dotnet pack](/dotnet/core/tools/dotnet-pack) en un shell de comandos:

```dotnetcli
dotnet pack
```

Cargue el paquete en NuGet usando el comando [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) en un shell de comandos.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:razor-pages/ui-class>
* [Adición de un archivo de configuración del enlazador XML a una biblioteca](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
