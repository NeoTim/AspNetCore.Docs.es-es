<span data-ttu-id="05830-101">Agregue las propiedades siguientes a la clase `Movie`:</span><span class="sxs-lookup"><span data-stu-id="05830-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="05830-102">la clase `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="05830-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="05830-103">El campo `Id`, que requiere la base de datos para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="05830-103">The `Id` field which is required by the database for the primary key.</span></span>
* <span data-ttu-id="05830-104">`[DataType(DataType.Date)]`:  el atributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) especifica el tipo de datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="05830-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="05830-105">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="05830-105">With this attribute:</span></span>

  * <span data-ttu-id="05830-106">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="05830-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="05830-107">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="05830-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="05830-108">Los elementos [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) se tratan en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="05830-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>