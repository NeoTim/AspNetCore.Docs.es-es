---
title: 'Páginas de Razor con EF Core en ASP.NET Core: Modelo de datos (5 de 8)'
author: tdykstra
description: En este tutorial agregará más entidades y relaciones, y personalizará el modelo de datos especificando reglas de formato, validación y asignación.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 34b977f70f3e7e58e4ab6fcf3d8f69800896a65d
ms.sourcegitcommit: 0774a61a3a6c1412a7da0e7d932dc60c506441fc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2019
ms.locfileid: "70059119"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a><span data-ttu-id="4d34f-103">Páginas de Razor con EF Core en ASP.NET Core: Modelo de datos (5 de 8)</span><span class="sxs-lookup"><span data-stu-id="4d34f-103">Razor Pages with EF Core in ASP.NET Core - Data Model - 5 of 8</span></span>

<span data-ttu-id="4d34f-104">Por [Tom Dykstra](https://github.com/tdykstra) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4d34f-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4d34f-105">En los tutoriales anteriores se trabajaba con un modelo de datos básico que se componía de tres entidades.</span><span class="sxs-lookup"><span data-stu-id="4d34f-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="4d34f-106">En este tutorial:</span><span class="sxs-lookup"><span data-stu-id="4d34f-106">In this tutorial:</span></span>

* <span data-ttu-id="4d34f-107">Se agregan más entidades y relaciones.</span><span class="sxs-lookup"><span data-stu-id="4d34f-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="4d34f-108">Se personaliza el modelo de datos especificando el formato, la validación y las reglas de asignación de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="4d34f-109">El modelo de datos completo se muestra en la ilustración siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-109">The completed data model is shown in the following illustration:</span></span>

![Diagrama de entidades](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="4d34f-111">La entidad Student</span><span class="sxs-lookup"><span data-stu-id="4d34f-111">The Student entity</span></span>

![Entidad Student](complex-data-model/_static/student-entity.png)

<span data-ttu-id="4d34f-113">Reemplace el código de *Models/Student.cs* por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-113">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="4d34f-114">En el código anterior se agrega una propiedad `FullName` y los atributos siguientes a las propiedades existentes:</span><span class="sxs-lookup"><span data-stu-id="4d34f-114">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="4d34f-115">La propiedad calculada FullName</span><span class="sxs-lookup"><span data-stu-id="4d34f-115">The FullName calculated property</span></span>

<span data-ttu-id="4d34f-116">`FullName` es una propiedad calculada que devuelve un valor que se crea mediante la concatenación de otras dos propiedades.</span><span class="sxs-lookup"><span data-stu-id="4d34f-116">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="4d34f-117">No se puede establecer `FullName`, por lo que solo tiene un descriptor de acceso get.</span><span class="sxs-lookup"><span data-stu-id="4d34f-117">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="4d34f-118">No se crea ninguna columna `FullName` en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-118">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="4d34f-119">El atributo DataType</span><span class="sxs-lookup"><span data-stu-id="4d34f-119">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="4d34f-120">Para las fechas de inscripción de alumnos, en todas las páginas se muestra actualmente la hora del día junto con la fecha, aunque solo es relevante la fecha.</span><span class="sxs-lookup"><span data-stu-id="4d34f-120">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="4d34f-121">Mediante los atributos de anotación de datos, puede realizar un cambio de código que fijará el formato de presentación en todas las páginas en la que se muestren los datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-121">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="4d34f-122">El atributo [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) especifica un tipo de datos más específico que el tipo intrínseco de base de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-122">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="4d34f-123">En este caso solo se debe mostrar la fecha, no la fecha y hora.</span><span class="sxs-lookup"><span data-stu-id="4d34f-123">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="4d34f-124">La [enumeración DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) proporciona muchos tipos de datos, como Date (Fecha), Time (Hora), PhoneNumber (Número de teléfono), Currency (Divisa), EmailAddress (Dirección de correo electrónico), etc. El atributo `DataType` también puede permitir que la aplicación proporcione automáticamente características específicas del tipo.</span><span class="sxs-lookup"><span data-stu-id="4d34f-124">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="4d34f-125">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="4d34f-125">For example:</span></span>

* <span data-ttu-id="4d34f-126">El vínculo `mailto:` se crea automáticamente para `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-126">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="4d34f-127">El selector de fecha se proporciona para `DataType.Date` en la mayoría de los exploradores.</span><span class="sxs-lookup"><span data-stu-id="4d34f-127">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="4d34f-128">El atributo `DataType` emite atributos `data-` de HTML 5 (se pronuncia "datos dash").</span><span class="sxs-lookup"><span data-stu-id="4d34f-128">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="4d34f-129">Los atributos `DataType` no proporcionan validación.</span><span class="sxs-lookup"><span data-stu-id="4d34f-129">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="4d34f-130">El atributo DisplayFormat</span><span class="sxs-lookup"><span data-stu-id="4d34f-130">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="4d34f-131">`DataType.Date` no especifica el formato de la fecha que se muestra.</span><span class="sxs-lookup"><span data-stu-id="4d34f-131">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="4d34f-132">De manera predeterminada, el campo de fecha se muestra según los formatos predeterminados basados en el elemento [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) del servidor.</span><span class="sxs-lookup"><span data-stu-id="4d34f-132">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="4d34f-133">El atributo `DisplayFormat` se usa para especificar el formato de fecha de forma explícita.</span><span class="sxs-lookup"><span data-stu-id="4d34f-133">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="4d34f-134">La configuración `ApplyFormatInEditMode` especifica que el formato también debe aplicarse a la interfaz de usuario de edición.</span><span class="sxs-lookup"><span data-stu-id="4d34f-134">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="4d34f-135">Algunos campos no deben usar `ApplyFormatInEditMode`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-135">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="4d34f-136">Por ejemplo, el símbolo de divisa generalmente no debe mostrarse en un cuadro de texto de edición.</span><span class="sxs-lookup"><span data-stu-id="4d34f-136">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="4d34f-137">El atributo `DisplayFormat` puede usarse por sí solo.</span><span class="sxs-lookup"><span data-stu-id="4d34f-137">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="4d34f-138">Normalmente se recomienda usar el atributo `DataType` con el atributo `DisplayFormat`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-138">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="4d34f-139">El atributo `DataType` transmite la semántica de los datos en lugar de cómo se representan en una pantalla.</span><span class="sxs-lookup"><span data-stu-id="4d34f-139">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="4d34f-140">El atributo `DataType` proporciona las siguientes ventajas que no están disponibles en `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="4d34f-140">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="4d34f-141">El explorador puede habilitar características de HTML5.</span><span class="sxs-lookup"><span data-stu-id="4d34f-141">The browser can enable HTML5 features.</span></span> <span data-ttu-id="4d34f-142">Por ejemplo, mostrar un control de calendario, el símbolo de divisa adecuado según la configuración regional, vínculos de correo electrónico y validación de entradas del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="4d34f-142">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="4d34f-143">De manera predeterminada, el explorador representa los datos con el formato correcto según la configuración regional.</span><span class="sxs-lookup"><span data-stu-id="4d34f-143">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="4d34f-144">Para obtener más información, vea la [documentación del asistente de etiquetas \<entrada&gt;](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="4d34f-144">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="4d34f-145">El atributo StringLength</span><span class="sxs-lookup"><span data-stu-id="4d34f-145">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="4d34f-146">Las reglas de validación de datos y los mensajes de error de validación se pueden especificar con atributos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="4d34f-147">El atributo [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) especifica la longitud mínima y máxima de caracteres que se permite en un campo de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="4d34f-148">En el código que se muestra se limitan los nombres a un máximo de 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="4d34f-148">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="4d34f-149">[Más adelante](#the-required-attribute) se muestra un ejemplo en el que se establece la longitud mínima de la cadena.</span><span class="sxs-lookup"><span data-stu-id="4d34f-149">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="4d34f-150">El atributo `StringLength` también proporciona validación del lado cliente y del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="4d34f-150">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="4d34f-151">El valor mínimo no influye en el esquema de base de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-151">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="4d34f-152">El atributo `StringLength` no impide que un usuario escriba un espacio en blanco para un nombre.</span><span class="sxs-lookup"><span data-stu-id="4d34f-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="4d34f-153">El atributo [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) se puede usar para aplicar restricciones a la entrada.</span><span class="sxs-lookup"><span data-stu-id="4d34f-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="4d34f-154">Por ejemplo, el código siguiente requiere que el primer carácter sea una letra mayúscula y el resto de caracteres sean alfabéticos:</span><span class="sxs-lookup"><span data-stu-id="4d34f-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4d34f-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d34f-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4d34f-156">En el **Explorador de objetos de SQL Server**, (SSOX) abra el diseñador de tablas de Student haciendo doble clic en la tabla **Student**.</span><span class="sxs-lookup"><span data-stu-id="4d34f-156">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabla de estudiantes en SSOX antes de las migraciones](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="4d34f-158">La imagen anterior muestra el esquema para la tabla `Student`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-158">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="4d34f-159">Los campos de nombre tienen el tipo `nvarchar(MAX)`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-159">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="4d34f-160">Cuando más adelante en este tutorial se cree y se aplique una migración, los campos de nombre se convierten en `nvarchar(50)` como resultado de los atributos de longitud de cadena.</span><span class="sxs-lookup"><span data-stu-id="4d34f-160">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4d34f-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d34f-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4d34f-162">En la herramienta SQLite, examine las definiciones de columna para la tabla `Student`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-162">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="4d34f-163">Los campos de nombre tienen el tipo `Text`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-163">The name fields have type `Text`.</span></span> <span data-ttu-id="4d34f-164">Observe que el primer campo de nombre se denomina `FirstMidName`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-164">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="4d34f-165">En la sección siguiente, cambiará el nombre de esa columna por `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-165">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="4d34f-166">El atributo Column</span><span class="sxs-lookup"><span data-stu-id="4d34f-166">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="4d34f-167">Los atributos pueden controlar cómo se asignan las clases y propiedades a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-167">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="4d34f-168">En el modelo `Student`, el atributo `Column` se usa para asignar el nombre de la propiedad `FirstMidName` a "FirstName" en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-168">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="4d34f-169">Cuando se crea la base de datos, los nombres de propiedad en el modelo se usan para los nombres de columna (excepto cuando se usa el atributo `Column`).</span><span class="sxs-lookup"><span data-stu-id="4d34f-169">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="4d34f-170">El modelo `Student` usa `FirstMidName` para el nombre de campo por la posibilidad de que el campo contenga también un segundo nombre.</span><span class="sxs-lookup"><span data-stu-id="4d34f-170">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="4d34f-171">Con el atributo `[Column]`, `Student.FirstMidName` en el modelo de datos se asigna a la columna `FirstName` de la tabla `Student`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-171">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="4d34f-172">La adición del atributo `Column` cambia el modelo de respaldo de `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="4d34f-173">El modelo que está haciendo la copia de seguridad de `SchoolContext` ya no coincide con la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="4d34f-174">Más adelante en este tutorial se agregará una migración para resolver esa discrepancia.</span><span class="sxs-lookup"><span data-stu-id="4d34f-174">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="4d34f-175">El atributo Required</span><span class="sxs-lookup"><span data-stu-id="4d34f-175">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="4d34f-176">El atributo `Required` hace que las propiedades de nombre sean campos obligatorios.</span><span class="sxs-lookup"><span data-stu-id="4d34f-176">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="4d34f-177">El atributo `Required` no es necesario para los tipos que no aceptan valores NULL, como los tipos de valor (por ejemplo `DateTime`, `int` y `double`).</span><span class="sxs-lookup"><span data-stu-id="4d34f-177">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="4d34f-178">Los tipos que no aceptan valores NULL se tratan automáticamente como campos obligatorios.</span><span class="sxs-lookup"><span data-stu-id="4d34f-178">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="4d34f-179">El atributo `Required` se debe usar con `MinimumLength` para que se aplique `MinimumLength`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-179">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

<span data-ttu-id="4d34f-180">`MinimumLength` y `Required` permiten que el espacio en blanco satisfaga la validación.</span><span class="sxs-lookup"><span data-stu-id="4d34f-180">`MinimumLength` and `Required` allow whitespace to satisfy the validation.</span></span> <span data-ttu-id="4d34f-181">Utilice el atributo `RegularExpression` para el control total sobre la cadena.</span><span class="sxs-lookup"><span data-stu-id="4d34f-181">Use the `RegularExpression` attribute for full control over the string.</span></span>

### <a name="the-display-attribute"></a><span data-ttu-id="4d34f-182">El atributo Display</span><span class="sxs-lookup"><span data-stu-id="4d34f-182">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="4d34f-183">El atributo `Display` especifica que el título de los cuadros de texto debe ser "First Name", "Last Name", "Full Name" y "Enrollment Date".</span><span class="sxs-lookup"><span data-stu-id="4d34f-183">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="4d34f-184">Los títulos predeterminados no tenían ningún espacio de división de palabras, por ejemplo "Lastname".</span><span class="sxs-lookup"><span data-stu-id="4d34f-184">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="4d34f-185">Crear una migración</span><span class="sxs-lookup"><span data-stu-id="4d34f-185">Create a migration</span></span>

<span data-ttu-id="4d34f-186">Ejecute la aplicación y vaya a la página Students.</span><span class="sxs-lookup"><span data-stu-id="4d34f-186">Run the app and go to the Students page.</span></span> <span data-ttu-id="4d34f-187">Se inicia una excepción.</span><span class="sxs-lookup"><span data-stu-id="4d34f-187">An exception is thrown.</span></span> <span data-ttu-id="4d34f-188">El atributo `[Column]` hace que EF espere encontrar una columna denominada `FirstName`, pero el nombre de la columna en la base de datos sigue siendo `FirstMidName`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-188">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4d34f-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d34f-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4d34f-190">El mensaje de error es similar al ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-190">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="4d34f-191">En la Consola del administrador de paquetes, escriba los comandos siguientes para crear una migración y actualizar la base de datos:</span><span class="sxs-lookup"><span data-stu-id="4d34f-191">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="4d34f-192">El primero de estos comandos genera el siguiente mensaje de advertencia:</span><span class="sxs-lookup"><span data-stu-id="4d34f-192">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="4d34f-193">La advertencia se genera porque los campos de nombre ahora están limitados a 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="4d34f-193">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="4d34f-194">Si un nombre en la base de datos tenía más de 50 caracteres, se perderían desde el 51 hasta el último carácter.</span><span class="sxs-lookup"><span data-stu-id="4d34f-194">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="4d34f-195">Abra la tabla de estudiantes en SSOX:</span><span class="sxs-lookup"><span data-stu-id="4d34f-195">Open the Student table in SSOX:</span></span>

  ![Tabla de estudiantes en SSOX después de las migraciones](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="4d34f-197">Antes de aplicar la migración, las columnas de nombre eran de tipo [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="4d34f-197">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="4d34f-198">Las columnas de nombre ahora son `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-198">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="4d34f-199">El nombre de columna ha cambiado de `FirstMidName` a `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-199">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4d34f-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d34f-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4d34f-201">El mensaje de error es similar al ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-201">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="4d34f-202">Abra una ventana de comandos en la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="4d34f-202">Open a command window in the project folder.</span></span> <span data-ttu-id="4d34f-203">Escriba los comandos siguientes para crear una migración y actualizar la base de datos:</span><span class="sxs-lookup"><span data-stu-id="4d34f-203">Enter the following commands to create a new migration and update the database:</span></span>

  ```console
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="4d34f-204">El comando de actualización de base de datos muestra un error similar al ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-204">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="4d34f-205">En este tutorial, la manera de solucionar este error consiste en eliminar y volver a crear la migración inicial.</span><span class="sxs-lookup"><span data-stu-id="4d34f-205">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="4d34f-206">Para obtener más información, vea la nota de advertencia de SQLite en la parte superior del [tutorial de migraciones](xref:data/ef-rp/migrations).</span><span class="sxs-lookup"><span data-stu-id="4d34f-206">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="4d34f-207">Elimine la carpeta *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="4d34f-207">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="4d34f-208">Ejecute los comandos siguientes para quitar la base de datos, crear una migración inicial y aplicarla:</span><span class="sxs-lookup"><span data-stu-id="4d34f-208">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```console
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="4d34f-209">Examine la tabla Student en la herramienta SQLite.</span><span class="sxs-lookup"><span data-stu-id="4d34f-209">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="4d34f-210">La columna que antes era FirstMidName ahora es FirstName.</span><span class="sxs-lookup"><span data-stu-id="4d34f-210">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="4d34f-211">Ejecute la aplicación y vaya a la página Students.</span><span class="sxs-lookup"><span data-stu-id="4d34f-211">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="4d34f-212">Observe que las horas no se escriben ni se muestran junto con las fechas.</span><span class="sxs-lookup"><span data-stu-id="4d34f-212">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="4d34f-213">Seleccione **Crear nuevo** e intente escribir un nombre de más de 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="4d34f-213">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="4d34f-214">En las secciones siguientes, la creación de la aplicación en algunas de las fases genera errores del compilador.</span><span class="sxs-lookup"><span data-stu-id="4d34f-214">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="4d34f-215">Las instrucciones especifican cuándo se debe compilar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4d34f-215">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="4d34f-216">La entidad Instructor</span><span class="sxs-lookup"><span data-stu-id="4d34f-216">The Instructor Entity</span></span>

![La entidad Instructor](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="4d34f-218">Cree *Models/Instructor.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-218">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="4d34f-219">En una sola línea puede haber varios atributos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-219">Multiple attributes can be on one line.</span></span> <span data-ttu-id="4d34f-220">Los atributos `HireDate` pudieron escribirse de la manera siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-220">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="4d34f-221">Propiedades de navegación</span><span class="sxs-lookup"><span data-stu-id="4d34f-221">Navigation properties</span></span>

<span data-ttu-id="4d34f-222">`CourseAssignments` y `OfficeAssignment` son propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="4d34f-222">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="4d34f-223">Un instructor puede impartir cualquier número de cursos, por lo que `CourseAssignments` se define como una colección.</span><span class="sxs-lookup"><span data-stu-id="4d34f-223">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="4d34f-224">Un instructor puede tener como máximo una oficina, por lo que la propiedad `OfficeAssignment` contiene una sola entidad `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-224">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="4d34f-225">`OfficeAssignment` es NULL si no se asigna ninguna oficina.</span><span class="sxs-lookup"><span data-stu-id="4d34f-225">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="4d34f-226">La entidad OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="4d34f-226">The OfficeAssignment entity</span></span>

![Entidad OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="4d34f-228">Cree *Models/OfficeAssignment.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-228">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="4d34f-229">El atributo Key</span><span class="sxs-lookup"><span data-stu-id="4d34f-229">The Key attribute</span></span>

<span data-ttu-id="4d34f-230">El atributo `[Key]` se usa para identificar una propiedad como la clave principal (PK) cuando el nombre de propiedad es diferente de classnameID o ID.</span><span class="sxs-lookup"><span data-stu-id="4d34f-230">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="4d34f-231">Hay una relación de uno a cero o uno entre las entidades `Instructor` y `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-231">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="4d34f-232">Solo existe una asignación de oficina en relación con el instructor a la que está asignada.</span><span class="sxs-lookup"><span data-stu-id="4d34f-232">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="4d34f-233">La clave principal de `OfficeAssignment` también es la clave externa (FK) para la entidad `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-233">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="4d34f-234">EF Core no puede reconocer automáticamente `InstructorID` como la clave principal de `OfficeAssignment` porque `InstructorID` no sigue la convención de nomenclatura de ID o classnameID.</span><span class="sxs-lookup"><span data-stu-id="4d34f-234">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="4d34f-235">Por tanto, se usa el atributo `Key` para identificar `InstructorID` como la clave principal:</span><span class="sxs-lookup"><span data-stu-id="4d34f-235">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="4d34f-236">De forma predeterminada, EF Core trata la clave como no generada por la base de datos porque la columna es para una relación de identificación.</span><span class="sxs-lookup"><span data-stu-id="4d34f-236">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="4d34f-237">La propiedad de navegación Instructor</span><span class="sxs-lookup"><span data-stu-id="4d34f-237">The Instructor navigation property</span></span>

<span data-ttu-id="4d34f-238">La propiedad de navegación `Instructor.OfficeAssignment` puede ser NULL porque es posible que no haya una fila `OfficeAssignment` para un instructor determinado.</span><span class="sxs-lookup"><span data-stu-id="4d34f-238">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="4d34f-239">Un instructor podría no tener una asignación de oficina.</span><span class="sxs-lookup"><span data-stu-id="4d34f-239">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="4d34f-240">La propiedad de navegación `OfficeAssignment.Instructor` siempre tendrá una entidad de instructor porque el tipo `InstructorID` de clave externa es `int`, un tipo de valor que no acepta valores NULL.</span><span class="sxs-lookup"><span data-stu-id="4d34f-240">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="4d34f-241">Una asignación de oficina no puede existir sin un instructor.</span><span class="sxs-lookup"><span data-stu-id="4d34f-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="4d34f-242">Cuando una entidad `Instructor` tiene una entidad `OfficeAssignment` relacionada, cada entidad tiene una referencia a la otra en su propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="4d34f-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="4d34f-243">La entidad Course</span><span class="sxs-lookup"><span data-stu-id="4d34f-243">The Course Entity</span></span>

![La entidad Course](complex-data-model/_static/course-entity.png)

<span data-ttu-id="4d34f-245">Actualice *Models/Course.cs* con el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="4d34f-245">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="4d34f-246">La entidad `Course` tiene una propiedad de clave externa (FK) `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-246">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="4d34f-247">`DepartmentID` apunta a la entidad relacionada `Department`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-247">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="4d34f-248">La entidad `Course` tiene una propiedad de navegación `Department`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-248">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="4d34f-249">EF Core no requiere una propiedad de clave externa para un modelo de datos cuando el modelo tiene una propiedad de navegación para una entidad relacionada.</span><span class="sxs-lookup"><span data-stu-id="4d34f-249">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="4d34f-250">EF Core crea automáticamente claves externas en la base de datos siempre que se necesiten.</span><span class="sxs-lookup"><span data-stu-id="4d34f-250">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="4d34f-251">EF Core crea [propiedades paralelas](/ef/core/modeling/shadow-properties) para las claves externas creadas automáticamente.</span><span class="sxs-lookup"><span data-stu-id="4d34f-251">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="4d34f-252">Pero la inclusión explícita de la clave externa en el modelo de datos puede hacer que las actualizaciones sean más sencillas y eficaces.</span><span class="sxs-lookup"><span data-stu-id="4d34f-252">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="4d34f-253">Por ejemplo, considere la posibilidad de un modelo donde la propiedad de la clave externa `DepartmentID` *no* está incluida.</span><span class="sxs-lookup"><span data-stu-id="4d34f-253">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="4d34f-254">Cuando se captura una entidad de curso para editar:</span><span class="sxs-lookup"><span data-stu-id="4d34f-254">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="4d34f-255">La propiedad `Department` es NULL si no se carga de forma explícita.</span><span class="sxs-lookup"><span data-stu-id="4d34f-255">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="4d34f-256">Para actualizar la entidad Course, la entidad `Department` debe capturarse en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="4d34f-256">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="4d34f-257">Cuando se incluye la propiedad de clave externa `DepartmentID` en el modelo de datos, no es necesario capturar la entidad `Department` antes de una actualización.</span><span class="sxs-lookup"><span data-stu-id="4d34f-257">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="4d34f-258">El atributo DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="4d34f-258">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="4d34f-259">El atributo `[DatabaseGenerated(DatabaseGeneratedOption.None)]` especifica que la aplicación proporciona la clave principal, en lugar de generarla la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-259">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="4d34f-260">De forma predeterminada, EF Core asume que la base de datos genera valores de clave principal.</span><span class="sxs-lookup"><span data-stu-id="4d34f-260">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="4d34f-261">La generación por parte de la base de datos suele ser el mejor enfoque.</span><span class="sxs-lookup"><span data-stu-id="4d34f-261">Database-generated is generally the best approach.</span></span> <span data-ttu-id="4d34f-262">Para las entidades `Course`, el usuario especifica la clave principal.</span><span class="sxs-lookup"><span data-stu-id="4d34f-262">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="4d34f-263">Por ejemplo, un número de curso como una serie de 1000 para el departamento de matemáticas, una serie de 2000 para el departamento de inglés.</span><span class="sxs-lookup"><span data-stu-id="4d34f-263">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="4d34f-264">También se puede usar el atributo `DatabaseGenerated` para generar valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="4d34f-264">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="4d34f-265">Por ejemplo, la base de datos puede generar de forma automática un campo de fecha para registrar la fecha en que se crea o actualiza una fila.</span><span class="sxs-lookup"><span data-stu-id="4d34f-265">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="4d34f-266">Para obtener más información, vea [Propiedades generadas](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="4d34f-266">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="4d34f-267">Propiedades de clave externa y de navegación</span><span class="sxs-lookup"><span data-stu-id="4d34f-267">Foreign key and navigation properties</span></span>

<span data-ttu-id="4d34f-268">Las propiedades de clave externa (FK) y las de navegación de la entidad `Course` reflejan las relaciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="4d34f-268">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="4d34f-269">Un curso se asigna a un departamento, por lo que hay una clave externa `DepartmentID` y una propiedad de navegación `Department`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-269">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="4d34f-270">Un curso puede tener cualquier número de alumnos inscritos en él, por lo que la propiedad de navegación `Enrollments` es una colección:</span><span class="sxs-lookup"><span data-stu-id="4d34f-270">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="4d34f-271">Un curso puede ser impartido por varios instructores, por lo que la propiedad de navegación `CourseAssignments` es una colección:</span><span class="sxs-lookup"><span data-stu-id="4d34f-271">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="4d34f-272">`CourseAssignment` se explica [más adelante](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="4d34f-272">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="4d34f-273">La entidad Department</span><span class="sxs-lookup"><span data-stu-id="4d34f-273">The Department entity</span></span>

![La entidad Department](complex-data-model/_static/department-entity.png)

<span data-ttu-id="4d34f-275">Cree *Models/Department.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-275">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="4d34f-276">El atributo Column</span><span class="sxs-lookup"><span data-stu-id="4d34f-276">The Column attribute</span></span>

<span data-ttu-id="4d34f-277">Anteriormente se usó el atributo `Column` para cambiar la asignación de nombres de columna.</span><span class="sxs-lookup"><span data-stu-id="4d34f-277">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="4d34f-278">En el código de la entidad `Department`, se usó el atributo `Column` para cambiar la asignación de tipos de datos de SQL.</span><span class="sxs-lookup"><span data-stu-id="4d34f-278">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="4d34f-279">La columna `Budget` se define mediante el tipo money de SQL Server en la base de datos:</span><span class="sxs-lookup"><span data-stu-id="4d34f-279">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="4d34f-280">Por lo general, la asignación de columnas no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="4d34f-280">Column mapping is generally not required.</span></span> <span data-ttu-id="4d34f-281">EF Core elige el tipo de datos de SQL Server apropiado en función del tipo CLR para la propiedad.</span><span class="sxs-lookup"><span data-stu-id="4d34f-281">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="4d34f-282">El tipo CLR `decimal` se asigna a un tipo `decimal` de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="4d34f-282">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="4d34f-283">`Budget` es para la divisa, y el tipo de datos money es más adecuado para la divisa.</span><span class="sxs-lookup"><span data-stu-id="4d34f-283">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="4d34f-284">Propiedades de clave externa y de navegación</span><span class="sxs-lookup"><span data-stu-id="4d34f-284">Foreign key and navigation properties</span></span>

<span data-ttu-id="4d34f-285">Las propiedades de clave externa y de navegación reflejan las relaciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="4d34f-285">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="4d34f-286">Un departamento puede tener o no un administrador.</span><span class="sxs-lookup"><span data-stu-id="4d34f-286">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="4d34f-287">Un administrador siempre es un instructor.</span><span class="sxs-lookup"><span data-stu-id="4d34f-287">An administrator is always an instructor.</span></span> <span data-ttu-id="4d34f-288">Por lo tanto, la propiedad `InstructorID` se incluye como la clave externa para la entidad `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-288">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="4d34f-289">La propiedad de navegación se denomina `Administrator` pero contiene una entidad `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="4d34f-289">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="4d34f-290">El signo de interrogación (?) en el código anterior especifica que la propiedad acepta valores NULL.</span><span class="sxs-lookup"><span data-stu-id="4d34f-290">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="4d34f-291">Un departamento puede tener varios cursos, por lo que hay una propiedad de navegación Courses:</span><span class="sxs-lookup"><span data-stu-id="4d34f-291">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="4d34f-292">Por convención, EF Core permite la eliminación en cascada de las claves externas que no acepten valores NULL ni relaciones de varios a varios.</span><span class="sxs-lookup"><span data-stu-id="4d34f-292">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="4d34f-293">Este comportamiento predeterminado puede dar lugar a reglas de eliminación en cascada circular.</span><span class="sxs-lookup"><span data-stu-id="4d34f-293">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="4d34f-294">Las reglas de eliminación en cascada circular inician una excepción cuando se agrega una migración.</span><span class="sxs-lookup"><span data-stu-id="4d34f-294">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="4d34f-295">Por ejemplo, si la propiedad `Department.InstructorID` se ha definido como que no acepta valores NULL, EF Core configurará una regla de eliminación en cascada.</span><span class="sxs-lookup"><span data-stu-id="4d34f-295">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="4d34f-296">En ese caso, el departamento se eliminará cuando se elimine el instructor asignado como administrador.</span><span class="sxs-lookup"><span data-stu-id="4d34f-296">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="4d34f-297">En este escenario, una regla de restricción tendría más sentido.</span><span class="sxs-lookup"><span data-stu-id="4d34f-297">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="4d34f-298">La API fluida siguiente establecería una regla de restricción y deshabilitaría la eliminación en cascada.</span><span class="sxs-lookup"><span data-stu-id="4d34f-298">The following fluent API would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="4d34f-299">La entidad Enrollment</span><span class="sxs-lookup"><span data-stu-id="4d34f-299">The Enrollment entity</span></span>

<span data-ttu-id="4d34f-300">Un registro de inscripción corresponde a un curso realizado por un alumno.</span><span class="sxs-lookup"><span data-stu-id="4d34f-300">An enrollment record is for one course taken by one student.</span></span>

![La entidad Enrollment](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="4d34f-302">Actualice *Models/Enrollment.cs* con el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="4d34f-302">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="4d34f-303">Propiedades de clave externa y de navegación</span><span class="sxs-lookup"><span data-stu-id="4d34f-303">Foreign key and navigation properties</span></span>

<span data-ttu-id="4d34f-304">Las propiedades de clave externa y de navegación reflejan las relaciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="4d34f-304">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="4d34f-305">Un registro de inscripción es para un curso, por lo que hay una propiedad de clave externa `CourseID` y una propiedad de navegación `Course`:</span><span class="sxs-lookup"><span data-stu-id="4d34f-305">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="4d34f-306">Un registro de inscripción es para un alumno, por lo que hay una propiedad de clave externa `StudentID` y una propiedad de navegación `Student`:</span><span class="sxs-lookup"><span data-stu-id="4d34f-306">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="4d34f-307">Relaciones Varios a Varios</span><span class="sxs-lookup"><span data-stu-id="4d34f-307">Many-to-Many Relationships</span></span>

<span data-ttu-id="4d34f-308">Hay una relación de varios a varios entre las entidades `Student` y `Course`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-308">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="4d34f-309">La entidad `Enrollment` funciona como una tabla combinada varios a varios *con carga útil* en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-309">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="4d34f-310">"Con carga útil" significa que la tabla `Enrollment` contiene datos adicionales, además de claves externas de las tablas combinadas (en este caso, la clave principal y `Grade`).</span><span class="sxs-lookup"><span data-stu-id="4d34f-310">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="4d34f-311">En la ilustración siguiente se muestra el aspecto de estas relaciones en un diagrama de entidades.</span><span class="sxs-lookup"><span data-stu-id="4d34f-311">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="4d34f-312">(Este diagrama se ha generado mediante [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) para EF 6.x.</span><span class="sxs-lookup"><span data-stu-id="4d34f-312">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="4d34f-313">Crear el diagrama no forma parte del tutorial).</span><span class="sxs-lookup"><span data-stu-id="4d34f-313">Creating the diagram isn't part of the tutorial.)</span></span>

![Relación de varios a varios entre estudiantes y cursos](complex-data-model/_static/student-course.png)

<span data-ttu-id="4d34f-315">Cada línea de relación tiene un 1 en un extremo y un asterisco (\*) en el otro, para indicar una relación uno a varios.</span><span class="sxs-lookup"><span data-stu-id="4d34f-315">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="4d34f-316">Si la tabla `Enrollment` no incluyera información de calificaciones, solo tendría que contener las dos claves externas (`CourseID` y `StudentID`).</span><span class="sxs-lookup"><span data-stu-id="4d34f-316">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="4d34f-317">Una tabla combinada de varios a varios sin carga útil se suele denominar una tabla combinada pura (PJT).</span><span class="sxs-lookup"><span data-stu-id="4d34f-317">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="4d34f-318">Las entidades `Instructor` y `Course` tienen una relación de varios a varios con una tabla combinada pura.</span><span class="sxs-lookup"><span data-stu-id="4d34f-318">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="4d34f-319">Nota: EF 6.x es compatible con las tablas de combinación implícitas con relaciones de varios a varios, pero EF Core, no.</span><span class="sxs-lookup"><span data-stu-id="4d34f-319">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="4d34f-320">Para obtener más información, consulte [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/) (Relaciones de varios a varios en EF Core 2.0).</span><span class="sxs-lookup"><span data-stu-id="4d34f-320">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="4d34f-321">La entidad CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="4d34f-321">The CourseAssignment entity</span></span>

![La entidad CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="4d34f-323">Cree *Models/CourseAssignment.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-323">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="4d34f-324">La relación de varios a varios entre instructores y cursos requiere una tabla de combinación y la entidad para esa tabla de combinación es CourseAssignment.</span><span class="sxs-lookup"><span data-stu-id="4d34f-324">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![Relación de varios a varios Instructor-to-Courses](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="4d34f-326">Es común denominar una entidad de combinación `EntityName1EntityName2`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-326">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="4d34f-327">Por ejemplo, la tabla de combinación Instructor-to-Courses con este patrón sería `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-327">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="4d34f-328">Pero se recomienda usar un nombre que describa la relación.</span><span class="sxs-lookup"><span data-stu-id="4d34f-328">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="4d34f-329">Los modelos de datos empiezan de manera sencilla y crecen.</span><span class="sxs-lookup"><span data-stu-id="4d34f-329">Data models start out simple and grow.</span></span> <span data-ttu-id="4d34f-330">Las tablas combinadas sin carga útil (PJT) evolucionan con frecuencia para incluir la carga útil.</span><span class="sxs-lookup"><span data-stu-id="4d34f-330">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="4d34f-331">A partir de un nombre de entidad descriptivo, no es necesario cambiar el nombre cuando la tabla combinada cambia.</span><span class="sxs-lookup"><span data-stu-id="4d34f-331">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="4d34f-332">Idealmente, la entidad de combinación tendrá su propio nombre natural (posiblemente una sola palabra) en el dominio de empresa.</span><span class="sxs-lookup"><span data-stu-id="4d34f-332">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="4d34f-333">Por ejemplo, Books y Customers podrían vincularse a través de una entidad combinada denominada Ratings.</span><span class="sxs-lookup"><span data-stu-id="4d34f-333">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="4d34f-334">Para la relación de varios a varios Instructor-to-Courses, se prefiere `CourseAssignment` a `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-334">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="4d34f-335">Clave compuesta</span><span class="sxs-lookup"><span data-stu-id="4d34f-335">Composite key</span></span>

<span data-ttu-id="4d34f-336">Las dos claves externas en `CourseAssignment` (`InstructorID` y `CourseID`) juntas identifican de forma única cada fila de la tabla `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-336">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="4d34f-337">`CourseAssignment` no requiere una clave principal dedicada.</span><span class="sxs-lookup"><span data-stu-id="4d34f-337">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="4d34f-338">Las propiedades `InstructorID` y `CourseID` funcionan como una clave principal compuesta.</span><span class="sxs-lookup"><span data-stu-id="4d34f-338">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="4d34f-339">La única manera de especificar claves principales compuestas en EF Core es con la *API fluida*.</span><span class="sxs-lookup"><span data-stu-id="4d34f-339">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="4d34f-340">La sección siguiente muestra cómo configurar la clave principal compuesta.</span><span class="sxs-lookup"><span data-stu-id="4d34f-340">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="4d34f-341">La clave compuesta asegura que:</span><span class="sxs-lookup"><span data-stu-id="4d34f-341">The composite key ensures that:</span></span>

* <span data-ttu-id="4d34f-342">Se permiten varias filas para un curso.</span><span class="sxs-lookup"><span data-stu-id="4d34f-342">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="4d34f-343">Se permiten varias filas para un instructor.</span><span class="sxs-lookup"><span data-stu-id="4d34f-343">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="4d34f-344">No se permiten varias filas para el mismo instructor y curso.</span><span class="sxs-lookup"><span data-stu-id="4d34f-344">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="4d34f-345">La entidad de combinación `Enrollment` define su propia clave principal, por lo que este tipo de duplicados son posibles.</span><span class="sxs-lookup"><span data-stu-id="4d34f-345">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="4d34f-346">Para evitar los duplicados:</span><span class="sxs-lookup"><span data-stu-id="4d34f-346">To prevent such duplicates:</span></span>

* <span data-ttu-id="4d34f-347">Agregue un índice único en los campos de clave externa, o</span><span class="sxs-lookup"><span data-stu-id="4d34f-347">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="4d34f-348">Configure `Enrollment` con una clave compuesta principal similar a `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-348">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="4d34f-349">Para obtener más información, vea [Índices](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="4d34f-349">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="4d34f-350">Actualizar el contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="4d34f-350">Update the database context</span></span>

<span data-ttu-id="4d34f-351">Actualice *Data/SchoolContext.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-351">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="4d34f-352">El código anterior agrega las nuevas entidades y configura la clave principal compuesta de la entidad `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-352">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="4d34f-353">Alternativa de la API fluida a los atributos</span><span class="sxs-lookup"><span data-stu-id="4d34f-353">Fluent API alternative to attributes</span></span>

<span data-ttu-id="4d34f-354">El método `OnModelCreating` del código anterior usa la *API fluida* para configurar el comportamiento de EF Core.</span><span class="sxs-lookup"><span data-stu-id="4d34f-354">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="4d34f-355">La API se denomina "fluida" porque a menudo se usa para encadenar una serie de llamadas de método en una única instrucción.</span><span class="sxs-lookup"><span data-stu-id="4d34f-355">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="4d34f-356">El [código siguiente](/ef/core/modeling/#use-fluent-api-to-configure-a-model) es un ejemplo de la API fluida:</span><span class="sxs-lookup"><span data-stu-id="4d34f-356">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="4d34f-357">En este tutorial, la API fluida solo se usa para la asignación de base de datos que no se puede realizar con atributos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-357">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="4d34f-358">Pero la API fluida puede especificar casi todas las reglas de formato, validación y asignación que se pueden realizar mediante el uso de atributos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-358">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="4d34f-359">Algunos atributos como `MinimumLength` no se pueden aplicar con la API fluida.</span><span class="sxs-lookup"><span data-stu-id="4d34f-359">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="4d34f-360">`MinimumLength` no cambia el esquema, solo aplica una regla de validación de longitud mínima.</span><span class="sxs-lookup"><span data-stu-id="4d34f-360">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="4d34f-361">Algunos desarrolladores prefieren usar la API fluida exclusivamente para mantener "limpias" las clases de entidad.</span><span class="sxs-lookup"><span data-stu-id="4d34f-361">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="4d34f-362">Se pueden mezclar atributos y la API fluida.</span><span class="sxs-lookup"><span data-stu-id="4d34f-362">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="4d34f-363">Hay algunas configuraciones que solo se pueden realizar con la API fluida (especificando una clave principal compuesta).</span><span class="sxs-lookup"><span data-stu-id="4d34f-363">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="4d34f-364">Hay algunas configuraciones que solo se pueden realizar con atributos (`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="4d34f-364">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="4d34f-365">La práctica recomendada para el uso de atributos o API fluida:</span><span class="sxs-lookup"><span data-stu-id="4d34f-365">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="4d34f-366">Elija uno de estos dos enfoques.</span><span class="sxs-lookup"><span data-stu-id="4d34f-366">Choose one of these two approaches.</span></span>
* <span data-ttu-id="4d34f-367">Use el enfoque elegido de forma tan coherente como sea posible.</span><span class="sxs-lookup"><span data-stu-id="4d34f-367">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="4d34f-368">Algunos de los atributos utilizados en este tutorial se usan para:</span><span class="sxs-lookup"><span data-stu-id="4d34f-368">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="4d34f-369">Solo validación (por ejemplo, `MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="4d34f-369">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="4d34f-370">Solo configuración de EF Core (por ejemplo, `HasKey`).</span><span class="sxs-lookup"><span data-stu-id="4d34f-370">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="4d34f-371">Validación y configuración de EF Core (por ejemplo, `[StringLength(50)]`).</span><span class="sxs-lookup"><span data-stu-id="4d34f-371">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="4d34f-372">Para obtener más información sobre la diferencia entre los atributos y la API fluida, vea [Métodos de configuración](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="4d34f-372">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="4d34f-373">Diagrama de entidades</span><span class="sxs-lookup"><span data-stu-id="4d34f-373">Entity diagram</span></span>

<span data-ttu-id="4d34f-374">En la siguiente ilustración se muestra el diagrama creado por EF Power Tools para el modelo School completado.</span><span class="sxs-lookup"><span data-stu-id="4d34f-374">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Diagrama de entidades](complex-data-model/_static/diagram.png)

<span data-ttu-id="4d34f-376">El diagrama anterior muestra:</span><span class="sxs-lookup"><span data-stu-id="4d34f-376">The preceding diagram shows:</span></span>

* <span data-ttu-id="4d34f-377">Varias líneas de relación uno a varios (1 a \*).</span><span class="sxs-lookup"><span data-stu-id="4d34f-377">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="4d34f-378">La línea de relación de uno a cero o uno (1 a 0..1) entre las entidades `Instructor` y `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-378">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="4d34f-379">La línea de relación de cero o uno o varios (0..1 a \*) entre las entidades `Instructor` y `Department`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-379">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="4d34f-380">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="4d34f-380">Seed the database</span></span>

<span data-ttu-id="4d34f-381">Actualice el código en *Data/DbInitializer.cs*:</span><span class="sxs-lookup"><span data-stu-id="4d34f-381">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="4d34f-382">El código anterior proporciona datos de inicialización para las nuevas entidades.</span><span class="sxs-lookup"><span data-stu-id="4d34f-382">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="4d34f-383">La mayor parte de este código crea objetos de entidad y carga los datos de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="4d34f-383">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="4d34f-384">Los datos de ejemplo se usan para pruebas.</span><span class="sxs-lookup"><span data-stu-id="4d34f-384">The sample data is used for testing.</span></span> <span data-ttu-id="4d34f-385">Consulte `Enrollments` y `CourseAssignments` para obtener ejemplos de cómo pueden inicializarse las tablas de combinación de varios a varios.</span><span class="sxs-lookup"><span data-stu-id="4d34f-385">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="4d34f-386">Agregar una migración</span><span class="sxs-lookup"><span data-stu-id="4d34f-386">Add a migration</span></span>

<span data-ttu-id="4d34f-387">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="4d34f-387">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4d34f-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d34f-388">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4d34f-389">En la Consola del administrador de paquetes, ejecute el comando siguiente.</span><span class="sxs-lookup"><span data-stu-id="4d34f-389">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="4d34f-390">El comando anterior muestra una advertencia sobre la posible pérdida de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-390">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="4d34f-391">Si se ejecuta el comando `database update`, se genera el error siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-391">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="4d34f-392">En la sección siguiente, verá qué puede hacer con respecto a este error.</span><span class="sxs-lookup"><span data-stu-id="4d34f-392">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4d34f-393">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d34f-393">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4d34f-394">Si agrega una migración y ejecuta el comando `database update`, se producirá el error siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-394">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="4d34f-395">En la sección siguiente, verá cómo evitar este error.</span><span class="sxs-lookup"><span data-stu-id="4d34f-395">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="4d34f-396">Aplicar la migración o quitar y volver a crear</span><span class="sxs-lookup"><span data-stu-id="4d34f-396">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="4d34f-397">Ahora que tiene una base de datos existente, debe pensar en cómo aplicarle cambios.</span><span class="sxs-lookup"><span data-stu-id="4d34f-397">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="4d34f-398">En este tutorial se muestran dos alternativas:</span><span class="sxs-lookup"><span data-stu-id="4d34f-398">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="4d34f-399">[Quitar y volver a crear la base de datos](#drop).</span><span class="sxs-lookup"><span data-stu-id="4d34f-399">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="4d34f-400">Elija esta sección si va a usar SQLite.</span><span class="sxs-lookup"><span data-stu-id="4d34f-400">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="4d34f-401">[Aplicar la migración a la base de datos existente](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="4d34f-401">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="4d34f-402">Las instrucciones de esta sección solo funcionan para SQL Server, **no para SQLite**.</span><span class="sxs-lookup"><span data-stu-id="4d34f-402">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="4d34f-403">Ambas opciones funcionan para SQL Server.</span><span class="sxs-lookup"><span data-stu-id="4d34f-403">Either choice works for SQL Server.</span></span> <span data-ttu-id="4d34f-404">Aunque el método de aplicación de la migración es más complejo y lento, es el enfoque preferido para entornos de producción del mundo real.</span><span class="sxs-lookup"><span data-stu-id="4d34f-404">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="4d34f-405">Quitar y volver a crear la base de datos</span><span class="sxs-lookup"><span data-stu-id="4d34f-405">Drop and re-create the database</span></span>

<span data-ttu-id="4d34f-406">[Omita esta sección](#apply-the-migration) si usa SQL Server y quiere realizar el enfoque de aplicación de la migración en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="4d34f-406">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="4d34f-407">Para obligar a EF Core a crear una base de datos, quite y actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="4d34f-407">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4d34f-408">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d34f-408">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4d34f-409">En la **Consola del Administrador de paquetes** (PMC), ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-409">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="4d34f-410">Elimine la carpeta *Migrations* y, después, ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-410">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4d34f-411">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d34f-411">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4d34f-412">Abra una ventana de comandos y desplácese hasta la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="4d34f-412">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="4d34f-413">La carpeta del proyecto contiene el archivo *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="4d34f-413">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="4d34f-414">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="4d34f-414">Run the following command:</span></span>

  ```console
  dotnet ef database drop --force
  ```

* <span data-ttu-id="4d34f-415">Elimine la carpeta *Migrations* y, después, ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-415">Delete the *Migrations* folder, then run the following command:</span></span>

  ```console
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="4d34f-416">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4d34f-416">Run the app.</span></span> <span data-ttu-id="4d34f-417">Ejecutar la aplicación ejecuta el método `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-417">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="4d34f-418">`DbInitializer.Initialize` rellena la base de datos nueva.</span><span class="sxs-lookup"><span data-stu-id="4d34f-418">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4d34f-419">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d34f-419">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4d34f-420">Abra la base de datos en SSOX:</span><span class="sxs-lookup"><span data-stu-id="4d34f-420">Open the database in SSOX:</span></span>

* <span data-ttu-id="4d34f-421">Si anteriormente se abrió SSOX, haga clic en el botón **Actualizar**.</span><span class="sxs-lookup"><span data-stu-id="4d34f-421">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="4d34f-422">Expanda el nodo **Tablas**.</span><span class="sxs-lookup"><span data-stu-id="4d34f-422">Expand the **Tables** node.</span></span> <span data-ttu-id="4d34f-423">Se muestran las tablas creadas.</span><span class="sxs-lookup"><span data-stu-id="4d34f-423">The created tables are displayed.</span></span>

  ![Tablas en SSOX](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="4d34f-425">Examine la tabla **CourseAssignment**:</span><span class="sxs-lookup"><span data-stu-id="4d34f-425">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="4d34f-426">Haga clic con el botón derecho en la tabla **CourseAssignment** y seleccione **Ver datos**.</span><span class="sxs-lookup"><span data-stu-id="4d34f-426">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="4d34f-427">Compruebe que la tabla **CourseAssignment** contiene datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-427">Verify the **CourseAssignment** table contains data.</span></span>

  ![Datos de CourseAssignment en SSOX](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4d34f-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d34f-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4d34f-430">Use la herramienta SQLite para examinar la base de datos:</span><span class="sxs-lookup"><span data-stu-id="4d34f-430">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="4d34f-431">Nuevas tablas y columnas.</span><span class="sxs-lookup"><span data-stu-id="4d34f-431">New tables and columns.</span></span>
* <span data-ttu-id="4d34f-432">Datos inicializados en tablas, por ejemplo, la tabla **CourseAssignment**.</span><span class="sxs-lookup"><span data-stu-id="4d34f-432">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="4d34f-433">Aplicar la migración</span><span class="sxs-lookup"><span data-stu-id="4d34f-433">Apply the migration</span></span>

<span data-ttu-id="4d34f-434">Esta sección es opcional.</span><span class="sxs-lookup"><span data-stu-id="4d34f-434">This section is optional.</span></span> <span data-ttu-id="4d34f-435">Estos pasos solo funcionan para SQL Server LocalDB y si ha pasado por alto la sección [Quitar y volver a crear la base de datos](#drop) anterior.</span><span class="sxs-lookup"><span data-stu-id="4d34f-435">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="4d34f-436">Cuando se ejecutan migraciones con datos existentes, puede haber restricciones de clave externa que no se cumplen con los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="4d34f-436">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="4d34f-437">Con los datos de producción, se deben realizar algunos pasos para migrar los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="4d34f-437">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="4d34f-438">En esta sección se proporciona un ejemplo de corrección de las infracciones de restricción de clave externa.</span><span class="sxs-lookup"><span data-stu-id="4d34f-438">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="4d34f-439">No realice estos cambios de código sin hacer una copia de seguridad.</span><span class="sxs-lookup"><span data-stu-id="4d34f-439">Don't make these code changes without a backup.</span></span> <span data-ttu-id="4d34f-440">No realice estos cambios de código si ha completado la sección anterior [Quitar y volver a crear la base de datos](#drop).</span><span class="sxs-lookup"><span data-stu-id="4d34f-440">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="4d34f-441">El archivo *{marca_de_tiempo}_ComplexDataModel.cs* contiene el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-441">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="4d34f-442">El código anterior agrega una clave externa `DepartmentID` que acepta valores NULL a la tabla `Course`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-442">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="4d34f-443">La base de datos del tutorial anterior contiene filas en `Course`, por lo que esa tabla no se puede actualizar mediante migraciones.</span><span class="sxs-lookup"><span data-stu-id="4d34f-443">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="4d34f-444">Para realizar la migración de `ComplexDataModel`, trabaje con los datos existentes:</span><span class="sxs-lookup"><span data-stu-id="4d34f-444">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="4d34f-445">Cambie el código para asignar a la nueva columna (`DepartmentID`) un valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="4d34f-445">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="4d34f-446">Cree un departamento falso denominado "Temp" para que actúe como el departamento predeterminado.</span><span class="sxs-lookup"><span data-stu-id="4d34f-446">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="4d34f-447">Corregir las restricciones de clave externa</span><span class="sxs-lookup"><span data-stu-id="4d34f-447">Fix the foreign key constraints</span></span>

<span data-ttu-id="4d34f-448">En la clase de migración `ComplexDataModel`, actualice el método `Up`:</span><span class="sxs-lookup"><span data-stu-id="4d34f-448">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="4d34f-449">Abra el archivo *{marca_de_tiempo}_ComplexDataModel.cs*.</span><span class="sxs-lookup"><span data-stu-id="4d34f-449">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="4d34f-450">Convierta en comentario la línea de código que agrega la columna `DepartmentID` a la tabla `Course`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-450">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="4d34f-451">Agregue el código resaltado siguiente.</span><span class="sxs-lookup"><span data-stu-id="4d34f-451">Add the following highlighted code.</span></span> <span data-ttu-id="4d34f-452">El nuevo código va después del bloque `.CreateTable( name: "Department"`:</span><span class="sxs-lookup"><span data-stu-id="4d34f-452">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

<span data-ttu-id="4d34f-453">[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]</span><span class="sxs-lookup"><span data-stu-id="4d34f-453">[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]</span></span>

<span data-ttu-id="4d34f-454">Con los cambios anteriores, las filas `Course` existentes estarán relacionadas con el departamento "Temp" después de ejecutar el método `ComplexDataModel.Up`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-454">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="4d34f-455">Para este tutorial se ha simplificado la manera de controlar la situación que se muestra aquí.</span><span class="sxs-lookup"><span data-stu-id="4d34f-455">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="4d34f-456">Una aplicación de producción debería:</span><span class="sxs-lookup"><span data-stu-id="4d34f-456">A production app would:</span></span>

* <span data-ttu-id="4d34f-457">Incluir código o scripts para agregar filas de `Department` y filas de `Course` relacionadas a las nuevas filas de `Department`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-457">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="4d34f-458">No use el departamento "Temp" o el valor predeterminado de `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-458">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4d34f-459">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d34f-459">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4d34f-460">En la **Consola del Administrador de paquetes** (PMC), ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-460">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="4d34f-461">Como el método `DbInitializer.Initialize` está diseñado para funcionar solo con una base de datos vacía, use SSOX para eliminar todas las filas de las tablas Student y Course.</span><span class="sxs-lookup"><span data-stu-id="4d34f-461">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="4d34f-462">(La eliminación en cascada se encargará de la tabla Enrollment).</span><span class="sxs-lookup"><span data-stu-id="4d34f-462">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4d34f-463">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d34f-463">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4d34f-464">Si usa SQL Server LocalDB con Visual Studio Code, ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-464">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```console
  dotnet ef database update
  ```

---

<span data-ttu-id="4d34f-465">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4d34f-465">Run the app.</span></span> <span data-ttu-id="4d34f-466">Ejecutar la aplicación ejecuta el método `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-466">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="4d34f-467">`DbInitializer.Initialize` rellena la base de datos nueva.</span><span class="sxs-lookup"><span data-stu-id="4d34f-467">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4d34f-468">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="4d34f-468">Next steps</span></span>

<span data-ttu-id="4d34f-469">En los dos tutoriales siguientes se muestra cómo leer y actualizar los datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="4d34f-469">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4d34f-470">[Tutorial anterior](xref:data/ef-rp/migrations)
> [Tutorial siguiente](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="4d34f-470">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4d34f-471">En los tutoriales anteriores se trabajaba con un modelo de datos básico que se componía de tres entidades.</span><span class="sxs-lookup"><span data-stu-id="4d34f-471">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="4d34f-472">En este tutorial:</span><span class="sxs-lookup"><span data-stu-id="4d34f-472">In this tutorial:</span></span>

* <span data-ttu-id="4d34f-473">Se agregan más entidades y relaciones.</span><span class="sxs-lookup"><span data-stu-id="4d34f-473">More entities and relationships are added.</span></span>
* <span data-ttu-id="4d34f-474">Se personaliza el modelo de datos especificando el formato, la validación y las reglas de asignación de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-474">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="4d34f-475">Las clases de entidad para el modelo de datos completado se muestran en la ilustración siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-475">The entity classes for the completed data model are shown in the following illustration:</span></span>

![Diagrama de entidades](complex-data-model/_static/diagram.png)

<span data-ttu-id="4d34f-477">Si experimenta problemas que no puede resolver, descargue la [aplicación completada](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="4d34f-477">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="4d34f-478">Personalizar el modelo de datos con atributos</span><span class="sxs-lookup"><span data-stu-id="4d34f-478">Customize the data model with attributes</span></span>

<span data-ttu-id="4d34f-479">En esta sección, se personaliza el modelo de datos mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-479">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="4d34f-480">El atributo DataType</span><span class="sxs-lookup"><span data-stu-id="4d34f-480">The DataType attribute</span></span>

<span data-ttu-id="4d34f-481">Las páginas de alumno actualmente muestran la hora de la fecha de inscripción.</span><span class="sxs-lookup"><span data-stu-id="4d34f-481">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="4d34f-482">Normalmente, los campos de fecha muestran solo la fecha y no la hora.</span><span class="sxs-lookup"><span data-stu-id="4d34f-482">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="4d34f-483">Actualice *Models/Student.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="4d34f-483">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="4d34f-484">El atributo [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) especifica un tipo de datos más específico que el tipo intrínseco de base de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-484">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="4d34f-485">En este caso solo se debe mostrar la fecha, no la fecha y hora.</span><span class="sxs-lookup"><span data-stu-id="4d34f-485">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="4d34f-486">La [enumeración DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) proporciona muchos tipos de datos, como Date (Fecha), Time (Hora), PhoneNumber (Número de teléfono), Currency (Divisa), EmailAddress (Dirección de correo electrónico), etc. El atributo `DataType` también puede permitir que la aplicación proporcione automáticamente características específicas del tipo.</span><span class="sxs-lookup"><span data-stu-id="4d34f-486">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="4d34f-487">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="4d34f-487">For example:</span></span>

* <span data-ttu-id="4d34f-488">El vínculo `mailto:` se crea automáticamente para `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-488">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="4d34f-489">El selector de fecha se proporciona para `DataType.Date` en la mayoría de los exploradores.</span><span class="sxs-lookup"><span data-stu-id="4d34f-489">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="4d34f-490">El atributo `DataType` emite atributos HTML 5 `data-` (se pronuncia "datos dash") para su uso por parte de los exploradores HTML 5.</span><span class="sxs-lookup"><span data-stu-id="4d34f-490">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="4d34f-491">Los atributos `DataType` no proporcionan validación.</span><span class="sxs-lookup"><span data-stu-id="4d34f-491">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="4d34f-492">`DataType.Date` no especifica el formato de la fecha que se muestra.</span><span class="sxs-lookup"><span data-stu-id="4d34f-492">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="4d34f-493">De manera predeterminada, el campo de fecha se muestra según los formatos predeterminados basados en el elemento [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) del servidor.</span><span class="sxs-lookup"><span data-stu-id="4d34f-493">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="4d34f-494">El atributo `DisplayFormat` se usa para especificar el formato de fecha de forma explícita:</span><span class="sxs-lookup"><span data-stu-id="4d34f-494">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="4d34f-495">La configuración `ApplyFormatInEditMode` especifica que el formato también debe aplicarse a la interfaz de usuario de edición.</span><span class="sxs-lookup"><span data-stu-id="4d34f-495">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="4d34f-496">Algunos campos no deben usar `ApplyFormatInEditMode`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-496">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="4d34f-497">Por ejemplo, el símbolo de divisa generalmente no debe mostrarse en un cuadro de texto de edición.</span><span class="sxs-lookup"><span data-stu-id="4d34f-497">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="4d34f-498">El atributo `DisplayFormat` puede usarse por sí solo.</span><span class="sxs-lookup"><span data-stu-id="4d34f-498">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="4d34f-499">Normalmente se recomienda usar el atributo `DataType` con el atributo `DisplayFormat`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-499">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="4d34f-500">El atributo `DataType` transmite la semántica de los datos en lugar de cómo se representan en una pantalla.</span><span class="sxs-lookup"><span data-stu-id="4d34f-500">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="4d34f-501">El atributo `DataType` proporciona las siguientes ventajas que no están disponibles en `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="4d34f-501">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="4d34f-502">El explorador puede habilitar características de HTML5.</span><span class="sxs-lookup"><span data-stu-id="4d34f-502">The browser can enable HTML5 features.</span></span> <span data-ttu-id="4d34f-503">Por ejemplo, mostrar un control de calendario, el símbolo de divisa adecuado según la configuración regional, vínculos de correo electrónico, validación de entradas del lado cliente, etc.</span><span class="sxs-lookup"><span data-stu-id="4d34f-503">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="4d34f-504">De manera predeterminada, el explorador representa los datos con el formato correcto según la configuración regional.</span><span class="sxs-lookup"><span data-stu-id="4d34f-504">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="4d34f-505">Para obtener más información, vea la [documentación del asistente de etiquetas \<entrada&gt;](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="4d34f-505">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="4d34f-506">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4d34f-506">Run the app.</span></span> <span data-ttu-id="4d34f-507">Vaya a la página de índice de Students.</span><span class="sxs-lookup"><span data-stu-id="4d34f-507">Navigate to the Students Index page.</span></span> <span data-ttu-id="4d34f-508">Ya no se muestran las horas.</span><span class="sxs-lookup"><span data-stu-id="4d34f-508">Times are no longer displayed.</span></span> <span data-ttu-id="4d34f-509">Todas las vistas que usa el modelo `Student` muestran la fecha sin hora.</span><span class="sxs-lookup"><span data-stu-id="4d34f-509">Every view that uses the `Student` model displays the date without time.</span></span>

![Página de índice de estudiantes en la que se muestran las fechas sin horas](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="4d34f-511">El atributo StringLength</span><span class="sxs-lookup"><span data-stu-id="4d34f-511">The StringLength attribute</span></span>

<span data-ttu-id="4d34f-512">Las reglas de validación de datos y los mensajes de error de validación se pueden especificar con atributos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-512">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="4d34f-513">El atributo [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) especifica la longitud mínima y máxima de caracteres que se permite en un campo de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-513">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="4d34f-514">El atributo `StringLength` también proporciona validación del lado cliente y del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="4d34f-514">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="4d34f-515">El valor mínimo no influye en el esquema de base de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-515">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="4d34f-516">Actualice el modelo `Student` con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-516">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="4d34f-517">El código anterior limita los nombres a no más de 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="4d34f-517">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="4d34f-518">El atributo `StringLength` no impide que un usuario escriba un espacio en blanco para un nombre.</span><span class="sxs-lookup"><span data-stu-id="4d34f-518">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="4d34f-519">El atributo [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) se usa para aplicar restricciones a la entrada.</span><span class="sxs-lookup"><span data-stu-id="4d34f-519">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="4d34f-520">Por ejemplo, el código siguiente requiere que el primer carácter sea una letra mayúscula y el resto de caracteres sean alfabéticos:</span><span class="sxs-lookup"><span data-stu-id="4d34f-520">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="4d34f-521">Ejecute la aplicación:</span><span class="sxs-lookup"><span data-stu-id="4d34f-521">Run the app:</span></span>

* <span data-ttu-id="4d34f-522">Vaya a la página Students.</span><span class="sxs-lookup"><span data-stu-id="4d34f-522">Navigate to the Students page.</span></span>
* <span data-ttu-id="4d34f-523">Seleccione **Create New** y escriba un nombre más de 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="4d34f-523">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="4d34f-524">Seleccione **Create**, la validación del lado cliente muestra un mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="4d34f-524">Select **Create**, client-side validation shows an error message.</span></span>

![Página de índice de estudiantes en la que se muestran errores de longitud de cadena](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="4d34f-526">En el **Explorador de objetos de SQL Server**, (SSOX) abra el diseñador de tablas de Student haciendo doble clic en la tabla **Student**.</span><span class="sxs-lookup"><span data-stu-id="4d34f-526">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabla de estudiantes en SSOX antes de las migraciones](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="4d34f-528">La imagen anterior muestra el esquema para la tabla `Student`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-528">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="4d34f-529">Los campos de nombre tienen tipo `nvarchar(MAX)` porque las migraciones no se han ejecutado en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-529">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="4d34f-530">Cuando se ejecutan las migraciones más adelante en este tutorial, los campos de nombre se convierten en `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-530">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="4d34f-531">El atributo Column</span><span class="sxs-lookup"><span data-stu-id="4d34f-531">The Column attribute</span></span>

<span data-ttu-id="4d34f-532">Los atributos pueden controlar cómo se asignan las clases y propiedades a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-532">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="4d34f-533">En esta sección, el atributo `Column` se usa para asignar el nombre de la propiedad `FirstMidName` a "FirstName" en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-533">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="4d34f-534">Cuando se crea la base de datos, los nombres de propiedad en el modelo se usan para los nombres de columna (excepto cuando se usa el atributo `Column`).</span><span class="sxs-lookup"><span data-stu-id="4d34f-534">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="4d34f-535">El modelo `Student` usa `FirstMidName` para el nombre de campo por la posibilidad de que el campo contenga también un segundo nombre.</span><span class="sxs-lookup"><span data-stu-id="4d34f-535">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="4d34f-536">Actualice el archivo *Models/Student.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="4d34f-536">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="4d34f-537">Con el cambio anterior, `Student.FirstMidName` en la aplicación se asigna a la columna `FirstName` de la tabla `Student`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-537">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="4d34f-538">La adición del atributo `Column` cambia el modelo de respaldo de `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-538">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="4d34f-539">El modelo que está haciendo la copia de seguridad de `SchoolContext` ya no coincide con la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-539">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="4d34f-540">Si la aplicación se ejecuta antes de aplicar las migraciones, se genera la siguiente excepción:</span><span class="sxs-lookup"><span data-stu-id="4d34f-540">If the app is run before applying migrations, the following exception is generated:</span></span>

```SQL
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="4d34f-541">Para actualizar la base de datos:</span><span class="sxs-lookup"><span data-stu-id="4d34f-541">To update the DB:</span></span>

* <span data-ttu-id="4d34f-542">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="4d34f-542">Build the project.</span></span>
* <span data-ttu-id="4d34f-543">Abra una ventana de comandos en la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="4d34f-543">Open a command window in the project folder.</span></span> <span data-ttu-id="4d34f-544">Escriba los comandos siguientes para crear una migración y actualizar la base de datos:</span><span class="sxs-lookup"><span data-stu-id="4d34f-544">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4d34f-545">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d34f-545">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4d34f-546">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d34f-546">Visual Studio Code</span></span>](#tab/visual-studio-code)

```console
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="4d34f-547">El comando `migrations add ColumnFirstName` genera el siguiente mensaje de advertencia:</span><span class="sxs-lookup"><span data-stu-id="4d34f-547">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="4d34f-548">La advertencia se genera porque los campos de nombre ahora están limitados a 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="4d34f-548">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="4d34f-549">Si un nombre en la base de datos tenía más de 50 caracteres, se perderían desde el 51 hasta el último carácter.</span><span class="sxs-lookup"><span data-stu-id="4d34f-549">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="4d34f-550">Pruebe la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4d34f-550">Test the app.</span></span>

<span data-ttu-id="4d34f-551">Abra la tabla de estudiantes en SSOX:</span><span class="sxs-lookup"><span data-stu-id="4d34f-551">Open the Student table in SSOX:</span></span>

![Tabla de estudiantes en SSOX después de las migraciones](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="4d34f-553">Antes de aplicar la migración, las columnas de nombre eran de tipo [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="4d34f-553">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="4d34f-554">Las columnas de nombre ahora son `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-554">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="4d34f-555">El nombre de columna ha cambiado de `FirstMidName` a `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-555">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="4d34f-556">En la sección siguiente, la creación de la aplicación en algunas de las fases genera errores del compilador.</span><span class="sxs-lookup"><span data-stu-id="4d34f-556">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="4d34f-557">Las instrucciones especifican cuándo se debe compilar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4d34f-557">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="4d34f-558">Actualizar la entidad Student</span><span class="sxs-lookup"><span data-stu-id="4d34f-558">Student entity update</span></span>

![Entidad Student](complex-data-model/_static/student-entity.png)

<span data-ttu-id="4d34f-560">Actualice *Models/Student.cs* con el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="4d34f-560">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="4d34f-561">El atributo Required</span><span class="sxs-lookup"><span data-stu-id="4d34f-561">The Required attribute</span></span>

<span data-ttu-id="4d34f-562">El atributo `Required` hace que las propiedades de nombre sean campos obligatorios.</span><span class="sxs-lookup"><span data-stu-id="4d34f-562">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="4d34f-563">El atributo `Required` no es necesario para los tipos que no aceptan valores NULL, como los tipos de valor (`DateTime`, `int`, `double`, etc.).</span><span class="sxs-lookup"><span data-stu-id="4d34f-563">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="4d34f-564">Los tipos que no aceptan valores NULL se tratan automáticamente como campos obligatorios.</span><span class="sxs-lookup"><span data-stu-id="4d34f-564">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="4d34f-565">El atributo `Required` se podría reemplazar con un parámetro de longitud mínima en el atributo `StringLength`:</span><span class="sxs-lookup"><span data-stu-id="4d34f-565">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="4d34f-566">El atributo Display</span><span class="sxs-lookup"><span data-stu-id="4d34f-566">The Display attribute</span></span>

<span data-ttu-id="4d34f-567">El atributo `Display` especifica que el título de los cuadros de texto debe ser "First Name", "Last Name", "Full Name" y "Enrollment Date".</span><span class="sxs-lookup"><span data-stu-id="4d34f-567">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="4d34f-568">Los títulos predeterminados no tenían ningún espacio de división de palabras, por ejemplo "Lastname".</span><span class="sxs-lookup"><span data-stu-id="4d34f-568">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="4d34f-569">La propiedad calculada FullName</span><span class="sxs-lookup"><span data-stu-id="4d34f-569">The FullName calculated property</span></span>

<span data-ttu-id="4d34f-570">`FullName` es una propiedad calculada que devuelve un valor que se crea mediante la concatenación de otras dos propiedades.</span><span class="sxs-lookup"><span data-stu-id="4d34f-570">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="4d34f-571">No se puede establecer `FullName`, tiene solo un descriptor de acceso get.</span><span class="sxs-lookup"><span data-stu-id="4d34f-571">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="4d34f-572">No se crea ninguna columna `FullName` en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-572">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="4d34f-573">Crear la entidad Instructor</span><span class="sxs-lookup"><span data-stu-id="4d34f-573">Create the Instructor Entity</span></span>

![La entidad Instructor](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="4d34f-575">Cree *Models/Instructor.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-575">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="4d34f-576">En una sola línea puede haber varios atributos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-576">Multiple attributes can be on one line.</span></span> <span data-ttu-id="4d34f-577">Los atributos `HireDate` pudieron escribirse de la manera siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-577">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="4d34f-578">Las propiedades de navegación CourseAssignments y OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="4d34f-578">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="4d34f-579">`CourseAssignments` y `OfficeAssignment` son propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="4d34f-579">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="4d34f-580">Un instructor puede impartir cualquier número de cursos, por lo que `CourseAssignments` se define como una colección.</span><span class="sxs-lookup"><span data-stu-id="4d34f-580">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="4d34f-581">Si una propiedad de navegación contiene varias entidades:</span><span class="sxs-lookup"><span data-stu-id="4d34f-581">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="4d34f-582">Debe ser un tipo de lista, donde se pueden agregar, eliminar y actualizar las entradas.</span><span class="sxs-lookup"><span data-stu-id="4d34f-582">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="4d34f-583">Los tipos de propiedad de navegación incluyen:</span><span class="sxs-lookup"><span data-stu-id="4d34f-583">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="4d34f-584">Si se especifica `ICollection<T>`, EF Core crea una colección `HashSet<T>` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4d34f-584">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="4d34f-585">La entidad `CourseAssignment` se explica en la sección sobre las relaciones de varios a varios.</span><span class="sxs-lookup"><span data-stu-id="4d34f-585">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="4d34f-586">Las reglas de negocio de Contoso University establecen que un instructor puede tener, a lo sumo, una oficina.</span><span class="sxs-lookup"><span data-stu-id="4d34f-586">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="4d34f-587">La propiedad `OfficeAssignment` contiene una única instancia de `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-587">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="4d34f-588">`OfficeAssignment` es NULL si no se asigna ninguna oficina.</span><span class="sxs-lookup"><span data-stu-id="4d34f-588">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="4d34f-589">Crear la entidad OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="4d34f-589">Create the OfficeAssignment entity</span></span>

![Entidad OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="4d34f-591">Cree *Models/OfficeAssignment.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-591">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="4d34f-592">El atributo Key</span><span class="sxs-lookup"><span data-stu-id="4d34f-592">The Key attribute</span></span>

<span data-ttu-id="4d34f-593">El atributo `[Key]` se usa para identificar una propiedad como la clave principal (PK) cuando el nombre de propiedad es diferente de classnameID o ID.</span><span class="sxs-lookup"><span data-stu-id="4d34f-593">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="4d34f-594">Hay una relación de uno a cero o uno entre las entidades `Instructor` y `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-594">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="4d34f-595">Solo existe una asignación de oficina en relación con el instructor a la que está asignada.</span><span class="sxs-lookup"><span data-stu-id="4d34f-595">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="4d34f-596">La clave principal de `OfficeAssignment` también es la clave externa (FK) para la entidad `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-596">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="4d34f-597">EF Core no reconoce automáticamente `InstructorID` como la clave principal de `OfficeAssignment` porque:</span><span class="sxs-lookup"><span data-stu-id="4d34f-597">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="4d34f-598">`InstructorID` no sigue la convención de nomenclatura de ID o classnameID.</span><span class="sxs-lookup"><span data-stu-id="4d34f-598">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="4d34f-599">Por tanto, se usa el atributo `Key` para identificar `InstructorID` como la clave principal:</span><span class="sxs-lookup"><span data-stu-id="4d34f-599">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="4d34f-600">De forma predeterminada, EF Core trata la clave como no generada por la base de datos porque la columna es para una relación de identificación.</span><span class="sxs-lookup"><span data-stu-id="4d34f-600">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="4d34f-601">La propiedad de navegación Instructor</span><span class="sxs-lookup"><span data-stu-id="4d34f-601">The Instructor navigation property</span></span>

<span data-ttu-id="4d34f-602">La propiedad de navegación `OfficeAssignment` para la entidad `Instructor` acepta valores NULL porque:</span><span class="sxs-lookup"><span data-stu-id="4d34f-602">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="4d34f-603">Los tipos de referencia, como las clases, aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="4d34f-603">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="4d34f-604">Un instructor podría no tener una asignación de oficina.</span><span class="sxs-lookup"><span data-stu-id="4d34f-604">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="4d34f-605">La entidad `OfficeAssignment` tiene una propiedad de navegación `Instructor` que no acepta valores NULL porque:</span><span class="sxs-lookup"><span data-stu-id="4d34f-605">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="4d34f-606">`InstructorID` no acepta valores NULL.</span><span class="sxs-lookup"><span data-stu-id="4d34f-606">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="4d34f-607">Una asignación de oficina no puede existir sin un instructor.</span><span class="sxs-lookup"><span data-stu-id="4d34f-607">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="4d34f-608">Cuando una entidad `Instructor` tiene una entidad `OfficeAssignment` relacionada, cada entidad tiene una referencia a la otra en su propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="4d34f-608">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="4d34f-609">El atributo `[Required]` puede aplicarse a la propiedad de navegación `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="4d34f-609">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="4d34f-610">El código anterior especifica que debe haber un instructor relacionado.</span><span class="sxs-lookup"><span data-stu-id="4d34f-610">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="4d34f-611">El código anterior no es necesario porque la clave externa `InstructorID`, que también es la clave principal, no acepta valores NULL.</span><span class="sxs-lookup"><span data-stu-id="4d34f-611">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="4d34f-612">Modificar la entidad Course</span><span class="sxs-lookup"><span data-stu-id="4d34f-612">Modify the Course Entity</span></span>

![La entidad Course](complex-data-model/_static/course-entity.png)

<span data-ttu-id="4d34f-614">Actualice *Models/Course.cs* con el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="4d34f-614">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="4d34f-615">La entidad `Course` tiene una propiedad de clave externa (FK) `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-615">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="4d34f-616">`DepartmentID` apunta a la entidad relacionada `Department`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-616">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="4d34f-617">La entidad `Course` tiene una propiedad de navegación `Department`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-617">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="4d34f-618">EF Core no requiere una propiedad de clave externa para un modelo de datos cuando el modelo tiene una propiedad de navegación para una entidad relacionada.</span><span class="sxs-lookup"><span data-stu-id="4d34f-618">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="4d34f-619">EF Core crea automáticamente claves externas en la base de datos siempre que se necesiten.</span><span class="sxs-lookup"><span data-stu-id="4d34f-619">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="4d34f-620">EF Core crea [propiedades paralelas](/ef/core/modeling/shadow-properties) para las claves externas creadas automáticamente.</span><span class="sxs-lookup"><span data-stu-id="4d34f-620">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="4d34f-621">Tener la clave externa en el modelo de datos puede hacer que las actualizaciones sean más sencillas y eficaces.</span><span class="sxs-lookup"><span data-stu-id="4d34f-621">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="4d34f-622">Por ejemplo, considere la posibilidad de un modelo donde la propiedad de la clave externa `DepartmentID` *no* está incluida.</span><span class="sxs-lookup"><span data-stu-id="4d34f-622">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="4d34f-623">Cuando se captura una entidad de curso para editar:</span><span class="sxs-lookup"><span data-stu-id="4d34f-623">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="4d34f-624">La entidad `Department` es NULL si no se carga explícitamente.</span><span class="sxs-lookup"><span data-stu-id="4d34f-624">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="4d34f-625">Para actualizar la entidad Course, la entidad `Department` debe capturarse en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="4d34f-625">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="4d34f-626">Cuando se incluye la propiedad de clave externa `DepartmentID` en el modelo de datos, no es necesario capturar la entidad `Department` antes de una actualización.</span><span class="sxs-lookup"><span data-stu-id="4d34f-626">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="4d34f-627">El atributo DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="4d34f-627">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="4d34f-628">El atributo `[DatabaseGenerated(DatabaseGeneratedOption.None)]` especifica que la aplicación proporciona la clave principal, en lugar de generarla la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-628">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="4d34f-629">De forma predeterminada, EF Core da por supuesto que la base de datos genera valores de clave principal.</span><span class="sxs-lookup"><span data-stu-id="4d34f-629">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="4d34f-630">Los valores de clave principal generados por la base de datos suelen ser el mejor método.</span><span class="sxs-lookup"><span data-stu-id="4d34f-630">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="4d34f-631">Para las entidades `Course`, el usuario especifica la clave principal.</span><span class="sxs-lookup"><span data-stu-id="4d34f-631">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="4d34f-632">Por ejemplo, un número de curso como una serie de 1000 para el departamento de matemáticas, una serie de 2000 para el departamento de inglés.</span><span class="sxs-lookup"><span data-stu-id="4d34f-632">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="4d34f-633">También se puede usar el atributo `DatabaseGenerated` para generar valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="4d34f-633">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="4d34f-634">Por ejemplo, la base de datos puede generar automáticamente un campo de fecha para registrar la fecha en que se crea o actualiza una fila.</span><span class="sxs-lookup"><span data-stu-id="4d34f-634">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="4d34f-635">Para obtener más información, vea [Propiedades generadas](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="4d34f-635">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="4d34f-636">Propiedades de clave externa y de navegación</span><span class="sxs-lookup"><span data-stu-id="4d34f-636">Foreign key and navigation properties</span></span>

<span data-ttu-id="4d34f-637">Las propiedades de clave externa (FK) y las de navegación de la entidad `Course` reflejan las relaciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="4d34f-637">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="4d34f-638">Un curso se asigna a un departamento, por lo que hay una clave externa `DepartmentID` y una propiedad de navegación `Department`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-638">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="4d34f-639">Un curso puede tener cualquier número de alumnos inscritos en él, por lo que la propiedad de navegación `Enrollments` es una colección:</span><span class="sxs-lookup"><span data-stu-id="4d34f-639">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="4d34f-640">Un curso puede ser impartido por varios instructores, por lo que la propiedad de navegación `CourseAssignments` es una colección:</span><span class="sxs-lookup"><span data-stu-id="4d34f-640">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="4d34f-641">`CourseAssignment` se explica [más adelante](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="4d34f-641">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="4d34f-642">Crear la entidad Department</span><span class="sxs-lookup"><span data-stu-id="4d34f-642">Create the Department entity</span></span>

![La entidad Department](complex-data-model/_static/department-entity.png)

<span data-ttu-id="4d34f-644">Cree *Models/Department.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-644">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="4d34f-645">El atributo Column</span><span class="sxs-lookup"><span data-stu-id="4d34f-645">The Column attribute</span></span>

<span data-ttu-id="4d34f-646">Anteriormente se usó el atributo `Column` para cambiar la asignación de nombres de columna.</span><span class="sxs-lookup"><span data-stu-id="4d34f-646">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="4d34f-647">En el código de la entidad `Department`, se usó el atributo `Column` para cambiar la asignación de tipos de datos de SQL.</span><span class="sxs-lookup"><span data-stu-id="4d34f-647">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="4d34f-648">La columna `Budget` se define mediante el tipo money de SQL Server en la base de datos:</span><span class="sxs-lookup"><span data-stu-id="4d34f-648">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="4d34f-649">Por lo general, la asignación de columnas no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="4d34f-649">Column mapping is generally not required.</span></span> <span data-ttu-id="4d34f-650">EF Core generalmente elige el tipo de datos de SQL Server apropiado en función del tipo CLR para la propiedad.</span><span class="sxs-lookup"><span data-stu-id="4d34f-650">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="4d34f-651">El tipo CLR `decimal` se asigna a un tipo `decimal` de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="4d34f-651">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="4d34f-652">`Budget` es para la divisa, y el tipo de datos money es más adecuado para la divisa.</span><span class="sxs-lookup"><span data-stu-id="4d34f-652">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="4d34f-653">Propiedades de clave externa y de navegación</span><span class="sxs-lookup"><span data-stu-id="4d34f-653">Foreign key and navigation properties</span></span>

<span data-ttu-id="4d34f-654">Las propiedades de clave externa y de navegación reflejan las relaciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="4d34f-654">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="4d34f-655">Un departamento puede tener o no un administrador.</span><span class="sxs-lookup"><span data-stu-id="4d34f-655">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="4d34f-656">Un administrador siempre es un instructor.</span><span class="sxs-lookup"><span data-stu-id="4d34f-656">An administrator is always an instructor.</span></span> <span data-ttu-id="4d34f-657">Por lo tanto, la propiedad `InstructorID` se incluye como la clave externa para la entidad `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-657">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="4d34f-658">La propiedad de navegación se denomina `Administrator` pero contiene una entidad `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="4d34f-658">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="4d34f-659">El signo de interrogación (?) en el código anterior especifica que la propiedad acepta valores NULL.</span><span class="sxs-lookup"><span data-stu-id="4d34f-659">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="4d34f-660">Un departamento puede tener varios cursos, por lo que hay una propiedad de navegación Courses:</span><span class="sxs-lookup"><span data-stu-id="4d34f-660">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="4d34f-661">Nota: Por convención, EF Core permite la eliminación en cascada de las claves externas que no acepten valores NULL ni relaciones de varios a varios.</span><span class="sxs-lookup"><span data-stu-id="4d34f-661">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="4d34f-662">La eliminación en cascada puede dar lugar a reglas de eliminación en cascada circular.</span><span class="sxs-lookup"><span data-stu-id="4d34f-662">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="4d34f-663">Las reglas de eliminación en cascada circular provocan una excepción cuando se agrega una migración.</span><span class="sxs-lookup"><span data-stu-id="4d34f-663">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="4d34f-664">Por ejemplo, si la propiedad `Department.InstructorID` no se ha definido como que acepta valores NULL:</span><span class="sxs-lookup"><span data-stu-id="4d34f-664">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="4d34f-665">EF Core configura una regla de eliminación en cascada para eliminar el departamento cuando se elimina el instructor.</span><span class="sxs-lookup"><span data-stu-id="4d34f-665">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="4d34f-666">Eliminar el departamento cuando se elimine el instructor no es el comportamiento previsto.</span><span class="sxs-lookup"><span data-stu-id="4d34f-666">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="4d34f-667">La API fluida siguiente establecería una regla de restricción en lugar de en cascada.</span><span class="sxs-lookup"><span data-stu-id="4d34f-667">The following fluent API would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="4d34f-668">El código anterior deshabilita la eliminación en cascada en la relación de instructor y departamento.</span><span class="sxs-lookup"><span data-stu-id="4d34f-668">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="4d34f-669">Actualizar la entidad Enrollment</span><span class="sxs-lookup"><span data-stu-id="4d34f-669">Update the Enrollment entity</span></span>

<span data-ttu-id="4d34f-670">Un registro de inscripción corresponde a un curso realizado por un alumno.</span><span class="sxs-lookup"><span data-stu-id="4d34f-670">An enrollment record is for one course taken by one student.</span></span>

![La entidad Enrollment](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="4d34f-672">Actualice *Models/Enrollment.cs* con el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="4d34f-672">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="4d34f-673">Propiedades de clave externa y de navegación</span><span class="sxs-lookup"><span data-stu-id="4d34f-673">Foreign key and navigation properties</span></span>

<span data-ttu-id="4d34f-674">Las propiedades de clave externa y de navegación reflejan las relaciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="4d34f-674">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="4d34f-675">Un registro de inscripción es para un curso, por lo que hay una propiedad de clave externa `CourseID` y una propiedad de navegación `Course`:</span><span class="sxs-lookup"><span data-stu-id="4d34f-675">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="4d34f-676">Un registro de inscripción es para un alumno, por lo que hay una propiedad de clave externa `StudentID` y una propiedad de navegación `Student`:</span><span class="sxs-lookup"><span data-stu-id="4d34f-676">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="4d34f-677">Relaciones Varios a Varios</span><span class="sxs-lookup"><span data-stu-id="4d34f-677">Many-to-Many Relationships</span></span>

<span data-ttu-id="4d34f-678">Hay una relación de varios a varios entre las entidades `Student` y `Course`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-678">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="4d34f-679">La entidad `Enrollment` funciona como una tabla combinada varios a varios *con carga útil* en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-679">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="4d34f-680">"Con carga útil" significa que la tabla `Enrollment` contiene datos adicionales, además de claves externas de las tablas combinadas (en este caso, la clave principal y `Grade`).</span><span class="sxs-lookup"><span data-stu-id="4d34f-680">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="4d34f-681">En la ilustración siguiente se muestra el aspecto de estas relaciones en un diagrama de entidades.</span><span class="sxs-lookup"><span data-stu-id="4d34f-681">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="4d34f-682">(Este diagrama se ha generado mediante [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) para EF 6.x.</span><span class="sxs-lookup"><span data-stu-id="4d34f-682">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="4d34f-683">Crear el diagrama no forma parte del tutorial).</span><span class="sxs-lookup"><span data-stu-id="4d34f-683">Creating the diagram isn't part of the tutorial.)</span></span>

![Relación de varios a varios entre estudiantes y cursos](complex-data-model/_static/student-course.png)

<span data-ttu-id="4d34f-685">Cada línea de relación tiene un 1 en un extremo y un asterisco (\*) en el otro, para indicar una relación uno a varios.</span><span class="sxs-lookup"><span data-stu-id="4d34f-685">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="4d34f-686">Si la tabla `Enrollment` no incluyera información de calificaciones, solo tendría que contener las dos claves externas (`CourseID` y `StudentID`).</span><span class="sxs-lookup"><span data-stu-id="4d34f-686">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="4d34f-687">Una tabla combinada de varios a varios sin carga útil se suele denominar una tabla combinada pura (PJT).</span><span class="sxs-lookup"><span data-stu-id="4d34f-687">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="4d34f-688">Las entidades `Instructor` y `Course` tienen una relación de varios a varios con una tabla combinada pura.</span><span class="sxs-lookup"><span data-stu-id="4d34f-688">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="4d34f-689">Nota: EF 6.x es compatible con las tablas de combinación implícitas con relaciones de varios a varios, pero EF Core, no.</span><span class="sxs-lookup"><span data-stu-id="4d34f-689">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="4d34f-690">Para obtener más información, consulte [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/) (Relaciones de varios a varios en EF Core 2.0).</span><span class="sxs-lookup"><span data-stu-id="4d34f-690">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="4d34f-691">La entidad CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="4d34f-691">The CourseAssignment entity</span></span>

![La entidad CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="4d34f-693">Cree *Models/CourseAssignment.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-693">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="4d34f-694">Relación Instructor-to-Courses</span><span class="sxs-lookup"><span data-stu-id="4d34f-694">Instructor-to-Courses</span></span>

![Relación de varios a varios Instructor-to-Courses](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="4d34f-696">La relación de varios a varios Instructor-to-Courses:</span><span class="sxs-lookup"><span data-stu-id="4d34f-696">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="4d34f-697">Requiere una tabla de combinación que debe estar representada por un conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="4d34f-697">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="4d34f-698">Es una tabla combinada pura (tabla sin carga útil).</span><span class="sxs-lookup"><span data-stu-id="4d34f-698">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="4d34f-699">Es común denominar una entidad de combinación `EntityName1EntityName2`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-699">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="4d34f-700">Por ejemplo, la tabla de combinación Instructor-to-Courses usando este patrón es `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-700">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="4d34f-701">Pero se recomienda usar un nombre que describa la relación.</span><span class="sxs-lookup"><span data-stu-id="4d34f-701">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="4d34f-702">Los modelos de datos empiezan de manera sencilla y crecen.</span><span class="sxs-lookup"><span data-stu-id="4d34f-702">Data models start out simple and grow.</span></span> <span data-ttu-id="4d34f-703">Las tablas combinadas sin carga útil (PJT) evolucionan con frecuencia para incluir la carga útil.</span><span class="sxs-lookup"><span data-stu-id="4d34f-703">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="4d34f-704">A partir de un nombre de entidad descriptivo, no es necesario cambiar el nombre cuando la tabla combinada cambia.</span><span class="sxs-lookup"><span data-stu-id="4d34f-704">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="4d34f-705">Idealmente, la entidad de combinación tendrá su propio nombre natural (posiblemente una sola palabra) en el dominio de empresa.</span><span class="sxs-lookup"><span data-stu-id="4d34f-705">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="4d34f-706">Por ejemplo, Books y Customers podrían vincularse a través de una entidad combinada denominada Ratings.</span><span class="sxs-lookup"><span data-stu-id="4d34f-706">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="4d34f-707">Para la relación de varios a varios Instructor-to-Courses, se prefiere `CourseAssignment` a `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-707">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="4d34f-708">Clave compuesta</span><span class="sxs-lookup"><span data-stu-id="4d34f-708">Composite key</span></span>

<span data-ttu-id="4d34f-709">Las claves externas no aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="4d34f-709">FKs are not nullable.</span></span> <span data-ttu-id="4d34f-710">Las dos claves externas en `CourseAssignment` (`InstructorID` y `CourseID`) juntas identifican de forma única cada fila de la tabla `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-710">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="4d34f-711">`CourseAssignment` no requiere una clave principal dedicada.</span><span class="sxs-lookup"><span data-stu-id="4d34f-711">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="4d34f-712">Las propiedades `InstructorID` y `CourseID` funcionan como una clave principal compuesta.</span><span class="sxs-lookup"><span data-stu-id="4d34f-712">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="4d34f-713">La única manera de especificar claves principales compuestas en EF Core es con la *API fluida*.</span><span class="sxs-lookup"><span data-stu-id="4d34f-713">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="4d34f-714">La sección siguiente muestra cómo configurar la clave principal compuesta.</span><span class="sxs-lookup"><span data-stu-id="4d34f-714">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="4d34f-715">La clave compuesta asegura que:</span><span class="sxs-lookup"><span data-stu-id="4d34f-715">The composite key ensures:</span></span>

* <span data-ttu-id="4d34f-716">Se permiten varias filas para un curso.</span><span class="sxs-lookup"><span data-stu-id="4d34f-716">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="4d34f-717">Se permiten varias filas para un instructor.</span><span class="sxs-lookup"><span data-stu-id="4d34f-717">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="4d34f-718">No se permiten varias filas para el mismo instructor y curso.</span><span class="sxs-lookup"><span data-stu-id="4d34f-718">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="4d34f-719">La entidad de combinación `Enrollment` define su propia clave principal, por lo que este tipo de duplicados son posibles.</span><span class="sxs-lookup"><span data-stu-id="4d34f-719">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="4d34f-720">Para evitar los duplicados:</span><span class="sxs-lookup"><span data-stu-id="4d34f-720">To prevent such duplicates:</span></span>

* <span data-ttu-id="4d34f-721">Agregue un índice único en los campos de clave externa, o</span><span class="sxs-lookup"><span data-stu-id="4d34f-721">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="4d34f-722">Configure `Enrollment` con una clave compuesta principal similar a `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-722">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="4d34f-723">Para obtener más información, vea [Índices](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="4d34f-723">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="4d34f-724">Actualizar el contexto de la base de datos</span><span class="sxs-lookup"><span data-stu-id="4d34f-724">Update the DB context</span></span>

<span data-ttu-id="4d34f-725">Agregue el código resaltado siguiente a *Data/SchoolContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="4d34f-725">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="4d34f-726">El código anterior agrega las nuevas entidades y configura la clave principal compuesta de la entidad `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-726">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="4d34f-727">Alternativa de la API fluida a los atributos</span><span class="sxs-lookup"><span data-stu-id="4d34f-727">Fluent API alternative to attributes</span></span>

<span data-ttu-id="4d34f-728">El método `OnModelCreating` del código anterior usa la *API fluida* para configurar el comportamiento de EF Core.</span><span class="sxs-lookup"><span data-stu-id="4d34f-728">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="4d34f-729">La API se denomina "fluida" porque a menudo se usa para encadenar una serie de llamadas de método en una única instrucción.</span><span class="sxs-lookup"><span data-stu-id="4d34f-729">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="4d34f-730">El [código siguiente](/ef/core/modeling/#use-fluent-api-to-configure-a-model) es un ejemplo de la API fluida:</span><span class="sxs-lookup"><span data-stu-id="4d34f-730">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="4d34f-731">En este tutorial, la API fluida se usa solo para la asignación de base de datos que no se puede realizar con atributos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-731">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="4d34f-732">Pero la API fluida puede especificar casi todas las reglas de formato, validación y asignación que se pueden realizar mediante el uso de atributos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-732">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="4d34f-733">Algunos atributos como `MinimumLength` no se pueden aplicar con la API fluida.</span><span class="sxs-lookup"><span data-stu-id="4d34f-733">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="4d34f-734">`MinimumLength` no cambia el esquema, solo aplica una regla de validación de longitud mínima.</span><span class="sxs-lookup"><span data-stu-id="4d34f-734">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="4d34f-735">Algunos desarrolladores prefieren usar la API fluida exclusivamente para mantener "limpias" las clases de entidad.</span><span class="sxs-lookup"><span data-stu-id="4d34f-735">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="4d34f-736">Se pueden mezclar atributos y la API fluida.</span><span class="sxs-lookup"><span data-stu-id="4d34f-736">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="4d34f-737">Hay algunas configuraciones que solo se pueden realizar con la API fluida (especificando una clave principal compuesta).</span><span class="sxs-lookup"><span data-stu-id="4d34f-737">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="4d34f-738">Hay algunas configuraciones que solo se pueden realizar con atributos (`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="4d34f-738">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="4d34f-739">La práctica recomendada para el uso de atributos o API fluida:</span><span class="sxs-lookup"><span data-stu-id="4d34f-739">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="4d34f-740">Elija uno de estos dos enfoques.</span><span class="sxs-lookup"><span data-stu-id="4d34f-740">Choose one of these two approaches.</span></span>
* <span data-ttu-id="4d34f-741">Use el enfoque elegido de forma tan coherente como sea posible.</span><span class="sxs-lookup"><span data-stu-id="4d34f-741">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="4d34f-742">Algunos de los atributos utilizados en este tutorial se usan para:</span><span class="sxs-lookup"><span data-stu-id="4d34f-742">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="4d34f-743">Solo validación (por ejemplo, `MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="4d34f-743">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="4d34f-744">Solo configuración de EF Core (por ejemplo, `HasKey`).</span><span class="sxs-lookup"><span data-stu-id="4d34f-744">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="4d34f-745">Validación y configuración de EF Core (por ejemplo, `[StringLength(50)]`).</span><span class="sxs-lookup"><span data-stu-id="4d34f-745">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="4d34f-746">Para obtener más información sobre la diferencia entre los atributos y la API fluida, vea [Métodos de configuración](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="4d34f-746">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="4d34f-747">Diagrama de entidades en el que se muestran las relaciones</span><span class="sxs-lookup"><span data-stu-id="4d34f-747">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="4d34f-748">En la siguiente ilustración se muestra el diagrama creado por EF Power Tools para el modelo School completado.</span><span class="sxs-lookup"><span data-stu-id="4d34f-748">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Diagrama de entidades](complex-data-model/_static/diagram.png)

<span data-ttu-id="4d34f-750">El diagrama anterior muestra:</span><span class="sxs-lookup"><span data-stu-id="4d34f-750">The preceding diagram shows:</span></span>

* <span data-ttu-id="4d34f-751">Varias líneas de relación uno a varios (1 a \*).</span><span class="sxs-lookup"><span data-stu-id="4d34f-751">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="4d34f-752">La línea de relación de uno a cero o uno (1 a 0..1) entre las entidades `Instructor` y `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-752">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="4d34f-753">La línea de relación de cero o uno o varios (0..1 a \*) entre las entidades `Instructor` y `Department`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-753">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="4d34f-754">Inicializar la base de datos con datos de prueba</span><span class="sxs-lookup"><span data-stu-id="4d34f-754">Seed the DB with Test Data</span></span>

<span data-ttu-id="4d34f-755">Actualice el código en *Data/DbInitializer.cs*:</span><span class="sxs-lookup"><span data-stu-id="4d34f-755">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="4d34f-756">El código anterior proporciona datos de inicialización para las nuevas entidades.</span><span class="sxs-lookup"><span data-stu-id="4d34f-756">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="4d34f-757">La mayor parte de este código crea objetos de entidad y carga los datos de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="4d34f-757">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="4d34f-758">Los datos de ejemplo se usan para pruebas.</span><span class="sxs-lookup"><span data-stu-id="4d34f-758">The sample data is used for testing.</span></span> <span data-ttu-id="4d34f-759">Consulte `Enrollments` y `CourseAssignments` para obtener ejemplos de cómo pueden inicializarse las tablas de combinación de varios a varios.</span><span class="sxs-lookup"><span data-stu-id="4d34f-759">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="4d34f-760">Agregar una migración</span><span class="sxs-lookup"><span data-stu-id="4d34f-760">Add a migration</span></span>

<span data-ttu-id="4d34f-761">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="4d34f-761">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4d34f-762">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d34f-762">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ComplexDataModel
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4d34f-763">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d34f-763">Visual Studio Code</span></span>](#tab/visual-studio-code)

```console
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="4d34f-764">El comando anterior muestra una advertencia sobre la posible pérdida de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-764">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="4d34f-765">Si se ejecuta el comando `database update`, se genera el error siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-765">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="4d34f-766">Aplicar la migración</span><span class="sxs-lookup"><span data-stu-id="4d34f-766">Apply the migration</span></span>

<span data-ttu-id="4d34f-767">Ahora que tiene una base de datos existente, debe pensar cómo aplicar los cambios futuros en ella.</span><span class="sxs-lookup"><span data-stu-id="4d34f-767">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="4d34f-768">En este tutorial se muestran dos enfoques:</span><span class="sxs-lookup"><span data-stu-id="4d34f-768">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="4d34f-769">Quitar y volver a crear la base de datos</span><span class="sxs-lookup"><span data-stu-id="4d34f-769">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="4d34f-770">[Aplicar la migración a la base de datos existente](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="4d34f-770">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="4d34f-771">Aunque este método es más complejo y lento, es el método preferido para entornos de producción del mundo real.</span><span class="sxs-lookup"><span data-stu-id="4d34f-771">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="4d34f-772">**Nota**: Esta sección del tutorial es opcional.</span><span class="sxs-lookup"><span data-stu-id="4d34f-772">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="4d34f-773">Puede realizar la operación de quitar y volver a crear, y omitir esta sección.</span><span class="sxs-lookup"><span data-stu-id="4d34f-773">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="4d34f-774">Si quiere seguir los pasos descritos en esta sección, no realice la operación de quitar y volver a crear.</span><span class="sxs-lookup"><span data-stu-id="4d34f-774">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="4d34f-775">Quitar y volver a crear la base de datos</span><span class="sxs-lookup"><span data-stu-id="4d34f-775">Drop and re-create the database</span></span>

<span data-ttu-id="4d34f-776">El código en la `DbInitializer` actualizada agrega los datos de inicialización para las nuevas entidades.</span><span class="sxs-lookup"><span data-stu-id="4d34f-776">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="4d34f-777">Para obligar a EF Core a crear una base de datos, quite y actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="4d34f-777">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4d34f-778">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d34f-778">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4d34f-779">En la **Consola del Administrador de paquetes** (PMC), ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-779">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
Update-Database
```

<span data-ttu-id="4d34f-780">Ejecute `Get-Help about_EntityFrameworkCore` desde PMC para obtener información de ayuda.</span><span class="sxs-lookup"><span data-stu-id="4d34f-780">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4d34f-781">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d34f-781">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4d34f-782">Abra una ventana de comandos y desplácese hasta la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="4d34f-782">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="4d34f-783">La carpeta del proyecto contiene el archivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="4d34f-783">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="4d34f-784">Escriba lo siguiente en la ventana de comandos:</span><span class="sxs-lookup"><span data-stu-id="4d34f-784">Enter the following in the command window:</span></span>

 ```console
 dotnet ef database drop
dotnet ef database update
 ```

---

<span data-ttu-id="4d34f-785">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4d34f-785">Run the app.</span></span> <span data-ttu-id="4d34f-786">Ejecutar la aplicación ejecuta el método `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-786">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="4d34f-787">`DbInitializer.Initialize` rellena la base de datos nueva.</span><span class="sxs-lookup"><span data-stu-id="4d34f-787">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="4d34f-788">Abra la base de datos en SSOX:</span><span class="sxs-lookup"><span data-stu-id="4d34f-788">Open the DB in SSOX:</span></span>

* <span data-ttu-id="4d34f-789">Si anteriormente se abrió SSOX, haga clic en el botón **Actualizar**.</span><span class="sxs-lookup"><span data-stu-id="4d34f-789">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="4d34f-790">Expanda el nodo **Tablas**.</span><span class="sxs-lookup"><span data-stu-id="4d34f-790">Expand the **Tables** node.</span></span> <span data-ttu-id="4d34f-791">Se muestran las tablas creadas.</span><span class="sxs-lookup"><span data-stu-id="4d34f-791">The created tables are displayed.</span></span>

![Tablas en SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="4d34f-793">Examine la tabla **CourseAssignment**:</span><span class="sxs-lookup"><span data-stu-id="4d34f-793">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="4d34f-794">Haga clic con el botón derecho en la tabla **CourseAssignment** y seleccione **Ver datos**.</span><span class="sxs-lookup"><span data-stu-id="4d34f-794">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="4d34f-795">Compruebe que la tabla **CourseAssignment** contiene datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-795">Verify the **CourseAssignment** table contains data.</span></span>

![Datos de CourseAssignment en SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="4d34f-797">Aplicar la migración a la base de datos existente</span><span class="sxs-lookup"><span data-stu-id="4d34f-797">Apply the migration to the existing database</span></span>

<span data-ttu-id="4d34f-798">Esta sección es opcional.</span><span class="sxs-lookup"><span data-stu-id="4d34f-798">This section is optional.</span></span> <span data-ttu-id="4d34f-799">Estos pasos solo funcionan si pasó por alto la sección [Quitar y volver a crear la base de datos](#drop).</span><span class="sxs-lookup"><span data-stu-id="4d34f-799">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="4d34f-800">Cuando se ejecutan migraciones con datos existentes, puede haber restricciones de clave externa que no se cumplen con los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="4d34f-800">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="4d34f-801">Con los datos de producción, se deben realizar algunos pasos para migrar los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="4d34f-801">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="4d34f-802">En esta sección se proporciona un ejemplo de corrección de las infracciones de restricción de clave externa.</span><span class="sxs-lookup"><span data-stu-id="4d34f-802">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="4d34f-803">No realice estos cambios de código sin hacer una copia de seguridad.</span><span class="sxs-lookup"><span data-stu-id="4d34f-803">Don't make these code changes without a backup.</span></span> <span data-ttu-id="4d34f-804">No realice estos cambios de código si realizó la sección anterior y actualizó la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4d34f-804">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="4d34f-805">El archivo *{marca_de_tiempo}_ComplexDataModel.cs* contiene el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d34f-805">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="4d34f-806">El código anterior agrega una clave externa `DepartmentID` que acepta valores NULL a la tabla `Course`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-806">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="4d34f-807">La base de datos del tutorial anterior contiene filas en `Course`, por lo que no se puede actualizar esa tabla mediante migraciones.</span><span class="sxs-lookup"><span data-stu-id="4d34f-807">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="4d34f-808">Para realizar la migración de `ComplexDataModel`, trabaje con los datos existentes:</span><span class="sxs-lookup"><span data-stu-id="4d34f-808">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="4d34f-809">Cambie el código para asignar a la nueva columna (`DepartmentID`) un valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="4d34f-809">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="4d34f-810">Cree un departamento falso denominado "Temp" para que actúe como el departamento predeterminado.</span><span class="sxs-lookup"><span data-stu-id="4d34f-810">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="4d34f-811">Corregir las restricciones de clave externa</span><span class="sxs-lookup"><span data-stu-id="4d34f-811">Fix the foreign key constraints</span></span>

<span data-ttu-id="4d34f-812">Actualice el método `Up` de las clases `ComplexDataModel`:</span><span class="sxs-lookup"><span data-stu-id="4d34f-812">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="4d34f-813">Abra el archivo *{marca_de_tiempo}_ComplexDataModel.cs*.</span><span class="sxs-lookup"><span data-stu-id="4d34f-813">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="4d34f-814">Convierta en comentario la línea de código que agrega la columna `DepartmentID` a la tabla `Course`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-814">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="4d34f-815">Agregue el código resaltado siguiente.</span><span class="sxs-lookup"><span data-stu-id="4d34f-815">Add the following highlighted code.</span></span> <span data-ttu-id="4d34f-816">El nuevo código va después del bloque `.CreateTable( name: "Department"`:</span><span class="sxs-lookup"><span data-stu-id="4d34f-816">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="4d34f-817">Con los cambios anteriores, las filas `Course` existentes estarán relacionadas con el departamento "Temp" después de ejecutar el método `ComplexDataModel` de `Up`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-817">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="4d34f-818">Una aplicación de producción debería:</span><span class="sxs-lookup"><span data-stu-id="4d34f-818">A production app would:</span></span>

* <span data-ttu-id="4d34f-819">Incluir código o scripts para agregar filas de `Department` y filas de `Course` relacionadas a las nuevas filas de `Department`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-819">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="4d34f-820">No use el departamento "Temp" o el valor predeterminado de `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="4d34f-820">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="4d34f-821">El siguiente tutorial trata los datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="4d34f-821">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4d34f-822">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4d34f-822">Additional resources</span></span>

* [<span data-ttu-id="4d34f-823">Versión en YouTube de este tutorial (parte 1)</span><span class="sxs-lookup"><span data-stu-id="4d34f-823">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="4d34f-824">Versión en YouTube de este tutorial (parte 2)</span><span class="sxs-lookup"><span data-stu-id="4d34f-824">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> <span data-ttu-id="4d34f-825">[Anterior](xref:data/ef-rp/migrations)
> [Siguiente](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="4d34f-825">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end
