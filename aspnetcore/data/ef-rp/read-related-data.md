---
title: 'Parte 6. Razor Pages con EF Core en ASP.NET Core: Lectura de datos relacionados'
author: rick-anderson
description: Parte 6 de la serie de tutoriales sobre Razor Pages y Entity Framework.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/read-related-data
ms.openlocfilehash: e67738015f64ca7077c2f87a8f7eabe722aac9d8
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652615"
---
# <a name="part-6-razor-pages-with-ef-core-in-aspnet-core---read-related-data"></a><span data-ttu-id="b0f24-103">Parte 6. Razor Pages con EF Core en ASP.NET Core: Lectura de datos relacionados</span><span class="sxs-lookup"><span data-stu-id="b0f24-103">Part 6, Razor Pages with EF Core in ASP.NET Core - Read Related Data</span></span>

<span data-ttu-id="b0f24-104">Por [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b0f24-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b0f24-105">En este tutorial se muestra cómo leer y mostrar datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="b0f24-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="b0f24-106">Los datos relacionados son los que EF Core carga en las propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="b0f24-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="b0f24-107">En las ilustraciones siguientes se muestran las páginas completadas para este tutorial:</span><span class="sxs-lookup"><span data-stu-id="b0f24-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Página de índice de cursos](read-related-data/_static/courses-index30.png)

![Página de índice de instructores](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="b0f24-110">Carga diligente, explícita y diferida</span><span class="sxs-lookup"><span data-stu-id="b0f24-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="b0f24-111">EF Core puede cargar datos relacionados en las propiedades de navegación de una entidad de varias maneras:</span><span class="sxs-lookup"><span data-stu-id="b0f24-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="b0f24-112">[Carga diligente](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="b0f24-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="b0f24-113">La carga diligente es cuando una consulta para un tipo de entidad también carga las entidades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="b0f24-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="b0f24-114">Cuando se lee una entidad, se recuperan sus datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="b0f24-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="b0f24-115">Esto normalmente da como resultado una única consulta de combinación en la que se recuperan todos los datos que se necesitan.</span><span class="sxs-lookup"><span data-stu-id="b0f24-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="b0f24-116">EF Core emitirá varias consultas para algunos tipos de carga diligente.</span><span class="sxs-lookup"><span data-stu-id="b0f24-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="b0f24-117">La emisión de varias consultas puede ser más eficaz que una sola consulta gigante.</span><span class="sxs-lookup"><span data-stu-id="b0f24-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="b0f24-118">La carga diligente se especifica con los métodos `Include` y `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Ejemplo de carga diligente](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="b0f24-120">La carga diligente envía varias consultas cuando se incluye una propiedad de navegación de colección:</span><span class="sxs-lookup"><span data-stu-id="b0f24-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="b0f24-121">Una consulta para la consulta principal</span><span class="sxs-lookup"><span data-stu-id="b0f24-121">One query for the main query</span></span> 
  * <span data-ttu-id="b0f24-122">Una consulta para cada colección "perimetral" en el árbol de la carga.</span><span class="sxs-lookup"><span data-stu-id="b0f24-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="b0f24-123">Separe las consultas con `Load`: los datos se pueden recuperar en distintas consultas y EF Core "corrige" las propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="b0f24-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="b0f24-124">"Corregir" significa que EF Core rellena de forma automática las propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="b0f24-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="b0f24-125">La separación de las consultas con `Load` es más parecido a la carga explícita que a la carga diligente.</span><span class="sxs-lookup"><span data-stu-id="b0f24-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Ejemplo de consultas independientes](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="b0f24-127">Nota: EF Core corrige automáticamente las propiedades de navegación para todas las entidades que se cargaron previamente en la instancia del contexto.</span><span class="sxs-lookup"><span data-stu-id="b0f24-127">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="b0f24-128">Incluso si los datos de una propiedad de navegación *no* se incluyen explícitamente, es posible que la propiedad se siga rellenando si algunas o todas las entidades relacionadas se cargaron previamente.</span><span class="sxs-lookup"><span data-stu-id="b0f24-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="b0f24-129">[Carga explícita](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="b0f24-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="b0f24-130">Cuando la entidad se lee por primera vez, no se recuperan datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="b0f24-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="b0f24-131">Se debe escribir código para recuperar los datos relacionados cuando sea necesario.</span><span class="sxs-lookup"><span data-stu-id="b0f24-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="b0f24-132">La carga explícita con consultas independientes da como resultado varias consultas que se envían a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b0f24-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="b0f24-133">Con la carga explícita, el código especifica las propiedades de navegación que se van a cargar.</span><span class="sxs-lookup"><span data-stu-id="b0f24-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="b0f24-134">Use el método `Load` para realizar la carga explícita.</span><span class="sxs-lookup"><span data-stu-id="b0f24-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="b0f24-135">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="b0f24-135">For example:</span></span>

  ![Ejemplo de carga explícita](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="b0f24-137">[Carga diferida](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="b0f24-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="b0f24-138">[Se ha agregado la carga diferida a EF Core en la versión 2.1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="b0f24-138">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="b0f24-139">Cuando la entidad se lee por primera vez, no se recuperan datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="b0f24-139">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="b0f24-140">La primera vez que se obtiene acceso a una propiedad de navegación, se recuperan automáticamente los datos necesarios para esa propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="b0f24-140">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="b0f24-141">Cada vez que se accede por primera vez a una propiedad de navegación, se envía una consulta a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b0f24-141">A query is sent to the database each time a navigation property is accessed for the first time.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="b0f24-142">Creación de las páginas Course</span><span class="sxs-lookup"><span data-stu-id="b0f24-142">Create Course pages</span></span>

<span data-ttu-id="b0f24-143">La entidad `Course` incluye una propiedad de navegación que contiene la entidad `Department` relacionada.</span><span class="sxs-lookup"><span data-stu-id="b0f24-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="b0f24-145">Para mostrar el nombre del departamento asignado a un curso:</span><span class="sxs-lookup"><span data-stu-id="b0f24-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="b0f24-146">Cargue la entidad `Department` relacionada en la propiedad de navegación `Course.Department`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="b0f24-147">Obtenga el nombre de la propiedad `Name` de la entidad `Department`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="b0f24-148">Scaffolding de las páginas Course</span><span class="sxs-lookup"><span data-stu-id="b0f24-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0f24-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0f24-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b0f24-150">Siga las instrucciones de [Scaffolding de las páginas Student](xref:data/ef-rp/intro#scaffold-student-pages) con las siguientes excepciones:</span><span class="sxs-lookup"><span data-stu-id="b0f24-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="b0f24-151">Cree una carpeta *Pages/Courses*.</span><span class="sxs-lookup"><span data-stu-id="b0f24-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="b0f24-152">Use `Course` para la clase del modelo.</span><span class="sxs-lookup"><span data-stu-id="b0f24-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="b0f24-153">Use la clase de contexto existente en lugar de crear una.</span><span class="sxs-lookup"><span data-stu-id="b0f24-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0f24-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0f24-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b0f24-155">Cree una carpeta *Pages/Courses*.</span><span class="sxs-lookup"><span data-stu-id="b0f24-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="b0f24-156">Ejecute el comando siguiente para aplicar scaffolding a las páginas Course.</span><span class="sxs-lookup"><span data-stu-id="b0f24-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="b0f24-157">**En Windows:**</span><span class="sxs-lookup"><span data-stu-id="b0f24-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="b0f24-158">**En Linux o macOS:**</span><span class="sxs-lookup"><span data-stu-id="b0f24-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="b0f24-159">Abra *Pages/Courses/Index.cshtml.cs* y examine el método `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="b0f24-160">El motor de scaffolding especificado realiza la carga diligente de la propiedad de navegación `Department`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="b0f24-161">El método `Include` especifica la carga diligente.</span><span class="sxs-lookup"><span data-stu-id="b0f24-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="b0f24-162">Ejecute la aplicación y haga clic en el vínculo **Courses**.</span><span class="sxs-lookup"><span data-stu-id="b0f24-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="b0f24-163">En la columna Department se muestra el `DepartmentID`, lo que no resulta útil.</span><span class="sxs-lookup"><span data-stu-id="b0f24-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="b0f24-164">Representación del nombre del departamento</span><span class="sxs-lookup"><span data-stu-id="b0f24-164">Display the department name</span></span>

<span data-ttu-id="b0f24-165">Actualice Pages/Courses/Index.cshtml.cs con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="b0f24-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="b0f24-166">En el código anterior se cambia la propiedad `Course` a `Courses` y se agrega `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="b0f24-167">`AsNoTracking` mejora el rendimiento porque no se realiza el seguimiento de las entidades devueltas.</span><span class="sxs-lookup"><span data-stu-id="b0f24-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="b0f24-168">No es necesario realizar el seguimiento de las entidades porque no se actualizan en el contexto actual.</span><span class="sxs-lookup"><span data-stu-id="b0f24-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="b0f24-169">Actualice *Pages/Courses/Index.cshtml* con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="b0f24-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="b0f24-170">Se han realizado los cambios siguientes en el código con scaffolding:</span><span class="sxs-lookup"><span data-stu-id="b0f24-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="b0f24-171">Se ha cambiado el nombre de la propiedad `Course` a `Courses`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="b0f24-172">Ha agregado una columna **Number** en la que se muestra el valor de propiedad `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="b0f24-173">De forma predeterminada, las claves principales no tienen scaffolding porque normalmente no tienen sentido para los usuarios finales.</span><span class="sxs-lookup"><span data-stu-id="b0f24-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="b0f24-174">Pero en este caso, la clave principal es significativa.</span><span class="sxs-lookup"><span data-stu-id="b0f24-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="b0f24-175">Ha cambiado la columna **Department** para mostrar el nombre del departamento.</span><span class="sxs-lookup"><span data-stu-id="b0f24-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="b0f24-176">El código muestra la propiedad `Name` de la entidad `Department` que se carga en la propiedad de navegación `Department`:</span><span class="sxs-lookup"><span data-stu-id="b0f24-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="b0f24-177">Ejecute la aplicación y haga clic en la pestaña **Courses** para ver la lista con los nombres de departamento.</span><span class="sxs-lookup"><span data-stu-id="b0f24-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Página de índice de cursos](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="b0f24-179">Carga de datos relacionados con Select</span><span class="sxs-lookup"><span data-stu-id="b0f24-179">Loading related data with Select</span></span>

<span data-ttu-id="b0f24-180">El método `OnGetAsync` carga los datos relacionados con el método `Include`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="b0f24-181">El método `Select` es una alternativa que solo carga los datos relacionados necesarios.</span><span class="sxs-lookup"><span data-stu-id="b0f24-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="b0f24-182">Para elementos individuales, como el `Department.Name`, se usa INNER JOIN de SQL.</span><span class="sxs-lookup"><span data-stu-id="b0f24-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="b0f24-183">En las colecciones, se usa otro acceso de base de datos, como también hace el operador `Include` en las colecciones.</span><span class="sxs-lookup"><span data-stu-id="b0f24-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="b0f24-184">En el código siguiente se cargan los datos relacionados con el método `Select`:</span><span class="sxs-lookup"><span data-stu-id="b0f24-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="b0f24-185">El código anterior no devuelve ningún tipo de entidad, por lo que no se realiza ningún seguimiento.</span><span class="sxs-lookup"><span data-stu-id="b0f24-185">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="b0f24-186">Para más información sobre el seguimiento de EF, consulte [Consultas de seguimiento frente a consultas de no seguimiento](/ef/core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="b0f24-186">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="b0f24-187">El `CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="b0f24-187">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="b0f24-188">Vea [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) e [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) para obtener un ejemplo completo.</span><span class="sxs-lookup"><span data-stu-id="b0f24-188">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="b0f24-189">Creación de las páginas Instructor</span><span class="sxs-lookup"><span data-stu-id="b0f24-189">Create Instructor pages</span></span>

<span data-ttu-id="b0f24-190">Esta sección se aplica scaffolding a las páginas Instructor y se agregan entidades Course y Enrollment a la página Instructors Index (índice de instructores).</span><span class="sxs-lookup"><span data-stu-id="b0f24-190">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="b0f24-191">![Página de índice de instructores](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="b0f24-191">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="b0f24-192">En esta página se leen y muestran los datos relacionados de las maneras siguientes:</span><span class="sxs-lookup"><span data-stu-id="b0f24-192">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="b0f24-193">En la lista de instructores se muestran datos relacionados de la entidad `OfficeAssignment` (Office en la imagen anterior).</span><span class="sxs-lookup"><span data-stu-id="b0f24-193">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="b0f24-194">Las entidades `Instructor` y `OfficeAssignment` se encuentran en una relación de uno a cero o uno.</span><span class="sxs-lookup"><span data-stu-id="b0f24-194">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="b0f24-195">Para las entidades `OfficeAssignment` se usa la carga diligente.</span><span class="sxs-lookup"><span data-stu-id="b0f24-195">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="b0f24-196">Normalmente la carga diligente es más eficaz cuando es necesario mostrar los datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="b0f24-196">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="b0f24-197">En este caso, se muestran las asignaciones de oficina para los instructores.</span><span class="sxs-lookup"><span data-stu-id="b0f24-197">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="b0f24-198">Cuando el usuario selecciona un instructor, se muestran las entidades `Course` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="b0f24-198">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="b0f24-199">Las entidades `Instructor` y `Course` se encuentran en una relación de varios a varios.</span><span class="sxs-lookup"><span data-stu-id="b0f24-199">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="b0f24-200">La carga diligente se usa con las entidades `Course` y sus entidades `Department` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="b0f24-200">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="b0f24-201">En este caso, es posible que las consultas independientes sean más eficaces porque solo se necesitan cursos para el instructor seleccionado.</span><span class="sxs-lookup"><span data-stu-id="b0f24-201">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="b0f24-202">En este ejemplo se muestra cómo usar la carga diligente para propiedades de navegación en entidades que se encuentran en propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="b0f24-202">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="b0f24-203">Cuando el usuario selecciona un curso, se muestran los datos relacionados de la entidad `Enrollments`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-203">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="b0f24-204">En la imagen anterior, se muestra el nombre del alumno y la calificación.</span><span class="sxs-lookup"><span data-stu-id="b0f24-204">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="b0f24-205">Las entidades `Course` y `Enrollment` se encuentran en una relación uno a varios.</span><span class="sxs-lookup"><span data-stu-id="b0f24-205">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="b0f24-206">Creación de un modelo de vista</span><span class="sxs-lookup"><span data-stu-id="b0f24-206">Create a view model</span></span>

<span data-ttu-id="b0f24-207">En la página Instructors se muestran datos de tres tablas diferentes.</span><span class="sxs-lookup"><span data-stu-id="b0f24-207">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="b0f24-208">Se necesita un modelo de vista que incluya tres entidades que representen las tres tablas.</span><span class="sxs-lookup"><span data-stu-id="b0f24-208">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="b0f24-209">Cree *SchoolViewModels/InstructorIndexData.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="b0f24-209">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="b0f24-210">Scaffolding de las páginas Instructor</span><span class="sxs-lookup"><span data-stu-id="b0f24-210">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0f24-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0f24-211">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b0f24-212">Siga las instrucciones de [Scaffolding de las páginas Student](xref:data/ef-rp/intro#scaffold-student-pages) con las siguientes excepciones:</span><span class="sxs-lookup"><span data-stu-id="b0f24-212">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="b0f24-213">Cree una carpeta *Pages/Instructors*.</span><span class="sxs-lookup"><span data-stu-id="b0f24-213">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="b0f24-214">Use `Instructor` para la clase del modelo.</span><span class="sxs-lookup"><span data-stu-id="b0f24-214">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="b0f24-215">Use la clase de contexto existente en lugar de crear una.</span><span class="sxs-lookup"><span data-stu-id="b0f24-215">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0f24-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0f24-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b0f24-217">Cree una carpeta *Pages/Instructors*.</span><span class="sxs-lookup"><span data-stu-id="b0f24-217">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="b0f24-218">Ejecute el comando siguiente para aplicar scaffolding a las páginas Instructor.</span><span class="sxs-lookup"><span data-stu-id="b0f24-218">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="b0f24-219">**En Windows:**</span><span class="sxs-lookup"><span data-stu-id="b0f24-219">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="b0f24-220">**En Linux o macOS:**</span><span class="sxs-lookup"><span data-stu-id="b0f24-220">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="b0f24-221">Para ver el aspecto de la página con scaffolding antes de actualizarla, ejecute la aplicación y vaya a la página de instructores.</span><span class="sxs-lookup"><span data-stu-id="b0f24-221">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="b0f24-222">Actualice *Pages/Instructors/Index.cshtml.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="b0f24-222">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="b0f24-223">El método `OnGetAsync` acepta datos de ruta opcionales para el identificador del instructor seleccionado.</span><span class="sxs-lookup"><span data-stu-id="b0f24-223">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="b0f24-224">Examine la consulta en el archivo *Pages/Instructors/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0f24-224">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="b0f24-225">El código especifica la carga diligente para las propiedades de navegación siguientes:</span><span class="sxs-lookup"><span data-stu-id="b0f24-225">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="b0f24-226">Observe la repetición de los métodos `Include` y `ThenInclude` para `CourseAssignments` y `Course`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-226">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="b0f24-227">Esta repetición es necesaria para especificar la carga diligente para dos propiedades de navegación de la entidad `Course`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-227">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="b0f24-228">El código siguiente se ejecuta cuando se selecciona un instructor (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="b0f24-228">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="b0f24-229">El instructor seleccionado se recupera de la lista de instructores del modelo de vista.</span><span class="sxs-lookup"><span data-stu-id="b0f24-229">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="b0f24-230">Se carga la propiedad `Courses` del modelo de vista con las entidades `Course` de la propiedad de navegación `CourseAssignments` de ese instructor.</span><span class="sxs-lookup"><span data-stu-id="b0f24-230">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="b0f24-231">El método `Where` devuelve una colección.</span><span class="sxs-lookup"><span data-stu-id="b0f24-231">The `Where` method returns a collection.</span></span> <span data-ttu-id="b0f24-232">Pero en este caso, el filtro seleccionará una sola entidad.</span><span class="sxs-lookup"><span data-stu-id="b0f24-232">But in this case, the filter will select a single entity.</span></span> <span data-ttu-id="b0f24-233">Por tanto, se llama al método `Single` para convertir la colección en una sola entidad `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-233">so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="b0f24-234">La entidad `Instructor` proporciona acceso a la propiedad `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-234">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="b0f24-235">`CourseAssignments` proporciona acceso a las entidades `Course` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="b0f24-235">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Relación de varios a varios Instructor-to-Courses](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="b0f24-237">El método `Single` se usa en una colección cuando la colección tiene un solo elemento.</span><span class="sxs-lookup"><span data-stu-id="b0f24-237">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="b0f24-238">El método `Single` inicia una excepción si la colección está vacía o hay más de un elemento.</span><span class="sxs-lookup"><span data-stu-id="b0f24-238">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="b0f24-239">Una alternativa es `SingleOrDefault`, que devuelve una valor predeterminado (NULL, en este caso) si la colección está vacía.</span><span class="sxs-lookup"><span data-stu-id="b0f24-239">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="b0f24-240">El código siguiente rellena la propiedad `Enrollments` del modelo de vista cuando se selecciona un curso:</span><span class="sxs-lookup"><span data-stu-id="b0f24-240">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="b0f24-241">Actualizar la página de índice de instructores</span><span class="sxs-lookup"><span data-stu-id="b0f24-241">Update the instructors Index page</span></span>

<span data-ttu-id="b0f24-242">Actualice *Pages/Instructors/Index.cshtml* con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="b0f24-242">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="b0f24-243">En el código anterior se realizan los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="b0f24-243">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="b0f24-244">Se actualiza la directiva `page` de `@page` a `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-244">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="b0f24-245">`"{id:int?}"` es una plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="b0f24-245">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="b0f24-246">La plantilla de ruta cambia las cadenas de consulta enteras de la dirección URL por datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="b0f24-246">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="b0f24-247">Por ejemplo, al hacer clic en el vínculo **Select** de un instructor con únicamente la directiva `@page`, se genera una dirección URL similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="b0f24-247">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="b0f24-248">Cuando la directiva de página es `@page "{id:int?}"`, la dirección URL es:</span><span class="sxs-lookup"><span data-stu-id="b0f24-248">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="b0f24-249">Se agrega una columna **Office** en la que se muestra `item.OfficeAssignment.Location` solo si `item.OfficeAssignment` no es NULL.</span><span class="sxs-lookup"><span data-stu-id="b0f24-249">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="b0f24-250">Dado que se trata de una relación de uno a cero o uno, es posible que no haya una entidad OfficeAssignment relacionada.</span><span class="sxs-lookup"><span data-stu-id="b0f24-250">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="b0f24-251">Se agrega una columna **Courses** en la que se muestran los cursos que imparte cada instructor.</span><span class="sxs-lookup"><span data-stu-id="b0f24-251">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="b0f24-252">Para obtener más información sobre esta sintaxis de Razor, vea [Transición de línea explícita](xref:mvc/views/razor#explicit-line-transition).</span><span class="sxs-lookup"><span data-stu-id="b0f24-252">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="b0f24-253">Se agrega código que agrega de forma dinámica `class="success"` al elemento `tr` del instructor y curso seleccionados.</span><span class="sxs-lookup"><span data-stu-id="b0f24-253">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="b0f24-254">Esto establece el color de fondo de la fila seleccionada mediante una clase de arranque.</span><span class="sxs-lookup"><span data-stu-id="b0f24-254">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="b0f24-255">Se agrega un hipervínculo nuevo con la etiqueta **Select**.</span><span class="sxs-lookup"><span data-stu-id="b0f24-255">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="b0f24-256">Este vínculo envía el identificador del instructor seleccionado al método `Index` y establece un color de fondo.</span><span class="sxs-lookup"><span data-stu-id="b0f24-256">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="b0f24-257">Se agrega una tabla de cursos para el instructor seleccionado.</span><span class="sxs-lookup"><span data-stu-id="b0f24-257">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="b0f24-258">Se agrega una tabla de inscripciones de alumnos para el curso seleccionado.</span><span class="sxs-lookup"><span data-stu-id="b0f24-258">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="b0f24-259">Ejecute la aplicación y haga clic en la pestaña **Instructors**. En la página se muestra la `Location` (oficina) de la entidad `OfficeAssignment` relacionada.</span><span class="sxs-lookup"><span data-stu-id="b0f24-259">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="b0f24-260">Si `OfficeAssignment` es NULL, se muestra una celda de tabla vacía.</span><span class="sxs-lookup"><span data-stu-id="b0f24-260">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="b0f24-261">Haga clic en el vínculo **Select** de un instructor.</span><span class="sxs-lookup"><span data-stu-id="b0f24-261">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="b0f24-262">Se muestran los cambios de estilo de fila y los cursos asignados a ese instructor.</span><span class="sxs-lookup"><span data-stu-id="b0f24-262">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="b0f24-263">Seleccione un curso para ver la lista de los estudiantes inscritos y sus calificaciones.</span><span class="sxs-lookup"><span data-stu-id="b0f24-263">Select a course to see the list of enrolled students and their grades.</span></span>

![Instructor y curso seleccionados en la página de índice de instructores](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="b0f24-265">Uso de Single</span><span class="sxs-lookup"><span data-stu-id="b0f24-265">Using Single</span></span>

<span data-ttu-id="b0f24-266">Se puede pasar el método `Single` en la condición `Where` en lugar de llamar al método `Where` por separado:</span><span class="sxs-lookup"><span data-stu-id="b0f24-266">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="b0f24-267">El uso de `Single` con una condición Where es una cuestión de preferencia personal.</span><span class="sxs-lookup"><span data-stu-id="b0f24-267">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="b0f24-268">No proporciona ninguna ventaja sobre el uso del método `Where`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-268">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="b0f24-269">Carga explícita</span><span class="sxs-lookup"><span data-stu-id="b0f24-269">Explicit loading</span></span>

<span data-ttu-id="b0f24-270">En el código actual se especifica la carga diligente para `Enrollments` y `Students`:</span><span class="sxs-lookup"><span data-stu-id="b0f24-270">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="b0f24-271">Imagine que los usuarios rara vez querrán ver las inscripciones en un curso.</span><span class="sxs-lookup"><span data-stu-id="b0f24-271">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="b0f24-272">En ese caso, una optimización sería cargar solamente los datos de inscripción si se solicitan.</span><span class="sxs-lookup"><span data-stu-id="b0f24-272">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="b0f24-273">En esta sección, se actualiza `OnGetAsync` para usar la carga explícita de `Enrollments` y `Students`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-273">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="b0f24-274">Actualice *Pages/Instructors/Index.cshtml.cs* con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="b0f24-274">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="b0f24-275">En el código anterior se quitan las llamadas al método *ThenInclude* para los datos de inscripción y estudiantes.</span><span class="sxs-lookup"><span data-stu-id="b0f24-275">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="b0f24-276">Si se selecciona un curso, el código de carga explícita recupera lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b0f24-276">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="b0f24-277">Las entidades `Enrollment` para el curso seleccionado.</span><span class="sxs-lookup"><span data-stu-id="b0f24-277">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="b0f24-278">Las entidades `Student` para cada `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-278">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="b0f24-279">Observe que en el código anterior `.AsNoTracking()` se convierte en comentario.</span><span class="sxs-lookup"><span data-stu-id="b0f24-279">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="b0f24-280">Las propiedades de navegación solo se pueden cargar explícitamente para las entidades sometidas a seguimiento.</span><span class="sxs-lookup"><span data-stu-id="b0f24-280">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="b0f24-281">Pruebe la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b0f24-281">Test the app.</span></span> <span data-ttu-id="b0f24-282">Desde la perspectiva del usuario, la aplicación se comporta exactamente igual a la versión anterior.</span><span class="sxs-lookup"><span data-stu-id="b0f24-282">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b0f24-283">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="b0f24-283">Next steps</span></span>

<span data-ttu-id="b0f24-284">En el siguiente tutorial se muestra cómo actualizar datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="b0f24-284">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="b0f24-285">[Tutorial anterior](xref:data/ef-rp/complex-data-model)
>[Tutorial siguiente](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="b0f24-285">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b0f24-286">En este tutorial, se leen y se muestran datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="b0f24-286">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="b0f24-287">Los datos relacionados son los que EF Core carga en las propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="b0f24-287">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="b0f24-288">Si experimenta problemas que no puede resolver, [descargue o vea la aplicación completada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="b0f24-288">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="b0f24-289">[Instrucciones de descarga](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="b0f24-289">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="b0f24-290">En las ilustraciones siguientes se muestran las páginas completadas para este tutorial:</span><span class="sxs-lookup"><span data-stu-id="b0f24-290">The following illustrations show the completed pages for this tutorial:</span></span>

![Página de índice de cursos](read-related-data/_static/courses-index.png)

![Página de índice de instructores](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="b0f24-293">Carga diligente, explícita y diferida de datos relacionados</span><span class="sxs-lookup"><span data-stu-id="b0f24-293">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="b0f24-294">EF Core puede cargar datos relacionados en las propiedades de navegación de una entidad de varias maneras:</span><span class="sxs-lookup"><span data-stu-id="b0f24-294">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="b0f24-295">[Carga diligente](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="b0f24-295">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="b0f24-296">La carga diligente es cuando una consulta para un tipo de entidad también carga las entidades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="b0f24-296">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="b0f24-297">Cuando se lee la entidad, se recuperan sus datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="b0f24-297">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="b0f24-298">Esto normalmente da como resultado una única consulta de combinación en la que se recuperan todos los datos que se necesitan.</span><span class="sxs-lookup"><span data-stu-id="b0f24-298">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="b0f24-299">EF Core emitirá varias consultas para algunos tipos de carga diligente.</span><span class="sxs-lookup"><span data-stu-id="b0f24-299">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="b0f24-300">La emisión de varias consultas puede ser más eficaz de lo que eran algunas consultas de EF6 cuando había una sola consulta.</span><span class="sxs-lookup"><span data-stu-id="b0f24-300">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="b0f24-301">La carga diligente se especifica con los métodos `Include` y `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-301">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Ejemplo de carga diligente](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="b0f24-303">La carga diligente envía varias consultas cuando se incluye una propiedad de navegación de colección:</span><span class="sxs-lookup"><span data-stu-id="b0f24-303">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="b0f24-304">Una consulta para la consulta principal</span><span class="sxs-lookup"><span data-stu-id="b0f24-304">One query for the main query</span></span> 
  * <span data-ttu-id="b0f24-305">Una consulta para cada colección "perimetral" en el árbol de la carga.</span><span class="sxs-lookup"><span data-stu-id="b0f24-305">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="b0f24-306">Separe las consultas con `Load`: los datos se pueden recuperar en distintas consultas y EF Core "corrige" las propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="b0f24-306">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="b0f24-307">"Corregir" significa que EF Core rellena automáticamente las propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="b0f24-307">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="b0f24-308">La separación de las consultas con `Load` es más parecido a la carga explícita que a la carga diligente.</span><span class="sxs-lookup"><span data-stu-id="b0f24-308">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Ejemplo de consultas independientes](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="b0f24-310">Nota: EF Core corrige automáticamente las propiedades de navegación para todas las entidades que se cargaron previamente en la instancia del contexto.</span><span class="sxs-lookup"><span data-stu-id="b0f24-310">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="b0f24-311">Incluso si los datos de una propiedad de navegación *no* se incluyen explícitamente, es posible que la propiedad se siga rellenando si algunas o todas las entidades relacionadas se cargaron previamente.</span><span class="sxs-lookup"><span data-stu-id="b0f24-311">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="b0f24-312">[Carga explícita](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="b0f24-312">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="b0f24-313">Cuando la entidad se lee por primera vez, no se recuperan datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="b0f24-313">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="b0f24-314">Se debe escribir código para recuperar los datos relacionados cuando sea necesario.</span><span class="sxs-lookup"><span data-stu-id="b0f24-314">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="b0f24-315">La carga explícita con consultas independientes da como resultado varias consultas que se envían a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b0f24-315">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="b0f24-316">Con la carga explícita, el código especifica las propiedades de navegación que se van a cargar.</span><span class="sxs-lookup"><span data-stu-id="b0f24-316">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="b0f24-317">Use el método `Load` para realizar la carga explícita.</span><span class="sxs-lookup"><span data-stu-id="b0f24-317">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="b0f24-318">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="b0f24-318">For example:</span></span>

  ![Ejemplo de carga explícita](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="b0f24-320">[Carga diferida](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="b0f24-320">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="b0f24-321">[Se ha agregado la carga diferida a EF Core en la versión 2.1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="b0f24-321">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="b0f24-322">Cuando la entidad se lee por primera vez, no se recuperan datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="b0f24-322">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="b0f24-323">La primera vez que se obtiene acceso a una propiedad de navegación, se recuperan automáticamente los datos necesarios para esa propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="b0f24-323">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="b0f24-324">Cada vez que se obtiene acceso a una propiedad de navegación, se envía una consulta a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b0f24-324">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="b0f24-325">El operador `Select` solo carga los datos relacionados necesarios.</span><span class="sxs-lookup"><span data-stu-id="b0f24-325">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="b0f24-326">Crear una página de cursos en la que se muestre el nombre de departamento</span><span class="sxs-lookup"><span data-stu-id="b0f24-326">Create a Course page that displays department name</span></span>

<span data-ttu-id="b0f24-327">La entidad Course incluye una propiedad de navegación que contiene la entidad `Department`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-327">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="b0f24-328">La entidad `Department` contiene el departamento al que se asigna el curso.</span><span class="sxs-lookup"><span data-stu-id="b0f24-328">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="b0f24-329">Para mostrar el nombre del departamento asignado en una lista de cursos:</span><span class="sxs-lookup"><span data-stu-id="b0f24-329">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="b0f24-330">Obtenga la propiedad `Name` desde la entidad `Department`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-330">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="b0f24-331">La entidad `Department` procede de la propiedad de navegación `Course.Department`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-331">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="b0f24-333">Aplicar scaffolding al modelo de Course</span><span class="sxs-lookup"><span data-stu-id="b0f24-333">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0f24-334">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0f24-334">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="b0f24-335">Siga las instrucciones que encontrará en [Aplicación de scaffolding al modelo de alumnos](xref:data/ef-rp/intro#scaffold-the-student-model) y use `Course` para la clase de modelo.</span><span class="sxs-lookup"><span data-stu-id="b0f24-335">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0f24-336">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0f24-336">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="b0f24-337">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="b0f24-337">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="b0f24-338">El comando anterior aplica scaffolding al modelo `Course`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-338">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="b0f24-339">Abra el proyecto en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b0f24-339">Open the project in Visual Studio.</span></span>

<span data-ttu-id="b0f24-340">Abra *Pages/Courses/Index.cshtml.cs* y examine el método `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-340">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="b0f24-341">El motor de scaffolding especificado realiza la carga diligente de la propiedad de navegación `Department`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-341">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="b0f24-342">El método `Include` especifica la carga diligente.</span><span class="sxs-lookup"><span data-stu-id="b0f24-342">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="b0f24-343">Ejecute la aplicación y haga clic en el vínculo **Courses**.</span><span class="sxs-lookup"><span data-stu-id="b0f24-343">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="b0f24-344">En la columna Department se muestra el `DepartmentID`, lo que no resulta útil.</span><span class="sxs-lookup"><span data-stu-id="b0f24-344">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="b0f24-345">Actualice el método `OnGetAsync` con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="b0f24-345">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="b0f24-346">El código anterior agrega `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-346">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="b0f24-347">`AsNoTracking` mejora el rendimiento porque no se realiza el seguimiento de las entidades devueltas.</span><span class="sxs-lookup"><span data-stu-id="b0f24-347">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="b0f24-348">No se realiza el seguimiento de las entidades porque no se actualizan en el contexto actual.</span><span class="sxs-lookup"><span data-stu-id="b0f24-348">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="b0f24-349">Actualice *Pages/Courses/Index.cshtml* con el marcado resaltado siguiente:</span><span class="sxs-lookup"><span data-stu-id="b0f24-349">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="b0f24-350">Se han realizado los cambios siguientes en el código con scaffolding:</span><span class="sxs-lookup"><span data-stu-id="b0f24-350">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="b0f24-351">Ha cambiado el título de Index a Courses.</span><span class="sxs-lookup"><span data-stu-id="b0f24-351">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="b0f24-352">Ha agregado una columna **Number** en la que se muestra el valor de propiedad `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-352">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="b0f24-353">De forma predeterminada, las claves principales no tienen scaffolding porque normalmente no tienen sentido para los usuarios finales.</span><span class="sxs-lookup"><span data-stu-id="b0f24-353">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="b0f24-354">Pero en este caso, la clave principal es significativa.</span><span class="sxs-lookup"><span data-stu-id="b0f24-354">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="b0f24-355">Ha cambiado la columna **Department** para mostrar el nombre del departamento.</span><span class="sxs-lookup"><span data-stu-id="b0f24-355">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="b0f24-356">El código muestra la propiedad `Name` de la entidad `Department` que se carga en la propiedad de navegación `Department`:</span><span class="sxs-lookup"><span data-stu-id="b0f24-356">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="b0f24-357">Ejecute la aplicación y haga clic en la pestaña **Courses** para ver la lista con los nombres de departamento.</span><span class="sxs-lookup"><span data-stu-id="b0f24-357">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Página de índice de cursos](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="b0f24-359">Carga de datos relacionados con Select</span><span class="sxs-lookup"><span data-stu-id="b0f24-359">Loading related data with Select</span></span>

<span data-ttu-id="b0f24-360">El método `OnGetAsync` carga los datos relacionados con el método `Include`:</span><span class="sxs-lookup"><span data-stu-id="b0f24-360">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="b0f24-361">El operador `Select` solo carga los datos relacionados necesarios.</span><span class="sxs-lookup"><span data-stu-id="b0f24-361">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="b0f24-362">Para elementos individuales, como el `Department.Name`, se usa INNER JOIN de SQL.</span><span class="sxs-lookup"><span data-stu-id="b0f24-362">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="b0f24-363">En las colecciones, se usa otro acceso de base de datos, como también hace el operador `Include` en las colecciones.</span><span class="sxs-lookup"><span data-stu-id="b0f24-363">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="b0f24-364">En el código siguiente se cargan los datos relacionados con el método `Select`:</span><span class="sxs-lookup"><span data-stu-id="b0f24-364">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="b0f24-365">El `CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="b0f24-365">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="b0f24-366">Vea [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) e [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) para obtener un ejemplo completo.</span><span class="sxs-lookup"><span data-stu-id="b0f24-366">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="b0f24-367">Crear una página de instructores en la que se muestran los cursos y las inscripciones</span><span class="sxs-lookup"><span data-stu-id="b0f24-367">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="b0f24-368">En esta sección, se crea la página de instructores.</span><span class="sxs-lookup"><span data-stu-id="b0f24-368">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="b0f24-369">![Página de índice de instructores](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="b0f24-369">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="b0f24-370">En esta página se leen y muestran los datos relacionados de las maneras siguientes:</span><span class="sxs-lookup"><span data-stu-id="b0f24-370">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="b0f24-371">En la lista de instructores se muestran datos relacionados de la entidad `OfficeAssignment` (Office en la imagen anterior).</span><span class="sxs-lookup"><span data-stu-id="b0f24-371">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="b0f24-372">Las entidades `Instructor` y `OfficeAssignment` se encuentran en una relación de uno a cero o uno.</span><span class="sxs-lookup"><span data-stu-id="b0f24-372">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="b0f24-373">Para las entidades `OfficeAssignment` se usa la carga diligente.</span><span class="sxs-lookup"><span data-stu-id="b0f24-373">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="b0f24-374">Normalmente la carga diligente es más eficaz cuando es necesario mostrar los datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="b0f24-374">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="b0f24-375">En este caso, se muestran las asignaciones de oficina para los instructores.</span><span class="sxs-lookup"><span data-stu-id="b0f24-375">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="b0f24-376">Cuando el usuario selecciona un instructor (Harui en la imagen anterior), se muestran las entidades `Course` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="b0f24-376">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="b0f24-377">Las entidades `Instructor` y `Course` se encuentran en una relación de varios a varios.</span><span class="sxs-lookup"><span data-stu-id="b0f24-377">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="b0f24-378">La carga diligente se usa con las entidades `Course` y sus entidades `Department` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="b0f24-378">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="b0f24-379">En este caso, es posible que las consultas independientes sean más eficaces porque solo se necesitan cursos para el instructor seleccionado.</span><span class="sxs-lookup"><span data-stu-id="b0f24-379">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="b0f24-380">En este ejemplo se muestra cómo usar la carga diligente para propiedades de navegación en entidades que se encuentran en propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="b0f24-380">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="b0f24-381">Cuando el usuario selecciona un curso (Chemistry [Química] en la imagen anterior), se muestran los datos relacionados de la entidad `Enrollments`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-381">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="b0f24-382">En la imagen anterior, se muestra el nombre del alumno y la calificación.</span><span class="sxs-lookup"><span data-stu-id="b0f24-382">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="b0f24-383">Las entidades `Course` y `Enrollment` se encuentran en una relación uno a varios.</span><span class="sxs-lookup"><span data-stu-id="b0f24-383">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="b0f24-384">Crear un modelo de vista para la vista de índice de instructores</span><span class="sxs-lookup"><span data-stu-id="b0f24-384">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="b0f24-385">En la página Instructors se muestran datos de tres tablas diferentes.</span><span class="sxs-lookup"><span data-stu-id="b0f24-385">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="b0f24-386">Se crea un modelo de vista que incluye las tres entidades que representan las tres tablas.</span><span class="sxs-lookup"><span data-stu-id="b0f24-386">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="b0f24-387">En la carpeta *SchoolViewModels*, cree *InstructorIndexData.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="b0f24-387">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="b0f24-388">Aplicar scaffolding al modelo de Instructor</span><span class="sxs-lookup"><span data-stu-id="b0f24-388">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0f24-389">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0f24-389">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="b0f24-390">Siga las instrucciones que encontrará en [Aplicación de scaffolding al modelo de alumnos](xref:data/ef-rp/intro#scaffold-the-student-model) y use `Instructor` para la clase de modelo.</span><span class="sxs-lookup"><span data-stu-id="b0f24-390">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0f24-391">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0f24-391">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="b0f24-392">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="b0f24-392">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="b0f24-393">El comando anterior aplica scaffolding al modelo `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-393">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="b0f24-394">Ejecute la aplicación y vaya a la página de instructores.</span><span class="sxs-lookup"><span data-stu-id="b0f24-394">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="b0f24-395">Reemplace *Pages/Instructors/Index.cshtml.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="b0f24-395">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="b0f24-396">El método `OnGetAsync` acepta datos de ruta opcionales para el identificador del instructor seleccionado.</span><span class="sxs-lookup"><span data-stu-id="b0f24-396">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="b0f24-397">Examine la consulta en el archivo *Pages/Instructors/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0f24-397">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="b0f24-398">La consulta tiene dos instrucciones include:</span><span class="sxs-lookup"><span data-stu-id="b0f24-398">The query has two includes:</span></span>

* <span data-ttu-id="b0f24-399">`OfficeAssignment`: se muestra en la [vista de instructores](#IP).</span><span class="sxs-lookup"><span data-stu-id="b0f24-399">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="b0f24-400">`CourseAssignments`: muestra los cursos impartidos.</span><span class="sxs-lookup"><span data-stu-id="b0f24-400">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="b0f24-401">Actualizar la página de índice de instructores</span><span class="sxs-lookup"><span data-stu-id="b0f24-401">Update the instructors Index page</span></span>

<span data-ttu-id="b0f24-402">Actualice *Pages/Instructors/Index.cshtml* con el marcado siguiente:</span><span class="sxs-lookup"><span data-stu-id="b0f24-402">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="b0f24-403">En el marcado anterior se realizan los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="b0f24-403">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="b0f24-404">Se actualiza la directiva `page` de `@page` a `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-404">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="b0f24-405">`"{id:int?}"` es una plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="b0f24-405">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="b0f24-406">La plantilla de ruta cambia las cadenas de consulta enteras de la dirección URL por datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="b0f24-406">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="b0f24-407">Por ejemplo, al hacer clic en el vínculo **Select** de un instructor con únicamente la directiva `@page`, se genera una dirección URL similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="b0f24-407">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="b0f24-408">Cuando la directiva de página es `@page "{id:int?}"`, la dirección URL anterior es:</span><span class="sxs-lookup"><span data-stu-id="b0f24-408">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="b0f24-409">El título de página es **Instructors**.</span><span class="sxs-lookup"><span data-stu-id="b0f24-409">Page title is **Instructors**.</span></span>
* <span data-ttu-id="b0f24-410">Se ha agregado una columna **Office** en la que se muestra `item.OfficeAssignment.Location` solo si `item.OfficeAssignment` no es NULL.</span><span class="sxs-lookup"><span data-stu-id="b0f24-410">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="b0f24-411">Dado que se trata de una relación de uno a cero o uno, es posible que no haya una entidad OfficeAssignment relacionada.</span><span class="sxs-lookup"><span data-stu-id="b0f24-411">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="b0f24-412">Se ha agregado una columna **Courses** en la que se muestran los cursos que imparte cada instructor.</span><span class="sxs-lookup"><span data-stu-id="b0f24-412">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="b0f24-413">Para obtener más información sobre esta sintaxis de Razor, vea [Transición de línea explícita](xref:mvc/views/razor#explicit-line-transition).</span><span class="sxs-lookup"><span data-stu-id="b0f24-413">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="b0f24-414">Ha agregado código que agrega dinámicamente `class="success"` al elemento `tr` del instructor seleccionado.</span><span class="sxs-lookup"><span data-stu-id="b0f24-414">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="b0f24-415">Esto establece el color de fondo de la fila seleccionada mediante una clase de arranque.</span><span class="sxs-lookup"><span data-stu-id="b0f24-415">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="b0f24-416">Se ha agregado un hipervínculo nuevo con la etiqueta **Select**.</span><span class="sxs-lookup"><span data-stu-id="b0f24-416">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="b0f24-417">Este vínculo envía el identificador del instructor seleccionado al método `Index` y establece un color de fondo.</span><span class="sxs-lookup"><span data-stu-id="b0f24-417">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="b0f24-418">Ejecute la aplicación y haga clic en la pestaña **Instructors**. En la página se muestra la `Location` (oficina) de la entidad `OfficeAssignment` relacionada.</span><span class="sxs-lookup"><span data-stu-id="b0f24-418">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="b0f24-419">Si OfficeAssignment es NULL, se muestra una celda de tabla vacía.</span><span class="sxs-lookup"><span data-stu-id="b0f24-419">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="b0f24-420">Haga clic en el vínculo **Select**.</span><span class="sxs-lookup"><span data-stu-id="b0f24-420">Click on the **Select** link.</span></span> <span data-ttu-id="b0f24-421">El estilo de la fila cambia.</span><span class="sxs-lookup"><span data-stu-id="b0f24-421">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="b0f24-422">Agregar cursos impartidos por el instructor seleccionado</span><span class="sxs-lookup"><span data-stu-id="b0f24-422">Add courses taught by selected instructor</span></span>

<span data-ttu-id="b0f24-423">Actualice el método `OnGetAsync` de *Pages/Instructors/Index.cshtml.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="b0f24-423">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="b0f24-424">Agregue `public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="b0f24-424">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="b0f24-425">Examine la consulta actualizada:</span><span class="sxs-lookup"><span data-stu-id="b0f24-425">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="b0f24-426">En la consulta anterior se agregan las entidades `Department`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-426">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="b0f24-427">El código siguiente se ejecuta cuando se selecciona un instructor (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="b0f24-427">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="b0f24-428">El instructor seleccionado se recupera de la lista de instructores del modelo de vista.</span><span class="sxs-lookup"><span data-stu-id="b0f24-428">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="b0f24-429">Se carga la propiedad `Courses` del modelo de vista con las entidades `Course` de la propiedad de navegación `CourseAssignments` de ese instructor.</span><span class="sxs-lookup"><span data-stu-id="b0f24-429">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="b0f24-430">El método `Where` devuelve una colección.</span><span class="sxs-lookup"><span data-stu-id="b0f24-430">The `Where` method returns a collection.</span></span> <span data-ttu-id="b0f24-431">En el método `Where` anterior, solo se devuelve una entidad `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-431">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="b0f24-432">El método `Single` convierte la colección en una sola entidad `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-432">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="b0f24-433">La entidad `Instructor` proporciona acceso a la propiedad `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-433">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="b0f24-434">`CourseAssignments` proporciona acceso a las entidades `Course` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="b0f24-434">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Relación de varios a varios Instructor-to-Courses](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="b0f24-436">El método `Single` se usa en una colección cuando la colección tiene un solo elemento.</span><span class="sxs-lookup"><span data-stu-id="b0f24-436">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="b0f24-437">El método `Single` inicia una excepción si la colección está vacía o hay más de un elemento.</span><span class="sxs-lookup"><span data-stu-id="b0f24-437">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="b0f24-438">Una alternativa es `SingleOrDefault`, que devuelve una valor predeterminado (NULL, en este caso) si la colección está vacía.</span><span class="sxs-lookup"><span data-stu-id="b0f24-438">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="b0f24-439">El uso de `SingleOrDefault` en una colección vacía:</span><span class="sxs-lookup"><span data-stu-id="b0f24-439">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="b0f24-440">Inicia una excepción (al tratar de buscar una propiedad `Courses` en una referencia nula).</span><span class="sxs-lookup"><span data-stu-id="b0f24-440">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="b0f24-441">El mensaje de excepción indicará con menos claridad la causa del problema.</span><span class="sxs-lookup"><span data-stu-id="b0f24-441">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="b0f24-442">El código siguiente rellena la propiedad `Enrollments` del modelo de vista cuando se selecciona un curso:</span><span class="sxs-lookup"><span data-stu-id="b0f24-442">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="b0f24-443">Agregue el siguiente marcado al final de la página de Razor *Pages/Instructors/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b0f24-443">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="b0f24-444">En el marcado anterior se muestra una lista de cursos relacionados con un instructor cuando se selecciona un instructor.</span><span class="sxs-lookup"><span data-stu-id="b0f24-444">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="b0f24-445">Pruebe la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b0f24-445">Test the app.</span></span> <span data-ttu-id="b0f24-446">Haga clic en un vínculo **Select** en la página de instructores.</span><span class="sxs-lookup"><span data-stu-id="b0f24-446">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="b0f24-447">Mostrar datos de estudiante</span><span class="sxs-lookup"><span data-stu-id="b0f24-447">Show student data</span></span>

<span data-ttu-id="b0f24-448">En esta sección, la aplicación se actualiza para mostrar los datos de estudiante para un curso seleccionado.</span><span class="sxs-lookup"><span data-stu-id="b0f24-448">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="b0f24-449">Actualice la consulta en el método `OnGetAsync` de *Pages/Instructors/Index.cshtml.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="b0f24-449">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="b0f24-450">Actualice *Pages/Instructors/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b0f24-450">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="b0f24-451">Agregue el marcado siguiente al final del archivo:</span><span class="sxs-lookup"><span data-stu-id="b0f24-451">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="b0f24-452">En el marcado anterior se muestra una lista de los estudiantes que están inscritos en el curso seleccionado.</span><span class="sxs-lookup"><span data-stu-id="b0f24-452">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="b0f24-453">Actualice la página y seleccione un instructor.</span><span class="sxs-lookup"><span data-stu-id="b0f24-453">Refresh the page and select an instructor.</span></span> <span data-ttu-id="b0f24-454">Seleccione un curso para ver la lista de los estudiantes inscritos y sus calificaciones.</span><span class="sxs-lookup"><span data-stu-id="b0f24-454">Select a course to see the list of enrolled students and their grades.</span></span>

![Instructor y curso seleccionados en la página de índice de instructores](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="b0f24-456">Uso de Single</span><span class="sxs-lookup"><span data-stu-id="b0f24-456">Using Single</span></span>

<span data-ttu-id="b0f24-457">Se puede pasar el método `Single` en la condición `Where` en lugar de llamar al método `Where` por separado:</span><span class="sxs-lookup"><span data-stu-id="b0f24-457">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="b0f24-458">El enfoque de `Single` anterior no ofrece ninguna ventaja con respecto a `Where`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-458">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="b0f24-459">Algunos desarrolladores prefieren el estilo del enfoque de `Single`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-459">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="b0f24-460">Carga explícita</span><span class="sxs-lookup"><span data-stu-id="b0f24-460">Explicit loading</span></span>

<span data-ttu-id="b0f24-461">En el código actual se especifica la carga diligente para `Enrollments` y `Students`:</span><span class="sxs-lookup"><span data-stu-id="b0f24-461">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="b0f24-462">Imagine que los usuarios rara vez querrán ver las inscripciones en un curso.</span><span class="sxs-lookup"><span data-stu-id="b0f24-462">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="b0f24-463">En ese caso, una optimización sería cargar solamente los datos de inscripción si se solicitan.</span><span class="sxs-lookup"><span data-stu-id="b0f24-463">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="b0f24-464">En esta sección, se actualiza `OnGetAsync` para usar la carga explícita de `Enrollments` y `Students`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-464">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="b0f24-465">Actualice `OnGetAsync` con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="b0f24-465">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="b0f24-466">En el código anterior se quitan las llamadas al método *ThenInclude* para los datos de inscripción y estudiantes.</span><span class="sxs-lookup"><span data-stu-id="b0f24-466">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="b0f24-467">Si se selecciona un curso, el código resaltado recupera lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b0f24-467">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="b0f24-468">Las entidades `Enrollment` para el curso seleccionado.</span><span class="sxs-lookup"><span data-stu-id="b0f24-468">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="b0f24-469">Las entidades `Student` para cada `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="b0f24-469">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="b0f24-470">Tenga en cuenta que en el código anterior `.AsNoTracking()` se convierte en comentario.</span><span class="sxs-lookup"><span data-stu-id="b0f24-470">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="b0f24-471">Las propiedades de navegación solo se pueden cargar explícitamente para las entidades sometidas a seguimiento.</span><span class="sxs-lookup"><span data-stu-id="b0f24-471">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="b0f24-472">Pruebe la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b0f24-472">Test the app.</span></span> <span data-ttu-id="b0f24-473">Desde la perspectiva de los usuarios, la aplicación se comporta exactamente igual a la versión anterior.</span><span class="sxs-lookup"><span data-stu-id="b0f24-473">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="b0f24-474">En el siguiente tutorial se muestra cómo actualizar datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="b0f24-474">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b0f24-475">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="b0f24-475">Additional resources</span></span>

* [<span data-ttu-id="b0f24-476">Versión en YouTube de este tutorial (parte 1)</span><span class="sxs-lookup"><span data-stu-id="b0f24-476">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="b0f24-477">Versión en YouTube de este tutorial (parte 2)</span><span class="sxs-lookup"><span data-stu-id="b0f24-477">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="b0f24-478">[Anterior](xref:data/ef-rp/complex-data-model)
>[Siguiente](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="b0f24-478">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
