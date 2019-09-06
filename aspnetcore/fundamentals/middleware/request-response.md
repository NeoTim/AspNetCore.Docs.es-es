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
# <a name="request-and-response-operations-in-aspnet-core"></a>Operaciones de solicitud y respuesta en ASP.NET Core

Por [Justin Kotalik](https://github.com/jkotalik)

En este artículo se explica cómo leer el cuerpo de la solicitud y escribir el cuerpo de respuesta. El código para estas operaciones podría ser necesario al escribir middleware. Fuera de la escritura de middleware, el código personalizado no suele ser necesario porque MVC y Razor Pages controlan las operaciones.

Hay dos abstracciones para los cuerpos de solicitud y respuesta: <xref:System.IO.Stream> y <xref:System.IO.Pipelines.Pipe>. Para leer la solicitud, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) es un objeto <xref:System.IO.Stream> y `HttpRequest.BodyReader` es un objeto <xref:System.IO.Pipelines.PipeReader>. Para escribir la respuesta, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) es un objeto `HttpResponse.BodyWriter` y es un objeto <xref:System.IO.Pipelines.PipeWriter>.

Se recomienda el uso de canalizaciones por encima del uso de secuencias. Las secuencias pueden ser más fáciles de usar en el caso de algunas operaciones sencillas, pero las canalizaciones son más ventajosas para el rendimiento y son más fáciles de usar en la mayoría de los casos. ASP.NET Core está empezando a usar internamente canalizaciones en lugar de secuencias. Ejemplos:

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

Las secuencias no se quitan del marco. Se seguirán usando en todo .NET, y además muchos tipos de secuencias no tienen equivalentes de canalización, como `FileStreams` y `ResponseCompression`.

## <a name="stream-examples"></a>Ejemplos de secuencias

Imagine que el objetivo es crear un middleware que lee el cuerpo de la solicitud entero como una lista de cadenas, que se divide en nuevas líneas. Una implementación de secuencias sencilla podría parecerse al ejemplo siguiente:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

Este código funciona, pero hay algunos problemas:

* Antes de realizar la anexión a `StringBuilder`, en el ejemplo se crea otra cadena (`encodedString`) que se desecha inmediatamente. Este proceso se produce con todos los bytes de la secuencia, por lo que el resultado es la asignación de memoria adicional del tamaño del cuerpo de la solicitud entera.
* En el ejemplo se lee toda la cadena antes de la división en nuevas líneas. Sería más eficaz buscar nuevas líneas en la matriz de bytes.

En este ejemplo se corrigen algunos de los problemas anteriores:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

Este ejemplo anterior:

* No se almacena en búfer todo el cuerpo de la solicitud en `StringBuilder` a menos que no haya ningún carácter de nueva línea.
* No se llama a `Split` en la cadena.

Sin embargo, todavía hay algunos problemas:

* Si los caracteres de nueva línea están dispersos, gran parte del cuerpo de la solicitud se almacena en búfer en la cadena.
* El código sigue creando cadenas (`remainingString`) y agrega al búfer de cadenas, lo que resulta en una asignación adicional.

Estos problemas se pueden corregir, pero el código se vuelve cada vez más complicado y las mejoras son pocas. Las canalizaciones ofrecen una manera de resolver estos problemas con una complejidad mínima del código.

## <a name="pipelines"></a>Canalizaciones

En el ejemplo siguiente se muestra cómo se puede gestionar el mismo escenario mediante un objeto `PipeReader`:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

En este ejemplo se solucionan muchos problemas que tenían las implementaciones de secuencias:

* No es necesario un búfer de cadenas porque `PipeReader` controla los bytes que no se han usado.
* Las cadenas codificadas se agregan directamente a la lista de cadenas devueltas.
* La creación de cadenas es de libre asignación, además de la memoria usada por la cadena (excepto la llamada `ToArray()`).

## <a name="adapters"></a>Adaptadores

Las propiedades `Body` y `BodyReader/BodyWriter` están disponibles para `HttpRequest` y `HttpResponse`. Al establecer `Body` en una secuencia diferente, un nuevo conjunto de adaptadores se adaptan automáticamente al tipo del otro. Si establece `HttpRequest.Body` en una nueva secuencia, `HttpRequest.BodyReader` se establece automáticamente en un nuevo objeto `PipeReader` que encapsula a `HttpRequest.Body`.

## <a name="startasync"></a>StartAsync

`HttpResponse.StartAsync` se usa para indicar que los encabezados no se pueden modificar y para ejecutar devoluciones de llamada `OnStarting`. Al usar Kestrel como servidor, si se llama a `StartAsync` antes de usar el objeto `PipeReader`, se garantiza que la memoria que devuelve `GetMemory` pertenezca al objeto interno <xref:System.IO.Pipelines.Pipe> de Kestrel en lugar de a un búfer externo.

## <a name="additional-resources"></a>Recursos adicionales

* [Introducción a System.IO.Pipelines](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>
