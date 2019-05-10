---
title: Agregar un campo nuevo a una página de Razor en ASP.NET Core
author: rick-anderson
description: Muestra cómo agregar un nuevo campo a una página de Razor con Entity Framework Core
ms.author: riande
ms.custom: mvc
ms.date: 12/5/2018
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: f73af673afebe0531f228dc0041dc708ba794047
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887620"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="094c2-103">Agregar un campo nuevo a una página de Razor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="094c2-103">Add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="094c2-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="094c2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="094c2-105">En esta sección, Migraciones de [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First se utiliza para:</span><span class="sxs-lookup"><span data-stu-id="094c2-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="094c2-106">Agregar un campo nuevo al modelo.</span><span class="sxs-lookup"><span data-stu-id="094c2-106">Add a new field to the model.</span></span>
* <span data-ttu-id="094c2-107">Migrar el cambio de esquema del campo nuevo a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="094c2-107">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="094c2-108">Al usar EF Code First para crear una base de datos automáticamente, Code First hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="094c2-108">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="094c2-109">Agrega una tabla a la base de datos para ayudar a saber si el esquema de la base de datos está sincronizado con las clases del modelo a partir del que se ha generado.</span><span class="sxs-lookup"><span data-stu-id="094c2-109">Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="094c2-110">Si las clases del modelo no están sincronizadas con la base de datos, EF produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="094c2-110">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="094c2-111">La comprobación automática de la sincronización del esquema/modelo facilita la detección de problemas de código o base de datos incoherentes.</span><span class="sxs-lookup"><span data-stu-id="094c2-111">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="094c2-112">Adición de una propiedad de clasificación al modelo Movie</span><span class="sxs-lookup"><span data-stu-id="094c2-112">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="094c2-113">Abra el archivo *Models/Movie.cs* y agregue una propiedad `Rating`:</span><span class="sxs-lookup"><span data-stu-id="094c2-113">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="094c2-114">Compile la aplicación.</span><span class="sxs-lookup"><span data-stu-id="094c2-114">Build the app.</span></span>

<span data-ttu-id="094c2-115">Edite *Pages/Movies/Index.cshtml* y agregue un campo `Rating`:</span><span class="sxs-lookup"><span data-stu-id="094c2-115">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml.?highlight=40-42,61-63)]

<span data-ttu-id="094c2-116">Actualice las páginas siguientes:</span><span class="sxs-lookup"><span data-stu-id="094c2-116">Update the following pages:</span></span>

* <span data-ttu-id="094c2-117">Agregue el campo `Rating` a las páginas Delete y Details.</span><span class="sxs-lookup"><span data-stu-id="094c2-117">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="094c2-118">Actualice [Create.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="094c2-118">Update [Create.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="094c2-119">Agregue el campo `Rating` a la página de edición.</span><span class="sxs-lookup"><span data-stu-id="094c2-119">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="094c2-120">La aplicación no funciona hasta que la base de datos se actualiza para incluir el nuevo campo.</span><span class="sxs-lookup"><span data-stu-id="094c2-120">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="094c2-121">Si se ejecuta ahora, la aplicación produce una `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="094c2-121">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="094c2-122">Este error se debe a que la clase del modelo Movie actualizado es diferente al esquema de la tabla Movie de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="094c2-122">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="094c2-123">(No hay ninguna columna `Rating` en la tabla de la base de datos).</span><span class="sxs-lookup"><span data-stu-id="094c2-123">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="094c2-124">Este error se puede resolver de varias maneras:</span><span class="sxs-lookup"><span data-stu-id="094c2-124">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="094c2-125">Haga que Entity Framework quite de forma automática la base de datos y la vuelva a crear con el nuevo esquema de la clase del modelo.</span><span class="sxs-lookup"><span data-stu-id="094c2-125">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="094c2-126">Este enfoque resulta conveniente al principio del ciclo de desarrollo; permite desarrollar a la vez y de manera rápida el esquema del modelo y la base de datos.</span><span class="sxs-lookup"><span data-stu-id="094c2-126">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="094c2-127">El inconveniente es que se pierden los datos existentes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="094c2-127">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="094c2-128">No use este enfoque en una base de datos de producción.</span><span class="sxs-lookup"><span data-stu-id="094c2-128">Don't use this approach on a production database!</span></span> <span data-ttu-id="094c2-129">El quitar la base de datos en los cambios de esquema y el usar un inicializador para inicializar automáticamente la base de datos con datos de prueba suele ser una forma productiva de desarrollar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="094c2-129">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="094c2-130">Modifique explícitamente el esquema de la base de datos existente para que coincida con las clases del modelo.</span><span class="sxs-lookup"><span data-stu-id="094c2-130">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="094c2-131">La ventaja de este enfoque es que se conservan los datos.</span><span class="sxs-lookup"><span data-stu-id="094c2-131">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="094c2-132">Puede realizar este cambio de forma manual o mediante la creación de un script de cambio de base de datos.</span><span class="sxs-lookup"><span data-stu-id="094c2-132">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="094c2-133">Use Migraciones de Code First para actualizar el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="094c2-133">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="094c2-134">Para este tutorial, use Migraciones de Code First.</span><span class="sxs-lookup"><span data-stu-id="094c2-134">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="094c2-135">Actualice la clase `SeedData` para que proporcione un valor para la nueva columna.</span><span class="sxs-lookup"><span data-stu-id="094c2-135">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="094c2-136">A continuación se muestra un cambio de ejemplo, aunque es conveniente realizar este cambio para cada bloque `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="094c2-136">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="094c2-137">Vea el [archivo completado SeedData.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="094c2-137">See the [completed SeedData.cs file](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="094c2-138">Compile la solución.</span><span class="sxs-lookup"><span data-stu-id="094c2-138">Build the solution.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="094c2-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="094c2-139">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="094c2-140">Agregar una migración para el campo de clasificación</span><span class="sxs-lookup"><span data-stu-id="094c2-140">Add a migration for the rating field</span></span>

<span data-ttu-id="094c2-141">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="094c2-141">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="094c2-142">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="094c2-142">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="094c2-143">El comando `Add-Migration` indica al marco de trabajo que:</span><span class="sxs-lookup"><span data-stu-id="094c2-143">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="094c2-144">Compare el modelo `Movie` con el esquema de base de datos `Movie`.</span><span class="sxs-lookup"><span data-stu-id="094c2-144">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="094c2-145">Cree código para migrar el esquema de la base de datos al nuevo modelo.</span><span class="sxs-lookup"><span data-stu-id="094c2-145">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="094c2-146">El nombre "Rating" es arbitrario y se usa para asignar nombre al archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="094c2-146">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="094c2-147">Resulta útil emplear un nombre descriptivo para el archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="094c2-147">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="094c2-148">El comando `Update-Database` le indica al marco que aplique los cambios de esquema a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="094c2-148">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="094c2-149">Si elimina todos los registros de la base de datos, el inicializador inicializará la base de datos e incluirá el campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="094c2-149">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="094c2-150">Puede hacerlo con los vínculos de eliminación en el explorador o desde el [Explorador de objetos de SQL Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="094c2-150">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="094c2-151">Otra opción es eliminar la base de datos y usar las migraciones para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="094c2-151">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="094c2-152">Para eliminar la base de datos de SSOX:</span><span class="sxs-lookup"><span data-stu-id="094c2-152">To delete the database in SSOX:</span></span>

* <span data-ttu-id="094c2-153">Seleccione la base de datos en SSOX.</span><span class="sxs-lookup"><span data-stu-id="094c2-153">Select the database in SSOX.</span></span>
* <span data-ttu-id="094c2-154">Haga clic con el botón derecho en la base de datos y seleccione *Eliminar*.</span><span class="sxs-lookup"><span data-stu-id="094c2-154">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="094c2-155">Active **Cerrar las conexiones existentes**.</span><span class="sxs-lookup"><span data-stu-id="094c2-155">Check **Close existing connections**.</span></span>
* <span data-ttu-id="094c2-156">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="094c2-156">Select **OK**.</span></span>
* <span data-ttu-id="094c2-157">En la [PMC](xref:tutorials/razor-pages/new-field#pmc), actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="094c2-157">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="094c2-158">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="094c2-158">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="094c2-159">Quitar y volver a crear la base de datos</span><span class="sxs-lookup"><span data-stu-id="094c2-159">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="094c2-160">Elimine la base de datos y use las migraciones para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="094c2-160">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="094c2-161">Para eliminar la base de datos, elimine el archivo de base de datos (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="094c2-161">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="094c2-162">Luego, ejecute el comando `ef database update`:</span><span class="sxs-lookup"><span data-stu-id="094c2-162">Then run the `ef database update` command:</span></span>

```console
dotnet ef database update
```

---

<span data-ttu-id="094c2-163">Ejecute la aplicación y compruebe que puede crear, editar o mostrar películas con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="094c2-163">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="094c2-164">Si la base de datos no se ha propagado, establezca un punto de interrupción en el método `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="094c2-164">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="094c2-165">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="094c2-165">Additional resources</span></span>

* [<span data-ttu-id="094c2-166">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="094c2-166">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="094c2-167">[Anterior: Adición de búsqueda](xref:tutorials/razor-pages/search)
> [Siguiente: Adición de validación](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="094c2-167">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>
