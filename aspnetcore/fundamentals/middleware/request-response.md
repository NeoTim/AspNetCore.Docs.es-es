---
title: Operaciones de solicitud y respuesta en ASP.NET Core
author: jkotalik
description: Aprenda a leer el cuerpo de la solicitud y a escribir el cuerpo de respuesta en ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 08/29/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: e992401da2d194b178afbe51a293d103def0f940
ms.sourcegitcommit: e6bd2bbe5683e9a7dbbc2f2eab644986e6dc8a87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70238156"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="f613f-103">Operaciones de solicitud y respuesta en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f613f-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="f613f-104">Por [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="f613f-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="f613f-105">En este artículo se explica cómo leer el cuerpo de la solicitud y escribir el cuerpo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="f613f-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="f613f-106">El código para estas operaciones podría ser necesario al escribir middleware.</span><span class="sxs-lookup"><span data-stu-id="f613f-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="f613f-107">Fuera de la escritura de middleware, el código personalizado no suele ser necesario porque MVC y Razor Pages controlan las operaciones.</span><span class="sxs-lookup"><span data-stu-id="f613f-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="f613f-108">Hay dos abstracciones para los cuerpos de solicitud y respuesta: <xref:System.IO.Stream> y <xref:System.IO.Pipelines.Pipe>.</span><span class="sxs-lookup"><span data-stu-id="f613f-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="f613f-109">Para leer la solicitud, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) es un objeto <xref:System.IO.Stream> y `HttpRequest.BodyReader` es un objeto <xref:System.IO.Pipelines.PipeReader>.</span><span class="sxs-lookup"><span data-stu-id="f613f-109">For request reading, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="f613f-110">Para escribir la respuesta, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) es un objeto `HttpResponse.BodyWriter` y es un objeto <xref:System.IO.Pipelines.PipeWriter>.</span><span class="sxs-lookup"><span data-stu-id="f613f-110">For response writing, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) is a `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="f613f-111">Se recomienda el uso de canalizaciones por encima del uso de secuencias.</span><span class="sxs-lookup"><span data-stu-id="f613f-111">Pipelines are recommended over streams.</span></span> <span data-ttu-id="f613f-112">Las secuencias pueden ser más fáciles de usar en el caso de algunas operaciones sencillas, pero las canalizaciones son más ventajosas para el rendimiento y son más fáciles de usar en la mayoría de los casos.</span><span class="sxs-lookup"><span data-stu-id="f613f-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="f613f-113">ASP.NET Core está empezando a usar internamente canalizaciones en lugar de secuencias.</span><span class="sxs-lookup"><span data-stu-id="f613f-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="f613f-114">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="f613f-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="f613f-115">Las secuencias no se quitan del marco.</span><span class="sxs-lookup"><span data-stu-id="f613f-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="f613f-116">Se seguirán usando en todo .NET, y además muchos tipos de secuencias no tienen equivalentes de canalización, como `FileStreams` y `ResponseCompression`.</span><span class="sxs-lookup"><span data-stu-id="f613f-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="f613f-117">Ejemplos de secuencias</span><span class="sxs-lookup"><span data-stu-id="f613f-117">Stream examples</span></span>

<span data-ttu-id="f613f-118">Imagine que el objetivo es crear un middleware que lee el cuerpo de la solicitud entero como una lista de cadenas, que se divide en nuevas líneas.</span><span class="sxs-lookup"><span data-stu-id="f613f-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="f613f-119">Una implementación de secuencias sencilla podría parecerse al ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f613f-119">A simple stream implementation might look like the following example:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

<span data-ttu-id="f613f-120">Este código funciona, pero hay algunos problemas:</span><span class="sxs-lookup"><span data-stu-id="f613f-120">This code works, but there are some issues:</span></span>

* <span data-ttu-id="f613f-121">Antes de realizar la anexión a `StringBuilder`, en el ejemplo se crea otra cadena (`encodedString`) que se desecha inmediatamente.</span><span class="sxs-lookup"><span data-stu-id="f613f-121">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="f613f-122">Este proceso se produce con todos los bytes de la secuencia, por lo que el resultado es la asignación de memoria adicional del tamaño del cuerpo de la solicitud entera.</span><span class="sxs-lookup"><span data-stu-id="f613f-122">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="f613f-123">En el ejemplo se lee toda la cadena antes de la división en nuevas líneas.</span><span class="sxs-lookup"><span data-stu-id="f613f-123">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="f613f-124">Sería más eficaz buscar nuevas líneas en la matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="f613f-124">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="f613f-125">En este ejemplo se corrigen algunos de los problemas anteriores:</span><span class="sxs-lookup"><span data-stu-id="f613f-125">Here's an example that fixes some of the preceding issues:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="f613f-126">Este ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="f613f-126">This preceding example:</span></span>

* <span data-ttu-id="f613f-127">No se almacena en búfer todo el cuerpo de la solicitud en `StringBuilder` a menos que no haya ningún carácter de nueva línea.</span><span class="sxs-lookup"><span data-stu-id="f613f-127">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="f613f-128">No se llama a `Split` en la cadena.</span><span class="sxs-lookup"><span data-stu-id="f613f-128">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="f613f-129">Sin embargo, todavía hay algunos problemas:</span><span class="sxs-lookup"><span data-stu-id="f613f-129">However, there are still are a few issues:</span></span>

* <span data-ttu-id="f613f-130">Si los caracteres de nueva línea están dispersos, gran parte del cuerpo de la solicitud se almacena en búfer en la cadena.</span><span class="sxs-lookup"><span data-stu-id="f613f-130">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="f613f-131">El código sigue creando cadenas (`remainingString`) y agrega al búfer de cadenas, lo que resulta en una asignación adicional.</span><span class="sxs-lookup"><span data-stu-id="f613f-131">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="f613f-132">Estos problemas se pueden corregir, pero el código se vuelve cada vez más complicado y las mejoras son pocas.</span><span class="sxs-lookup"><span data-stu-id="f613f-132">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="f613f-133">Las canalizaciones ofrecen una manera de resolver estos problemas con una complejidad mínima del código.</span><span class="sxs-lookup"><span data-stu-id="f613f-133">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="f613f-134">Canalizaciones</span><span class="sxs-lookup"><span data-stu-id="f613f-134">Pipelines</span></span>

<span data-ttu-id="f613f-135">En el ejemplo siguiente se muestra cómo se puede gestionar el mismo escenario mediante un objeto `PipeReader`:</span><span class="sxs-lookup"><span data-stu-id="f613f-135">The following example shows how the same scenario can be handled using a `PipeReader`:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="f613f-136">En este ejemplo se solucionan muchos problemas que tenían las implementaciones de secuencias:</span><span class="sxs-lookup"><span data-stu-id="f613f-136">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="f613f-137">No es necesario un búfer de cadenas porque `PipeReader` controla los bytes que no se han usado.</span><span class="sxs-lookup"><span data-stu-id="f613f-137">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="f613f-138">Las cadenas codificadas se agregan directamente a la lista de cadenas devueltas.</span><span class="sxs-lookup"><span data-stu-id="f613f-138">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="f613f-139">La creación de cadenas es de libre asignación, además de la memoria usada por la cadena (excepto la llamada `ToArray()`).</span><span class="sxs-lookup"><span data-stu-id="f613f-139">String creation is allocation-free besides the memory used by the string (except the `ToArray()` call).</span></span>

## <a name="adapters"></a><span data-ttu-id="f613f-140">Adaptadores</span><span class="sxs-lookup"><span data-stu-id="f613f-140">Adapters</span></span>

<span data-ttu-id="f613f-141">Las propiedades `Body` y `BodyReader/BodyWriter` están disponibles para `HttpRequest` y `HttpResponse`.</span><span class="sxs-lookup"><span data-stu-id="f613f-141">Both `Body` and `BodyReader/BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="f613f-142">Al establecer `Body` en una secuencia diferente, un nuevo conjunto de adaptadores se adaptan automáticamente al tipo del otro.</span><span class="sxs-lookup"><span data-stu-id="f613f-142">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="f613f-143">Si establece `HttpRequest.Body` en una nueva secuencia, `HttpRequest.BodyReader` se establece automáticamente en un nuevo objeto `PipeReader` que encapsula a `HttpRequest.Body`.</span><span class="sxs-lookup"><span data-stu-id="f613f-143">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="f613f-144">StartAsync</span><span class="sxs-lookup"><span data-stu-id="f613f-144">StartAsync</span></span>

<span data-ttu-id="f613f-145">`HttpResponse.StartAsync` se usa para indicar que los encabezados no se pueden modificar y para ejecutar devoluciones de llamada `OnStarting`.</span><span class="sxs-lookup"><span data-stu-id="f613f-145">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="f613f-146">Al usar Kestrel como servidor, si se llama a `StartAsync` antes de usar el objeto `PipeReader`, se garantiza que la memoria que devuelve `GetMemory` pertenezca al objeto interno <xref:System.IO.Pipelines.Pipe> de Kestrel en lugar de a un búfer externo.</span><span class="sxs-lookup"><span data-stu-id="f613f-146">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f613f-147">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="f613f-147">Additional resources</span></span>

* [<span data-ttu-id="f613f-148">Introducción a System.IO.Pipelines</span><span class="sxs-lookup"><span data-stu-id="f613f-148">Introducing System.IO.Pipelines</span></span>](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>
