---
title: Actualizar las páginas generadas en una aplicación ASP.NET Core
author: rick-anderson
description: Sepa cómo actualizar las páginas generadas en una aplicación ASP.NET Core.
ms.author: riande
ms.date: 12/20/2018
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 0f6535462fe2d308825bf7289c10d2b0690cebd4
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78650429"
---
# <a name="update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="faebe-103">Actualizar las páginas generadas en una aplicación ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="faebe-103">Update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="faebe-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="faebe-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="faebe-105">La aplicación de películas con scaffolding pinta bien, pero la presentación no es ideal.</span><span class="sxs-lookup"><span data-stu-id="faebe-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="faebe-106">**FechaDeLanzamiento** debe ser **Fecha de lanzamiento** (tres palabras).</span><span class="sxs-lookup"><span data-stu-id="faebe-106">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Aplicación Movie abierta en Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="faebe-108">Actualización del código generado</span><span class="sxs-lookup"><span data-stu-id="faebe-108">Update the generated code</span></span>

<span data-ttu-id="faebe-109">Abra el archivo *Models/Movie.cs* y agregue las líneas resaltadas mostradas en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="faebe-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="faebe-110">La anotación de datos `[Column(TypeName = "decimal(18, 2)")]` permite que Entity Framework Core asigne correctamente `Price` a la moneda en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="faebe-110">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="faebe-111">Para más información, vea [Tipos de datos](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="faebe-111">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="faebe-112">En el próximo tutorial, hablaremos de [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6).</span><span class="sxs-lookup"><span data-stu-id="faebe-112">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="faebe-113">El atributo [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) especifica qué se muestra como nombre de un campo (en este caso, "Release Date" en lugar de "ReleaseDate").</span><span class="sxs-lookup"><span data-stu-id="faebe-113">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="faebe-114">El atributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) especifica el tipo de los datos (Date), así que la información de hora almacenada en el campo no se muestra.</span><span class="sxs-lookup"><span data-stu-id="faebe-114">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="faebe-115">Vaya a Pages/Movies y mantenga el mouse sobre un vínculo de **edición** para ver la dirección URL de destino.</span><span class="sxs-lookup"><span data-stu-id="faebe-115">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Ventana del explorador con el mouse sobre el vínculo Edit (Editar) donde se muestra una dirección URL de vínculo http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="faebe-117">Los vínculos **Edit**, **Details** y **Delete** son generados por el [asistente de etiquetas de delimitador](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) del archivo *Pages/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="faebe-117">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="faebe-118">Los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="faebe-118">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="faebe-119">En el código anterior, `AnchorTagHelper` genera de forma dinámica el valor del atributo `href` HTML desde la página de Razor (la ruta es relativa), el elemento `asp-page` y el identificador de ruta (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="faebe-119">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="faebe-120">Vea [Generación de direcciones URL para las páginas](xref:razor-pages/index#url-generation-for-pages) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="faebe-120">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="faebe-121">Use **Ver código fuente** en su explorador preferido para examinar el marcado generado.</span><span class="sxs-lookup"><span data-stu-id="faebe-121">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="faebe-122">A continuación se muestra una parte del HTML generado:</span><span class="sxs-lookup"><span data-stu-id="faebe-122">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="faebe-123">Los vínculos generados de forma dinámica pasan el identificador de la película con una cadena de consulta (por ejemplo, el `?id=1` de `https://localhost:5001/Movies/Details?id=1`).</span><span class="sxs-lookup"><span data-stu-id="faebe-123">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

### <a name="add-route-template"></a><span data-ttu-id="faebe-124">Adición de la plantilla de ruta</span><span class="sxs-lookup"><span data-stu-id="faebe-124">Add route template</span></span>

<span data-ttu-id="faebe-125">Actualice las páginas de edición, detalles y eliminación de Razor para usar la plantilla de ruta "{id:int}".</span><span class="sxs-lookup"><span data-stu-id="faebe-125">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="faebe-126">Cambie la directiva de página de cada una de estas páginas de `@page` a `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="faebe-126">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="faebe-127">Ejecute la aplicación y luego vea el origen.</span><span class="sxs-lookup"><span data-stu-id="faebe-127">Run the app and then view source.</span></span> <span data-ttu-id="faebe-128">El HTML generado agrega el identificador a la parte de la ruta de acceso de la dirección URL:</span><span class="sxs-lookup"><span data-stu-id="faebe-128">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="faebe-129">Una solicitud a la página con la plantilla de ruta "{id:int}" que **no** incluya el entero devolverá un error HTTP 404 (no encontrado).</span><span class="sxs-lookup"><span data-stu-id="faebe-129">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="faebe-130">Por ejemplo, `http://localhost:5000/Movies/Details` devolverá un error 404.</span><span class="sxs-lookup"><span data-stu-id="faebe-130">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="faebe-131">Para que el identificador sea opcional, anexe `?` a la restricción de ruta:</span><span class="sxs-lookup"><span data-stu-id="faebe-131">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="faebe-132">Para probar el comportamiento de `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="faebe-132">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="faebe-133">Establezca la directiva de página de *Pages/Movies/Details.cshtml* en `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="faebe-133">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="faebe-134">Establezca un punto de interrupción en `public async Task<IActionResult> OnGetAsync(int? id)` (en *Pages/Movies/Details.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="faebe-134">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="faebe-135">Navegue a `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="faebe-135">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="faebe-136">Con la directiva `@page "{id:int}"`, el punto de interrupción nunca se alcanza.</span><span class="sxs-lookup"><span data-stu-id="faebe-136">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="faebe-137">El motor de enrutamiento devuelve HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="faebe-137">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="faebe-138">Con `@page "{id:int?}"`, el método `OnGetAsync` devuelve `NotFound` (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="faebe-138">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="faebe-139">Revisión del control de excepciones de simultaneidad</span><span class="sxs-lookup"><span data-stu-id="faebe-139">Review concurrency exception handling</span></span>

<span data-ttu-id="faebe-140">Revise el método `OnPostAsync` en el archivo *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="faebe-140">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="faebe-141">El código anterior detecta las excepciones de simultaneidad cuando el cliente uno elimina la película y el otro cliente publica cambios en ella.</span><span class="sxs-lookup"><span data-stu-id="faebe-141">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="faebe-142">Para probar el bloque `catch`:</span><span class="sxs-lookup"><span data-stu-id="faebe-142">To test the `catch` block:</span></span>

* <span data-ttu-id="faebe-143">Establecer un punto de interrupción en `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="faebe-143">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="faebe-144">Seleccione **Editar** para una película y realice cambios, pero no seleccione **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="faebe-144">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="faebe-145">En otra ventana del explorador, seleccione el vínculo de **eliminación** de la misma película y luego elimínela.</span><span class="sxs-lookup"><span data-stu-id="faebe-145">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="faebe-146">En la ventana anterior del explorador, publique los cambios en la película.</span><span class="sxs-lookup"><span data-stu-id="faebe-146">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="faebe-147">Es posible que el código de producción quiera detectar conflictos de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="faebe-147">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="faebe-148">Vea [Administración de conflictos de simultaneidad](xref:data/ef-rp/concurrency) para más información.</span><span class="sxs-lookup"><span data-stu-id="faebe-148">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="faebe-149">Revisión de publicaciones y enlaces</span><span class="sxs-lookup"><span data-stu-id="faebe-149">Posting and binding review</span></span>

<span data-ttu-id="faebe-150">Examine el archivo *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="faebe-150">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="faebe-151">Cuando se realiza una solicitud HTTP GET a la página Movies/Edit (por ejemplo, `http://localhost:5000/Movies/Edit/2`):</span><span class="sxs-lookup"><span data-stu-id="faebe-151">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="faebe-152">El método `OnGetAsync` obtiene la película en la base de datos y devuelve el método `Page`.</span><span class="sxs-lookup"><span data-stu-id="faebe-152">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="faebe-153">El método `Page` presenta la página de Razor *Pages/Movies/Edit.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="faebe-153">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="faebe-154">El archivo *Pages/Movies/Edit.cshtml* contiene la directiva de modelo (`@model RazorPagesMovie.Pages.Movies.EditModel`), que hace que el modelo de película esté disponible en la página.</span><span class="sxs-lookup"><span data-stu-id="faebe-154">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="faebe-155">Se abre el formulario de edición con los valores de la película.</span><span class="sxs-lookup"><span data-stu-id="faebe-155">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="faebe-156">Cuando se publica la página Movies/Edit:</span><span class="sxs-lookup"><span data-stu-id="faebe-156">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="faebe-157">Los valores del formulario de la página se enlazan a la propiedad `Movie`.</span><span class="sxs-lookup"><span data-stu-id="faebe-157">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="faebe-158">El atributo `[BindProperty]` habilita el [enlace de modelos](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="faebe-158">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="faebe-159">Si hay errores en el estado del modelo (por ejemplo, `ReleaseDate` no se puede convertir en una fecha), el formulario se vuelve a mostrar con los valores enviados.</span><span class="sxs-lookup"><span data-stu-id="faebe-159">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="faebe-160">Si no hay ningún error en el modelo, se guarda la película.</span><span class="sxs-lookup"><span data-stu-id="faebe-160">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="faebe-161">Los métodos HTTP GET de las páginas de índice, creación y eliminación de Razor siguen un patrón similar.</span><span class="sxs-lookup"><span data-stu-id="faebe-161">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="faebe-162">El método HTTP POST `OnPostAsync` de la página de creación de Razor sigue un patrón similar al del método `OnPostAsync` de la página de edición de Razor.</span><span class="sxs-lookup"><span data-stu-id="faebe-162">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="faebe-163">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="faebe-163">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="faebe-164">[Anterior: Trabajo con una base de datos](xref:tutorials/razor-pages/sql)
> [Siguiente: Adición de búsqueda](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="faebe-164">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="faebe-165">La aplicación de películas con scaffolding pinta bien, pero la presentación no es ideal.</span><span class="sxs-lookup"><span data-stu-id="faebe-165">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="faebe-166">**FechaDeLanzamiento** debe ser **Fecha de lanzamiento** (tres palabras).</span><span class="sxs-lookup"><span data-stu-id="faebe-166">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Aplicación Movie abierta en Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="faebe-168">Actualización del código generado</span><span class="sxs-lookup"><span data-stu-id="faebe-168">Update the generated code</span></span>

<span data-ttu-id="faebe-169">Abra el archivo *Models/Movie.cs* y agregue las líneas resaltadas mostradas en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="faebe-169">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="faebe-170">La anotación de datos `[Column(TypeName = "decimal(18, 2)")]` permite que Entity Framework Core asigne correctamente `Price` a la moneda en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="faebe-170">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="faebe-171">Para más información, vea [Tipos de datos](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="faebe-171">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="faebe-172">En el próximo tutorial, hablaremos de [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6).</span><span class="sxs-lookup"><span data-stu-id="faebe-172">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="faebe-173">El atributo [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) especifica qué se muestra como nombre de un campo (en este caso, "Release Date" en lugar de "ReleaseDate").</span><span class="sxs-lookup"><span data-stu-id="faebe-173">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="faebe-174">El atributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) especifica el tipo de los datos (Date), así que la información de hora almacenada en el campo no se muestra.</span><span class="sxs-lookup"><span data-stu-id="faebe-174">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="faebe-175">Vaya a Pages/Movies y mantenga el mouse sobre un vínculo de **edición** para ver la dirección URL de destino.</span><span class="sxs-lookup"><span data-stu-id="faebe-175">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Ventana del explorador con el mouse sobre el vínculo Edit (Editar) donde se muestra una dirección URL de vínculo http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="faebe-177">Los vínculos **Edit**, **Details** y **Delete** son generados por el [asistente de etiquetas de delimitador](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) del archivo *Pages/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="faebe-177">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="faebe-178">Los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="faebe-178">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="faebe-179">En el código anterior, `AnchorTagHelper` genera de forma dinámica el valor del atributo `href` HTML desde la página de Razor (la ruta es relativa), el elemento `asp-page` y el identificador de ruta (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="faebe-179">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="faebe-180">Vea [Generación de direcciones URL para las páginas](xref:razor-pages/index#url-generation-for-pages) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="faebe-180">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="faebe-181">Use **Ver código fuente** en su explorador preferido para examinar el marcado generado.</span><span class="sxs-lookup"><span data-stu-id="faebe-181">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="faebe-182">A continuación se muestra una parte del HTML generado:</span><span class="sxs-lookup"><span data-stu-id="faebe-182">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="faebe-183">Los vínculos generados de forma dinámica pasan el identificador de la película con una cadena de consulta (por ejemplo, el `?id=1` de `https://localhost:5001/Movies/Details?id=1`).</span><span class="sxs-lookup"><span data-stu-id="faebe-183">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

<span data-ttu-id="faebe-184">Actualice las páginas de edición, detalles y eliminación de Razor para usar la plantilla de ruta "{id:int}".</span><span class="sxs-lookup"><span data-stu-id="faebe-184">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="faebe-185">Cambie la directiva de página de cada una de estas páginas de `@page` a `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="faebe-185">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="faebe-186">Ejecute la aplicación y luego vea el origen.</span><span class="sxs-lookup"><span data-stu-id="faebe-186">Run the app and then view source.</span></span> <span data-ttu-id="faebe-187">El HTML generado agrega el identificador a la parte de la ruta de acceso de la dirección URL:</span><span class="sxs-lookup"><span data-stu-id="faebe-187">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="faebe-188">Una solicitud a la página con la plantilla de ruta "{id:int}" que **no** incluya el entero devolverá un error HTTP 404 (no encontrado).</span><span class="sxs-lookup"><span data-stu-id="faebe-188">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="faebe-189">Por ejemplo, `http://localhost:5000/Movies/Details` devolverá un error 404.</span><span class="sxs-lookup"><span data-stu-id="faebe-189">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="faebe-190">Para que el identificador sea opcional, anexe `?` a la restricción de ruta:</span><span class="sxs-lookup"><span data-stu-id="faebe-190">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="faebe-191">Para probar el comportamiento de `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="faebe-191">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="faebe-192">Establezca la directiva de página de *Pages/Movies/Details.cshtml* en `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="faebe-192">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="faebe-193">Establezca un punto de interrupción en `public async Task<IActionResult> OnGetAsync(int? id)` (en *Pages/Movies/Details.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="faebe-193">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="faebe-194">Navegue a `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="faebe-194">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="faebe-195">Con la directiva `@page "{id:int}"`, el punto de interrupción nunca se alcanza.</span><span class="sxs-lookup"><span data-stu-id="faebe-195">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="faebe-196">El motor de enrutamiento devuelve HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="faebe-196">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="faebe-197">Con `@page "{id:int?}"`, el método `OnGetAsync` devuelve `NotFound` (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="faebe-197">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="faebe-198">Revisión del control de excepciones de simultaneidad</span><span class="sxs-lookup"><span data-stu-id="faebe-198">Review concurrency exception handling</span></span>

<span data-ttu-id="faebe-199">Revise el método `OnPostAsync` en el archivo *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="faebe-199">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="faebe-200">El código anterior detecta las excepciones de simultaneidad cuando el cliente uno elimina la película y el otro cliente publica cambios en ella.</span><span class="sxs-lookup"><span data-stu-id="faebe-200">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="faebe-201">Para probar el bloque `catch`:</span><span class="sxs-lookup"><span data-stu-id="faebe-201">To test the `catch` block:</span></span>

* <span data-ttu-id="faebe-202">Establecer un punto de interrupción en `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="faebe-202">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="faebe-203">Seleccione **Editar** para una película y realice cambios, pero no seleccione **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="faebe-203">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="faebe-204">En otra ventana del explorador, seleccione el vínculo de **eliminación** de la misma película y luego elimínela.</span><span class="sxs-lookup"><span data-stu-id="faebe-204">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="faebe-205">En la ventana anterior del explorador, publique los cambios en la película.</span><span class="sxs-lookup"><span data-stu-id="faebe-205">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="faebe-206">Es posible que el código de producción quiera detectar conflictos de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="faebe-206">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="faebe-207">Vea [Administración de conflictos de simultaneidad](xref:data/ef-rp/concurrency) para más información.</span><span class="sxs-lookup"><span data-stu-id="faebe-207">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="faebe-208">Revisión de publicaciones y enlaces</span><span class="sxs-lookup"><span data-stu-id="faebe-208">Posting and binding review</span></span>

<span data-ttu-id="faebe-209">Examine el archivo *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="faebe-209">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="faebe-210">Cuando se realiza una solicitud HTTP GET a la página Movies/Edit (por ejemplo, `http://localhost:5000/Movies/Edit/2`):</span><span class="sxs-lookup"><span data-stu-id="faebe-210">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="faebe-211">El método `OnGetAsync` obtiene la película en la base de datos y devuelve el método `Page`.</span><span class="sxs-lookup"><span data-stu-id="faebe-211">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="faebe-212">El método `Page` presenta la página de Razor *Pages/Movies/Edit.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="faebe-212">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="faebe-213">El archivo *Pages/Movies/Edit.cshtml* contiene la directiva de modelo (`@model RazorPagesMovie.Pages.Movies.EditModel`), que hace que el modelo de película esté disponible en la página.</span><span class="sxs-lookup"><span data-stu-id="faebe-213">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="faebe-214">Se abre el formulario de edición con los valores de la película.</span><span class="sxs-lookup"><span data-stu-id="faebe-214">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="faebe-215">Cuando se publica la página Movies/Edit:</span><span class="sxs-lookup"><span data-stu-id="faebe-215">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="faebe-216">Los valores del formulario de la página se enlazan a la propiedad `Movie`.</span><span class="sxs-lookup"><span data-stu-id="faebe-216">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="faebe-217">El atributo `[BindProperty]` habilita el [enlace de modelos](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="faebe-217">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="faebe-218">Si hay errores en el estado del modelo (por ejemplo, `ReleaseDate` no se puede convertir en una fecha), el formulario se muestra con los valores enviados.</span><span class="sxs-lookup"><span data-stu-id="faebe-218">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="faebe-219">Si no hay ningún error en el modelo, se guarda la película.</span><span class="sxs-lookup"><span data-stu-id="faebe-219">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="faebe-220">Los métodos HTTP GET de las páginas de índice, creación y eliminación de Razor siguen un patrón similar.</span><span class="sxs-lookup"><span data-stu-id="faebe-220">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="faebe-221">El método HTTP POST `OnPostAsync` de la página de creación de Razor sigue un patrón similar al del método `OnPostAsync` de la página de edición de Razor.</span><span class="sxs-lookup"><span data-stu-id="faebe-221">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="faebe-222">La búsqueda se incluye en el tutorial siguiente.</span><span class="sxs-lookup"><span data-stu-id="faebe-222">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="faebe-223">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="faebe-223">Additional resources</span></span>

* [<span data-ttu-id="faebe-224">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="faebe-224">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="faebe-225">[Anterior: Trabajo con una base de datos](xref:tutorials/razor-pages/sql)
> [Siguiente: Adición de búsqueda](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="faebe-225">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
