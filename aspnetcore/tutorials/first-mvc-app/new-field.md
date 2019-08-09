---
title: Agregar un campo nuevo a una aplicación de ASP.NET Core MVC
author: rick-anderson
description: Obtenga información sobre cómo usar Migraciones de Entity Framework Code First para agregar un nuevo campo al modelo y migrar ese cambio a una base de datos.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: f22a90dd7662bfb34e2f7adb87fa611012d78533
ms.sourcegitcommit: b5e63714afc26e94be49a92619586df5189ed93a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739582"
---
# <a name="add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="ecdbb-103">Agregar un campo nuevo a una aplicación de ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="ecdbb-103">Add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="ecdbb-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ecdbb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ecdbb-105">En esta sección, Migraciones de [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First se utiliza para:</span><span class="sxs-lookup"><span data-stu-id="ecdbb-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="ecdbb-106">Agregar un campo nuevo al modelo.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-106">Add a new field to the model.</span></span>
* <span data-ttu-id="ecdbb-107">Migrar el nuevo campo a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="ecdbb-108">Al usar Code First de EF para crear una base de datos automáticamente, Code First hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ecdbb-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="ecdbb-109">Agrega una tabla a la base de datos para realizar un seguimiento del esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="ecdbb-110">Comprueba que la base de datos está sincronizada con las clases del modelo desde las que se ha generado.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-110">Verifies the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="ecdbb-111">Si no está sincronizado, EF produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="ecdbb-112">Esto facilita la detección de problemas de código o base de datos incoherentes.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="ecdbb-113">Adición de una propiedad de clasificación al modelo Movie</span><span class="sxs-lookup"><span data-stu-id="ecdbb-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="ecdbb-114">Agregue una `Rating` propiedad a *Models/Movie.cs*:</span><span class="sxs-lookup"><span data-stu-id="ecdbb-114">Add a `Rating` property to *Models/Movie.cs*:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="ecdbb-115">Compilar la aplicación</span><span class="sxs-lookup"><span data-stu-id="ecdbb-115">Build the app</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ecdbb-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ecdbb-116">Visual Studio</span></span>](#tab/visual-studio)

 <span data-ttu-id="ecdbb-117">Ctrl+Mayús+B</span><span class="sxs-lookup"><span data-stu-id="ecdbb-117">Ctrl+Shift+B</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ecdbb-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ecdbb-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

`dotnet build`

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ecdbb-119">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ecdbb-119">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ecdbb-120">Comando (⌘) + B</span><span class="sxs-lookup"><span data-stu-id="ecdbb-120">Command ⌘ + B</span></span>

------

<span data-ttu-id="ecdbb-121">Dado que ha agregado un nuevo campo a la clase `Movie`, debe actualizar la lista de enlaces permitidos para que se incluya esta nueva propiedad.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-121">Because you've added a new field to the `Movie` class, you need to update the binding white list so this new property will be included.</span></span> <span data-ttu-id="ecdbb-122">En *MoviesController.cs*, actualice el atributo `[Bind]` de los métodos de acción `Create` y `Edit` para incluir la propiedad `Rating`:</span><span class="sxs-lookup"><span data-stu-id="ecdbb-122">In *MoviesController.cs*, update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="ecdbb-123">Actualice las plantillas de vista para mostrar, crear y editar la nueva propiedad `Rating` en la vista del explorador.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-123">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="ecdbb-124">Edite el archivo */Views/Movies/Index.cshtml* y agregue un campo `Rating`:</span><span class="sxs-lookup"><span data-stu-id="ecdbb-124">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

<span data-ttu-id="ecdbb-125">Actualice */Views/Movies/Create.cshtml* con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-125">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

# <a name="visual-studio--visual-studio-for-mactabvisual-studiovisual-studio-mac"></a>[<span data-ttu-id="ecdbb-126">Visual Studio/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ecdbb-126">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="ecdbb-127">Puede copiar o pegar el elemento "form group" anterior y permitir que IntelliSense le ayude a actualizar los campos.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-127">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="ecdbb-128">IntelliSense funciona con [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="ecdbb-128">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![El desarrollador ha escrito la letra R para el valor del atributo de asp-for en el segundo elemento de etiqueta de la vista.](new-field/_static/cr.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ecdbb-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ecdbb-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

<span data-ttu-id="ecdbb-133">Actualice el resto de las plantillas.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-133">Update the the remaining templates.</span></span>

<span data-ttu-id="ecdbb-134">Actualice la clase `SeedData` para que proporcione un valor para la nueva columna.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-134">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="ecdbb-135">A continuación se muestra un cambio de ejemplo, aunque es conveniente realizarlo con cada `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-135">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="ecdbb-136">La aplicación no funciona hasta que la base de datos se actualiza para incluir el nuevo campo.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-136">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="ecdbb-137">Si se ejecuta ahora, se produce la siguiente `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="ecdbb-137">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="ecdbb-138">Este error se produce porque la clase del modelo Movie actualizada es diferente a la del esquema de la tabla Movie de la base de datos existente.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-138">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="ecdbb-139">(No hay ninguna columna `Rating` en la tabla de la base de datos).</span><span class="sxs-lookup"><span data-stu-id="ecdbb-139">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="ecdbb-140">Este error se puede resolver de varias maneras:</span><span class="sxs-lookup"><span data-stu-id="ecdbb-140">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="ecdbb-141">Haga que Entity Framework quite de forma automática la base de datos y la vuelva a crear basándose en el nuevo esquema de la clase del modelo.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-141">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="ecdbb-142">Este enfoque resulta muy conveniente al principio del ciclo de desarrollo cuando se realiza el desarrollo activo en una base de datos de prueba; permite desarrollar rápidamente el esquema del modelo y la base de datos juntos.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-142">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="ecdbb-143">La desventaja es que se pierden los datos existentes en la base de datos, así que no use este enfoque en una base de datos de producción.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-143">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="ecdbb-144">Usar un inicializador para inicializar automáticamente una base de datos con datos de prueba suele ser una manera productiva de desarrollar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-144">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="ecdbb-145">Se trata de un buen enfoque para el desarrollo inicial y cuando se usa SQLite.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-145">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="ecdbb-146">Modifique explícitamente el esquema de la base de datos existente para que coincida con las clases del modelo.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-146">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="ecdbb-147">La ventaja de este enfoque es que se conservan los datos.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-147">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="ecdbb-148">Puede realizar este cambio de forma manual o mediante la creación de un script de cambio de base de datos.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-148">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="ecdbb-149">Use Migraciones de Code First para actualizar el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-149">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="ecdbb-150">En este tutorial se usa Migraciones de Code First.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-150">For this tutorial, Code First Migrations is used.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ecdbb-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ecdbb-151">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ecdbb-152">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-152">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![Menú de PMC](adding-model/_static/pmc.png)

<span data-ttu-id="ecdbb-154">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="ecdbb-154">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="ecdbb-155">El comando `Add-Migration` indica el marco de trabajo de migración para examinar el modelo `Movie` actual con el esquema de base de datos `Movie` actual y para crear el código con el que se migrará la base de datos al nuevo modelo.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-155">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

<span data-ttu-id="ecdbb-156">El nombre "Rating" es arbitrario y se usa para asignar nombre al archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-156">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="ecdbb-157">Resulta útil emplear un nombre descriptivo para el archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-157">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="ecdbb-158">Si se eliminan todos los registros de la base de datos, el método de inicialización inicializa la base de datos e incluye el campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-158">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="ecdbb-159">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ecdbb-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="ecdbb-160">Elimine la base de datos y use las migraciones para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-160">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="ecdbb-161">Para eliminar la base de datos, elimine el archivo de base de datos (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="ecdbb-161">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="ecdbb-162">Luego, ejecute el comando `ef database update`:</span><span class="sxs-lookup"><span data-stu-id="ecdbb-162">Then run the `ef database update` command:</span></span>

```console
dotnet ef database update
```

---
<!-- End of VS tabs -->

<span data-ttu-id="ecdbb-163">Ejecute la aplicación y compruebe que puede crear, editar o mostrar películas con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-163">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="ecdbb-164">Debe agregar el campo `Rating` a las plantillas de vista `Edit`, `Details` y `Delete`.</span><span class="sxs-lookup"><span data-stu-id="ecdbb-164">You should add the `Rating` field to the `Edit`, `Details`, and `Delete` view templates.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="ecdbb-165">[Anterior](search.md)
> [Siguiente](validation.md)</span><span class="sxs-lookup"><span data-stu-id="ecdbb-165">[Previous](search.md)
[Next](validation.md)</span></span>
