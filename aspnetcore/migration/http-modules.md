---
title: Migración de controladores y módulos HTTP a middleware de ASP.NET Core
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
uid: migration/http-modules
ms.openlocfilehash: bdf27ccb742d4bc05bac71e6c96d71c38dcb4b62
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652001"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="c614a-102">Migración de controladores y módulos HTTP a middleware de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c614a-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="c614a-103">En este artículo se muestra cómo migrar [módulos y controladores http de ASP.net existentes de System. WebServer](/iis/configuration/system.webserver/) a ASP.net Core [middleware](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="c614a-103">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="c614a-104">Módulos y controladores revisados</span><span class="sxs-lookup"><span data-stu-id="c614a-104">Modules and handlers revisited</span></span>

<span data-ttu-id="c614a-105">Antes de continuar con ASP.NET Core middleware, vamos a resumir primero cómo funcionan los módulos y controladores HTTP:</span><span class="sxs-lookup"><span data-stu-id="c614a-105">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![Controlador de módulos](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="c614a-107">**Los controladores son:**</span><span class="sxs-lookup"><span data-stu-id="c614a-107">**Handlers are:**</span></span>

* <span data-ttu-id="c614a-108">Clases que implementan [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span><span class="sxs-lookup"><span data-stu-id="c614a-108">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

* <span data-ttu-id="c614a-109">Se utiliza para controlar las solicitudes con un nombre de archivo o una extensión determinados, como *. Report*</span><span class="sxs-lookup"><span data-stu-id="c614a-109">Used to handle requests with a given file name or extension, such as *.report*</span></span>

* <span data-ttu-id="c614a-110">[Configurado](/iis/configuration/system.webserver/handlers/) en *Web. config*</span><span class="sxs-lookup"><span data-stu-id="c614a-110">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="c614a-111">**Los módulos son:**</span><span class="sxs-lookup"><span data-stu-id="c614a-111">**Modules are:**</span></span>

* <span data-ttu-id="c614a-112">Clases que implementan [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span><span class="sxs-lookup"><span data-stu-id="c614a-112">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

* <span data-ttu-id="c614a-113">Se invoca para cada solicitud</span><span class="sxs-lookup"><span data-stu-id="c614a-113">Invoked for every request</span></span>

* <span data-ttu-id="c614a-114">Capaz de cortocircuitar (detener el procesamiento de una solicitud)</span><span class="sxs-lookup"><span data-stu-id="c614a-114">Able to short-circuit (stop further processing of a request)</span></span>

* <span data-ttu-id="c614a-115">Puede agregarse a la respuesta HTTP o crear su propia</span><span class="sxs-lookup"><span data-stu-id="c614a-115">Able to add to the HTTP response, or create their own</span></span>

* <span data-ttu-id="c614a-116">[Configurado](/iis/configuration/system.webserver/modules/) en *Web. config*</span><span class="sxs-lookup"><span data-stu-id="c614a-116">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="c614a-117">**El orden en que los módulos procesan las solicitudes entrantes viene determinado por:**</span><span class="sxs-lookup"><span data-stu-id="c614a-117">**The order in which modules process incoming requests is determined by:**</span></span>

1. <span data-ttu-id="c614a-118">[Ciclo de vida](https://msdn.microsoft.com/library/ms227673.aspx)de la aplicación, que es una serie de eventos desencadenados por ASP.net: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Cada módulo puede crear un controlador para uno o más eventos.</span><span class="sxs-lookup"><span data-stu-id="c614a-118">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

2. <span data-ttu-id="c614a-119">Para el mismo evento, el orden en el que están configurados en *Web. config*.</span><span class="sxs-lookup"><span data-stu-id="c614a-119">For the same event, the order in which they're configured in *Web.config*.</span></span>

<span data-ttu-id="c614a-120">Además de los módulos, puede agregar controladores para los eventos de ciclo de vida al archivo *global.asax.CS* .</span><span class="sxs-lookup"><span data-stu-id="c614a-120">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="c614a-121">Estos controladores se ejecutan después de los controladores de los módulos configurados.</span><span class="sxs-lookup"><span data-stu-id="c614a-121">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="c614a-122">De controladores y módulos a middleware</span><span class="sxs-lookup"><span data-stu-id="c614a-122">From handlers and modules to middleware</span></span>

<span data-ttu-id="c614a-123">**El middleware es más sencillo que los módulos y controladores HTTP:**</span><span class="sxs-lookup"><span data-stu-id="c614a-123">**Middleware are simpler than HTTP modules and handlers:**</span></span>

* <span data-ttu-id="c614a-124">Los módulos, controladores, *global.asax.CS*, *Web. config* (excepto para la configuración de IIS) y el ciclo de vida de la aplicación han desaparecido</span><span class="sxs-lookup"><span data-stu-id="c614a-124">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

* <span data-ttu-id="c614a-125">Los roles de los módulos y controladores se han tomado por middleware</span><span class="sxs-lookup"><span data-stu-id="c614a-125">The roles of both modules and handlers have been taken over by middleware</span></span>

* <span data-ttu-id="c614a-126">El middleware se configura mediante código en lugar de en *Web. config.*</span><span class="sxs-lookup"><span data-stu-id="c614a-126">Middleware are configured using code rather than in *Web.config*</span></span>

* <span data-ttu-id="c614a-127">La [bifurcación de canalizaciones](xref:fundamentals/middleware/index#use-run-and-map) permite enviar solicitudes a middleware específico, en función de no solo la dirección URL sino también de encabezados de solicitud, cadenas de consulta, etc.</span><span class="sxs-lookup"><span data-stu-id="c614a-127">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

<span data-ttu-id="c614a-128">**El middleware es muy similar a los módulos:**</span><span class="sxs-lookup"><span data-stu-id="c614a-128">**Middleware are very similar to modules:**</span></span>

* <span data-ttu-id="c614a-129">Se invoca en principio para cada solicitud</span><span class="sxs-lookup"><span data-stu-id="c614a-129">Invoked in principle for every request</span></span>

* <span data-ttu-id="c614a-130">Es capaz de cortocircuitar una solicitud, [no pasar la solicitud al siguiente middleware](#http-modules-shortcircuiting-middleware) .</span><span class="sxs-lookup"><span data-stu-id="c614a-130">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

* <span data-ttu-id="c614a-131">Puede crear su propia respuesta HTTP</span><span class="sxs-lookup"><span data-stu-id="c614a-131">Able to create their own HTTP response</span></span>

<span data-ttu-id="c614a-132">**El middleware y los módulos se procesan en un orden diferente:**</span><span class="sxs-lookup"><span data-stu-id="c614a-132">**Middleware and modules are processed in a different order:**</span></span>

* <span data-ttu-id="c614a-133">El orden de middleware se basa en el orden en que se insertan en la canalización de solicitudes, mientras que el orden de los módulos se basa principalmente en los eventos del [ciclo de vida](https://msdn.microsoft.com/library/ms227673.aspx) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c614a-133">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

* <span data-ttu-id="c614a-134">El orden de las respuestas es el orden inverso al de las solicitudes, mientras que el orden de los módulos es el mismo para las solicitudes y las respuestas.</span><span class="sxs-lookup"><span data-stu-id="c614a-134">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

* <span data-ttu-id="c614a-135">Consulte [creación de una canalización de middleware con IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="c614a-135">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![Software intermedio](http-modules/_static/middleware.png)

<span data-ttu-id="c614a-137">Tenga en cuenta que en la imagen anterior, el middleware de autenticación cortocircuitó la solicitud.</span><span class="sxs-lookup"><span data-stu-id="c614a-137">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="c614a-138">Migrar código de módulo a middleware</span><span class="sxs-lookup"><span data-stu-id="c614a-138">Migrating module code to middleware</span></span>

<span data-ttu-id="c614a-139">Un módulo HTTP existente tendrá un aspecto similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="c614a-139">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="c614a-140">Como se muestra en la página de [middleware](xref:fundamentals/middleware/index) , un middleware ASP.net Core es una clase que expone un método `Invoke` que toma un `HttpContext` y devuelve un `Task`.</span><span class="sxs-lookup"><span data-stu-id="c614a-140">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="c614a-141">El nuevo middleware tendrá el siguiente aspecto:</span><span class="sxs-lookup"><span data-stu-id="c614a-141">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="c614a-142">La plantilla de middleware anterior se tomó de la sección sobre [escritura de middleware](xref:fundamentals/middleware/write).</span><span class="sxs-lookup"><span data-stu-id="c614a-142">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="c614a-143">La clase auxiliar *MyMiddlewareExtensions* facilita la configuración del middleware en la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="c614a-143">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="c614a-144">El método `UseMyMiddleware` agrega la clase de middleware a la canalización de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="c614a-144">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="c614a-145">Los servicios que requiere el middleware se insertan en el constructor del middleware.</span><span class="sxs-lookup"><span data-stu-id="c614a-145">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="c614a-146">El módulo puede finalizar una solicitud, por ejemplo, si el usuario no está autorizado:</span><span class="sxs-lookup"><span data-stu-id="c614a-146">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="c614a-147">Para controlar esto, un middleware no llama a `Invoke` en el siguiente middleware de la canalización.</span><span class="sxs-lookup"><span data-stu-id="c614a-147">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="c614a-148">Tenga en cuenta que esto no finaliza completamente la solicitud, ya que los middleware anteriores se seguirán invocando cuando la respuesta vuelva a su camino a través de la canalización.</span><span class="sxs-lookup"><span data-stu-id="c614a-148">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="c614a-149">Al migrar la funcionalidad del módulo al nuevo middleware, es posible que el código no se compile porque la clase `HttpContext` ha cambiado significativamente en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c614a-149">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="c614a-150">[Más adelante](#migrating-to-the-new-httpcontext), verá cómo migrar al nuevo ASP.net Core HttpContext.</span><span class="sxs-lookup"><span data-stu-id="c614a-150">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="c614a-151">Migración de la inserción de módulos en la canalización de solicitudes</span><span class="sxs-lookup"><span data-stu-id="c614a-151">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="c614a-152">Normalmente, los módulos HTTP se agregan a la canalización de solicitudes mediante *Web. config*:</span><span class="sxs-lookup"><span data-stu-id="c614a-152">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="c614a-153">Convierta esto [agregando el nuevo middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) a la canalización de solicitudes en la clase `Startup`:</span><span class="sxs-lookup"><span data-stu-id="c614a-153">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="c614a-154">El punto exacto de la canalización en el que se inserta el middleware nuevo depende del evento que se ha administrado como módulo (`BeginRequest`, `EndRequest`, etc.) y su orden en la lista de módulos de *Web. config*.</span><span class="sxs-lookup"><span data-stu-id="c614a-154">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="c614a-155">Como se indicó anteriormente, no hay ningún ciclo de vida de la aplicación en ASP.NET Core y el orden en el que el middleware procesa las respuestas difiere del orden utilizado por los módulos.</span><span class="sxs-lookup"><span data-stu-id="c614a-155">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="c614a-156">Esto podría hacer que su decisión de ordenación sea más desafiante.</span><span class="sxs-lookup"><span data-stu-id="c614a-156">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="c614a-157">Si la ordenación se convierte en un problema, puede dividir el módulo en varios componentes de middleware que se pueden ordenar de forma independiente.</span><span class="sxs-lookup"><span data-stu-id="c614a-157">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="c614a-158">Migrar el código del controlador a middleware</span><span class="sxs-lookup"><span data-stu-id="c614a-158">Migrating handler code to middleware</span></span>

<span data-ttu-id="c614a-159">Un controlador HTTP tiene un aspecto similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="c614a-159">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="c614a-160">En el proyecto de ASP.NET Core, se traducirá a un middleware similar a este:</span><span class="sxs-lookup"><span data-stu-id="c614a-160">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="c614a-161">Este middleware es muy similar al middleware correspondiente a los módulos.</span><span class="sxs-lookup"><span data-stu-id="c614a-161">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="c614a-162">La única diferencia real es que no hay ninguna llamada a `_next.Invoke(context)`.</span><span class="sxs-lookup"><span data-stu-id="c614a-162">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="c614a-163">Esto tiene sentido, porque el controlador está al final de la canalización de solicitudes, por lo que no habrá ningún middleware siguiente que invocar.</span><span class="sxs-lookup"><span data-stu-id="c614a-163">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="c614a-164">Migrar la inserción del controlador a la canalización de solicitudes</span><span class="sxs-lookup"><span data-stu-id="c614a-164">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="c614a-165">La configuración de un controlador HTTP se realiza en *Web. config* y tiene un aspecto similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="c614a-165">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="c614a-166">Puede convertir esto agregando el nuevo middleware del controlador a la canalización de solicitudes en la clase de `Startup`, similar a middleware convertido desde módulos.</span><span class="sxs-lookup"><span data-stu-id="c614a-166">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="c614a-167">El problema con este enfoque es que enviaría todas las solicitudes al nuevo middleware del controlador.</span><span class="sxs-lookup"><span data-stu-id="c614a-167">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="c614a-168">Sin embargo, solo desea que las solicitudes con una extensión determinada lleguen a su middleware.</span><span class="sxs-lookup"><span data-stu-id="c614a-168">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="c614a-169">Esto le proporcionaría la misma funcionalidad que tenía con el controlador HTTP.</span><span class="sxs-lookup"><span data-stu-id="c614a-169">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="c614a-170">Una solución consiste en bifurcar la canalización para las solicitudes con una extensión determinada, mediante el método de extensión `MapWhen`.</span><span class="sxs-lookup"><span data-stu-id="c614a-170">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="c614a-171">Esto se realiza en el mismo método `Configure` en el que se agrega el otro middleware:</span><span class="sxs-lookup"><span data-stu-id="c614a-171">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="c614a-172">`MapWhen` toma estos parámetros:</span><span class="sxs-lookup"><span data-stu-id="c614a-172">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="c614a-173">Una expresión lambda que toma el `HttpContext` y devuelve `true` si la solicitud debe desplazarse por la rama.</span><span class="sxs-lookup"><span data-stu-id="c614a-173">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="c614a-174">Esto significa que puede crear una bifurcación de solicitudes no solo basándose en su extensión, sino también en encabezados de solicitud, parámetros de cadena de consulta, etc.</span><span class="sxs-lookup"><span data-stu-id="c614a-174">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="c614a-175">Una expresión lambda que toma un `IApplicationBuilder` y agrega todo el middleware para la rama.</span><span class="sxs-lookup"><span data-stu-id="c614a-175">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="c614a-176">Esto significa que puede Agregar middleware adicional a la rama delante del middleware del controlador.</span><span class="sxs-lookup"><span data-stu-id="c614a-176">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="c614a-177">Middleware que se agrega a la canalización antes de que se invoque la rama en todas las solicitudes; la rama no tendrá ningún impacto en ellos.</span><span class="sxs-lookup"><span data-stu-id="c614a-177">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="c614a-178">Cargar opciones de middleware mediante el patrón de opciones</span><span class="sxs-lookup"><span data-stu-id="c614a-178">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="c614a-179">Algunos módulos y controladores tienen opciones de configuración que se almacenan en *Web. config*. Sin embargo, en ASP.NET Core se usa un nuevo modelo de configuración en lugar de *Web. config*.</span><span class="sxs-lookup"><span data-stu-id="c614a-179">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="c614a-180">El nuevo [sistema de configuración](xref:fundamentals/configuration/index) le proporciona estas opciones para solucionar este problemas:</span><span class="sxs-lookup"><span data-stu-id="c614a-180">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="c614a-181">Inserte directamente las opciones en el middleware, tal como se muestra en la [sección siguiente](#loading-middleware-options-through-direct-injection).</span><span class="sxs-lookup"><span data-stu-id="c614a-181">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="c614a-182">Use el [patrón de opciones](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="c614a-182">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="c614a-183">Cree una clase que contenga las opciones de middleware, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c614a-183">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="c614a-184">Almacenar los valores de opción</span><span class="sxs-lookup"><span data-stu-id="c614a-184">Store the option values</span></span>

   <span data-ttu-id="c614a-185">El sistema de configuración permite almacenar valores de opciones en cualquier lugar que desee.</span><span class="sxs-lookup"><span data-stu-id="c614a-185">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="c614a-186">Sin embargo, la mayoría de los sitios usan *appSettings. JSON*, por lo que tomaremos este enfoque:</span><span class="sxs-lookup"><span data-stu-id="c614a-186">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   <span data-ttu-id="c614a-187">*MyMiddlewareOptionsSection* aquí es un nombre de sección.</span><span class="sxs-lookup"><span data-stu-id="c614a-187">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="c614a-188">No tiene que ser el mismo que el nombre de la clase de opciones.</span><span class="sxs-lookup"><span data-stu-id="c614a-188">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="c614a-189">Asociar los valores de opción a la clase Options</span><span class="sxs-lookup"><span data-stu-id="c614a-189">Associate the option values with the options class</span></span>

    <span data-ttu-id="c614a-190">El patrón de opciones usa el marco de inserción de dependencias de ASP.NET Core para asociar el tipo de opciones (como `MyMiddlewareOptions`) con un objeto `MyMiddlewareOptions` que tiene las opciones reales.</span><span class="sxs-lookup"><span data-stu-id="c614a-190">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="c614a-191">Actualice la clase `Startup`:</span><span class="sxs-lookup"><span data-stu-id="c614a-191">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="c614a-192">Si utiliza *appSettings. JSON*, agréguelo al generador de configuración en el constructor `Startup`:</span><span class="sxs-lookup"><span data-stu-id="c614a-192">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="c614a-193">Configurar el servicio de opciones:</span><span class="sxs-lookup"><span data-stu-id="c614a-193">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="c614a-194">Asocie las opciones a la clase de opciones:</span><span class="sxs-lookup"><span data-stu-id="c614a-194">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="c614a-195">Inserte las opciones en el constructor de middleware.</span><span class="sxs-lookup"><span data-stu-id="c614a-195">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="c614a-196">Esto es similar a insertar opciones en un controlador.</span><span class="sxs-lookup"><span data-stu-id="c614a-196">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="c614a-197">El método de extensión [UseMiddleware](#http-modules-usemiddleware) que agrega el middleware al `IApplicationBuilder` se encarga de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="c614a-197">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="c614a-198">Esto no se limita a `IOptions` objetos.</span><span class="sxs-lookup"><span data-stu-id="c614a-198">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="c614a-199">Cualquier otro objeto que requiera el middleware puede insertarse de esta manera.</span><span class="sxs-lookup"><span data-stu-id="c614a-199">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="c614a-200">Carga de opciones de middleware mediante inyección directa</span><span class="sxs-lookup"><span data-stu-id="c614a-200">Loading middleware options through direct injection</span></span>

<span data-ttu-id="c614a-201">El patrón de opciones tiene la ventaja de que crea un acoplamiento flexible entre los valores de opciones y sus consumidores.</span><span class="sxs-lookup"><span data-stu-id="c614a-201">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="c614a-202">Una vez que haya asociado una clase de opciones con los valores de las opciones reales, cualquier otra clase puede obtener acceso a las opciones a través del marco de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="c614a-202">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="c614a-203">No es necesario pasar valores de opciones.</span><span class="sxs-lookup"><span data-stu-id="c614a-203">There's no need to pass around options values.</span></span>

<span data-ttu-id="c614a-204">Esto se interrumpe si desea utilizar el mismo middleware dos veces, con distintas opciones.</span><span class="sxs-lookup"><span data-stu-id="c614a-204">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="c614a-205">Por ejemplo, un middleware de autorización usado en distintas bifurcaciones, lo que permite diferentes roles.</span><span class="sxs-lookup"><span data-stu-id="c614a-205">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="c614a-206">No se pueden asociar dos objetos de opciones diferentes a una clase de opciones.</span><span class="sxs-lookup"><span data-stu-id="c614a-206">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="c614a-207">La solución consiste en obtener los objetos de opciones con los valores reales de las opciones de la clase `Startup` y pasarlos directamente a cada instancia del middleware.</span><span class="sxs-lookup"><span data-stu-id="c614a-207">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="c614a-208">Agregar una segunda clave a *appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="c614a-208">Add a second key to *appsettings.json*</span></span>

   <span data-ttu-id="c614a-209">Para agregar un segundo conjunto de opciones al archivo *appSettings. JSON* , use una nueva clave para identificarla de forma única:</span><span class="sxs-lookup"><span data-stu-id="c614a-209">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="c614a-210">Recupera los valores de opciones y los pasa a middleware.</span><span class="sxs-lookup"><span data-stu-id="c614a-210">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="c614a-211">El método de extensión `Use...` (que agrega el middleware a la canalización) es un lugar lógico para pasar los valores de opción:</span><span class="sxs-lookup"><span data-stu-id="c614a-211">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="c614a-212">Habilite el middleware para tomar un parámetro de opciones.</span><span class="sxs-lookup"><span data-stu-id="c614a-212">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="c614a-213">Proporcione una sobrecarga del método de extensión `Use...` (que toma el parámetro Options y lo pasa a `UseMiddleware`).</span><span class="sxs-lookup"><span data-stu-id="c614a-213">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="c614a-214">Cuando se llama a `UseMiddleware` con parámetros, pasa los parámetros a su constructor de middleware cuando crea una instancia del objeto middleware.</span><span class="sxs-lookup"><span data-stu-id="c614a-214">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="c614a-215">Observe cómo incluye el objeto de opciones en un objeto de `OptionsWrapper`.</span><span class="sxs-lookup"><span data-stu-id="c614a-215">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="c614a-216">Esto implementa `IOptions`, según lo esperado por el constructor de middleware.</span><span class="sxs-lookup"><span data-stu-id="c614a-216">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="c614a-217">Migración al nuevo HttpContext</span><span class="sxs-lookup"><span data-stu-id="c614a-217">Migrating to the new HttpContext</span></span>

<span data-ttu-id="c614a-218">Anteriormente, vio que el método `Invoke` del middleware toma un parámetro de tipo `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="c614a-218">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="c614a-219">`HttpContext` ha cambiado significativamente en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c614a-219">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="c614a-220">En esta sección se muestra cómo trasladar las propiedades usadas con más frecuencia de [System. Web. HttpContext](/dotnet/api/system.web.httpcontext) al nuevo `Microsoft.AspNetCore.Http.HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="c614a-220">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="c614a-221">HttpContext</span><span class="sxs-lookup"><span data-stu-id="c614a-221">HttpContext</span></span>

<span data-ttu-id="c614a-222">**HttpContext. Items** se convierte en:</span><span class="sxs-lookup"><span data-stu-id="c614a-222">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="c614a-223">**IDENTIFICADOR de solicitud único (no homólogo System. Web. HttpContext)**</span><span class="sxs-lookup"><span data-stu-id="c614a-223">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="c614a-224">Proporciona un identificador único para cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="c614a-224">Gives you a unique id for each request.</span></span> <span data-ttu-id="c614a-225">Es muy útil incluir en los registros.</span><span class="sxs-lookup"><span data-stu-id="c614a-225">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="c614a-226">HttpContext.Request</span><span class="sxs-lookup"><span data-stu-id="c614a-226">HttpContext.Request</span></span>

<span data-ttu-id="c614a-227">**HttpContext. request. HttpMethod** se convierte en:</span><span class="sxs-lookup"><span data-stu-id="c614a-227">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="c614a-228">**HttpContext. request. QueryString** se convierte en:</span><span class="sxs-lookup"><span data-stu-id="c614a-228">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="c614a-229">**HttpContext. request. URL** y **HttpContext. request. RawUrl** se traducen a:</span><span class="sxs-lookup"><span data-stu-id="c614a-229">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="c614a-230">**HttpContext. request. IsSecureConnection** se convierte en:</span><span class="sxs-lookup"><span data-stu-id="c614a-230">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="c614a-231">**HttpContext. request. UserHostAddress** se convierte en:</span><span class="sxs-lookup"><span data-stu-id="c614a-231">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="c614a-232">**HttpContext. request. cookies** se convierte en:</span><span class="sxs-lookup"><span data-stu-id="c614a-232">**HttpContext.Request.Cookies** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

<span data-ttu-id="c614a-233">**HttpContext. request. RequestContext. RouteData** se convierte en:</span><span class="sxs-lookup"><span data-stu-id="c614a-233">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="c614a-234">**HttpContext. request. Headers** se convierte en:</span><span class="sxs-lookup"><span data-stu-id="c614a-234">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="c614a-235">**HttpContext. request. UserAgent** se convierte en:</span><span class="sxs-lookup"><span data-stu-id="c614a-235">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="c614a-236">**HttpContext. request. UrlReferrer** se convierte en:</span><span class="sxs-lookup"><span data-stu-id="c614a-236">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="c614a-237">**HttpContext. request. ContentType** se convierte en:</span><span class="sxs-lookup"><span data-stu-id="c614a-237">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="c614a-238">**HttpContext. request. Form** se convierte en:</span><span class="sxs-lookup"><span data-stu-id="c614a-238">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="c614a-239">Lea los valores del formulario solo si el subtipo de contenido es *x-www-form-urlencoded* o *form-data*.</span><span class="sxs-lookup"><span data-stu-id="c614a-239">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="c614a-240">**HttpContext. request. InputStream** se convierte en:</span><span class="sxs-lookup"><span data-stu-id="c614a-240">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="c614a-241">Use este código únicamente en un middleware de tipo de controlador, al final de una canalización.</span><span class="sxs-lookup"><span data-stu-id="c614a-241">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="c614a-242">Puede leer el cuerpo sin procesar como se muestra anteriormente solo una vez por solicitud.</span><span class="sxs-lookup"><span data-stu-id="c614a-242">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="c614a-243">El middleware que intenta leer el cuerpo después de la primera lectura leerá un cuerpo vacío.</span><span class="sxs-lookup"><span data-stu-id="c614a-243">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="c614a-244">Esto no se aplica a la lectura de un formulario como se mostró anteriormente, ya que se realiza desde un búfer.</span><span class="sxs-lookup"><span data-stu-id="c614a-244">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="c614a-245">HttpContext.Response</span><span class="sxs-lookup"><span data-stu-id="c614a-245">HttpContext.Response</span></span>

<span data-ttu-id="c614a-246">**HttpContext. Response. status** y **HttpContext. Response. StatusDescription** se traducen a:</span><span class="sxs-lookup"><span data-stu-id="c614a-246">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="c614a-247">**HttpContext. Response. ContentEncoding** y **HttpContext. Response. ContentType** se traducen a:</span><span class="sxs-lookup"><span data-stu-id="c614a-247">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="c614a-248">**HttpContext. Response. ContentType** por sí mismo también se convierte en:</span><span class="sxs-lookup"><span data-stu-id="c614a-248">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="c614a-249">**HttpContext. Response. Output** se convierte en:</span><span class="sxs-lookup"><span data-stu-id="c614a-249">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="c614a-250">**HttpContext. Response. TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="c614a-250">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="c614a-251">El servicio de un archivo se describe [aquí](../fundamentals/request-features.md#middleware-and-request-features).</span><span class="sxs-lookup"><span data-stu-id="c614a-251">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="c614a-252">**HttpContext. Response. Headers**</span><span class="sxs-lookup"><span data-stu-id="c614a-252">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="c614a-253">El envío de encabezados de respuesta es complicado por el hecho de que, si los establece después de haber escrito algo en el cuerpo de la respuesta, no se enviarán.</span><span class="sxs-lookup"><span data-stu-id="c614a-253">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="c614a-254">La solución consiste en establecer un método de devolución de llamada que se llamará justo antes de que se inicie la respuesta.</span><span class="sxs-lookup"><span data-stu-id="c614a-254">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="c614a-255">Lo mejor es hacerlo al principio del método `Invoke` en el middleware.</span><span class="sxs-lookup"><span data-stu-id="c614a-255">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="c614a-256">Es este método de devolución de llamada que establece los encabezados de respuesta.</span><span class="sxs-lookup"><span data-stu-id="c614a-256">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="c614a-257">El código siguiente establece un método de devolución de llamada llamado `SetHeaders`:</span><span class="sxs-lookup"><span data-stu-id="c614a-257">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="c614a-258">El método de devolución de llamada `SetHeaders` tendría el siguiente aspecto:</span><span class="sxs-lookup"><span data-stu-id="c614a-258">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="c614a-259">**HttpContext. Response. cookies**</span><span class="sxs-lookup"><span data-stu-id="c614a-259">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="c614a-260">Las cookies viajan al explorador en un encabezado de respuesta *Set-Cookie* .</span><span class="sxs-lookup"><span data-stu-id="c614a-260">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="c614a-261">Como resultado, el envío de cookies requiere la misma devolución de llamada que se usa para enviar encabezados de respuesta:</span><span class="sxs-lookup"><span data-stu-id="c614a-261">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="c614a-262">El método de devolución de llamada de `SetCookies` sería similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="c614a-262">The `SetCookies` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a><span data-ttu-id="c614a-263">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c614a-263">Additional resources</span></span>

* [<span data-ttu-id="c614a-264">Información general sobre los controladores HTTP y los módulos HTTP</span><span class="sxs-lookup"><span data-stu-id="c614a-264">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="c614a-265">Configuración</span><span class="sxs-lookup"><span data-stu-id="c614a-265">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="c614a-266">Inicio de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="c614a-266">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="c614a-267">Software intermedio</span><span class="sxs-lookup"><span data-stu-id="c614a-267">Middleware</span></span>](xref:fundamentals/middleware/index)
