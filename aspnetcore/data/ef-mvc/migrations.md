---
title: 'Tutorial: Uso de la característica de migraciones: ASP.NET MVC con EF Core'
description: En este tutorial, empezará usando la característica de migraciones de EF Core para administrar cambios en el modelo de datos en una aplicación ASP.NET Core MVC.
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/migrations
ms.openlocfilehash: 3d2ae12bf8eda4f7997008758d4d29434a8371a7
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/17/2019
ms.locfileid: "59012609"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a><span data-ttu-id="5afc8-103">Tutorial: Uso de la característica de migraciones: ASP.NET MVC con EF Core</span><span class="sxs-lookup"><span data-stu-id="5afc8-103">Tutorial: Using the migrations feature - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="5afc8-104">En este tutorial, empezará usando la característica de migraciones de EF Core para administrar cambios en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="5afc8-104">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="5afc8-105">En los tutoriales posteriores, agregará más migraciones a medida que cambie el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="5afc8-105">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="5afc8-106">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="5afc8-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5afc8-107">Obtiene información sobre las migraciones</span><span class="sxs-lookup"><span data-stu-id="5afc8-107">Learn about migrations</span></span>
> * <span data-ttu-id="5afc8-108">Cambiar la cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="5afc8-108">Change the connection string</span></span>
> * <span data-ttu-id="5afc8-109">Crear una migración inicial</span><span class="sxs-lookup"><span data-stu-id="5afc8-109">Create an initial migration</span></span>
> * <span data-ttu-id="5afc8-110">Examina los métodos Up y Down</span><span class="sxs-lookup"><span data-stu-id="5afc8-110">Examine Up and Down methods</span></span>
> * <span data-ttu-id="5afc8-111">Obtiene información sobre la instantánea del modelo de datos</span><span class="sxs-lookup"><span data-stu-id="5afc8-111">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="5afc8-112">Aplicar la migración</span><span class="sxs-lookup"><span data-stu-id="5afc8-112">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5afc8-113">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="5afc8-113">Prerequisites</span></span>

* [<span data-ttu-id="5afc8-114">Ordenar, filtrar y paginar</span><span class="sxs-lookup"><span data-stu-id="5afc8-114">Sorting, filtering, and paging</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="5afc8-115">Acerca de las migraciones</span><span class="sxs-lookup"><span data-stu-id="5afc8-115">About migrations</span></span>

<span data-ttu-id="5afc8-116">Al desarrollar una aplicación nueva, el modelo de datos cambia con frecuencia y, cada vez que lo hace, se deja de sincronizar con la base de datos.</span><span class="sxs-lookup"><span data-stu-id="5afc8-116">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="5afc8-117">Estos tutoriales se iniciaron con la configuración de Entity Framework para crear la base de datos si no existía.</span><span class="sxs-lookup"><span data-stu-id="5afc8-117">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="5afc8-118">Después, cada vez que cambie el modelo de datos (agregar, quitar o cambiar las clases de entidad, o bien cambiar la clase DbContext), puede eliminar la base de datos y EF crea una que coincida con el modelo y la inicializa con datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="5afc8-118">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="5afc8-119">Este método para mantener la base de datos sincronizada con el modelo de datos funciona bien hasta que la aplicación se implemente en producción.</span><span class="sxs-lookup"><span data-stu-id="5afc8-119">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="5afc8-120">Cuando la aplicación se ejecuta en producción, normalmente almacena los datos que le interesa mantener y no querrá perderlo todo cada vez que realice un cambio, como al agregar una columna nueva.</span><span class="sxs-lookup"><span data-stu-id="5afc8-120">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="5afc8-121">La característica Migraciones de EF Core soluciona este problema habilitando EF para actualizar el esquema de la base de datos en lugar de crear una.</span><span class="sxs-lookup"><span data-stu-id="5afc8-121">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

<span data-ttu-id="5afc8-122">Para trabajar con las migraciones, puede usar la **Consola del Administrador de paquetes** (PMC) o la interfaz de la línea de comandos (CLI).</span><span class="sxs-lookup"><span data-stu-id="5afc8-122">To work with migrations, you can use the **Package Manager Console** (PMC) or the command-line interface (CLI).</span></span>  <span data-ttu-id="5afc8-123">En estos tutoriales se muestra cómo usar los comandos de la CLI.</span><span class="sxs-lookup"><span data-stu-id="5afc8-123">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="5afc8-124">[Al final de este tutorial](#pmc) encontrará información sobre la PMC.</span><span class="sxs-lookup"><span data-stu-id="5afc8-124">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="5afc8-125">Cambiar la cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="5afc8-125">Change the connection string</span></span>

<span data-ttu-id="5afc8-126">En el archivo *appsettings.json*, cambie el nombre de la base de datos en la cadena de conexión por ContosoUniversity2 u otro nombre que no haya usado en el equipo que esté usando.</span><span class="sxs-lookup"><span data-stu-id="5afc8-126">In the *appsettings.json* file, change the name of the database in the connection string to ContosoUniversity2 or some other name that you haven't used on the computer you're using.</span></span>

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

<span data-ttu-id="5afc8-127">Este cambio configura el proyecto para que la primera migración cree una base de datos.</span><span class="sxs-lookup"><span data-stu-id="5afc8-127">This change sets up the project so that the first migration will create a new database.</span></span> <span data-ttu-id="5afc8-128">Esto no es necesario para comenzar a usar las migraciones, pero más adelante se verá por qué es una buena idea.</span><span class="sxs-lookup"><span data-stu-id="5afc8-128">This isn't required to get started with migrations, but you'll see later why it's a good idea.</span></span>

> [!NOTE]
> <span data-ttu-id="5afc8-129">Como alternativa a cambiar el nombre de la base de datos, puede eliminar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="5afc8-129">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="5afc8-130">Use el **Explorador de objetos de SQL Server** (SSOX) o el comando de la CLI `database drop`:</span><span class="sxs-lookup"><span data-stu-id="5afc8-130">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```console
> dotnet ef database drop
> ```
>
> <span data-ttu-id="5afc8-131">En la siguiente sección se explica cómo ejecutar comandos de la CLI.</span><span class="sxs-lookup"><span data-stu-id="5afc8-131">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="5afc8-132">Crear una migración inicial</span><span class="sxs-lookup"><span data-stu-id="5afc8-132">Create an initial migration</span></span>

<span data-ttu-id="5afc8-133">Guarde los cambios y compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="5afc8-133">Save your changes and build the project.</span></span> <span data-ttu-id="5afc8-134">Después, abra una ventana de comandos y desplácese hasta la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="5afc8-134">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="5afc8-135">Esta es una forma rápida de hacerlo:</span><span class="sxs-lookup"><span data-stu-id="5afc8-135">Here's a quick way to do that:</span></span>

* <span data-ttu-id="5afc8-136">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y elija **Abrir la carpeta en el Explorador de archivos** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="5afc8-136">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![Elemento de menú Abrir en el Explorador de archivos](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="5afc8-138">Escriba "cmd" en la barra de direcciones y presione Entrar.</span><span class="sxs-lookup"><span data-stu-id="5afc8-138">Enter "cmd" in the address bar and press Enter.</span></span>

  ![Abrir ventana de comandos](migrations/_static/open-command-window.png)

<span data-ttu-id="5afc8-140">Escriba el siguiente comando en la ventana de comandos:</span><span class="sxs-lookup"><span data-stu-id="5afc8-140">Enter the following command in the command window:</span></span>

```console
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="5afc8-141">En la ventana de comandos verá un resultado similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="5afc8-141">You see output like the following in the command window:</span></span>

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> <span data-ttu-id="5afc8-142">Si ve un mensaje de error *No se encuentra ningún archivo ejecutable que coincida con el comando "dotnet-ef"*, vea [esta entrada de blog](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) para obtener ayuda para solucionar problemas.</span><span class="sxs-lookup"><span data-stu-id="5afc8-142">If you see an error message *No executable found matching command "dotnet-ef"*, see [this blog post](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) for help troubleshooting.</span></span>

<span data-ttu-id="5afc8-143">Si ve un mensaje de error "*No se puede obtener acceso al archivo... ContosoUniversity.dll porque lo está usando otro proceso.*", busque el icono de IIS Express en la bandeja del sistema de Windows, haga clic con el botón derecho en él y, después, haga clic en **ContosoUniversity > Detener sitio**.</span><span class="sxs-lookup"><span data-stu-id="5afc8-143">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="5afc8-144">Examina los métodos Up y Down</span><span class="sxs-lookup"><span data-stu-id="5afc8-144">Examine Up and Down methods</span></span>

<span data-ttu-id="5afc8-145">Cuando ejecutó el comando `migrations add`, EF generó el código que va a crear la base de datos desde cero.</span><span class="sxs-lookup"><span data-stu-id="5afc8-145">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="5afc8-146">Este código está en la carpeta *Migrations*, en el archivo denominado *\<marca_de_tiempo>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="5afc8-146">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="5afc8-147">El método `Up` de la clase `InitialCreate` crea las tablas de base de datos que corresponden a los conjuntos de entidades del modelo de datos y el método `Down` las elimina, como se muestra en el ejemplo siguiente.</span><span class="sxs-lookup"><span data-stu-id="5afc8-147">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="5afc8-148">Las migraciones llaman al método `Up` para implementar los cambios del modelo de datos para una migración.</span><span class="sxs-lookup"><span data-stu-id="5afc8-148">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="5afc8-149">Cuando se escribe un comando para revertir la actualización, las migraciones llaman al método `Down`.</span><span class="sxs-lookup"><span data-stu-id="5afc8-149">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="5afc8-150">Este código es para la migración inicial que se creó cuando se escribió el comando `migrations add InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="5afc8-150">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="5afc8-151">El parámetro de nombre de la migración ("InitialCreate" en el ejemplo) se usa para el nombre de archivo y puede ser lo que quiera.</span><span class="sxs-lookup"><span data-stu-id="5afc8-151">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="5afc8-152">Es más recomendable elegir una palabra o frase que resuma lo que se hace en la migración.</span><span class="sxs-lookup"><span data-stu-id="5afc8-152">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="5afc8-153">Por ejemplo, podría denominar "AddDepartmentTable" a una migración posterior.</span><span class="sxs-lookup"><span data-stu-id="5afc8-153">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="5afc8-154">Si creó la migración inicial cuando la base de datos ya existía, se genera el código de creación de la base de datos pero no es necesario ejecutarlo porque la base de datos ya coincide con el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="5afc8-154">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="5afc8-155">Al implementar la aplicación en otro entorno donde la base de datos todavía no existe, se ejecutará este código para crear la base de datos, por lo que es recomendable probarlo primero.</span><span class="sxs-lookup"><span data-stu-id="5afc8-155">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="5afc8-156">Por ese motivo se cambió antes el nombre de la base de datos en la cadena de conexión, para que las migraciones puedan crear uno desde cero.</span><span class="sxs-lookup"><span data-stu-id="5afc8-156">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="5afc8-157">La instantánea del modelo de datos</span><span class="sxs-lookup"><span data-stu-id="5afc8-157">The data model snapshot</span></span>

<span data-ttu-id="5afc8-158">Las migraciones crean una *instantánea* del esquema de la base de datos actual en *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="5afc8-158">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="5afc8-159">Cuando se agrega una migración, EF determina qué ha cambiado mediante la comparación del modelo de datos con el archivo de instantánea.</span><span class="sxs-lookup"><span data-stu-id="5afc8-159">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="5afc8-160">Cuando elimine una migración, use el comando [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="5afc8-160">When deleting a migration, use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command.</span></span> <span data-ttu-id="5afc8-161">`dotnet ef migrations remove` elimina la migración y garantiza que la instantánea se restablece correctamente.</span><span class="sxs-lookup"><span data-stu-id="5afc8-161">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span>

<span data-ttu-id="5afc8-162">Vea [Migraciones en entornos de equipo](/ef/core/managing-schemas/migrations/teams) para más información sobre cómo se usa el archivo de instantánea.</span><span class="sxs-lookup"><span data-stu-id="5afc8-162">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="5afc8-163">Aplicar la migración</span><span class="sxs-lookup"><span data-stu-id="5afc8-163">Apply the migration</span></span>

<span data-ttu-id="5afc8-164">En la ventana de comandos, escriba el comando siguiente para crear la base de datos y tablas en su interior.</span><span class="sxs-lookup"><span data-stu-id="5afc8-164">In the command window, enter the following command to create the database and tables in it.</span></span>

```console
dotnet ef database update
```

<span data-ttu-id="5afc8-165">El resultado del comando es similar al comando `migrations add`, con la excepción de que verá registros para los comandos SQL que configuran la base de datos.</span><span class="sxs-lookup"><span data-stu-id="5afc8-165">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="5afc8-166">La mayoría de los registros se omite en la siguiente salida de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="5afc8-166">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="5afc8-167">Si prefiere no ver este nivel de detalle en los mensajes de registro, puede cambiarlo en el archivo *appsettings.Development.json*.</span><span class="sxs-lookup"><span data-stu-id="5afc8-167">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="5afc8-168">Para obtener más información, vea <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="5afc8-168">For more information, see <xref:fundamentals/logging/index>.</span></span>

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'2.2.0-rtm-35687');
Done.
```

<span data-ttu-id="5afc8-169">Use el **Explorador de objetos de SQL Server** para inspeccionar la base de datos como hizo en el primer tutorial.</span><span class="sxs-lookup"><span data-stu-id="5afc8-169">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="5afc8-170">Observará la adición de una tabla \_\_EFMigrationsHistory que realiza el seguimiento de las migraciones que se han aplicado a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="5afc8-170">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="5afc8-171">Si examina los datos de esa tabla, verá una fila para la primera migración.</span><span class="sxs-lookup"><span data-stu-id="5afc8-171">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="5afc8-172">(En el último registro del ejemplo de salida de la CLI anterior se muestra la instrucción INSERT que crea esta fila).</span><span class="sxs-lookup"><span data-stu-id="5afc8-172">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="5afc8-173">Ejecute la aplicación para comprobar que todo funciona igual que antes.</span><span class="sxs-lookup"><span data-stu-id="5afc8-173">Run the application to verify that everything still works the same as before.</span></span>

![Página de índice de Students](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="5afc8-175">Comparar CLI y PMC</span><span class="sxs-lookup"><span data-stu-id="5afc8-175">Compare CLI and PMC</span></span>

<span data-ttu-id="5afc8-176">Las herramientas de EF para la administración de migraciones están disponibles desde los comandos de la CLI de .NET Core o los cmdlets de PowerShell en la ventana **Consola del Administrador de paquetes** (PMC) de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5afc8-176">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="5afc8-177">En este tutorial se muestra cómo usar la CLI, pero puede usar la PMC si lo prefiere.</span><span class="sxs-lookup"><span data-stu-id="5afc8-177">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="5afc8-178">Los comandos de EF para los comandos de la PMC están en el paquete [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools).</span><span class="sxs-lookup"><span data-stu-id="5afc8-178">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="5afc8-179">Este paquete se incluye en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), por lo que no es necesario agregar una referencia de paquete si la aplicación tiene una para `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="5afc8-179">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="5afc8-180">**Importante:** Este no es el mismo paquete que el que se instala para la CLI mediante la edición del archivo *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="5afc8-180">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="5afc8-181">El nombre de este paquete termina en `Tools`, a diferencia del nombre de paquete de la CLI que termina en `Tools.DotNet`.</span><span class="sxs-lookup"><span data-stu-id="5afc8-181">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="5afc8-182">Para obtener más información sobre los comandos de la CLI, vea [CLI de .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="5afc8-182">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="5afc8-183">Para obtener más información sobre los comandos de la PMC, vea [Consola del Administrador de paquetes (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="5afc8-183">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="5afc8-184">Obtención del código</span><span class="sxs-lookup"><span data-stu-id="5afc8-184">Get the code</span></span>

[<span data-ttu-id="5afc8-185">Descargue o vea la aplicación completa.</span><span class="sxs-lookup"><span data-stu-id="5afc8-185">Download or view the completed application.</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a><span data-ttu-id="5afc8-186">Paso siguiente</span><span class="sxs-lookup"><span data-stu-id="5afc8-186">Next step</span></span>

<span data-ttu-id="5afc8-187">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="5afc8-187">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5afc8-188">Obtenido información sobre las migraciones</span><span class="sxs-lookup"><span data-stu-id="5afc8-188">Learned about migrations</span></span>
> * <span data-ttu-id="5afc8-189">Obtenido información sobre los paquetes de migración de NuGet</span><span class="sxs-lookup"><span data-stu-id="5afc8-189">Learned about NuGet migration packages</span></span>
> * <span data-ttu-id="5afc8-190">Cambiado la cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="5afc8-190">Changed the connection string</span></span>
> * <span data-ttu-id="5afc8-191">Creado una migración inicial</span><span class="sxs-lookup"><span data-stu-id="5afc8-191">Created an initial migration</span></span>
> * <span data-ttu-id="5afc8-192">Examinado los métodos Up y Down</span><span class="sxs-lookup"><span data-stu-id="5afc8-192">Examined Up and Down methods</span></span>
> * <span data-ttu-id="5afc8-193">Obtenido información sobre la instantánea del modelo de datos</span><span class="sxs-lookup"><span data-stu-id="5afc8-193">Learned about the data model snapshot</span></span>
> * <span data-ttu-id="5afc8-194">Aplicado la migración</span><span class="sxs-lookup"><span data-stu-id="5afc8-194">Applied the migration</span></span>

<span data-ttu-id="5afc8-195">Pase al tutorial siguiente para comenzar a examinar temas más avanzados sobre la expansión del modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="5afc8-195">Advance to the next tutorial to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="5afc8-196">Por el camino, podrá crear y aplicar migraciones adicionales.</span><span class="sxs-lookup"><span data-stu-id="5afc8-196">Along the way you'll create and apply additional migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="5afc8-197">Creación y aplicación de migraciones adicionales</span><span class="sxs-lookup"><span data-stu-id="5afc8-197">Create and apply additional migrations</span></span>](complex-data-model.md)
