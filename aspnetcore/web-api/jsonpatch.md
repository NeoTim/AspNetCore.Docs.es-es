---
title: JsonPatch en la API web de ASP.NET Core
author: rick-anderson
description: Aprenda a administrar solicitudes JSON Patch en una API web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
uid: web-api/jsonpatch
ms.openlocfilehash: be4115e870dac818aeb6b1e65ddfb21e89d9cf25
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2020
ms.locfileid: "80625881"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a>JsonPatch en la API web de ASP.NET Core

Por [Tom Dykstra](https://github.com/tdykstra) y [Kirk Larkin](https://github.com/serpent5)

::: moniker range=">= aspnetcore-3.0"

En este artículo se explica cómo administrar solicitudes JSON Patch en una API web ASP.NET Core.

## <a name="package-installation"></a>Instalación del paquete

Para habilitar la compatibilidad con revisiones JSON en la aplicación, siga estos pasos:

1. Instalar el paquete NuGet [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/).
1. Actualice el método `Startup.ConfigureServices` del <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>proyecto para llamar a . Por ejemplo:

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

`AddNewtonsoftJson` es compatible con los métodos de registro del servicio MVC:

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a>JSON Patch, AddNewtonsoftJson y System.Text.Json

`AddNewtonsoftJson`reemplaza los `System.Text.Json`formateadores de entrada y salida basados en base utilizados para dar formato a **todo** el contenido JSON. Para agregar compatibilidad con `Newtonsoft.Json`JSON Patch mediante , sin modificar los `Startup.ConfigureServices` demás formateadores, actualice el método del proyecto de la siguiente manera:

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

El código anterior requiere `Microsoft.AspNetCore.Mvc.NewtonsoftJson` el paquete `using` y las instrucciones siguientes:

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a>Método de solicitud HTTP PATCH

Los métodos PUT y [PATCH](https://tools.ietf.org/html/rfc5789) se usan para actualizar un recurso existente. La diferencia entre ellos es que PUT reemplaza el recurso entero, mientras que PATCH especifica únicamente los cambios.

## <a name="json-patch"></a>JSON Patch

[JSON Patch](https://tools.ietf.org/html/rfc6902) es un formato para especificar las actualizaciones que se aplicarán a un recurso. Un documento JSON Patch tiene una matriz de *operaciones*. Cada operación identifica un tipo determinado de cambio. Algunos ejemplos de estos cambios son agregar un elemento de matriz o reemplazar un valor de propiedad.

Por ejemplo, los siguientes documentos JSON representan un recurso, un documento de revisión JSON para el recurso y el resultado de aplicar las operaciones Patch.

### <a name="resource-example"></a>Ejemplo de recursos

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>Ejemplo de revisión de JSON

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

En el código JSON anterior:

* La propiedad `op` indica el tipo de operación.
* La propiedad `path` indica el elemento que se va a actualizar.
* La propiedad `value` proporciona el nuevo valor.

### <a name="resource-after-patch"></a>Recurso después de la revisión

Este es el recurso después de aplicar el documento JSON Patch anterior:

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

Los cambios realizados mediante la aplicación de un documento de revisión JSON a un recurso son atómicos. Si se produce un error en cualquier operación de la lista, no se aplica ninguna operación de la lista.

## <a name="path-syntax"></a>Sintaxis de path

La propiedad [path](https://tools.ietf.org/html/rfc6901) de un objeto de operación tiene barras inversas entre niveles. Por ejemplo, `"/address/zipCode"`.

Para especificar elementos de matriz se usan índices de base cero. El primer elemento de la matriz `addresses` estaría en `/addresses/0`. Para `add` finales de una matriz, utilice`-`un guión ( `/addresses/-`) en lugar de un número de índice: .

### <a name="operations"></a>Operaciones

En la siguiente tabla se muestran las operaciones admitidas, como se ha definido en la [especificación de JSON Patch](https://tools.ietf.org/html/rfc6902):

|Operación  | Notas |
|-----------|--------------------------------|
| `add`     | Agrega un elemento de propiedad o matriz. Para la propiedad existente: establece el valor.|
| `remove`  | Quita un elemento de propiedad o matriz. |
| `replace` | Lo mismo que `remove` seguido de `add` en la misma ubicación. |
| `move`    | Lo mismo que `remove` desde el origen seguido de `add` al destino mediante el valor del origen. |
| `copy`    | Lo mismo que `add` al destino mediante el valor del origen. |
| `test`    | Devuelve el código de estado correcto si el valor en `path` = al `value` proporcionado.|

## <a name="json-patch-in-aspnet-core"></a>Parche JSON en ASP.NET Core

La implementación de ASP.NET Core de JSON Patch se proporciona en el paquete NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).

## <a name="action-method-code"></a>Código del método de acción

En un controlador de API, un método de acción para JSON Patch:

* Se anota con el atributo `HttpPatch`.
* Acepta `JsonPatchDocument<T>`, normalmente con `[FromBody]`.
* Llama a `ApplyTo` en el documento de revisión para aplicar los cambios.

Este es un ejemplo:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Este código de la aplicación `Customer` de ejemplo funciona con el siguiente modelo:

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

El método de acción de ejemplo:

* Construye un objeto `Customer`.
* Aplica la revisión.
* Devuelve el resultado en el cuerpo de la respuesta.

En una aplicación real, el código recuperaría los datos de un almacén como una base de datos y actualizaría la base de datos después de aplicar la revisión.

### <a name="model-state"></a>Estado del modelo

En el ejemplo anterior del método de acción, se llama a una sobrecarga de `ApplyTo` que toma el estado del modelo como uno de sus parámetros. Con esta opción, puede obtener mensajes de error en las respuestas. En el ejemplo siguiente se muestra el cuerpo de una respuesta 400 Solicitud incorrecta de una operación `test`:

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>Objetos dinámicos

En el siguiente ejemplo de método de acción se muestra cómo aplicar una revisión a un objeto dinámico:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>La operación add

* Si `path` apunta a un elemento de matriz: inserta un nuevo elemento delante del especificado por `path`.
* Si `path` apunta a una propiedad: establece el valor de la propiedad.
* Si `path` apunta a una ubicación que no existe:
  * Si el recurso para revisar es un objeto dinámico: agrega una propiedad.
  * Si el recurso para revisar es un objeto estático: la solicitud produce un error.

El siguiente documento de revisión de ejemplo establece el valor de `CustomerName` y agrega un objeto `Order` al final de la matriz `Orders`.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>La operación remove

* Si `path` apunta a un elemento de matriz: quita el elemento.
* Si `path` apunta a una propiedad:
  * Si el recurso para revisar es un objeto dinámico: quita la propiedad.
  * Si el recurso para revisar es un objeto estático:
    * Si la propiedad acepta valores NULL: la establece en null.
    * Si la propiedad es distinta de null, la establece en `default<T>`.

El siguiente documento `CustomerName` de revisión `Orders[0]`de ejemplo se establece en null y elimina:

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>La operación replace

Esta operación es funcionalmente igual que `remove` seguida de `add`.

El siguiente documento de revisión de `Orders[0]`ejemplo `Order` establece el valor de `CustomerName` y se reemplaza por un nuevo objeto:

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>La operación move

* Si `path` apunta a un elemento de matriz: copia el elemento `from` en la ubicación del elemento `path` y, luego, ejecuta una operación `remove` en el elemento `from`.
* Si `path` apunta a una propiedad: copia el valor de la propiedad `from` en la propiedad `path` y, luego, ejecuta la operación `remove` en la propiedad `from`.
* Si `path` apunta a una propiedad que no existe:
  * Si el recurso para revisar es un objeto estático: la solicitud produce un error.
  * Si el recurso para revisar es un objeto dinámico: copia la propiedad `from` en la ubicación indicada por `path` y, luego, ejecuta una operación `remove` en la propiedad `from`.

En el siguiente documento de revisión de ejemplo:

* Se copia el valor de `Orders[0].OrderName` en `CustomerName`.
* Se establece `Orders[0].OrderName` en null.
* Se mueve `Orders[1]` delante de `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>La operación copy

Esta operación es funcionalmente igual que la operación `move` sin el paso `remove` final.

En el siguiente documento de revisión de ejemplo:

* Se copia el valor de `Orders[0].OrderName` en `CustomerName`.
* Se inserta una copia de `Orders[1]` delante de `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>La operación test

Si el valor de la ubicación indicada por `path` es diferente del valor proporcionado en `value`, la solicitud produce un error. En ese caso, la solicitud PATCH entera produce un error incluso si todas las demás operaciones del documento de revisión se realizan correctamente.

La operación `test` se usa habitualmente para impedir una actualización cuando hay un conflicto de simultaneidad.

El siguiente documento de revisión de ejemplo no tiene ningún efecto si el valor inicial de `CustomerName` es "John", porque la prueba produce un error:

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Obtención del código

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples). ([Cómo descargar](xref:index#how-to-download-a-sample)).

Para probar el ejemplo, ejecute la aplicación y envíe solicitudes HTTP con la configuración siguiente:

* Dirección URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* Método HTTP: `PATCH`
* Encabezado: `Content-Type: application/json-patch+json`
* Cuerpo: copie y pegue uno de los ejemplos de documentos de revisión JSON de la carpeta del proyecto *JSON.*

## <a name="additional-resources"></a>Recursos adicionales

* [Especificación del método PATCH de IETF RFC 5789](https://tools.ietf.org/html/rfc5789)
* [Especificación del método JSON PATCH de IETF RFC 6902](https://tools.ietf.org/html/rfc6902)
* [Especificación del formato de ruta de acceso JSON Patch de IETF RFC 6901](https://tools.ietf.org/html/rfc6901)
* [Documentación de JSON Patch](https://jsonpatch.com/). Incluye vínculos a recursos para crear documentos JSON Patch.
* [Código fuente de JSON Patch para ASP.NET Core](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

En este artículo se explica cómo administrar solicitudes JSON Patch en una API web ASP.NET Core.

## <a name="patch-http-request-method"></a>Método de solicitud HTTP PATCH

Los métodos PUT y [PATCH](https://tools.ietf.org/html/rfc5789) se usan para actualizar un recurso existente. La diferencia entre ellos es que PUT reemplaza el recurso entero, mientras que PATCH especifica únicamente los cambios.

## <a name="json-patch"></a>JSON Patch

[JSON Patch](https://tools.ietf.org/html/rfc6902) es un formato para especificar las actualizaciones que se aplicarán a un recurso. Un documento JSON Patch tiene una matriz de *operaciones*. Cada operación identifica un determinado tipo de cambio, como agregar un elemento de matriz o reemplazar un valor de propiedad.

Por ejemplo, los documentos JSON siguientes representan un recurso, un documento de revisión JSON para el recurso y el resultado de aplicar las operaciones de revisión.

### <a name="resource-example"></a>Ejemplo de recursos

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>Ejemplo de revisión de JSON

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

En el código JSON anterior:

* La propiedad `op` indica el tipo de operación.
* La propiedad `path` indica el elemento que se va a actualizar.
* La propiedad `value` proporciona el nuevo valor.

### <a name="resource-after-patch"></a>Recurso después de la revisión

Este es el recurso después de aplicar el documento JSON Patch anterior:

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

Los cambios realizados mediante la aplicación de un documento JSON Patch a un recurso son atómicos: si alguna operación de la lista produce un error, no se aplica ninguna operación de la lista.

## <a name="path-syntax"></a>Sintaxis de path

La propiedad [path](https://tools.ietf.org/html/rfc6901) de un objeto de operación tiene barras inversas entre niveles. Por ejemplo, `"/address/zipCode"`.

Para especificar elementos de matriz se usan índices de base cero. El primer elemento de la matriz `addresses` estaría en `/addresses/0`. Para usar `add` al final de una matriz, use un guion (-) en lugar de un número de índice: `/addresses/-`.

### <a name="operations"></a>Operaciones

En la siguiente tabla se muestran las operaciones admitidas, como se ha definido en la [especificación de JSON Patch](https://tools.ietf.org/html/rfc6902):

|Operación  | Notas |
|-----------|--------------------------------|
| `add`     | Agrega un elemento de propiedad o matriz. Para la propiedad existente: establece el valor.|
| `remove`  | Quita un elemento de propiedad o matriz. |
| `replace` | Lo mismo que `remove` seguido de `add` en la misma ubicación. |
| `move`    | Lo mismo que `remove` desde el origen seguido de `add` al destino mediante el valor del origen. |
| `copy`    | Lo mismo que `add` al destino mediante el valor del origen. |
| `test`    | Devuelve el código de estado correcto si el valor en `path` = al `value` proporcionado.|

## <a name="jsonpatch-in-aspnet-core"></a>JsonPatch en ASP.NET Core

La implementación de ASP.NET Core de JSON Patch se proporciona en el paquete NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/). El paquete se incluye en el metapaquete [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app).

## <a name="action-method-code"></a>Código del método de acción

En un controlador de API, un método de acción para JSON Patch:

* Se anota con el atributo `HttpPatch`.
* Acepta `JsonPatchDocument<T>`, normalmente con `[FromBody]`.
* Llama a `ApplyTo` en el documento de revisión para aplicar los cambios.

Este es un ejemplo:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Este código de la aplicación de ejemplo funciona con el siguiente modelo `Customer`.

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

El método de acción de ejemplo:

* Construye un objeto `Customer`.
* Aplica la revisión.
* Devuelve el resultado en el cuerpo de la respuesta.

 En una aplicación real, el código recuperaría los datos de un almacén como una base de datos y actualizaría la base de datos después de aplicar la revisión.

### <a name="model-state"></a>Estado del modelo

En el ejemplo anterior del método de acción, se llama a una sobrecarga de `ApplyTo` que toma el estado del modelo como uno de sus parámetros. Con esta opción, puede obtener mensajes de error en las respuestas. En el ejemplo siguiente se muestra el cuerpo de una respuesta 400 Solicitud incorrecta de una operación `test`:

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>Objetos dinámicos

En el ejemplo siguiente de método de acción se muestra cómo aplicar una revisión a un objeto dinámico.

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>La operación add

* Si `path` apunta a un elemento de matriz: inserta un nuevo elemento delante del especificado por `path`.
* Si `path` apunta a una propiedad: establece el valor de la propiedad.
* Si `path` apunta a una ubicación que no existe:
  * Si el recurso para revisar es un objeto dinámico: agrega una propiedad.
  * Si el recurso para revisar es un objeto estático: la solicitud produce un error.

El siguiente documento de revisión de ejemplo establece el valor de `CustomerName` y agrega un objeto `Order` al final de la matriz `Orders`.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>La operación remove

* Si `path` apunta a un elemento de matriz: quita el elemento.
* Si `path` apunta a una propiedad:
  * Si el recurso para revisar es un objeto dinámico: quita la propiedad.
  * Si el recurso para revisar es un objeto estático:
    * Si la propiedad acepta valores NULL: la establece en null.
    * Si la propiedad es distinta de null, la establece en `default<T>`.

En el siguiente documento de revisión de ejemplo, se establece `CustomerName` en null y se elimina `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>La operación replace

Esta operación es funcionalmente igual que `remove` seguida de `add`.

En el siguiente documento de revisión de ejemplo se establece el valor de `CustomerName` y se reemplaza `Orders[0]` por un nuevo objeto `Order`.

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>La operación move

* Si `path` apunta a un elemento de matriz: copia el elemento `from` en la ubicación del elemento `path` y, luego, ejecuta una operación `remove` en el elemento `from`.
* Si `path` apunta a una propiedad: copia el valor de la propiedad `from` en la propiedad `path` y, luego, ejecuta la operación `remove` en la propiedad `from`.
* Si `path` apunta a una propiedad que no existe:
  * Si el recurso para revisar es un objeto estático: la solicitud produce un error.
  * Si el recurso para revisar es un objeto dinámico: copia la propiedad `from` en la ubicación indicada por `path` y, luego, ejecuta una operación `remove` en la propiedad `from`.

En el siguiente documento de revisión de ejemplo:

* Se copia el valor de `Orders[0].OrderName` en `CustomerName`.
* Se establece `Orders[0].OrderName` en null.
* Se mueve `Orders[1]` delante de `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>La operación copy

Esta operación es funcionalmente igual que la operación `move` sin el paso `remove` final.

En el siguiente documento de revisión de ejemplo:

* Se copia el valor de `Orders[0].OrderName` en `CustomerName`.
* Se inserta una copia de `Orders[1]` delante de `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>La operación test

Si el valor de la ubicación indicada por `path` es diferente del valor proporcionado en `value`, la solicitud produce un error. En ese caso, la solicitud PATCH entera produce un error incluso si todas las demás operaciones del documento de revisión se realizan correctamente.

La operación `test` se usa habitualmente para impedir una actualización cuando hay un conflicto de simultaneidad.

El siguiente documento de revisión de ejemplo no tiene ningún efecto si el valor inicial de `CustomerName` es "John", porque la prueba produce un error:

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Obtención del código

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2). ([Cómo descargar](xref:index#how-to-download-a-sample)).

Para probar el ejemplo, ejecute la aplicación y envíe solicitudes HTTP con la configuración siguiente:

* Dirección URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* Método HTTP: `PATCH`
* Encabezado: `Content-Type: application/json-patch+json`
* Cuerpo: copie y pegue uno de los ejemplos de documentos de revisión JSON de la carpeta del proyecto *JSON.*

## <a name="additional-resources"></a>Recursos adicionales

* [Especificación del método PATCH de IETF RFC 5789](https://tools.ietf.org/html/rfc5789)
* [Especificación del método JSON PATCH de IETF RFC 6902](https://tools.ietf.org/html/rfc6902)
* [Especificación del formato de ruta de acceso JSON Patch de IETF RFC 6901](https://tools.ietf.org/html/rfc6901)
* [Documentación de JSON Patch](https://jsonpatch.com/). Incluye vínculos a recursos para crear documentos JSON Patch.
* [Código fuente de JSON Patch para ASP.NET Core](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
