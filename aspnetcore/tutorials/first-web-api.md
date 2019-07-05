---
title: 'Tutorial: Creación de una API web con ASP.NET Core'
author: rick-anderson
description: Aprenda a crear de una API web con ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2019
uid: tutorials/first-web-api
ms.openlocfilehash: a53f7019c1079296f073e743ddbf9d90fc5abad3
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555874"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="8365a-103">Tutorial: Creación de una API web con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8365a-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="8365a-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="8365a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="8365a-105">En este tutorial se enseñan los conceptos básicos de la compilación de una API web con ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8365a-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

<span data-ttu-id="8365a-106">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="8365a-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8365a-107">Crear un proyecto de API web.</span><span class="sxs-lookup"><span data-stu-id="8365a-107">Create a web API project.</span></span>
> * <span data-ttu-id="8365a-108">Agregar una clase de modelo.</span><span class="sxs-lookup"><span data-stu-id="8365a-108">Add a model class.</span></span>
> * <span data-ttu-id="8365a-109">Crear el contexto de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8365a-109">Create the database context.</span></span>
> * <span data-ttu-id="8365a-110">Registrar el contexto de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8365a-110">Register the database context.</span></span>
> * <span data-ttu-id="8365a-111">Agregar un controlador.</span><span class="sxs-lookup"><span data-stu-id="8365a-111">Add a controller.</span></span>
> * <span data-ttu-id="8365a-112">Agregar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="8365a-112">Add CRUD methods.</span></span>
> * <span data-ttu-id="8365a-113">Configurar el enrutamiento y las rutas de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="8365a-113">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="8365a-114">Especificar los valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="8365a-114">Specify return values.</span></span>
> * <span data-ttu-id="8365a-115">Llamar a la API web con Postman.</span><span class="sxs-lookup"><span data-stu-id="8365a-115">Call the web API with Postman.</span></span>
> * <span data-ttu-id="8365a-116">Llamar a la API web con jQuery.</span><span class="sxs-lookup"><span data-stu-id="8365a-116">Call the web API with jQuery.</span></span>

<span data-ttu-id="8365a-117">Al final, tendrá una API web que pueda administrar las tareas "pendientes" almacenadas en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="8365a-117">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="8365a-118">Información general</span><span class="sxs-lookup"><span data-stu-id="8365a-118">Overview</span></span>

<span data-ttu-id="8365a-119">En este tutorial se crea la siguiente API:</span><span class="sxs-lookup"><span data-stu-id="8365a-119">This tutorial creates the following API:</span></span>

|<span data-ttu-id="8365a-120">API</span><span class="sxs-lookup"><span data-stu-id="8365a-120">API</span></span> | <span data-ttu-id="8365a-121">DESCRIPCIÓN</span><span class="sxs-lookup"><span data-stu-id="8365a-121">Description</span></span> | <span data-ttu-id="8365a-122">Cuerpo de la solicitud</span><span class="sxs-lookup"><span data-stu-id="8365a-122">Request body</span></span> | <span data-ttu-id="8365a-123">Cuerpo de la respuesta</span><span class="sxs-lookup"><span data-stu-id="8365a-123">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="8365a-124">GET /api/todo</span><span class="sxs-lookup"><span data-stu-id="8365a-124">GET /api/todo</span></span> | <span data-ttu-id="8365a-125">Obtener todas las tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="8365a-125">Get all to-do items</span></span> | <span data-ttu-id="8365a-126">Ninguna</span><span class="sxs-lookup"><span data-stu-id="8365a-126">None</span></span> | <span data-ttu-id="8365a-127">Matriz de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="8365a-127">Array of to-do items</span></span>|
|<span data-ttu-id="8365a-128">GET /api/todo/{id}</span><span class="sxs-lookup"><span data-stu-id="8365a-128">GET /api/todo/{id}</span></span> | <span data-ttu-id="8365a-129">Obtener un elemento por identificador</span><span class="sxs-lookup"><span data-stu-id="8365a-129">Get an item by ID</span></span> | <span data-ttu-id="8365a-130">None</span><span class="sxs-lookup"><span data-stu-id="8365a-130">None</span></span> | <span data-ttu-id="8365a-131">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="8365a-131">To-do item</span></span>|
|<span data-ttu-id="8365a-132">POST /api/todo</span><span class="sxs-lookup"><span data-stu-id="8365a-132">POST /api/todo</span></span> | <span data-ttu-id="8365a-133">Incorporación de un nuevo elemento</span><span class="sxs-lookup"><span data-stu-id="8365a-133">Add a new item</span></span> | <span data-ttu-id="8365a-134">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="8365a-134">To-do item</span></span> | <span data-ttu-id="8365a-135">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="8365a-135">To-do item</span></span> |
|<span data-ttu-id="8365a-136">PUT /api/todo/{id}</span><span class="sxs-lookup"><span data-stu-id="8365a-136">PUT /api/todo/{id}</span></span> | <span data-ttu-id="8365a-137">Actualizar un elemento existente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="8365a-137">Update an existing item &nbsp;</span></span> | <span data-ttu-id="8365a-138">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="8365a-138">To-do item</span></span> | <span data-ttu-id="8365a-139">None</span><span class="sxs-lookup"><span data-stu-id="8365a-139">None</span></span> |
|<span data-ttu-id="8365a-140">DELETE /api/todo/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="8365a-140">DELETE /api/todo/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="8365a-141">Eliminar un elemento &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="8365a-141">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="8365a-142">None</span><span class="sxs-lookup"><span data-stu-id="8365a-142">None</span></span> | <span data-ttu-id="8365a-143">None</span><span class="sxs-lookup"><span data-stu-id="8365a-143">None</span></span>|

<span data-ttu-id="8365a-144">En el diagrama siguiente, se muestra el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8365a-144">The following diagram shows the design of the app.</span></span>

![El cliente está representado por un cuadro a la izquierda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="8365a-150">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="8365a-150">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8365a-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8365a-151">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8365a-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8365a-152">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8365a-153">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8365a-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="8365a-154">Creación de un proyecto web</span><span class="sxs-lookup"><span data-stu-id="8365a-154">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8365a-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8365a-155">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8365a-156">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="8365a-156">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="8365a-157">Seleccione la plantilla **Aplicación web ASP.NET Core** y haga clic en **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="8365a-157">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="8365a-158">Asigne al proyecto el nombre *TodoApi* y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="8365a-158">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="8365a-159">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 2.2** estén seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="8365a-159">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="8365a-160">Seleccione la plantilla **API** y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="8365a-160">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="8365a-161">**No** seleccione **Habilitar compatibilidad con Docker**.</span><span class="sxs-lookup"><span data-stu-id="8365a-161">**Don't** select **Enable Docker Support**.</span></span>

![Cuadro de diálogo de nuevo proyecto de VS](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8365a-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8365a-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8365a-164">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="8365a-164">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="8365a-165">Cambie los directorios (`cd`) a la carpeta que va a contener la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="8365a-165">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="8365a-166">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="8365a-166">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="8365a-167">Estos comandos crean un nuevo proyecto de API web y abren una nueva instancia de Visual Studio Code en la carpeta del proyecto nuevo.</span><span class="sxs-lookup"><span data-stu-id="8365a-167">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="8365a-168">Cuando en un cuadro de diálogo se le pregunte si quiere agregar al proyecto los recursos necesarios, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="8365a-168">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8365a-169">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8365a-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8365a-170">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="8365a-170">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="8365a-172">Seleccione **Aplicación .NET Core** > **API web de ASP.NET Core** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="8365a-172">Select **.NET Core App** > **ASP.NET Core Web API** > **Next**.</span></span>

  ![Cuadro de diálogo de nuevo proyecto de macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="8365a-174">En el cuadro de diálogo **Configurar la nueva API web de ASP.NET Core**, acepte la **plataforma de destino** predeterminada de \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="8365a-174">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="8365a-175">Escriba *TodoApi* en **Nombre del proyecto** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="8365a-175">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![cuadro de diálogo de configuración](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="8365a-177">Prueba de la API</span><span class="sxs-lookup"><span data-stu-id="8365a-177">Test the API</span></span>

<span data-ttu-id="8365a-178">La plantilla del proyecto crea una API `values`.</span><span class="sxs-lookup"><span data-stu-id="8365a-178">The project template creates a `values` API.</span></span> <span data-ttu-id="8365a-179">Llame al método `Get` desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8365a-179">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8365a-180">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8365a-180">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8365a-181">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8365a-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="8365a-182">Visual Studio inicia un explorador y navega hasta `https://localhost:<port>/api/values`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="8365a-182">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="8365a-183">Si aparece un cuadro de diálogo en que se le pregunta si debe confiar en el certificado de IIS Express, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="8365a-183">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="8365a-184">En el cuadro de diálogo **Advertencia de seguridad** que aparece a continuación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="8365a-184">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8365a-185">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8365a-185">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8365a-186">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8365a-186">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="8365a-187">En un explorador, vaya a la siguiente dirección URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="8365a-187">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8365a-188">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8365a-188">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8365a-189">Seleccione **Ejecutar** > **Iniciar con depuración** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8365a-189">Select **Run** > **Start With Debugging** to launch the app.</span></span> <span data-ttu-id="8365a-190">Visual Studio para Mac inicia un explorador y navega hasta `https://localhost:<port>`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="8365a-190">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="8365a-191">Se devuelve un error HTTP 404 (No encontrado).</span><span class="sxs-lookup"><span data-stu-id="8365a-191">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="8365a-192">Anexe `/api/values` a la dirección URL (cambie la dirección URL a `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="8365a-192">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="8365a-193">Se devuelve el siguiente JSON:</span><span class="sxs-lookup"><span data-stu-id="8365a-193">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="8365a-194">Incorporación de una clase de modelo</span><span class="sxs-lookup"><span data-stu-id="8365a-194">Add a model class</span></span>

<span data-ttu-id="8365a-195">Un *modelo* es un conjunto de clases que representan los datos que la aplicación administra.</span><span class="sxs-lookup"><span data-stu-id="8365a-195">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="8365a-196">El modelo para esta aplicación es una clase `TodoItem` única.</span><span class="sxs-lookup"><span data-stu-id="8365a-196">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8365a-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8365a-197">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8365a-198">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="8365a-198">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="8365a-199">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="8365a-199">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="8365a-200">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="8365a-200">Name the folder *Models*.</span></span>

* <span data-ttu-id="8365a-201">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="8365a-201">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="8365a-202">Asigne a la clase el nombre *TodoItem* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="8365a-202">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="8365a-203">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8365a-203">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8365a-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8365a-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8365a-205">Agregue una carpeta denominada *Models*.</span><span class="sxs-lookup"><span data-stu-id="8365a-205">Add a folder named *Models*.</span></span>

* <span data-ttu-id="8365a-206">Agregue una clase `TodoItem` a la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8365a-206">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8365a-207">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8365a-207">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8365a-208">Haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="8365a-208">Right-click the project.</span></span> <span data-ttu-id="8365a-209">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="8365a-209">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="8365a-210">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="8365a-210">Name the folder *Models*.</span></span>

  ![nueva carpeta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="8365a-212">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Nuevo archivo** > **General** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="8365a-212">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="8365a-213">Asigne a la clase el nombre *TodoItem* y haga clic en **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="8365a-213">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="8365a-214">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8365a-214">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="8365a-215">La propiedad `Id` funciona como clave única en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="8365a-215">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="8365a-216">Las clases de modelo pueden ir en cualquier lugar del proyecto, pero convencionalmente e usa la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="8365a-216">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="8365a-217">Incorporación de un contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="8365a-217">Add a database context</span></span>

<span data-ttu-id="8365a-218">El *contexto de base de datos* es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="8365a-218">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="8365a-219">Esta clase se crea derivándola de la clase `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="8365a-219">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8365a-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8365a-220">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8365a-221">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="8365a-221">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="8365a-222">Asigne a la clase el nombre *TodoContext* y haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="8365a-222">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="8365a-223">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8365a-223">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="8365a-224">Agregue una clase `TodoContext` a la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="8365a-224">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="8365a-225">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8365a-225">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="8365a-226">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="8365a-226">Register the database context</span></span>

<span data-ttu-id="8365a-227">En ASP.NET Core, los servicios (como el contexto de la base de datos) deben registrarse con el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8365a-227">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="8365a-228">El contenedor proporciona el servicio a los controladores.</span><span class="sxs-lookup"><span data-stu-id="8365a-228">The container provides the service to controllers.</span></span>

<span data-ttu-id="8365a-229">Actualice *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="8365a-229">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="8365a-230">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="8365a-230">The preceding code:</span></span>

* <span data-ttu-id="8365a-231">Elimina las declaraciones `using` no utilizadas.</span><span class="sxs-lookup"><span data-stu-id="8365a-231">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="8365a-232">Agrega el contexto de base de datos para el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="8365a-232">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="8365a-233">Especifica que el contexto de base de datos usará una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="8365a-233">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="8365a-234">Incorporación de un controlador</span><span class="sxs-lookup"><span data-stu-id="8365a-234">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8365a-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8365a-235">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8365a-236">Haga clic con el botón derecho en la carpeta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="8365a-236">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="8365a-237">Seleccione **Agregar** > **Nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="8365a-237">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="8365a-238">En el cuadro de diálogo **Agregar nuevo elemento**, seleccione la plantilla **Clase de controlador de API**.</span><span class="sxs-lookup"><span data-stu-id="8365a-238">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="8365a-239">Asigne a la clase el nombre *TodoController* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="8365a-239">Name the class *TodoController*, and select **Add**.</span></span>

  ![Cuadro de diálogo Agregar nuevo elemento con la palabra "controller" en el cuadro de búsqueda y la opción Clase de controlador de API web seleccionada](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="8365a-241">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8365a-241">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="8365a-242">En la carpeta *Controllers*, cree una clase denominada `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="8365a-242">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="8365a-243">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8365a-243">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="8365a-244">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="8365a-244">The preceding code:</span></span>

* <span data-ttu-id="8365a-245">Define una clase de controlador de API sin métodos.</span><span class="sxs-lookup"><span data-stu-id="8365a-245">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="8365a-246">Representa la clase con el atributo [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="8365a-246">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="8365a-247">Este atributo indica que el controlador responde a las solicitudes de la API web.</span><span class="sxs-lookup"><span data-stu-id="8365a-247">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="8365a-248">Para información sobre comportamientos específicos que permite el atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="8365a-248">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="8365a-249">Utiliza la inserción de dependencias para insertar el contexto de base de datos (`TodoContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="8365a-249">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="8365a-250">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="8365a-250">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="8365a-251">Si la base de datos está vacía, le agrega un elemento denominado `Item1`.</span><span class="sxs-lookup"><span data-stu-id="8365a-251">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="8365a-252">Este código está en el constructor, de manera que se ejecuta cada vez que hay una nueva solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="8365a-252">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="8365a-253">Si elimina todos los elementos, el constructor volverá a crear `Item1` la próxima vez que se llame a un método de API.</span><span class="sxs-lookup"><span data-stu-id="8365a-253">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="8365a-254">De este modo, es posible que parezca que la eliminación no ha funcionado, cuando en realidad sí lo ha hecho.</span><span class="sxs-lookup"><span data-stu-id="8365a-254">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="8365a-255">Incorporación de métodos Get</span><span class="sxs-lookup"><span data-stu-id="8365a-255">Add Get methods</span></span>

<span data-ttu-id="8365a-256">Para proporcionar una API que recupere tareas pendientes, agregue estos métodos a la clase `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="8365a-256">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="8365a-257">Estos métodos implementan dos puntos de conexión GET:</span><span class="sxs-lookup"><span data-stu-id="8365a-257">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="8365a-258">Llame a los dos puntos de conexión desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8365a-258">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="8365a-259">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8365a-259">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="8365a-260">La llamada a `GetTodoItems` genera la siguiente respuesta HTTP:</span><span class="sxs-lookup"><span data-stu-id="8365a-260">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="8365a-261">Enrutamiento y rutas URL</span><span class="sxs-lookup"><span data-stu-id="8365a-261">Routing and URL paths</span></span>

<span data-ttu-id="8365a-262">El atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica un método que responde a una solicitud HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="8365a-262">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="8365a-263">La ruta de dirección URL para cada método se construye como sigue:</span><span class="sxs-lookup"><span data-stu-id="8365a-263">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="8365a-264">Comience por la cadena de plantilla en el atributo `Route` del controlador:</span><span class="sxs-lookup"><span data-stu-id="8365a-264">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="8365a-265">Reemplace `[controller]` por el nombre del controlador, que convencionalmente es el nombre de clase de controlador sin el sufijo "Controller".</span><span class="sxs-lookup"><span data-stu-id="8365a-265">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="8365a-266">En este ejemplo, el nombre de clase de controlador es **Todo**Controller; por tanto, el nombre del controlador es "todo".</span><span class="sxs-lookup"><span data-stu-id="8365a-266">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="8365a-267">El [enrutamiento](xref:mvc/controllers/routing) en ASP.NET Core no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="8365a-267">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="8365a-268">Si el atributo `[HttpGet]` tiene una plantilla de ruta (por ejemplo, `[HttpGet("products")]`), anéxela a la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="8365a-268">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="8365a-269">En este ejemplo no se usa una plantilla.</span><span class="sxs-lookup"><span data-stu-id="8365a-269">This sample doesn't use a template.</span></span> <span data-ttu-id="8365a-270">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="8365a-270">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="8365a-271">En el siguiente método `GetTodoItem`, `"{id}"` es una variable de marcador de posición correspondiente al identificador único de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="8365a-271">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="8365a-272">Al invocar a `GetTodoItem`, el valor `"{id}"` de la dirección URL se proporciona al método en su parámetro `id`.</span><span class="sxs-lookup"><span data-stu-id="8365a-272">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="8365a-273">Valores devueltos</span><span class="sxs-lookup"><span data-stu-id="8365a-273">Return values</span></span>

<span data-ttu-id="8365a-274">El tipo de valor devuelto de los métodos `GetTodoItems` y `GetTodoItem` es [ActionResult\<T > type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="8365a-274">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="8365a-275">ASP.NET Core serializa automáticamente el objeto a [JSON](https://www.json.org/) y escribe el JSON en el cuerpo del mensaje de respuesta.</span><span class="sxs-lookup"><span data-stu-id="8365a-275">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="8365a-276">El código de respuesta para este tipo de valor devuelto es el 200, suponiendo que no haya ninguna excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="8365a-276">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="8365a-277">Las excepciones no controladas se convierten en errores 5xx.</span><span class="sxs-lookup"><span data-stu-id="8365a-277">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="8365a-278">Los tipos de valores devueltos `ActionResult` pueden representar una gama amplia de códigos de estado HTTP.</span><span class="sxs-lookup"><span data-stu-id="8365a-278">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="8365a-279">Por ejemplo, `GetTodoItem` puede devolver dos valores de estado diferentes:</span><span class="sxs-lookup"><span data-stu-id="8365a-279">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="8365a-280">Si no hay ningún elemento que coincida con el identificador solicitado, el método devolverá un código de error 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="8365a-280">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="8365a-281">En caso contrario, el método devuelve 200 con un cuerpo de respuesta JSON.</span><span class="sxs-lookup"><span data-stu-id="8365a-281">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="8365a-282">Devolver `item` genera una respuesta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="8365a-282">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="8365a-283">Prueba del método GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="8365a-283">Test the GetTodoItems method</span></span>

<span data-ttu-id="8365a-284">En este tutorial se usa Postman para probar la API web.</span><span class="sxs-lookup"><span data-stu-id="8365a-284">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="8365a-285">Instale [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="8365a-285">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="8365a-286">Inicie la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="8365a-286">Start the web app.</span></span>
* <span data-ttu-id="8365a-287">Inicie Postman.</span><span class="sxs-lookup"><span data-stu-id="8365a-287">Start Postman.</span></span>
* <span data-ttu-id="8365a-288">Deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="8365a-288">Disable **SSL certificate verification**</span></span>
  
  * <span data-ttu-id="8365a-289">En **Archivo > Configuración** (pestaña \**General*), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="8365a-289">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="8365a-290">Vuelva a habilitar la comprobación del certificado SSL tras probar el controlador.</span><span class="sxs-lookup"><span data-stu-id="8365a-290">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="8365a-291">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="8365a-291">Create a new request.</span></span>
  * <span data-ttu-id="8365a-292">Establezca el método HTTP en **GET**.</span><span class="sxs-lookup"><span data-stu-id="8365a-292">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="8365a-293">Establezca la dirección URL de la solicitud en `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="8365a-293">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="8365a-294">Por ejemplo: `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="8365a-294">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="8365a-295">Establezca **Vista de dos paneles** en Postman.</span><span class="sxs-lookup"><span data-stu-id="8365a-295">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="8365a-296">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="8365a-296">Select **Send**.</span></span>

![Postman con solicitud Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="8365a-298">Incorporación de un método Create</span><span class="sxs-lookup"><span data-stu-id="8365a-298">Add a Create method</span></span>

<span data-ttu-id="8365a-299">Agregue el siguiente método `PostTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="8365a-299">Add the following `PostTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="8365a-300">El código anterior es un método HTTP POST, según indica el atributo [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="8365a-300">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="8365a-301">El método obtiene el valor de tareas pendientes del cuerpo de la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="8365a-301">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="8365a-302">El método `CreatedAtAction` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="8365a-302">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="8365a-303">Devuelve un código de estado HTTP 201 cuando se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="8365a-303">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="8365a-304">HTTP 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.</span><span class="sxs-lookup"><span data-stu-id="8365a-304">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="8365a-305">Agrega un encabezado `Location` a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8365a-305">Adds a `Location` header to the response.</span></span> <span data-ttu-id="8365a-306">El encabezado `Location` especifica el identificador URI de la tarea pendiente recién creada.</span><span class="sxs-lookup"><span data-stu-id="8365a-306">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="8365a-307">Para obtener más información, consulte [10.2.2 201 creado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="8365a-307">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="8365a-308">Hace referencia a la acción `GetTodoItem` para crear el identificador URI del encabezado `Location`.</span><span class="sxs-lookup"><span data-stu-id="8365a-308">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="8365a-309">La palabra clave `nameof` de C# se usa para evitar que se codifique de forma rígida el nombre de acción en la llamada a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="8365a-309">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="8365a-310">Prueba del método PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="8365a-310">Test the PostTodoItem method</span></span>

* <span data-ttu-id="8365a-311">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="8365a-311">Build the project.</span></span>
* <span data-ttu-id="8365a-312">En Postman, establezca el método HTTP en `POST`.</span><span class="sxs-lookup"><span data-stu-id="8365a-312">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="8365a-313">Seleccione la pestaña **Cuerpo**.</span><span class="sxs-lookup"><span data-stu-id="8365a-313">Select the **Body** tab.</span></span>
* <span data-ttu-id="8365a-314">Seleccione el botón de radio **Raw** (Sin formato).</span><span class="sxs-lookup"><span data-stu-id="8365a-314">Select the **raw** radio button.</span></span>
* <span data-ttu-id="8365a-315">Establezca el tipo en **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="8365a-315">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="8365a-316">En el cuerpo de la solicitud, introduzca JSON para una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="8365a-316">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="8365a-317">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="8365a-317">Select **Send**.</span></span>

  ![Postman con solicitud de creación](first-web-api/_static/create.png)

  <span data-ttu-id="8365a-319">Si recibe un error 405 (Método no permitido), probablemente sea el resultado de no haber compilado el proyecto después de agregar el método `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="8365a-319">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="8365a-320">Prueba del URI del encabezado de ubicación</span><span class="sxs-lookup"><span data-stu-id="8365a-320">Test the location header URI</span></span>

* <span data-ttu-id="8365a-321">Seleccione la pestaña **Encabezados** en el panel **Respuesta**.</span><span class="sxs-lookup"><span data-stu-id="8365a-321">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="8365a-322">Copie el valor de encabezado **Ubicación**:</span><span class="sxs-lookup"><span data-stu-id="8365a-322">Copy the **Location** header value:</span></span>

  ![Pestaña Encabezados de la consola de Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="8365a-324">Establezca el método en GET.</span><span class="sxs-lookup"><span data-stu-id="8365a-324">Set the method to GET.</span></span>
* <span data-ttu-id="8365a-325">Pegue el URI (por ejemplo, `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="8365a-325">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="8365a-326">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="8365a-326">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="8365a-327">Incorporación de un método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="8365a-327">Add a PutTodoItem method</span></span>

<span data-ttu-id="8365a-328">Agregue el siguiente método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="8365a-328">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="8365a-329">`PutTodoItem` es similar a `PostTodoItem`, salvo por el hecho de que usa HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="8365a-329">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="8365a-330">La respuesta es [204 Sin contenido](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="8365a-330">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="8365a-331">Según la especificación HTTP, una solicitud PUT requiere que el cliente envíe toda la entidad actualizada, no solo los cambios.</span><span class="sxs-lookup"><span data-stu-id="8365a-331">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="8365a-332">Para admitir actualizaciones parciales, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="8365a-332">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="8365a-333">Si recibe un error al llamar a `PutTodoItem`, llame a `GET` para asegurarse de que hay un elemento en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8365a-333">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="8365a-334">Prueba del método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="8365a-334">Test the PutTodoItem method</span></span>

<span data-ttu-id="8365a-335">En este ejemplo se usa una base de datos en memoria que se debe iniciar cada vez que se inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8365a-335">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="8365a-336">Debe haber un elemento en la base de datos antes de que realice una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="8365a-336">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="8365a-337">Llame a GET para asegurarse de que hay un elemento en la base de datos antes de realizar una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="8365a-337">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="8365a-338">Actualice la tarea pendiente que tiene el id. = 1 y establezca su nombre en "feed fish":</span><span class="sxs-lookup"><span data-stu-id="8365a-338">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="8365a-339">En la imagen siguiente, se muestra la actualización de Postman:</span><span class="sxs-lookup"><span data-stu-id="8365a-339">The following image shows the Postman update:</span></span>

![Consola de Postman que muestra la respuesta 204 (Sin contenido)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="8365a-341">Incorporación de un método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="8365a-341">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="8365a-342">Agregue el siguiente método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="8365a-342">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="8365a-343">La respuesta de `DeleteTodoItem` es [204 (Sin contenido)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="8365a-343">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="8365a-344">Prueba del método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="8365a-344">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="8365a-345">Use Postman para eliminar una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="8365a-345">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="8365a-346">Establezca el método en `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="8365a-346">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="8365a-347">Establezca el URI del objeto que quiera eliminar, por ejemplo, `https://localhost:5001/api/todo/1`.</span><span class="sxs-lookup"><span data-stu-id="8365a-347">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="8365a-348">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="8365a-348">Select **Send**</span></span>

<span data-ttu-id="8365a-349">La aplicación de ejemplo permite eliminar todos los elementos.</span><span class="sxs-lookup"><span data-stu-id="8365a-349">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="8365a-350">Sin embargo, al eliminar el último elemento, se creará uno nuevo en el constructor de clase de modelo la próxima vez que se llame a la API.</span><span class="sxs-lookup"><span data-stu-id="8365a-350">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="8365a-351">Llamada a la API con jQuery</span><span class="sxs-lookup"><span data-stu-id="8365a-351">Call the API with jQuery</span></span>

<span data-ttu-id="8365a-352">En esta sección, se agrega una página HTML que usa jQuery para llamar a la API web.</span><span class="sxs-lookup"><span data-stu-id="8365a-352">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="8365a-353">jQuery inicia la solicitud y actualiza la página con los detalles de la respuesta de la API.</span><span class="sxs-lookup"><span data-stu-id="8365a-353">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="8365a-354">Configure la aplicación para [atender archivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) y [habilitar la asignación de archivos predeterminada](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) mediante la actualización de *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="8365a-354">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

::: moniker range=">= aspnetcore-2.2"
<span data-ttu-id="8365a-355">Cree una carpeta *wwwroot* en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="8365a-355">Create a *wwwroot* folder in the project directory.</span></span>
::: moniker-end

<span data-ttu-id="8365a-356">Agregue un archivo HTML denominado *index.html* al directorio *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="8365a-356">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="8365a-357">Reemplace el contenido por el siguiente marcado:</span><span class="sxs-lookup"><span data-stu-id="8365a-357">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="8365a-358">Agregue un archivo JavaScript denominado *site.js* al directorio *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="8365a-358">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="8365a-359">Reemplace el contenido por el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="8365a-359">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="8365a-360">Puede que sea necesario realizar un cambio en la configuración de inicio del proyecto de ASP.NET Core para probar la página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="8365a-360">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="8365a-361">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="8365a-361">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="8365a-362">Quite la propiedad `launchUrl` para forzar a la aplicación a abrirse en *index.html*, esto es, el archivo predeterminado del proyecto.</span><span class="sxs-lookup"><span data-stu-id="8365a-362">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="8365a-363">Existen varias formas de obtener jQuery.</span><span class="sxs-lookup"><span data-stu-id="8365a-363">There are several ways to get jQuery.</span></span> <span data-ttu-id="8365a-364">En el fragmento de código anterior, la biblioteca se carga desde una red CDN.</span><span class="sxs-lookup"><span data-stu-id="8365a-364">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="8365a-365">En este ejemplo se llama a todos los métodos CRUD de la API.</span><span class="sxs-lookup"><span data-stu-id="8365a-365">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="8365a-366">A continuación, encontrará algunas explicaciones de las llamadas a la API.</span><span class="sxs-lookup"><span data-stu-id="8365a-366">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="8365a-367">Obtención de una lista de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="8365a-367">Get a list of to-do items</span></span>

<span data-ttu-id="8365a-368">La función de JQuery [ajax](https://api.jquery.com/jquery.ajax/) envía una solicitud `GET` a la API, que devuelve código JSON que representa una matriz de tareas pendientes.</span><span class="sxs-lookup"><span data-stu-id="8365a-368">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="8365a-369">La función de devolución de llamada `success` se invoca si la solicitud se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="8365a-369">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="8365a-370">En la devolución de llamada, el DOM se actualiza con la información de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="8365a-370">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="8365a-371">Incorporación de una tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="8365a-371">Add a to-do item</span></span>

<span data-ttu-id="8365a-372">La función [ajax](https://api.jquery.com/jquery.ajax/) envía una solicitud `POST` con la tarea pendiente en su cuerpo.</span><span class="sxs-lookup"><span data-stu-id="8365a-372">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="8365a-373">Las opciones `accepts` y `contentType` se establecen en `application/json` para especificar el tipo de medio que se va a recibir y a enviar.</span><span class="sxs-lookup"><span data-stu-id="8365a-373">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="8365a-374">La tarea pendiente se convierte en JSON mediante [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="8365a-374">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="8365a-375">Cuando la API devuelve un código de estado correcto, se invoca la función `getData` para actualizar la tabla HTML.</span><span class="sxs-lookup"><span data-stu-id="8365a-375">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="8365a-376">Actualizar una tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="8365a-376">Update a to-do item</span></span>

<span data-ttu-id="8365a-377">El hecho de actualizar una tarea pendiente es similar al de agregar una.</span><span class="sxs-lookup"><span data-stu-id="8365a-377">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="8365a-378">El valor `url` cambia para agregar el identificador único del elemento, y `type` es `PUT`.</span><span class="sxs-lookup"><span data-stu-id="8365a-378">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="8365a-379">Eliminar una tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="8365a-379">Delete a to-do item</span></span>

<span data-ttu-id="8365a-380">Para eliminar una tarea pendiente, hay que establecer el valor `type` de la llamada de AJAX en `DELETE` y especificar el identificador único de la tarea en la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="8365a-380">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

## <a name="additional-resources"></a><span data-ttu-id="8365a-381">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="8365a-381">Additional resources</span></span>

<span data-ttu-id="8365a-382">[Vea o descargue el código de ejemplo para este tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="8365a-382">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="8365a-383">Vea [cómo descargarlo](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="8365a-383">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="8365a-384">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="8365a-384">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="8365a-385">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="8365a-385">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

## <a name="next-steps"></a><span data-ttu-id="8365a-386">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="8365a-386">Next steps</span></span>

<span data-ttu-id="8365a-387">En este tutorial ha aprendido a:</span><span class="sxs-lookup"><span data-stu-id="8365a-387">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8365a-388">Crear un proyecto de API web.</span><span class="sxs-lookup"><span data-stu-id="8365a-388">Create a web api project.</span></span>
> * <span data-ttu-id="8365a-389">Agregar una clase de modelo.</span><span class="sxs-lookup"><span data-stu-id="8365a-389">Add a model class.</span></span>
> * <span data-ttu-id="8365a-390">Crear el contexto de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8365a-390">Create the database context.</span></span>
> * <span data-ttu-id="8365a-391">Registrar el contexto de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8365a-391">Register the database context.</span></span>
> * <span data-ttu-id="8365a-392">Agregar un controlador.</span><span class="sxs-lookup"><span data-stu-id="8365a-392">Add a controller.</span></span>
> * <span data-ttu-id="8365a-393">Agregar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="8365a-393">Add CRUD methods.</span></span>
> * <span data-ttu-id="8365a-394">Configurar el enrutamiento y las rutas de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="8365a-394">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="8365a-395">Especificar los valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="8365a-395">Specify return values.</span></span>
> * <span data-ttu-id="8365a-396">Llamar a la API web con Postman.</span><span class="sxs-lookup"><span data-stu-id="8365a-396">Call the web API with Postman.</span></span>
> * <span data-ttu-id="8365a-397">Llamar a la API web con jQuery.</span><span class="sxs-lookup"><span data-stu-id="8365a-397">Call the web api with jQuery.</span></span>

<span data-ttu-id="8365a-398">Pase al siguiente tutorial para obtener información sobre cómo generar páginas de ayuda de API:</span><span class="sxs-lookup"><span data-stu-id="8365a-398">Advance to the next tutorial to learn how to generate API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
