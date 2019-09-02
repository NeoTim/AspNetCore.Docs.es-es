---
title: 'Páginas de Razor con EF Core en ASP.NET Core: Simultaneidad (8 de 8)'
author: tdykstra
description: Este tutorial muestra cómo tratar los conflictos cuando varios usuarios actualizan la misma entidad al mismo tiempo.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/concurrency
ms.openlocfilehash: da57633c345ec087b1a4f24ddc7771e7a2d04720
ms.sourcegitcommit: 0774a61a3a6c1412a7da0e7d932dc60c506441fc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2019
ms.locfileid: "70059081"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---concurrency---8-of-8"></a><span data-ttu-id="57067-103">Páginas de Razor con EF Core en ASP.NET Core: Simultaneidad (8 de 8)</span><span class="sxs-lookup"><span data-stu-id="57067-103">Razor Pages with EF Core in ASP.NET Core - Concurrency - 8 of 8</span></span>

<span data-ttu-id="57067-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra) y [Jon P Smith](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="57067-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="57067-105">Este tutorial muestra cómo tratar los conflictos cuando varios usuarios actualizan una entidad de forma simultánea (al mismo tiempo).</span><span class="sxs-lookup"><span data-stu-id="57067-105">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="57067-106">Conflictos de simultaneidad</span><span class="sxs-lookup"><span data-stu-id="57067-106">Concurrency conflicts</span></span>

<span data-ttu-id="57067-107">Un conflicto de simultaneidad se produce cuando:</span><span class="sxs-lookup"><span data-stu-id="57067-107">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="57067-108">Un usuario va a la página de edición de una entidad.</span><span class="sxs-lookup"><span data-stu-id="57067-108">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="57067-109">Otro usuario actualiza la misma entidad antes de que el cambio del primer usuario se escriba en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="57067-109">Another user updates the same entity before the first user's change is written to the database.</span></span>

<span data-ttu-id="57067-110">Si la detección de simultaneidad no está habilitada, quien actualice la base de datos en último lugar sobrescribe los cambios del otro usuario.</span><span class="sxs-lookup"><span data-stu-id="57067-110">If concurrency detection isn't enabled, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="57067-111">Si este riesgo es aceptable, es posible que el costo de programar la simultaneidad supere las ventajas.</span><span class="sxs-lookup"><span data-stu-id="57067-111">If this risk is acceptable, the cost of programming for concurrency might outweigh the benefit.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="57067-112">Simultaneidad pesimista (bloqueo)</span><span class="sxs-lookup"><span data-stu-id="57067-112">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="57067-113">Una manera de evitar conflictos de simultaneidad consiste en usar bloqueos de base de datos.</span><span class="sxs-lookup"><span data-stu-id="57067-113">One way to prevent concurrency conflicts is to use database locks.</span></span> <span data-ttu-id="57067-114">Esto se denomina simultaneidad pesimista.</span><span class="sxs-lookup"><span data-stu-id="57067-114">This is called pessimistic concurrency.</span></span> <span data-ttu-id="57067-115">Antes de que la aplicación lea una fila de base de datos que pretende actualizar, solicita un bloqueo.</span><span class="sxs-lookup"><span data-stu-id="57067-115">Before the app reads a database row that it intends to update, it requests a lock.</span></span> <span data-ttu-id="57067-116">Una vez que una fila está bloqueada para el acceso de actualización, ningún otro usuario puede bloquear la fila hasta que se libere el primer bloqueo.</span><span class="sxs-lookup"><span data-stu-id="57067-116">Once a row is locked for update access, no other users are allowed to lock the row until the first lock is released.</span></span>

<span data-ttu-id="57067-117">Administrar los bloqueos tiene desventajas.</span><span class="sxs-lookup"><span data-stu-id="57067-117">Managing locks has disadvantages.</span></span> <span data-ttu-id="57067-118">Puede ser complejo de programar y causar problemas de rendimiento a medida que aumente el número de usuarios.</span><span class="sxs-lookup"><span data-stu-id="57067-118">It can be complex to program and can cause performance problems as the number of users increases.</span></span> <span data-ttu-id="57067-119">Entity Framework Core no proporciona ninguna compatibilidad integrada para ello y en este tutorial no se muestra cómo implementarla.</span><span class="sxs-lookup"><span data-stu-id="57067-119">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="57067-120">Simultaneidad optimista</span><span class="sxs-lookup"><span data-stu-id="57067-120">Optimistic concurrency</span></span>

<span data-ttu-id="57067-121">La simultaneidad optimista permite que se produzcan conflictos de simultaneidad y luego reacciona correctamente si ocurren.</span><span class="sxs-lookup"><span data-stu-id="57067-121">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="57067-122">Por ejemplo, Jane visita la página de edición de Department y cambia el presupuesto para el departamento de inglés de 350.000,00 a 0,00 USD.</span><span class="sxs-lookup"><span data-stu-id="57067-122">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Cambiar el presupuesto a 0](concurrency/_static/change-budget30.png)

<span data-ttu-id="57067-124">Antes de que Jane haga clic en **Save**, John visita la misma página y cambia el campo Start Date de 9/1/2007 a 9/1/2013.</span><span class="sxs-lookup"><span data-stu-id="57067-124">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Cambiar la fecha de inicio a 2013](concurrency/_static/change-date30.png)

<span data-ttu-id="57067-126">Primero, Jane hace clic en **Guardar** y ve que el cambio surte efecto, ya que en el explorador se muestra la página de índice con el valor cero como la cantidad del presupuesto.</span><span class="sxs-lookup"><span data-stu-id="57067-126">Jane clicks **Save** first and sees her change take effect, since the browser displays the Index page with zero as the Budget amount.</span></span>

<span data-ttu-id="57067-127">John hace clic en **Save** en una página Edit que sigue mostrando un presupuesto de 350.000,00 USD.</span><span class="sxs-lookup"><span data-stu-id="57067-127">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="57067-128">Lo que sucede después viene determinado por cómo se controlan los conflictos de simultaneidad:</span><span class="sxs-lookup"><span data-stu-id="57067-128">What happens next is determined by how you handle concurrency conflicts:</span></span>

* <span data-ttu-id="57067-129">Puede realizar un seguimiento de la propiedad que ha modificado un usuario y actualizar solo las columnas correspondientes de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="57067-129">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

  <span data-ttu-id="57067-130">En el escenario, no se perderá ningún dato.</span><span class="sxs-lookup"><span data-stu-id="57067-130">In the scenario, no data would be lost.</span></span> <span data-ttu-id="57067-131">Los dos usuarios actualizaron diferentes propiedades.</span><span class="sxs-lookup"><span data-stu-id="57067-131">Different properties were updated by the two users.</span></span> <span data-ttu-id="57067-132">La próxima vez que un usuario examine el departamento de inglés, verá los cambios tanto de Jane como de John.</span><span class="sxs-lookup"><span data-stu-id="57067-132">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="57067-133">Este método de actualización puede reducir el número de conflictos que pueden dar lugar a una pérdida de datos.</span><span class="sxs-lookup"><span data-stu-id="57067-133">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="57067-134">Este enfoque presenta algunos inconvenientes:</span><span class="sxs-lookup"><span data-stu-id="57067-134">This approach has some disadvantages:</span></span>
 
  * <span data-ttu-id="57067-135">No puede evitar la pérdida de datos si se realizan cambios paralelos a la misma propiedad.</span><span class="sxs-lookup"><span data-stu-id="57067-135">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="57067-136">Por lo general, no es práctico en una aplicación web.</span><span class="sxs-lookup"><span data-stu-id="57067-136">Is generally not practical in a web app.</span></span> <span data-ttu-id="57067-137">Requiere mantener un estado significativo para realizar un seguimiento de todos los valores capturados y nuevos.</span><span class="sxs-lookup"><span data-stu-id="57067-137">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="57067-138">El mantenimiento de grandes cantidades de estado puede afectar al rendimiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="57067-138">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="57067-139">Puede aumentar la complejidad de las aplicaciones en comparación con la detección de simultaneidad en una entidad.</span><span class="sxs-lookup"><span data-stu-id="57067-139">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="57067-140">Puede permitir que los cambios de John sobrescriban los cambios de Jane.</span><span class="sxs-lookup"><span data-stu-id="57067-140">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="57067-141">La próxima vez que un usuario examine el departamento de inglés, verá 9/1/2013 y el valor de 350.000,00 USD capturado.</span><span class="sxs-lookup"><span data-stu-id="57067-141">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="57067-142">Este enfoque se denomina un escenario de *Prevalece el cliente* o *Prevalece el último*.</span><span class="sxs-lookup"><span data-stu-id="57067-142">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="57067-143">(Todos los valores del cliente tienen prioridad sobre lo que aparece en el almacén de datos). Si no hace ninguna codificación para el control de la simultaneidad, Prevalece el cliente se realizará automáticamente.</span><span class="sxs-lookup"><span data-stu-id="57067-143">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="57067-144">Puede evitar que el cambio de John se actualice en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="57067-144">You can prevent John's change from being updated in the database.</span></span> <span data-ttu-id="57067-145">Normalmente, la aplicación podría:</span><span class="sxs-lookup"><span data-stu-id="57067-145">Typically, the app would:</span></span>

  * <span data-ttu-id="57067-146">Mostrar un mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="57067-146">Display an error message.</span></span>
  * <span data-ttu-id="57067-147">Mostrar el estado actual de los datos.</span><span class="sxs-lookup"><span data-stu-id="57067-147">Show the current state of the data.</span></span>
  * <span data-ttu-id="57067-148">Permitir al usuario volver a aplicar los cambios.</span><span class="sxs-lookup"><span data-stu-id="57067-148">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="57067-149">Esto se denomina un escenario de *Prevalece el almacén*.</span><span class="sxs-lookup"><span data-stu-id="57067-149">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="57067-150">(Los valores del almacén de datos tienen prioridad sobre los valores enviados por el cliente). En este tutorial implementará el escenario de Prevalece el almacén.</span><span class="sxs-lookup"><span data-stu-id="57067-150">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="57067-151">Este método garantiza que ningún cambio se sobrescriba sin que se avise al usuario.</span><span class="sxs-lookup"><span data-stu-id="57067-151">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="conflict-detection-in-ef-core"></a><span data-ttu-id="57067-152">Detección de conflictos en EF Core</span><span class="sxs-lookup"><span data-stu-id="57067-152">Conflict detection in EF Core</span></span>

<span data-ttu-id="57067-153">EF Core inicia excepciones `DbConcurrencyException` cuando detecta conflictos.</span><span class="sxs-lookup"><span data-stu-id="57067-153">EF Core throws `DbConcurrencyException` exceptions when it detects conflicts.</span></span> <span data-ttu-id="57067-154">El modelo de datos se debe configurar para habilitar la detección de conflictos.</span><span class="sxs-lookup"><span data-stu-id="57067-154">The data model has to be configured to enable conflict detection.</span></span> <span data-ttu-id="57067-155">Las opciones para habilitar la detección de conflictos incluyen las siguientes:</span><span class="sxs-lookup"><span data-stu-id="57067-155">Options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="57067-156">Configurar EF Core para incluir los valores originales de las columnas configuradas como [tokens de simultaneidad](/ef/core/modeling/concurrency) en la cláusula Where de los comandos Update y Delete.</span><span class="sxs-lookup"><span data-stu-id="57067-156">Configure EF Core to include the original values of columns configured as [concurrency tokens](/ef/core/modeling/concurrency) in the Where clause of Update and Delete commands.</span></span>

  <span data-ttu-id="57067-157">Cuando se llama a `SaveChanges`, la cláusula Where busca los valores originales de todas las propiedades anotadas con el atributo [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute).</span><span class="sxs-lookup"><span data-stu-id="57067-157">When `SaveChanges` is called, the Where clause looks for the original values of any properties annotated with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) attribute.</span></span> <span data-ttu-id="57067-158">La instrucción de actualización no encontrará una fila para actualizar si alguna de las propiedades de token de simultaneidad ha cambiado desde la primera vez que se ha leído la fila.</span><span class="sxs-lookup"><span data-stu-id="57067-158">The update statement won't find a row to update if any of the concurrency token properties changed since the row was first read.</span></span> <span data-ttu-id="57067-159">EF Core interpreta que se trata de un conflicto de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="57067-159">EF Core interprets that as a concurrency conflict.</span></span> <span data-ttu-id="57067-160">Para las tablas de base de datos que tienen muchas columnas, este enfoque puede dar lugar a cláusulas Where muy grandes y puede requerir grandes cantidades de estado.</span><span class="sxs-lookup"><span data-stu-id="57067-160">For database tables that have many columns, this approach can result in very large Where clauses, and can require large amounts of state.</span></span> <span data-ttu-id="57067-161">Por tanto, generalmente este enfoque no se recomienda y no es el método usado en este tutorial.</span><span class="sxs-lookup"><span data-stu-id="57067-161">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

* <span data-ttu-id="57067-162">En la tabla de la base de datos, incluya una columna de seguimiento que pueda usarse para determinar si una fila ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="57067-162">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span>

  <span data-ttu-id="57067-163">En una base de datos de SQL Server, el tipo de datos de la columna de seguimiento es `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="57067-163">In a SQL Server database, the data type of the tracking column is `rowversion`.</span></span> <span data-ttu-id="57067-164">El valor `rowversion` es un número secuencial que se incrementa cada vez que se actualiza la fila.</span><span class="sxs-lookup"><span data-stu-id="57067-164">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="57067-165">En un comando Update o Delete, la cláusula Where incluye el valor original de la columna de seguimiento (el número de versión de la fila original).</span><span class="sxs-lookup"><span data-stu-id="57067-165">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version number).</span></span> <span data-ttu-id="57067-166">Si otro usuario ha cambiado la fila que se va a actualizar, el valor de la columna `rowversion` es diferente del valor original.</span><span class="sxs-lookup"><span data-stu-id="57067-166">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value.</span></span> <span data-ttu-id="57067-167">En ese caso, la instrucción Update o Delete no puede encontrar la fila para actualizar debido a la cláusula Where.</span><span class="sxs-lookup"><span data-stu-id="57067-167">In that case, the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="57067-168">EF Core inicia una excepción de simultaneidad cuando no hay ninguna fila afectada por un comando Update o Delete.</span><span class="sxs-lookup"><span data-stu-id="57067-168">EF Core throws a concurrency exception when no rows are affected by an Update or Delete command.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="57067-169">Agrega una propiedad de seguimiento</span><span class="sxs-lookup"><span data-stu-id="57067-169">Add a tracking property</span></span>

<span data-ttu-id="57067-170">En *Models/Department.cs*, agregue una propiedad de seguimiento denominada RowVersion:</span><span class="sxs-lookup"><span data-stu-id="57067-170">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

<span data-ttu-id="57067-171">El atributo [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) es lo que identifica la columna como una columna de seguimiento de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="57067-171">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute is what identifies the column as a concurrency tracking column.</span></span> <span data-ttu-id="57067-172">La API fluida es una forma alternativa de especificar la propiedad de seguimiento:</span><span class="sxs-lookup"><span data-stu-id="57067-172">The fluent API is an alternative way to specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="57067-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57067-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="57067-174">En el caso de una base de datos de SQL Server, el atributo `[Timestamp]` de una propiedad de entidad definida como una matriz de bytes:</span><span class="sxs-lookup"><span data-stu-id="57067-174">For a SQL Server database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="57067-175">Hace que la columna se incluya en las cláusulas WHERE de DELETE y UPDATE.</span><span class="sxs-lookup"><span data-stu-id="57067-175">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="57067-176">Establece el tipo de columna de la base de datos en [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="57067-176">Sets the column type in the database to [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span></span>

<span data-ttu-id="57067-177">La base de datos genera un número de versión de fila secuencial que se incrementa cada vez que se actualiza la fila.</span><span class="sxs-lookup"><span data-stu-id="57067-177">The database generates a sequential row version number that's incremented each time the row is updated.</span></span> <span data-ttu-id="57067-178">En un comando `Update` o `Delete`, la cláusula `Where` incluye el valor de versión de fila capturado.</span><span class="sxs-lookup"><span data-stu-id="57067-178">In an `Update` or `Delete` command, the `Where` clause includes the fetched row version value.</span></span> <span data-ttu-id="57067-179">Si la fila que se va a actualizar ha cambiado desde que se ha capturado:</span><span class="sxs-lookup"><span data-stu-id="57067-179">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="57067-180">El valor de la versión de fila actual no coincide con el valor capturado.</span><span class="sxs-lookup"><span data-stu-id="57067-180">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="57067-181">Los comandos `Update` o `Delete` no encuentran una fila porque la cláusula `Where` busca el valor de versión de la fila capturada.</span><span class="sxs-lookup"><span data-stu-id="57067-181">The `Update` or `Delete` commands don't find a row because the `Where` clause looks for the fetched row version value.</span></span>
* <span data-ttu-id="57067-182">Se produce una excepción `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="57067-182">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="57067-183">El código siguiente muestra una parte del T-SQL generado por EF Core cuando se actualiza el nombre de Department:</span><span class="sxs-lookup"><span data-stu-id="57067-183">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

<span data-ttu-id="57067-184">El código resaltado anteriormente muestra la cláusula `WHERE` que contiene `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="57067-184">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="57067-185">Si la base de datos `RowVersion` no es igual al parámetro `RowVersion` (`@p2`), no se ha actualizado ninguna fila.</span><span class="sxs-lookup"><span data-stu-id="57067-185">If the database `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="57067-186">El código resaltado a continuación muestra el T-SQL que comprueba que se actualizó exactamente una fila:</span><span class="sxs-lookup"><span data-stu-id="57067-186">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

<span data-ttu-id="57067-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) devuelve el número de filas afectadas por la última instrucción.</span><span class="sxs-lookup"><span data-stu-id="57067-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="57067-188">Si no se actualiza ninguna fila, EF Core inicia una excepción `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="57067-188">If no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="57067-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57067-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="57067-190">En el caso de una base de datos de SQLite, el atributo `[Timestamp]` de una propiedad de entidad definida como una matriz de bytes:</span><span class="sxs-lookup"><span data-stu-id="57067-190">For a SQLite database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="57067-191">Hace que la columna se incluya en las cláusulas WHERE de DELETE y UPDATE.</span><span class="sxs-lookup"><span data-stu-id="57067-191">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="57067-192">Se asigna a un tipo de columna BLOB.</span><span class="sxs-lookup"><span data-stu-id="57067-192">Maps to a BLOB column type.</span></span>

<span data-ttu-id="57067-193">Los desencadenadores de base de datos actualizan la columna RowVersion con una nueva matriz de bytes aleatoria cada vez que se actualiza una fila.</span><span class="sxs-lookup"><span data-stu-id="57067-193">Database triggers update the RowVersion column with a new random byte array whenever a row is updated.</span></span> <span data-ttu-id="57067-194">En un comando `Update` o `Delete`, la cláusula `Where` incluye el valor capturado de la columna RowVersion.</span><span class="sxs-lookup"><span data-stu-id="57067-194">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of the RowVersion column.</span></span> <span data-ttu-id="57067-195">Si la fila que se va a actualizar ha cambiado desde que se ha capturado:</span><span class="sxs-lookup"><span data-stu-id="57067-195">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="57067-196">El valor de la versión de fila actual no coincide con el valor capturado.</span><span class="sxs-lookup"><span data-stu-id="57067-196">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="57067-197">El comando `Update` o `Delete` no encuentran una fila porque la cláusula `Where` busca el valor de versión de la fila original.</span><span class="sxs-lookup"><span data-stu-id="57067-197">The `Update` or `Delete` command doesn't find a row because the `Where` clause looks for the original row version value.</span></span>
* <span data-ttu-id="57067-198">Se produce una excepción `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="57067-198">A `DbUpdateConcurrencyException` is thrown.</span></span>

---

### <a name="update-the-database"></a><span data-ttu-id="57067-199">Actualizar la base de datos</span><span class="sxs-lookup"><span data-stu-id="57067-199">Update the database</span></span>

<span data-ttu-id="57067-200">Agregar la propiedad `RowVersion` cambia el modelo de datos, lo que requiere una migración.</span><span class="sxs-lookup"><span data-stu-id="57067-200">Adding the `RowVersion` property changes the data model, which requires a migration.</span></span>

<span data-ttu-id="57067-201">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="57067-201">Build the project.</span></span> 

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="57067-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57067-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="57067-203">Ejecute el comando siguiente en la Consola del administrador de paquetes:</span><span class="sxs-lookup"><span data-stu-id="57067-203">Run the following command in the PMC:</span></span>

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="57067-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57067-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="57067-205">Ejecute el comando siguiente en un terminal:</span><span class="sxs-lookup"><span data-stu-id="57067-205">Run the following command in a terminal:</span></span>

  ```console
  dotnet ef migrations add RowVersion
  ```

---

<span data-ttu-id="57067-206">Este comando:</span><span class="sxs-lookup"><span data-stu-id="57067-206">This command:</span></span>

* <span data-ttu-id="57067-207">Crea el archivo de migración *Migrations/{marca de tiempo}_RowVersion.cs*.</span><span class="sxs-lookup"><span data-stu-id="57067-207">Creates the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="57067-208">Actualizan el archivo *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="57067-208">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="57067-209">La actualización agrega el siguiente código resaltado al método `BuildModel`:</span><span class="sxs-lookup"><span data-stu-id="57067-209">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="57067-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57067-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="57067-211">Ejecute el comando siguiente en la Consola del administrador de paquetes:</span><span class="sxs-lookup"><span data-stu-id="57067-211">Run the following command in the PMC:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="57067-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57067-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="57067-213">Abra el archivo `Migrations/<timestamp>_RowVersion.cs` y agregue el código resaltado:</span><span class="sxs-lookup"><span data-stu-id="57067-213">Open the `Migrations/<timestamp>_RowVersion.cs` file and add the highlighted code:</span></span>

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  <span data-ttu-id="57067-214">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="57067-214">The preceding code:</span></span>

  * <span data-ttu-id="57067-215">Actualiza las filas existentes con valores de blob aleatorios.</span><span class="sxs-lookup"><span data-stu-id="57067-215">Updates existing rows with random blob values.</span></span>
  * <span data-ttu-id="57067-216">Agrega desencadenadores de base de datos que establecen la columna RowVersion en un valor de blob aleatorio cada vez que se actualiza una fila.</span><span class="sxs-lookup"><span data-stu-id="57067-216">Adds database triggers that set the RowVersion column to a random blob value whenever a row is updated.</span></span>

* <span data-ttu-id="57067-217">Ejecute el comando siguiente en un terminal:</span><span class="sxs-lookup"><span data-stu-id="57067-217">Run the following command in a terminal:</span></span>

  ```console
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a><span data-ttu-id="57067-218">Scaffolding de las páginas Department</span><span class="sxs-lookup"><span data-stu-id="57067-218">Scaffold Department pages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="57067-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57067-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="57067-220">Siga las instrucciones de [Scaffolding de las páginas Student](xref:data/ef-rp/intro#scaffold-student-pages) con las siguientes excepciones:</span><span class="sxs-lookup"><span data-stu-id="57067-220">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

* <span data-ttu-id="57067-221">Cree una carpeta *Pages/Departments*.</span><span class="sxs-lookup"><span data-stu-id="57067-221">Create a *Pages/Departments* folder.</span></span>  
* <span data-ttu-id="57067-222">Use `Department` para la clase del modelo.</span><span class="sxs-lookup"><span data-stu-id="57067-222">Use `Department` for the model class.</span></span>
  * <span data-ttu-id="57067-223">Use la clase de contexto existente en lugar de crear una.</span><span class="sxs-lookup"><span data-stu-id="57067-223">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="57067-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57067-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="57067-225">Cree una carpeta *Pages/Departments*.</span><span class="sxs-lookup"><span data-stu-id="57067-225">Create a *Pages/Departments* folder.</span></span>

* <span data-ttu-id="57067-226">Ejecute el comando siguiente para aplicar scaffolding a las páginas Department.</span><span class="sxs-lookup"><span data-stu-id="57067-226">Run the following command to scaffold the Department pages.</span></span>

  <span data-ttu-id="57067-227">**En Windows:**</span><span class="sxs-lookup"><span data-stu-id="57067-227">**On Windows:**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  <span data-ttu-id="57067-228">**En Linux o macOS:**</span><span class="sxs-lookup"><span data-stu-id="57067-228">**On Linux or macOS:**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="57067-229">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="57067-229">Build the project.</span></span>

## <a name="update-the-index-page"></a><span data-ttu-id="57067-230">Actualización de la página Index</span><span class="sxs-lookup"><span data-stu-id="57067-230">Update the Index page</span></span>

<span data-ttu-id="57067-231">La herramienta de scaffolding ha creado una columna `RowVersion` para la página de índice, pero ese campo no se debería mostrar en una aplicación de producción.</span><span class="sxs-lookup"><span data-stu-id="57067-231">The scaffolding tool created a `RowVersion` column for the Index page, but that field wouldn't be displayed in a production app.</span></span> <span data-ttu-id="57067-232">En este tutorial, se muestra el último byte de `RowVersion` para ayudar a entender el funcionamiento de la simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="57067-232">In this tutorial, the last byte of the `RowVersion` is displayed to help show how concurrency handling works.</span></span> <span data-ttu-id="57067-233">No se garantiza que el último byte sea único por si mismo.</span><span class="sxs-lookup"><span data-stu-id="57067-233">The last byte isn't guaranteed to be unique by itself.</span></span>

<span data-ttu-id="57067-234">Actualice la página *Pages\Departments\Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="57067-234">Update *Pages\Departments\Index.cshtml* page:</span></span>

* <span data-ttu-id="57067-235">Reemplace Index por Departments.</span><span class="sxs-lookup"><span data-stu-id="57067-235">Replace Index with Departments.</span></span>
* <span data-ttu-id="57067-236">Cambie el código que contiene `RowVersion` para mostrar solo el último byte de la matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="57067-236">Change the code containing `RowVersion` to show just the last byte of the byte array.</span></span>
* <span data-ttu-id="57067-237">Reemplace FirstMidName por FullName.</span><span class="sxs-lookup"><span data-stu-id="57067-237">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="57067-238">En el código siguiente se muestra la página actualizada:</span><span class="sxs-lookup"><span data-stu-id="57067-238">The following code shows the updated page:</span></span>

[!code-html[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a><span data-ttu-id="57067-239">Actualizar el modelo de la página Edit</span><span class="sxs-lookup"><span data-stu-id="57067-239">Update the Edit page model</span></span>

<span data-ttu-id="57067-240">Actualice *Pages\Departments\Edit.cshtml.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="57067-240">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

<span data-ttu-id="57067-241">[OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) se actualiza con el valor `rowVersion` de la entidad cuando se capturó en el método `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="57067-241">The [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity when it was fetched in the `OnGet` method.</span></span> <span data-ttu-id="57067-242">EF Core genera un comando UPDATE de SQL con una cláusula WHERE que contiene el valor `RowVersion` original.</span><span class="sxs-lookup"><span data-stu-id="57067-242">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="57067-243">Si no hay ninguna fila afectada por el comando UPDATE (ninguna fila tiene el valor `RowVersion` original), se produce una excepción `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="57067-243">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

<span data-ttu-id="57067-244">En el código resaltado anterior:</span><span class="sxs-lookup"><span data-stu-id="57067-244">In the preceding highlighted code:</span></span>

* <span data-ttu-id="57067-245">El valor de `Department.RowVersion` es lo que estaba en la entidad cuando se capturó originalmente en la solicitud Get para la página Edit.</span><span class="sxs-lookup"><span data-stu-id="57067-245">The value in `Department.RowVersion` is what was in the entity when it was originally fetched in the Get request for the Edit page.</span></span> <span data-ttu-id="57067-246">El valor se proporciona al método `OnPost` mediante un campo oculto en la página de Razor que muestra la entidad que se va a editar.</span><span class="sxs-lookup"><span data-stu-id="57067-246">The value is provided to the `OnPost` method by a hidden field in the Razor page that displays the entity to be edited.</span></span> <span data-ttu-id="57067-247">El enlazador de modelos copia el valor del campo oculto en `Department.RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="57067-247">The hidden field value is copied to `Department.RowVersion` by the model binder.</span></span>
* <span data-ttu-id="57067-248">`OriginalValue` es lo que EF Core usará en la cláusula Where.</span><span class="sxs-lookup"><span data-stu-id="57067-248">`OriginalValue` is what EF Core will use in the Where clause.</span></span> <span data-ttu-id="57067-249">Antes de que se ejecute la línea de código resaltada, `OriginalValue` tiene el valor que estaba en la base de datos cuando se ha llamado a `FirstOrDefaultAsync` en este método, lo que podría ser diferente de lo que se mostraba en la página Edit.</span><span class="sxs-lookup"><span data-stu-id="57067-249">Before the highlighted line of code executes, `OriginalValue` has the value that was in the database when `FirstOrDefaultAsync` was called in this method, which might be different from what was displayed on the Edit page.</span></span>
* <span data-ttu-id="57067-250">El código resaltado garantiza que EF Core usa el valor `RowVersion` original de la entidad `Department` mostrada en la cláusula Where de la instrucción UPDATE de SQL.</span><span class="sxs-lookup"><span data-stu-id="57067-250">The highlighted code makes sure that EF Core uses the original `RowVersion` value from the displayed `Department` entity in the SQL UPDATE statement's Where clause.</span></span>

<span data-ttu-id="57067-251">Cuando se produce un error de simultaneidad, el código resaltado siguiente obtiene los valores de cliente (los valores publicados para este método) y los valores de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="57067-251">When a concurrency error happens, the following highlighted code gets the client values (the values posted to this method) and the database values.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

<span data-ttu-id="57067-252">En el código siguiente se agrega un mensaje de error personalizado para cada columna que tiene valores de la base de datos diferentes a los publicados en `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="57067-252">The following code adds a custom error message for each column that has database values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

<span data-ttu-id="57067-253">En el código resaltado siguiente se establece el valor `RowVersion` en el nuevo valor recuperado de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="57067-253">The following highlighted code sets the `RowVersion` value to the new value retrieved from the database.</span></span> <span data-ttu-id="57067-254">La próxima vez que el usuario haga clic en **Save**, solo se detectarán los errores de simultaneidad que se produzcan desde la última visualización de la página Edit.</span><span class="sxs-lookup"><span data-stu-id="57067-254">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

<span data-ttu-id="57067-255">La instrucción `ModelState.Remove` es necesaria porque `ModelState` tiene el valor `RowVersion` antiguo.</span><span class="sxs-lookup"><span data-stu-id="57067-255">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="57067-256">En la página de Razor, el valor `ModelState` de un campo tiene prioridad sobre los valores de propiedad de modelo cuando ambos están presentes.</span><span class="sxs-lookup"><span data-stu-id="57067-256">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

### <a name="update-the-razor-page"></a><span data-ttu-id="57067-257">Actualización de la página de Razor</span><span class="sxs-lookup"><span data-stu-id="57067-257">Update the Razor page</span></span>

<span data-ttu-id="57067-258">Actualice *Pages/Departments/Edit.cshtml* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="57067-258">Update *Pages/Departments/Edit.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="57067-259">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="57067-259">The preceding code:</span></span>

* <span data-ttu-id="57067-260">Se actualiza la directiva `page` de `@page` a `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="57067-260">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="57067-261">Agrega una versión de fila oculta.</span><span class="sxs-lookup"><span data-stu-id="57067-261">Adds a hidden row version.</span></span> <span data-ttu-id="57067-262">Se debe agregar `RowVersion` para que la devolución enlace el valor.</span><span class="sxs-lookup"><span data-stu-id="57067-262">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="57067-263">Muestra el último byte de `RowVersion` para fines de depuración.</span><span class="sxs-lookup"><span data-stu-id="57067-263">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="57067-264">Reemplaza `ViewData` con `InstructorNameSL` fuertemente tipadas.</span><span class="sxs-lookup"><span data-stu-id="57067-264">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

### <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="57067-265">Comprobar los conflictos de simultaneidad con la página Edit</span><span class="sxs-lookup"><span data-stu-id="57067-265">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="57067-266">Abra dos instancias de exploradores de Edit en el departamento de inglés:</span><span class="sxs-lookup"><span data-stu-id="57067-266">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="57067-267">Ejecute la aplicación y seleccione Departments.</span><span class="sxs-lookup"><span data-stu-id="57067-267">Run the app and select Departments.</span></span>
* <span data-ttu-id="57067-268">Haga clic con el botón derecho en el hipervínculo **Edit** del departamento de inglés y seleccione **Abrir en nueva pestaña**.</span><span class="sxs-lookup"><span data-stu-id="57067-268">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="57067-269">En la primera pestaña, haga clic en el hipervínculo **Edit** del departamento de inglés.</span><span class="sxs-lookup"><span data-stu-id="57067-269">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="57067-270">Las dos pestañas del explorador muestran la misma información.</span><span class="sxs-lookup"><span data-stu-id="57067-270">The two browser tabs display the same information.</span></span>

<span data-ttu-id="57067-271">Cambie el nombre en la primera pestaña del explorador y haga clic en **Save**.</span><span class="sxs-lookup"><span data-stu-id="57067-271">Change the name in the first browser tab and click **Save**.</span></span>

![Página 1 de Department Edit después del cambio](concurrency/_static/edit-after-change-130.png)

<span data-ttu-id="57067-273">El explorador muestra la página de índice con el valor modificado y el indicador rowVersion actualizado.</span><span class="sxs-lookup"><span data-stu-id="57067-273">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="57067-274">Tenga en cuenta el indicador rowVersion actualizado, que se muestra en el segundo postback en la otra pestaña.</span><span class="sxs-lookup"><span data-stu-id="57067-274">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="57067-275">Cambie otro campo en la segunda pestaña del explorador.</span><span class="sxs-lookup"><span data-stu-id="57067-275">Change a different field in the second browser tab.</span></span>

![Página 2 de Department Edit después del cambio](concurrency/_static/edit-after-change-230.png)

<span data-ttu-id="57067-277">Haga clic en **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="57067-277">Click **Save**.</span></span> <span data-ttu-id="57067-278">Verá mensajes de error para todos los campos que no coinciden con los valores de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="57067-278">You see error messages for all fields that don't match the database values:</span></span>

![Mensaje de error de la página Department Edit](concurrency/_static/edit-error30.png)

<span data-ttu-id="57067-280">Esta ventana del explorador no planeaba cambiar el campo Name.</span><span class="sxs-lookup"><span data-stu-id="57067-280">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="57067-281">Copie y pegue el valor actual (Languages) en el campo Name.</span><span class="sxs-lookup"><span data-stu-id="57067-281">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="57067-282">Presione TAB para salir del campo. La validación del lado cliente quita el mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="57067-282">Tab out. Client-side validation removes the error message.</span></span>

<span data-ttu-id="57067-283">Vuelva a hacer clic en **Save**.</span><span class="sxs-lookup"><span data-stu-id="57067-283">Click **Save** again.</span></span> <span data-ttu-id="57067-284">Se guarda el valor especificado en la segunda pestaña del explorador.</span><span class="sxs-lookup"><span data-stu-id="57067-284">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="57067-285">Verá los valores guardados en la página de índice.</span><span class="sxs-lookup"><span data-stu-id="57067-285">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="57067-286">Actualizar la página Delete</span><span class="sxs-lookup"><span data-stu-id="57067-286">Update the Delete page</span></span>

<span data-ttu-id="57067-287">Actualice *Pages/Departments/Delete.cshtml.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="57067-287">Update *Pages/Departments/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="57067-288">La página Delete detecta los conflictos de simultaneidad cuando la entidad ha cambiado después de que se capturase.</span><span class="sxs-lookup"><span data-stu-id="57067-288">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="57067-289">`Department.RowVersion` es la versión de fila cuando se capturó la entidad.</span><span class="sxs-lookup"><span data-stu-id="57067-289">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="57067-290">Cuando EF Core crea el comando DELETE de SQL, incluye una cláusula WHERE con `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="57067-290">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="57067-291">Si el comando DELETE de SQL tiene como resultado cero filas afectadas:</span><span class="sxs-lookup"><span data-stu-id="57067-291">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="57067-292">El valor `RowVersion` del comando DELETE de SQL no coincide con el valor `RowVersion` de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="57067-292">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the database.</span></span>
* <span data-ttu-id="57067-293">Se produce una excepción DbUpdateConcurrencyException.</span><span class="sxs-lookup"><span data-stu-id="57067-293">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="57067-294">Se llama a `OnGetAsync` con el `concurrencyError`.</span><span class="sxs-lookup"><span data-stu-id="57067-294">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-razor-page"></a><span data-ttu-id="57067-295">Actualización la página de Razor Delete</span><span class="sxs-lookup"><span data-stu-id="57067-295">Update the Delete Razor page</span></span>

<span data-ttu-id="57067-296">Actualice *Pages/Departments/Delete.cshtml* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="57067-296">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="57067-297">En el código anterior se realizan los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="57067-297">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="57067-298">Se actualiza la directiva `page` de `@page` a `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="57067-298">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="57067-299">Se agrega un mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="57067-299">Adds an error message.</span></span>
* <span data-ttu-id="57067-300">Se reemplaza FirstMidName por FullName en el campo **Administrator**.</span><span class="sxs-lookup"><span data-stu-id="57067-300">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="57067-301">Se cambia `RowVersion` para que muestre el último byte.</span><span class="sxs-lookup"><span data-stu-id="57067-301">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="57067-302">Agrega una versión de fila oculta.</span><span class="sxs-lookup"><span data-stu-id="57067-302">Adds a hidden row version.</span></span> <span data-ttu-id="57067-303">Se debe agregar `RowVersion` para que la devolución de postgit enlace el valor.</span><span class="sxs-lookup"><span data-stu-id="57067-303">`RowVersion` must be added so postgit add back binds the value.</span></span>

### <a name="test-concurrency-conflicts"></a><span data-ttu-id="57067-304">Prueba los conflictos de simultaneidad</span><span class="sxs-lookup"><span data-stu-id="57067-304">Test concurrency conflicts</span></span>

<span data-ttu-id="57067-305">Cree un departamento de prueba.</span><span class="sxs-lookup"><span data-stu-id="57067-305">Create a test department.</span></span>

<span data-ttu-id="57067-306">Abra dos instancias de exploradores de Delete en el departamento de prueba:</span><span class="sxs-lookup"><span data-stu-id="57067-306">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="57067-307">Ejecute la aplicación y seleccione Departments.</span><span class="sxs-lookup"><span data-stu-id="57067-307">Run the app and select Departments.</span></span>
* <span data-ttu-id="57067-308">Haga clic con el botón derecho en el hipervínculo **Delete** del departamento de prueba y seleccione **Abrir en nueva pestaña**.</span><span class="sxs-lookup"><span data-stu-id="57067-308">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="57067-309">Haga clic en el hipervínculo **Edit** del departamento de prueba.</span><span class="sxs-lookup"><span data-stu-id="57067-309">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="57067-310">Las dos pestañas del explorador muestran la misma información.</span><span class="sxs-lookup"><span data-stu-id="57067-310">The two browser tabs display the same information.</span></span>

<span data-ttu-id="57067-311">Cambie el presupuesto en la primera pestaña del explorador y haga clic en **Save**.</span><span class="sxs-lookup"><span data-stu-id="57067-311">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="57067-312">El explorador muestra la página de índice con el valor modificado y el indicador rowVersion actualizado.</span><span class="sxs-lookup"><span data-stu-id="57067-312">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="57067-313">Tenga en cuenta el indicador rowVersion actualizado, que se muestra en el segundo postback en la otra pestaña.</span><span class="sxs-lookup"><span data-stu-id="57067-313">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="57067-314">Elimine el departamento de prueba de la segunda pestaña. Se mostrará un error de simultaneidad con los valores actuales de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="57067-314">Delete the test department from the second tab. A concurrency error is display with the current values from the database.</span></span> <span data-ttu-id="57067-315">Al hacer clic en **Delete** se elimina la entidad, a menos que se haya actualizado `RowVersion`. El departamento se ha eliminado.</span><span class="sxs-lookup"><span data-stu-id="57067-315">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.department has been deleted.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="57067-316">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="57067-316">Additional resources</span></span>

* [<span data-ttu-id="57067-317">Tokens de simultaneidad en EF Core</span><span class="sxs-lookup"><span data-stu-id="57067-317">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="57067-318">Controlar la simultaneidad en EF Core</span><span class="sxs-lookup"><span data-stu-id="57067-318">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="57067-319">Depuración del código fuente de ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="57067-319">Debugging ASP.NET Core 2.x source</span></span>](https://github.com/aspnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a><span data-ttu-id="57067-320">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="57067-320">Next steps</span></span>

<span data-ttu-id="57067-321">Este es el último tutorial de la serie.</span><span class="sxs-lookup"><span data-stu-id="57067-321">This is the last tutorial in the series.</span></span> <span data-ttu-id="57067-322">En la [versión para MVC de esta serie de tutoriales](xref:data/ef-mvc/index) se describen temas adicionales.</span><span class="sxs-lookup"><span data-stu-id="57067-322">Additional topics are covered in the [MVC version of this tutorial series](xref:data/ef-mvc/index).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="57067-323">Tutorial anterior</span><span class="sxs-lookup"><span data-stu-id="57067-323">Previous tutorial</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="57067-324">Este tutorial muestra cómo tratar los conflictos cuando varios usuarios actualizan una entidad de forma simultánea (al mismo tiempo).</span><span class="sxs-lookup"><span data-stu-id="57067-324">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span> <span data-ttu-id="57067-325">Si experimenta problemas que no puede resolver, [descargue o vea la aplicación completada](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="57067-325">If you run into problems you can't solve, [download or view the completed app.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="57067-326">[Instrucciones de descarga](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="57067-326">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="57067-327">Conflictos de simultaneidad</span><span class="sxs-lookup"><span data-stu-id="57067-327">Concurrency conflicts</span></span>

<span data-ttu-id="57067-328">Un conflicto de simultaneidad se produce cuando:</span><span class="sxs-lookup"><span data-stu-id="57067-328">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="57067-329">Un usuario va a la página de edición de una entidad.</span><span class="sxs-lookup"><span data-stu-id="57067-329">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="57067-330">Otro usuario actualiza la misma entidad antes de que el cambio del primer usuario se escriba en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="57067-330">Another user updates the same entity before the first user's change is written to the DB.</span></span>

<span data-ttu-id="57067-331">Si no está habilitada la detección de simultaneidad, cuando se produzcan actualizaciones simultáneas:</span><span class="sxs-lookup"><span data-stu-id="57067-331">If concurrency detection isn't enabled, when concurrent updates occur:</span></span>

* <span data-ttu-id="57067-332">Prevalece la última actualización.</span><span class="sxs-lookup"><span data-stu-id="57067-332">The last update wins.</span></span> <span data-ttu-id="57067-333">Es decir, los últimos valores de actualización se guardan en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="57067-333">That is, the last update values are saved to the DB.</span></span>
* <span data-ttu-id="57067-334">La primera de las actualizaciones actuales se pierde.</span><span class="sxs-lookup"><span data-stu-id="57067-334">The first of the current updates are lost.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="57067-335">Simultaneidad optimista</span><span class="sxs-lookup"><span data-stu-id="57067-335">Optimistic concurrency</span></span>

<span data-ttu-id="57067-336">La simultaneidad optimista permite que se produzcan conflictos de simultaneidad y luego reacciona correctamente si ocurren.</span><span class="sxs-lookup"><span data-stu-id="57067-336">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="57067-337">Por ejemplo, Jane visita la página de edición de Department y cambia el presupuesto para el departamento de inglés de 350.000,00 a 0,00 USD.</span><span class="sxs-lookup"><span data-stu-id="57067-337">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Cambiar el presupuesto a 0](concurrency/_static/change-budget.png)

<span data-ttu-id="57067-339">Antes de que Jane haga clic en **Save**, John visita la misma página y cambia el campo Start Date de 9/1/2007 a 9/1/2013.</span><span class="sxs-lookup"><span data-stu-id="57067-339">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Cambiar la fecha de inicio a 2013](concurrency/_static/change-date.png)

<span data-ttu-id="57067-341">Jane hace clic en **Save** primero y ve su cambio cuando el explorador muestra la página de índice.</span><span class="sxs-lookup"><span data-stu-id="57067-341">Jane clicks **Save** first and sees her change when the browser displays the Index page.</span></span>

![Presupuesto cambiado a cero](concurrency/_static/budget-zero.png)

<span data-ttu-id="57067-343">John hace clic en **Save** en una página Edit que sigue mostrando un presupuesto de 350.000,00 USD.</span><span class="sxs-lookup"><span data-stu-id="57067-343">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="57067-344">Lo que sucede después viene determinado por cómo controla los conflictos de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="57067-344">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="57067-345">La simultaneidad optimista incluye las siguientes opciones:</span><span class="sxs-lookup"><span data-stu-id="57067-345">Optimistic concurrency includes the following options:</span></span>

* <span data-ttu-id="57067-346">Puede realizar un seguimiento de la propiedad que ha modificado un usuario y actualizar solo las columnas correspondientes de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="57067-346">You can keep track of which property a user has modified and update only the corresponding columns in the DB.</span></span>

  <span data-ttu-id="57067-347">En el escenario, no se perderá ningún dato.</span><span class="sxs-lookup"><span data-stu-id="57067-347">In the scenario, no data would be lost.</span></span> <span data-ttu-id="57067-348">Los dos usuarios actualizaron diferentes propiedades.</span><span class="sxs-lookup"><span data-stu-id="57067-348">Different properties were updated by the two users.</span></span> <span data-ttu-id="57067-349">La próxima vez que un usuario examine el departamento de inglés, verá los cambios tanto de Jane como de John.</span><span class="sxs-lookup"><span data-stu-id="57067-349">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="57067-350">Este método de actualización puede reducir el número de conflictos que pueden dar lugar a una pérdida de datos.</span><span class="sxs-lookup"><span data-stu-id="57067-350">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="57067-351">Este enfoque:</span><span class="sxs-lookup"><span data-stu-id="57067-351">This approach:</span></span>
 
  * <span data-ttu-id="57067-352">No puede evitar la pérdida de datos si se realizan cambios paralelos a la misma propiedad.</span><span class="sxs-lookup"><span data-stu-id="57067-352">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="57067-353">Por lo general, no es práctico en una aplicación web.</span><span class="sxs-lookup"><span data-stu-id="57067-353">Is generally not practical in a web app.</span></span> <span data-ttu-id="57067-354">Requiere mantener un estado significativo para realizar un seguimiento de todos los valores capturados y nuevos.</span><span class="sxs-lookup"><span data-stu-id="57067-354">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="57067-355">El mantenimiento de grandes cantidades de estado puede afectar al rendimiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="57067-355">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="57067-356">Puede aumentar la complejidad de las aplicaciones en comparación con la detección de simultaneidad en una entidad.</span><span class="sxs-lookup"><span data-stu-id="57067-356">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="57067-357">Puede permitir que los cambios de John sobrescriban los cambios de Jane.</span><span class="sxs-lookup"><span data-stu-id="57067-357">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="57067-358">La próxima vez que un usuario examine el departamento de inglés, verá 9/1/2013 y el valor de 350.000,00 USD capturado.</span><span class="sxs-lookup"><span data-stu-id="57067-358">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="57067-359">Este enfoque se denomina un escenario de *Prevalece el cliente* o *Prevalece el último*.</span><span class="sxs-lookup"><span data-stu-id="57067-359">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="57067-360">(Todos los valores del cliente tienen prioridad sobre lo que aparece en el almacén de datos). Si no hace ninguna codificación para el control de la simultaneidad, Prevalece el cliente se realizará automáticamente.</span><span class="sxs-lookup"><span data-stu-id="57067-360">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="57067-361">Puede evitar que el cambio de John se actualice en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="57067-361">You can prevent John's change from being updated in the DB.</span></span> <span data-ttu-id="57067-362">Normalmente, la aplicación podría:</span><span class="sxs-lookup"><span data-stu-id="57067-362">Typically, the app would:</span></span>

  * <span data-ttu-id="57067-363">Mostrar un mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="57067-363">Display an error message.</span></span>
  * <span data-ttu-id="57067-364">Mostrar el estado actual de los datos.</span><span class="sxs-lookup"><span data-stu-id="57067-364">Show the current state of the data.</span></span>
  * <span data-ttu-id="57067-365">Permitir al usuario volver a aplicar los cambios.</span><span class="sxs-lookup"><span data-stu-id="57067-365">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="57067-366">Esto se denomina un escenario de *Prevalece el almacén*.</span><span class="sxs-lookup"><span data-stu-id="57067-366">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="57067-367">(Los valores del almacén de datos tienen prioridad sobre los valores enviados por el cliente). En este tutorial implementará el escenario de Prevalece el almacén.</span><span class="sxs-lookup"><span data-stu-id="57067-367">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="57067-368">Este método garantiza que ningún cambio se sobrescriba sin que se avise al usuario.</span><span class="sxs-lookup"><span data-stu-id="57067-368">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="handling-concurrency"></a><span data-ttu-id="57067-369">Administrar la simultaneidad</span><span class="sxs-lookup"><span data-stu-id="57067-369">Handling concurrency</span></span> 

<span data-ttu-id="57067-370">Cuando una propiedad se configura como un [token de simultaneidad](/ef/core/modeling/concurrency):</span><span class="sxs-lookup"><span data-stu-id="57067-370">When a property is configured as a [concurrency token](/ef/core/modeling/concurrency):</span></span>

* <span data-ttu-id="57067-371">EF Core comprueba que no se ha modificado la propiedad después de que se capturase.</span><span class="sxs-lookup"><span data-stu-id="57067-371">EF Core verifies that property has not been modified after it was fetched.</span></span> <span data-ttu-id="57067-372">La comprobación se produce cuando se llama a [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) o [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_).</span><span class="sxs-lookup"><span data-stu-id="57067-372">The check occurs when [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) or [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span>
* <span data-ttu-id="57067-373">Si se ha cambiado la propiedad después de haberla capturado, se produce una excepción [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="57067-373">If the property has been changed after it was fetched, a [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) is thrown.</span></span> 

<span data-ttu-id="57067-374">Deben configurarse el modelo de datos y la base de datos para que admitan producir una excepción `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="57067-374">The DB and data model must be configured to support throwing `DbUpdateConcurrencyException`.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-property"></a><span data-ttu-id="57067-375">Detectar conflictos de simultaneidad en una propiedad</span><span class="sxs-lookup"><span data-stu-id="57067-375">Detecting concurrency conflicts on a property</span></span>

<span data-ttu-id="57067-376">Se pueden detectar conflictos de simultaneidad en el nivel de propiedad con el atributo [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="57067-376">Concurrency conflicts can be detected at the property level with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) attribute.</span></span> <span data-ttu-id="57067-377">El atributo se puede aplicar a varias propiedades en el modelo.</span><span class="sxs-lookup"><span data-stu-id="57067-377">The attribute can be applied to multiple properties on the model.</span></span> <span data-ttu-id="57067-378">Para obtener más información, consulte [Anotaciones de datos: ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span><span class="sxs-lookup"><span data-stu-id="57067-378">For more information, see [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span></span>

<span data-ttu-id="57067-379">El atributo `[ConcurrencyCheck]` no se usa en este tutorial.</span><span class="sxs-lookup"><span data-stu-id="57067-379">The `[ConcurrencyCheck]` attribute isn't used in this tutorial.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-row"></a><span data-ttu-id="57067-380">Detectar conflictos de simultaneidad en una fila</span><span class="sxs-lookup"><span data-stu-id="57067-380">Detecting concurrency conflicts on a row</span></span>

<span data-ttu-id="57067-381">Para detectar conflictos de simultaneidad, se agrega al modelo una columna de seguimiento [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="57067-381">To detect concurrency conflicts, a [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) tracking column is added to the model.</span></span>  <span data-ttu-id="57067-382">`rowversion`:</span><span class="sxs-lookup"><span data-stu-id="57067-382">`rowversion` :</span></span>

* <span data-ttu-id="57067-383">Es específico de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="57067-383">Is SQL Server specific.</span></span> <span data-ttu-id="57067-384">Otras bases de datos podrían no proporcionar una característica similar.</span><span class="sxs-lookup"><span data-stu-id="57067-384">Other databases may not provide a similar feature.</span></span>
* <span data-ttu-id="57067-385">Se usa para determinar que no se ha cambiado una entidad desde que se capturó de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="57067-385">Is used to determine that an entity has not been changed since it was fetched from the DB.</span></span> 

<span data-ttu-id="57067-386">La base de datos genera un número `rowversion` secuencial que se incrementa cada vez que se actualiza la fila.</span><span class="sxs-lookup"><span data-stu-id="57067-386">The DB generates a sequential `rowversion` number that's incremented each time the row is updated.</span></span> <span data-ttu-id="57067-387">En un comando `Update` o `Delete`, la cláusula `Where` incluye el valor capturado de `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="57067-387">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of `rowversion`.</span></span> <span data-ttu-id="57067-388">Si la fila que se está actualizando ha cambiado:</span><span class="sxs-lookup"><span data-stu-id="57067-388">If the row being updated has changed:</span></span>

* <span data-ttu-id="57067-389">`rowversion` no coincide con el valor capturado.</span><span class="sxs-lookup"><span data-stu-id="57067-389">`rowversion` doesn't match the fetched value.</span></span>
* <span data-ttu-id="57067-390">Los comandos `Update` o `Delete` no encuentran una fila porque la cláusula `Where` incluye la `rowversion` capturada.</span><span class="sxs-lookup"><span data-stu-id="57067-390">The `Update` or `Delete` commands don't find a row because the `Where` clause includes the fetched `rowversion`.</span></span>
* <span data-ttu-id="57067-391">Se produce una excepción `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="57067-391">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="57067-392">En EF Core, cuando un comando `Update` o `Delete` no han actualizado ninguna fila, se produce una excepción de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="57067-392">In EF Core, when no rows have been updated by an `Update` or `Delete` command, a concurrency exception is thrown.</span></span>

### <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="57067-393">Agregar una propiedad de seguimiento a la entidad Department</span><span class="sxs-lookup"><span data-stu-id="57067-393">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="57067-394">En *Models/Department.cs*, agregue una propiedad de seguimiento denominada RowVersion:</span><span class="sxs-lookup"><span data-stu-id="57067-394">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="57067-395">El atributo [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) especifica que esta columna se incluye en la cláusula `Where` de los comandos `Update` y `Delete`.</span><span class="sxs-lookup"><span data-stu-id="57067-395">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute specifies that this column is included in the `Where` clause of `Update` and `Delete` commands.</span></span> <span data-ttu-id="57067-396">El atributo se denomina `Timestamp` porque las versiones anteriores de SQL Server usaban un tipo de datos `timestamp` antes de que el tipo `rowversion` de SQL lo sustituyera por otro.</span><span class="sxs-lookup"><span data-stu-id="57067-396">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` type replaced it.</span></span>

<span data-ttu-id="57067-397">La API fluida también puede especificar la propiedad de seguimiento:</span><span class="sxs-lookup"><span data-stu-id="57067-397">The fluent API can also specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

<span data-ttu-id="57067-398">El código siguiente muestra una parte del T-SQL generado por EF Core cuando se actualiza el nombre de Department:</span><span class="sxs-lookup"><span data-stu-id="57067-398">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

<span data-ttu-id="57067-399">El código resaltado anteriormente muestra la cláusula `WHERE` que contiene `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="57067-399">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="57067-400">Si la base de datos `RowVersion` no es igual al parámetro `RowVersion` (`@p2`), no se ha actualizado ninguna fila.</span><span class="sxs-lookup"><span data-stu-id="57067-400">If the DB `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="57067-401">El código resaltado a continuación muestra el T-SQL que comprueba que se actualizó exactamente una fila:</span><span class="sxs-lookup"><span data-stu-id="57067-401">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

<span data-ttu-id="57067-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) devuelve el número de filas afectadas por la última instrucción.</span><span class="sxs-lookup"><span data-stu-id="57067-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="57067-403">Si no se actualiza ninguna fila, EF Core produce una excepción `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="57067-403">In no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="57067-404">Puede ver el T-SQL que genera EF Core en la ventana de salida de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="57067-404">You can see the T-SQL EF Core generates in the output window of Visual Studio.</span></span>

### <a name="update-the-db"></a><span data-ttu-id="57067-405">Actualizar la base de datos</span><span class="sxs-lookup"><span data-stu-id="57067-405">Update the DB</span></span>

<span data-ttu-id="57067-406">Agregar la propiedad `RowVersion` cambia el modelo de base de datos, lo que requiere una migración.</span><span class="sxs-lookup"><span data-stu-id="57067-406">Adding the `RowVersion` property changes the DB model, which requires a migration.</span></span>

<span data-ttu-id="57067-407">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="57067-407">Build the project.</span></span> <span data-ttu-id="57067-408">Escriba lo siguiente en una ventana de comandos:</span><span class="sxs-lookup"><span data-stu-id="57067-408">Enter the following in a command window:</span></span>

```console
dotnet ef migrations add RowVersion
dotnet ef database update
```

<span data-ttu-id="57067-409">Los comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="57067-409">The preceding commands:</span></span>

* <span data-ttu-id="57067-410">Agregan el archivo de migración *Migrations/{time stamp}_RowVersion.cs*.</span><span class="sxs-lookup"><span data-stu-id="57067-410">Adds the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="57067-411">Actualizan el archivo *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="57067-411">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="57067-412">La actualización agrega el siguiente código resaltado al método `BuildModel`:</span><span class="sxs-lookup"><span data-stu-id="57067-412">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* <span data-ttu-id="57067-413">Ejecutan las migraciones para actualizar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="57067-413">Runs migrations to update the DB.</span></span>

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a><span data-ttu-id="57067-414">Aplicar la técnica scaffolding al modelo Departments</span><span class="sxs-lookup"><span data-stu-id="57067-414">Scaffold the Departments model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="57067-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57067-415">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="57067-416">Siga las instrucciones que encontrará en [Aplicación de scaffolding al modelo de alumnos](xref:data/ef-rp/intro#scaffold-student-pages) y use `Department` para la clase de modelo.</span><span class="sxs-lookup"><span data-stu-id="57067-416">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Department` for the model class.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="57067-417">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57067-417">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="57067-418">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="57067-418">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="57067-419">El comando anterior aplica scaffolding al modelo `Department`.</span><span class="sxs-lookup"><span data-stu-id="57067-419">The preceding command scaffolds the `Department` model.</span></span> <span data-ttu-id="57067-420">Abra el proyecto en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="57067-420">Open the project in Visual Studio.</span></span>

<span data-ttu-id="57067-421">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="57067-421">Build the project.</span></span>

### <a name="update-the-departments-index-page"></a><span data-ttu-id="57067-422">Actualizar la página de índice de Departments</span><span class="sxs-lookup"><span data-stu-id="57067-422">Update the Departments Index page</span></span>

<span data-ttu-id="57067-423">El motor de scaffolding creó una columna `RowVersion` para la página de índice, pero ese campo no debería mostrarse.</span><span class="sxs-lookup"><span data-stu-id="57067-423">The scaffolding engine created a `RowVersion` column for the Index page, but that field shouldn't be displayed.</span></span> <span data-ttu-id="57067-424">En este tutorial, el último byte de la `RowVersion` se muestra para ayudar a entender la simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="57067-424">In this tutorial, the last byte of the `RowVersion` is displayed to help understand concurrency.</span></span> <span data-ttu-id="57067-425">No se garantiza que el último byte sea único.</span><span class="sxs-lookup"><span data-stu-id="57067-425">The last byte isn't guaranteed to be unique.</span></span> <span data-ttu-id="57067-426">Una aplicación real no mostraría `RowVersion` ni el último byte de `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="57067-426">A real app wouldn't display `RowVersion` or the last byte of `RowVersion`.</span></span>

<span data-ttu-id="57067-427">Actualice la página Index:</span><span class="sxs-lookup"><span data-stu-id="57067-427">Update the Index page:</span></span>

* <span data-ttu-id="57067-428">Reemplace Index por Departments.</span><span class="sxs-lookup"><span data-stu-id="57067-428">Replace Index with Departments.</span></span>
* <span data-ttu-id="57067-429">Reemplace el marcado que contiene `RowVersion` por el último byte de `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="57067-429">Replace the markup containing `RowVersion` with the last byte of `RowVersion`.</span></span>
* <span data-ttu-id="57067-430">Reemplace FirstMidName por FullName.</span><span class="sxs-lookup"><span data-stu-id="57067-430">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="57067-431">El marcado siguiente muestra la página actualizada:</span><span class="sxs-lookup"><span data-stu-id="57067-431">The following markup shows the updated page:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a><span data-ttu-id="57067-432">Actualizar el modelo de la página Edit</span><span class="sxs-lookup"><span data-stu-id="57067-432">Update the Edit page model</span></span>

<span data-ttu-id="57067-433">Actualice *Pages\Departments\Edit.cshtml.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="57067-433">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="57067-434">Para detectar un problema de simultaneidad, el [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) se actualiza con el valor `rowVersion` de la entidad de la que se capturó.</span><span class="sxs-lookup"><span data-stu-id="57067-434">To detect a concurrency issue, the [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity it was fetched.</span></span> <span data-ttu-id="57067-435">EF Core genera un comando UPDATE de SQL con una cláusula WHERE que contiene el valor `RowVersion` original.</span><span class="sxs-lookup"><span data-stu-id="57067-435">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="57067-436">Si no hay ninguna fila afectada por el comando UPDATE (ninguna fila tiene el valor `RowVersion` original), se produce una excepción `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="57067-436">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

<span data-ttu-id="57067-437">En el código anterior, `Department.RowVersion` es el valor cuando se capturó la entidad.</span><span class="sxs-lookup"><span data-stu-id="57067-437">In the preceding code, `Department.RowVersion` is the value when the entity was fetched.</span></span> <span data-ttu-id="57067-438">`OriginalValue` es el valor de la base de datos cuando se llamó a `FirstOrDefaultAsync` en este método.</span><span class="sxs-lookup"><span data-stu-id="57067-438">`OriginalValue` is the value in the DB when `FirstOrDefaultAsync` was called in this method.</span></span>

<span data-ttu-id="57067-439">El código siguiente obtiene los valores de cliente (es decir, los valores registrados en este método) y los valores de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="57067-439">The following code gets the client values (the values posted to this method) and the DB values:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

<span data-ttu-id="57067-440">El código siguiente agrega un mensaje de error personalizado para cada columna que tiene valores de la base de datos diferentes de lo que publicado en `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="57067-440">The following code adds a custom error message for each column that has DB values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

<span data-ttu-id="57067-441">El código resaltado a continuación establece el valor `RowVersion` para el nuevo valor recuperado de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="57067-441">The following highlighted code sets the `RowVersion` value to the new value retrieved from the DB.</span></span> <span data-ttu-id="57067-442">La próxima vez que el usuario haga clic en **Save**, solo se detectarán los errores de simultaneidad que se produzcan desde la última visualización de la página Edit.</span><span class="sxs-lookup"><span data-stu-id="57067-442">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

<span data-ttu-id="57067-443">La instrucción `ModelState.Remove` es necesaria porque `ModelState` tiene el valor `RowVersion` antiguo.</span><span class="sxs-lookup"><span data-stu-id="57067-443">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="57067-444">En la página de Razor, el valor `ModelState` de un campo tiene prioridad sobre los valores de propiedad de modelo cuando ambos están presentes.</span><span class="sxs-lookup"><span data-stu-id="57067-444">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="57067-445">Actualizar la página Edit</span><span class="sxs-lookup"><span data-stu-id="57067-445">Update the Edit page</span></span>

<span data-ttu-id="57067-446">Actualice *Pages/Departments/Edit.cshtml* con el siguiente marcado:</span><span class="sxs-lookup"><span data-stu-id="57067-446">Update *Pages/Departments/Edit.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="57067-447">El marcado anterior:</span><span class="sxs-lookup"><span data-stu-id="57067-447">The preceding markup:</span></span>

* <span data-ttu-id="57067-448">Actualiza la directiva `page` de `@page` a `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="57067-448">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="57067-449">Agrega una versión de fila oculta.</span><span class="sxs-lookup"><span data-stu-id="57067-449">Adds a hidden row version.</span></span> <span data-ttu-id="57067-450">Se debe agregar `RowVersion` para que la devolución enlace el valor.</span><span class="sxs-lookup"><span data-stu-id="57067-450">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="57067-451">Muestra el último byte de `RowVersion` para fines de depuración.</span><span class="sxs-lookup"><span data-stu-id="57067-451">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="57067-452">Reemplaza `ViewData` con `InstructorNameSL` fuertemente tipadas.</span><span class="sxs-lookup"><span data-stu-id="57067-452">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

## <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="57067-453">Comprobar los conflictos de simultaneidad con la página Edit</span><span class="sxs-lookup"><span data-stu-id="57067-453">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="57067-454">Abra dos instancias de exploradores de Edit en el departamento de inglés:</span><span class="sxs-lookup"><span data-stu-id="57067-454">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="57067-455">Ejecute la aplicación y seleccione Departments.</span><span class="sxs-lookup"><span data-stu-id="57067-455">Run the app and select Departments.</span></span>
* <span data-ttu-id="57067-456">Haga clic con el botón derecho en el hipervínculo **Edit** del departamento de inglés y seleccione **Abrir en nueva pestaña**.</span><span class="sxs-lookup"><span data-stu-id="57067-456">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="57067-457">En la primera pestaña, haga clic en el hipervínculo **Edit** del departamento de inglés.</span><span class="sxs-lookup"><span data-stu-id="57067-457">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="57067-458">Las dos pestañas del explorador muestran la misma información.</span><span class="sxs-lookup"><span data-stu-id="57067-458">The two browser tabs display the same information.</span></span>

<span data-ttu-id="57067-459">Cambie el nombre en la primera pestaña del explorador y haga clic en **Save**.</span><span class="sxs-lookup"><span data-stu-id="57067-459">Change the name in the first browser tab and click **Save**.</span></span>

![Página 1 de Department Edit después del cambio](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="57067-461">El explorador muestra la página de índice con el valor modificado y el indicador rowVersion actualizado.</span><span class="sxs-lookup"><span data-stu-id="57067-461">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="57067-462">Tenga en cuenta el indicador rowVersion actualizado, que se muestra en el segundo postback en la otra pestaña.</span><span class="sxs-lookup"><span data-stu-id="57067-462">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="57067-463">Cambie otro campo en la segunda pestaña del explorador.</span><span class="sxs-lookup"><span data-stu-id="57067-463">Change a different field in the second browser tab.</span></span>

![Página 2 de Department Edit después del cambio](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="57067-465">Haga clic en **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="57067-465">Click **Save**.</span></span> <span data-ttu-id="57067-466">Verá mensajes de error para todos los campos que no coinciden con los valores de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="57067-466">You see error messages for all fields that don't match the DB values:</span></span>

![Mensaje de error de la página Department Edit](concurrency/_static/edit-error.png)

<span data-ttu-id="57067-468">Esta ventana del explorador no planeaba cambiar el campo Name.</span><span class="sxs-lookup"><span data-stu-id="57067-468">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="57067-469">Copie y pegue el valor actual (Languages) en el campo Name.</span><span class="sxs-lookup"><span data-stu-id="57067-469">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="57067-470">Presione TAB para salir del campo. La validación del lado cliente quita el mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="57067-470">Tab out. Client-side validation removes the error message.</span></span>

![Mensaje de error de la página Department Edit](concurrency/_static/cv.png)

<span data-ttu-id="57067-472">Vuelva a hacer clic en **Save**.</span><span class="sxs-lookup"><span data-stu-id="57067-472">Click **Save** again.</span></span> <span data-ttu-id="57067-473">Se guarda el valor especificado en la segunda pestaña del explorador.</span><span class="sxs-lookup"><span data-stu-id="57067-473">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="57067-474">Verá los valores guardados en la página de índice.</span><span class="sxs-lookup"><span data-stu-id="57067-474">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="57067-475">Actualizar la página Delete</span><span class="sxs-lookup"><span data-stu-id="57067-475">Update the Delete page</span></span>

<span data-ttu-id="57067-476">Actualice el modelo de la página Delete con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="57067-476">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="57067-477">La página Delete detecta los conflictos de simultaneidad cuando la entidad ha cambiado después de que se capturase.</span><span class="sxs-lookup"><span data-stu-id="57067-477">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="57067-478">`Department.RowVersion` es la versión de fila cuando se capturó la entidad.</span><span class="sxs-lookup"><span data-stu-id="57067-478">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="57067-479">Cuando EF Core crea el comando DELETE de SQL, incluye una cláusula WHERE con `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="57067-479">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="57067-480">Si el comando DELETE de SQL tiene como resultado cero filas afectadas:</span><span class="sxs-lookup"><span data-stu-id="57067-480">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="57067-481">La `RowVersion` del comando DELETE de SQL no coincide con la `RowVersion` de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="57067-481">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the DB.</span></span>
* <span data-ttu-id="57067-482">Se produce una excepción DbUpdateConcurrencyException.</span><span class="sxs-lookup"><span data-stu-id="57067-482">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="57067-483">Se llama a `OnGetAsync` con el `concurrencyError`.</span><span class="sxs-lookup"><span data-stu-id="57067-483">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="57067-484">Actualizar la página Delete</span><span class="sxs-lookup"><span data-stu-id="57067-484">Update the Delete page</span></span>

<span data-ttu-id="57067-485">Actualice *Pages/Departments/Delete.cshtml* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="57067-485">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="57067-486">En el código anterior se realizan los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="57067-486">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="57067-487">Se actualiza la directiva `page` de `@page` a `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="57067-487">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="57067-488">Se agrega un mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="57067-488">Adds an error message.</span></span>
* <span data-ttu-id="57067-489">Se reemplaza FirstMidName por FullName en el campo **Administrator**.</span><span class="sxs-lookup"><span data-stu-id="57067-489">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="57067-490">Se cambia `RowVersion` para que muestre el último byte.</span><span class="sxs-lookup"><span data-stu-id="57067-490">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="57067-491">Se agrega una versión de fila oculta.</span><span class="sxs-lookup"><span data-stu-id="57067-491">Adds a hidden row version.</span></span> <span data-ttu-id="57067-492">Se debe agregar `RowVersion` para que la devolución enlace el valor.</span><span class="sxs-lookup"><span data-stu-id="57067-492">`RowVersion` must be added so post back binds the value.</span></span>

### <a name="test-concurrency-conflicts-with-the-delete-page"></a><span data-ttu-id="57067-493">Comprobar los conflictos de simultaneidad con la página Delete</span><span class="sxs-lookup"><span data-stu-id="57067-493">Test concurrency conflicts with the Delete page</span></span>

<span data-ttu-id="57067-494">Cree un departamento de prueba.</span><span class="sxs-lookup"><span data-stu-id="57067-494">Create a test department.</span></span>

<span data-ttu-id="57067-495">Abra dos instancias de exploradores de Delete en el departamento de prueba:</span><span class="sxs-lookup"><span data-stu-id="57067-495">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="57067-496">Ejecute la aplicación y seleccione Departments.</span><span class="sxs-lookup"><span data-stu-id="57067-496">Run the app and select Departments.</span></span>
* <span data-ttu-id="57067-497">Haga clic con el botón derecho en el hipervínculo **Delete** del departamento de prueba y seleccione **Abrir en nueva pestaña**.</span><span class="sxs-lookup"><span data-stu-id="57067-497">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="57067-498">Haga clic en el hipervínculo **Edit** del departamento de prueba.</span><span class="sxs-lookup"><span data-stu-id="57067-498">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="57067-499">Las dos pestañas del explorador muestran la misma información.</span><span class="sxs-lookup"><span data-stu-id="57067-499">The two browser tabs display the same information.</span></span>

<span data-ttu-id="57067-500">Cambie el presupuesto en la primera pestaña del explorador y haga clic en **Save**.</span><span class="sxs-lookup"><span data-stu-id="57067-500">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="57067-501">El explorador muestra la página de índice con el valor modificado y el indicador rowVersion actualizado.</span><span class="sxs-lookup"><span data-stu-id="57067-501">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="57067-502">Tenga en cuenta el indicador rowVersion actualizado, que se muestra en el segundo postback en la otra pestaña.</span><span class="sxs-lookup"><span data-stu-id="57067-502">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="57067-503">Elimine el departamento de prueba de la segunda pestaña. Se mostrará un error de simultaneidad con los valores actuales de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="57067-503">Delete the test department from the second tab. A concurrency error is display with the current values from the DB.</span></span> <span data-ttu-id="57067-504">Al hacer clic en **Delete** se elimina la entidad, a menos que se haya actualizado `RowVersion`. El departamento se ha eliminado.</span><span class="sxs-lookup"><span data-stu-id="57067-504">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.department has been deleted.</span></span>

<span data-ttu-id="57067-505">Vea [Herencia](xref:data/ef-mvc/inheritance) para obtener información sobre cómo se hereda un modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="57067-505">See [Inheritance](xref:data/ef-mvc/inheritance) on how to inherit a data model.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="57067-506">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="57067-506">Additional resources</span></span>

* [<span data-ttu-id="57067-507">Tokens de simultaneidad en EF Core</span><span class="sxs-lookup"><span data-stu-id="57067-507">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="57067-508">Controlar la simultaneidad en EF Core</span><span class="sxs-lookup"><span data-stu-id="57067-508">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="57067-509">Versión de YouTube de este tutorial (gestión de conflictos de simultaneidad)</span><span class="sxs-lookup"><span data-stu-id="57067-509">YouTube version of this tutorial(Handling Concurrency Conflicts)</span></span>](https://youtu.be/EosxHTFgYps)
* [<span data-ttu-id="57067-510">Versión en YouTube de este tutorial (parte 2)</span><span class="sxs-lookup"><span data-stu-id="57067-510">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [<span data-ttu-id="57067-511">Versión en YouTube de este tutorial (parte 3)</span><span class="sxs-lookup"><span data-stu-id="57067-511">YouTube version of this tutorial(Part 3)</span></span>](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [<span data-ttu-id="57067-512">Anterior</span><span class="sxs-lookup"><span data-stu-id="57067-512">Previous</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

