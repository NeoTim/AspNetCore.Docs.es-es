---
title: Uso de Grunt en ASP.NET Core
author: rick-anderson
description: Uso de Grunt en ASP.NET Core
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/using-grunt
ms.openlocfilehash: b51973e82bb1bd382be68a501c40ba613217fb03
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773645"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="10ded-103">Uso de Grunt en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="10ded-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="10ded-104">Grunt es ejecutor de tareas de JavaScript que automatiza la minificación de scripts, la compilación de TypeScript, las herramientas de "lint" de calidad del código, los preprocesadores de CSS y las tareas repetitivas que se necesitan para admitir el desarrollo del cliente.</span><span class="sxs-lookup"><span data-stu-id="10ded-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="10ded-105">Grunt es totalmente compatible con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="10ded-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="10ded-106">En este ejemplo se usa un proyecto de ASP.NET Core vacío como punto inicial para mostrar cómo automatizar el proceso de compilación del cliente desde cero.</span><span class="sxs-lookup"><span data-stu-id="10ded-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="10ded-107">El ejemplo finalizado limpia el directorio de implementación de destino, combina los archivos JavaScript, comprueba la calidad del código, condensa el contenido del archivo JavaScript y se implementa en la raíz de la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="10ded-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="10ded-108">Usaremos los siguientes paquetes:</span><span class="sxs-lookup"><span data-stu-id="10ded-108">We will use the following packages:</span></span>

* <span data-ttu-id="10ded-109">**grunt**: El paquete del ejecutor de tareas de Grunt.</span><span class="sxs-lookup"><span data-stu-id="10ded-109">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="10ded-110">**grunt-contrib-clean**: Un complemento que quita archivos o directorios.</span><span class="sxs-lookup"><span data-stu-id="10ded-110">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="10ded-111">**grunt-contrib-jshint**: Un complemento que revisa la calidad del código de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="10ded-111">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="10ded-112">**grunt-contrib-concat**: Un complemento que une archivos en un único archivo.</span><span class="sxs-lookup"><span data-stu-id="10ded-112">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="10ded-113">**grunt-contrib-uglify**: Un complemento que minifica JavaScript para reducir el tamaño.</span><span class="sxs-lookup"><span data-stu-id="10ded-113">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="10ded-114">**grunt-contrib-watch**: Un complemento que inspecciona la actividad de archivo.</span><span class="sxs-lookup"><span data-stu-id="10ded-114">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="10ded-115">Preparar la aplicación</span><span class="sxs-lookup"><span data-stu-id="10ded-115">Preparing the application</span></span>

<span data-ttu-id="10ded-116">Para empezar, configure una nueva aplicación web vacía y agregue los archivos de ejemplo de TypeScript.</span><span class="sxs-lookup"><span data-stu-id="10ded-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="10ded-117">Los archivos TypeScript se compilan automáticamente en JavaScript con la configuración predeterminada de Visual Studio y serán nuestra materia prima para procesar con Grunt.</span><span class="sxs-lookup"><span data-stu-id="10ded-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="10ded-118">En Visual Studio, cree un nuevo `ASP.NET Web Application`.</span><span class="sxs-lookup"><span data-stu-id="10ded-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="10ded-119">En el cuadro de diálogo **Nuevo proyecto ASP.NET**, seleccione la plantilla **Vacía** de ASP.NET Core y haga clic en el botón Aceptar.</span><span class="sxs-lookup"><span data-stu-id="10ded-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="10ded-120">En el Explorador de soluciones, revise la estructura del proyecto.</span><span class="sxs-lookup"><span data-stu-id="10ded-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="10ded-121">La carpeta `\src` incluye nodos `wwwroot` y `Dependencies` vacíos.</span><span class="sxs-lookup"><span data-stu-id="10ded-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![solución web vacía](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="10ded-123">Agregue una nueva carpeta denominada `TypeScript`al directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="10ded-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="10ded-124">Antes de agregar archivos, asegúrese de que Visual Studio tiene activada la opción 'compilar al guardar' para los archivos TypeScript.</span><span class="sxs-lookup"><span data-stu-id="10ded-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="10ded-125">Navegue a **Herramientas** > **Opciones** > **Editor de texto** > **Typescript** > **Proyecto**:</span><span class="sxs-lookup"><span data-stu-id="10ded-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![compilación automática de la configuración de opciones de los archivos TypeScript](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="10ded-127">Haga clic con el botón derecho en el directorio `TypeScript` y seleccione **Agregar > Nuevo elemento** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="10ded-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="10ded-128">Seleccione el elemento **Archivo JavaScript** y asigne al archivo el nombre *Tastes.ts* (tenga en cuenta la extensión \*.ts).</span><span class="sxs-lookup"><span data-stu-id="10ded-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="10ded-129">Copie la línea del siguiente código de TypeScript en el archivo (al guardar, aparecerá un nuevo archivo *Tastes.js* con el origen de JavaScript).</span><span class="sxs-lookup"><span data-stu-id="10ded-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="10ded-130">Agregue un segundo archivo al directorio **TypeScript** y asígnele el nombre `Food.ts`.</span><span class="sxs-lookup"><span data-stu-id="10ded-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="10ded-131">Copie el código que se indica a continuación en el archivo.</span><span class="sxs-lookup"><span data-stu-id="10ded-131">Copy the code below into the file.</span></span>

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a><span data-ttu-id="10ded-132">Configuración de NPM</span><span class="sxs-lookup"><span data-stu-id="10ded-132">Configuring NPM</span></span>

<span data-ttu-id="10ded-133">A continuación, configure NPM para descargar grunt y grunt-tasks.</span><span class="sxs-lookup"><span data-stu-id="10ded-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="10ded-134">En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y, en el menú contextual, seleccione **Agregar > Nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="10ded-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="10ded-135">Seleccione el elemento **archivo de configuración de NPM**, deje el nombre predeterminado *package.json* y haga clic en el botón **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="10ded-135">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="10ded-136">En el archivo *package.json*, dentro de las llaves del objeto `devDependencies` y escriba "grunt".</span><span class="sxs-lookup"><span data-stu-id="10ded-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="10ded-137">Seleccione `grunt` en la lista de IntelliSense y presione la tecla Entrar.</span><span class="sxs-lookup"><span data-stu-id="10ded-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="10ded-138">Visual Studio citará el nombre del paquete de grunt y agregará un signo de dos puntos.</span><span class="sxs-lookup"><span data-stu-id="10ded-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="10ded-139">A la derecha de los dos puntos, seleccione la versión estable más reciente del paquete en la parte superior de la lista de IntelliSense (presione `Ctrl-Space` si IntelliSense no aparece).</span><span class="sxs-lookup"><span data-stu-id="10ded-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![IntelliSense de grunt](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="10ded-141">NPM usa el [versionamiento semántico](https://semver.org/) para organizar las dependencias.</span><span class="sxs-lookup"><span data-stu-id="10ded-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="10ded-142">El versionamiento semántico, también conocido como SemVer, identifica paquetes con el esquema de numeración \<major>.\<minor>.\<patch>.</span><span class="sxs-lookup"><span data-stu-id="10ded-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="10ded-143">IntelliSense simplifica el versionamiento semántico mostrando únicamente algunas opciones comunes.</span><span class="sxs-lookup"><span data-stu-id="10ded-143">Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="10ded-144">El elemento superior de la lista de IntelliSense (0.4.5 en el ejemplo anterior) se considera la versión estable más reciente del paquete.</span><span class="sxs-lookup"><span data-stu-id="10ded-144">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="10ded-145">El símbolo de intercalación (^) coincide con la versión principal más reciente y la tilde de la ñ (~) coincide con la versión secundaria más reciente.</span><span class="sxs-lookup"><span data-stu-id="10ded-145">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="10ded-146">Vea la [referencia del analizador de versiones SemVer de NPM](https://www.npmjs.com/package/semver) como guía para el expresividad completa que SemVer proporciona.</span><span class="sxs-lookup"><span data-stu-id="10ded-146">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="10ded-147">Agregue más dependencias para cargar paquetes grunt-contrib-\* para *clean*, *jshint*, *concat*, *uglify* y *watch*, tal como se muestra en el siguiente ejemplo.</span><span class="sxs-lookup"><span data-stu-id="10ded-147">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="10ded-148">No es necesario que las versiones coincidan con el ejemplo.</span><span class="sxs-lookup"><span data-stu-id="10ded-148">The versions don't need to match the example.</span></span>

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. <span data-ttu-id="10ded-149">Guarde el archivo *package.json*.</span><span class="sxs-lookup"><span data-stu-id="10ded-149">Save the *package.json* file.</span></span>

<span data-ttu-id="10ded-150">Se descargarán los paquetes de cada elemento `devDependencies`, junto con los archivos que requiera cada paquete.</span><span class="sxs-lookup"><span data-stu-id="10ded-150">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="10ded-151">Puede buscar los archivos de paquete en el directorio *node_modules* habilitando el botón **Mostrar todos los archivos** en **Explorador de soluciones**.</span><span class="sxs-lookup"><span data-stu-id="10ded-151">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="10ded-153">Si es necesario, puede restaurar manualmente las dependencias en **Explorador de soluciones** haciendo clic con el botón derecho en `Dependencies\NPM` y seleccionando la opción del menú **Restaurar paquetes**.</span><span class="sxs-lookup"><span data-stu-id="10ded-153">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![restaurar paquetes](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="10ded-155">Configuración de Grunt</span><span class="sxs-lookup"><span data-stu-id="10ded-155">Configuring Grunt</span></span>

<span data-ttu-id="10ded-156">Grunt está configurado con un manifiesto denominado *Gruntfile.js* que define, carga y registra las tareas que se pueden ejecutar manualmente o configurar para que se ejecuten automáticamente en función de los eventos de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="10ded-156">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="10ded-157">Haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **Nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="10ded-157">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="10ded-158">Seleccione la plantilla de elemento **Archivo JavaScript**, cambie el nombre a *Gruntfile.js* y haga clic en el botón **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="10ded-158">Select the **JavaScript File** item template, change the name to *Gruntfile.js*, and click the **Add** button.</span></span>

1. <span data-ttu-id="10ded-159">Agregue el código siguiente a *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="10ded-159">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="10ded-160">La función `initConfig` establece las opciones para cada paquete y el resto del módulo carga y registra las tareas.</span><span class="sxs-lookup"><span data-stu-id="10ded-160">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="10ded-161">Dentro de la función `initConfig`, agregue opciones para la tarea `clean`, tal como se muestra en el ejemplo *Gruntfile.js* a continuación.</span><span class="sxs-lookup"><span data-stu-id="10ded-161">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="10ded-162">La tarea `clean` acepta una matriz de cadenas de directorio.</span><span class="sxs-lookup"><span data-stu-id="10ded-162">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="10ded-163">Esta tarea quita archivos de *wwwroot/lib* y quita todo el directorio */temp*.</span><span class="sxs-lookup"><span data-stu-id="10ded-163">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="10ded-164">Debajo de la función `initConfig`, agregue una llamada a `grunt.loadNpmTasks`.</span><span class="sxs-lookup"><span data-stu-id="10ded-164">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="10ded-165">Esto hará que la tarea se pueda ejecutar desde Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="10ded-165">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="10ded-166">Guarde *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="10ded-166">Save *Gruntfile.js*.</span></span> <span data-ttu-id="10ded-167">El archivo debe tener un aspecto similar al de la siguiente captura de pantalla.</span><span class="sxs-lookup"><span data-stu-id="10ded-167">The file should look something like the screenshot below.</span></span>

    ![initial gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="10ded-169">Haga clic con el botón derecho en  *Gruntfile.js* y seleccione **Explorador del Ejecutor de tareas** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="10ded-169">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="10ded-170">Se abrirá la ventana **Explorador del Ejecutor de tareas**.</span><span class="sxs-lookup"><span data-stu-id="10ded-170">The **Task Runner Explorer** window will open.</span></span>

    ![menú del explorador del ejecutor de tareas](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="10ded-172">Compruebe que `clean` aparezca en **Tareas** en el **Explorador del Ejecutor de tareas**.</span><span class="sxs-lookup"><span data-stu-id="10ded-172">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![lista de tareas del explorador del ejecutor de tareas](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="10ded-174">Haga clic con el botón derecho en la tarea de limpieza y seleccione **Ejecutar** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="10ded-174">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="10ded-175">Una ventana de comandos muestra el progreso de la tarea.</span><span class="sxs-lookup"><span data-stu-id="10ded-175">A command window displays progress of the task.</span></span>

    ![tarea de limpieza para ejecutar el explorador del ejecutor de tareas](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="10ded-177">No hay archivos o directorios para limpiar todavía.</span><span class="sxs-lookup"><span data-stu-id="10ded-177">There are no files or directories to clean yet.</span></span> <span data-ttu-id="10ded-178">Si lo desea, puede crearlos manualmente en el Explorador de soluciones y, a continuación, ejecutar la tarea de limpieza como prueba.</span><span class="sxs-lookup"><span data-stu-id="10ded-178">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="10ded-179">En la función `initConfig`, agregue una entrada para `concat` mediante el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="10ded-179">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="10ded-180">La matriz de propiedades `src` enumera los archivos que se van a combinar, en el orden en que deben combinarse.</span><span class="sxs-lookup"><span data-stu-id="10ded-180">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="10ded-181">La propiedad `dest` asigna la ruta de acceso al archivo combinado que se genera.</span><span class="sxs-lookup"><span data-stu-id="10ded-181">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="10ded-182">La propiedad `all` en el código anterior es el nombre de un destino.</span><span class="sxs-lookup"><span data-stu-id="10ded-182">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="10ded-183">Los destinos se usan en algunas tareas de Grunt para permitir varios entornos de compilación.</span><span class="sxs-lookup"><span data-stu-id="10ded-183">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="10ded-184">Puede ver los destinos integrados mediante IntelliSense o asignarse el suyo propio.</span><span class="sxs-lookup"><span data-stu-id="10ded-184">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="10ded-185">Agregue la tarea `jshint` mediante el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="10ded-185">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="10ded-186">La utilidad `code-quality` de jshint se ejecuta en cada archivo JavaScript que se encuentra en el directorio *temp*.</span><span class="sxs-lookup"><span data-stu-id="10ded-186">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="10ded-187">La opción "-W069" es un error producido por jshint cuando JavaScript usa la sintaxis de corchetes para asignar una propiedad en lugar de la notación de puntos, es decir, `Tastes["Sweet"]` en lugar de `Tastes.Sweet`.</span><span class="sxs-lookup"><span data-stu-id="10ded-187">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="10ded-188">La opción desactiva la advertencia para permitir que continúe el resto del proceso.</span><span class="sxs-lookup"><span data-stu-id="10ded-188">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="10ded-189">Agregue la tarea `uglify` mediante el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="10ded-189">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="10ded-190">La tarea minifica el archivo *combined.js* que se encuentra en el directorio temporal y crea el archivo de resultados en wwwroot/lib siguiendo la convención de nomenclatura estándar *\<nombre de archivo\>.min.js*.</span><span class="sxs-lookup"><span data-stu-id="10ded-190">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="10ded-191">En la llamada a `grunt.loadNpmTasks` que carga `grunt-contrib-clean`, incluya la misma llamada para jshint, concat y uglify con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="10ded-191">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="10ded-192">Guarde *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="10ded-192">Save *Gruntfile.js*.</span></span> <span data-ttu-id="10ded-193">El archivo debe tener un aspecto similar al del siguiente ejemplo.</span><span class="sxs-lookup"><span data-stu-id="10ded-193">The file should look something like the example below.</span></span>

    ![ejemplo de archivo grunt completo](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="10ded-195">Tenga en cuenta que la lista de tareas del **explorador del ejecutor de tareas** incluye tareas `clean`, `concat`, `jshint` y `uglify`.</span><span class="sxs-lookup"><span data-stu-id="10ded-195">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="10ded-196">Ejecute cada tarea en orden y observe los resultados en **Explorador de soluciones**.</span><span class="sxs-lookup"><span data-stu-id="10ded-196">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="10ded-197">Cada tarea se debe ejecutar sin errores.</span><span class="sxs-lookup"><span data-stu-id="10ded-197">Each task should run without errors.</span></span>

    ![ejecutor de tareas explorador ejecutar cada tarea](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="10ded-199">La tarea concat crea un nuevo archivo *combined.js* y lo coloca en el directorio temporal.</span><span class="sxs-lookup"><span data-stu-id="10ded-199">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="10ded-200">La tarea `jshint` simplemente se ejecuta y no genera resultados.</span><span class="sxs-lookup"><span data-stu-id="10ded-200">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="10ded-201">La tarea `uglify` crea un nuevo archivo *combined.min.js* y lo coloca en *wwwroot/lib*.</span><span class="sxs-lookup"><span data-stu-id="10ded-201">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="10ded-202">Al finalizar, la solución debe tener un aspecto similar al de la siguiente captura de pantalla:</span><span class="sxs-lookup"><span data-stu-id="10ded-202">On completion, the solution should look something like the screenshot below:</span></span>

    ![explorador de soluciones después de todas las tareas](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="10ded-204">Para obtener más información sobre las opciones de cada paquete, visite [https://www.npmjs.com/](https://www.npmjs.com/) y busque el nombre del paquete en el cuadro de búsqueda de la página principal.</span><span class="sxs-lookup"><span data-stu-id="10ded-204">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="10ded-205">Por ejemplo, puede buscar el paquete grunt-contrib-clean para obtener un vínculo de documentación que explique todos sus parámetros.</span><span class="sxs-lookup"><span data-stu-id="10ded-205">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="10ded-206">Todo junto</span><span class="sxs-lookup"><span data-stu-id="10ded-206">All together now</span></span>

<span data-ttu-id="10ded-207">Utilice el método `registerTask()` de Grunt para ejecutar una serie de tareas en una secuencia determinada.</span><span class="sxs-lookup"><span data-stu-id="10ded-207">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="10ded-208">Por ejemplo, para ejecutar los pasos de ejemplo anteriores en el orden clean-> concat-> jshint-> uglify, agregue el código siguiente al módulo.</span><span class="sxs-lookup"><span data-stu-id="10ded-208">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="10ded-209">El código se debe agregar al mismo nivel que las llamadas a loadNpmTasks(), fuera de initConfig.</span><span class="sxs-lookup"><span data-stu-id="10ded-209">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="10ded-210">La nueva tarea aparece en el explorador del ejecutor de tareas en Tareas de alias.</span><span class="sxs-lookup"><span data-stu-id="10ded-210">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="10ded-211">Puede hacer clic con el botón derecho y ejecutarlo como lo haría con otras tareas.</span><span class="sxs-lookup"><span data-stu-id="10ded-211">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="10ded-212">La tarea `all` ejecutará `clean`, `concat`, `jshint` y `uglify`, en orden.</span><span class="sxs-lookup"><span data-stu-id="10ded-212">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![tareas grunt alias](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="10ded-214">Observación de cambios</span><span class="sxs-lookup"><span data-stu-id="10ded-214">Watching for changes</span></span>

<span data-ttu-id="10ded-215">Una tarea `watch` controla los archivos y directorios.</span><span class="sxs-lookup"><span data-stu-id="10ded-215">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="10ded-216">La inspección desencadena tareas automáticamente si detecta cambios.</span><span class="sxs-lookup"><span data-stu-id="10ded-216">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="10ded-217">Agregue el código siguiente a initConfig para ver los cambios en los archivos \*.js en el directorio TypeScript.</span><span class="sxs-lookup"><span data-stu-id="10ded-217">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="10ded-218">Si se cambia un archivo JavaScript, `watch` ejecutará la tarea `all`.</span><span class="sxs-lookup"><span data-stu-id="10ded-218">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="10ded-219">Agregue una llamada a `loadNpmTasks()` para mostrar la tarea `watch` en el explorador del ejecutor de tareas.</span><span class="sxs-lookup"><span data-stu-id="10ded-219">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="10ded-220">Haga clic con el botón derecho en la tarea de inspección del explorador del ejecutor de tareas y seleccione Ejecutar en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="10ded-220">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="10ded-221">La ventana Comandos que muestra la tarea de inspección en ejecución mostrará un mensaje "Esperando…".</span><span class="sxs-lookup"><span data-stu-id="10ded-221">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="10ded-222">Abra uno de los archivos TypeScript, agregue un espacio y, a continuación, guarde el archivo.</span><span class="sxs-lookup"><span data-stu-id="10ded-222">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="10ded-223">Esto desencadenará la tarea de inspección y desencadenará las demás tareas para que se ejecuten en orden.</span><span class="sxs-lookup"><span data-stu-id="10ded-223">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="10ded-224">La siguiente captura de pantalla muestra una ejecución de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="10ded-224">The screenshot below shows a sample run.</span></span>

![resultados de tareas en ejecución](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="10ded-226">Enlace a eventos de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="10ded-226">Binding to Visual Studio events</span></span>

<span data-ttu-id="10ded-227">A menos que desee iniciar manualmente las tareas cada vez que trabaje en Visual Studio, enlace tareas a los eventos **Antes de la compilación**, **Después de la compilación**, **Limpiar** y **Apertura de proyecto**.</span><span class="sxs-lookup"><span data-stu-id="10ded-227">Unless you want to manually start your tasks every time you work in Visual Studio, bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="10ded-228">Enlace `watch` para que se ejecute cada vez que se abra Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="10ded-228">Bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="10ded-229">En el explorador del ejecutor de tareas, haga clic con el botón derecho en la tarea de inspección y seleccione **Enlaces** > **Apertura de proyecto** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="10ded-229">In Task Runner Explorer, right-click the watch task and select **Bindings** > **Project Open** from the context menu.</span></span>

![enlazar una tarea a la apertura del proyecto](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="10ded-231">Descargue y vuelva a cargar el proyecto.</span><span class="sxs-lookup"><span data-stu-id="10ded-231">Unload and reload the project.</span></span> <span data-ttu-id="10ded-232">Cuando el proyecto se vuelva a cargar, la tarea de inspección comienza a ejecutarse automáticamente.</span><span class="sxs-lookup"><span data-stu-id="10ded-232">When the project loads again, the watch task starts running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="10ded-233">Resumen</span><span class="sxs-lookup"><span data-stu-id="10ded-233">Summary</span></span>

<span data-ttu-id="10ded-234">Grunt es un ejecutor de tareas eficaz que se puede usar para automatizar la mayoría de las tareas de compilación de cliente.</span><span class="sxs-lookup"><span data-stu-id="10ded-234">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="10ded-235">Grunt aprovecha NPM para ofrecer sus paquetes y ofrece integración de herramientas con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="10ded-235">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="10ded-236">El explorador del ejecutor de tareas de Visual Studio detecta cambios en los archivos de configuración y proporciona una interfaz adecuada para ejecutar tareas, ver tareas en ejecución y enlazar tareas a eventos de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="10ded-236">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>
