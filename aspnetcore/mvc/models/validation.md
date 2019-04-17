---
title: Validación de modelos en ASP.NET Core MVC
author: tdykstra
description: Obtenga información sobre la validación de modelos en ASP.NET Core MVC y Razor Pages.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
monikerRange: '>= aspnetcore-2.1'
uid: mvc/models/validation
ms.openlocfilehash: 1ae3c20478b02d6f654e65fdf34c88e1ffb837f8
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/10/2019
ms.locfileid: "59468742"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a>Validación de modelos en ASP.NET Core MVC y Razor Pages

En este artículo se explica cómo validar la entrada del usuario en una aplicación ASP.NET Core MVC o Razor Pages.

[Vea o descargue el código de ejemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).

## <a name="model-state"></a>Estado del modelo

El estado del modelo representa los errores que proceden de dos subsistemas: el enlace de modelos y la validación de modelos. Los errores que se originan en el [enlace de modelos](model-binding.md) suelen ser errores de conversión de datos (por ejemplo, se especifica una "x" en un campo que espera un entero). La validación de modelos se produce después de enlace de modelos y notifica errores cuando los datos no se ajustan a las reglas de negocios (por ejemplo, se especifica un 0 en un campo que espera una clasificación entre 1 y 5).

Tanto el enlace como la validación de modelos se producen antes de la ejecución de una acción de controlador o un método de controlador de Razor Pages. En el caso de las aplicaciones web, la aplicación es responsable de inspeccionar `ModelState.IsValid` y reaccionar de manera apropiada. Normalmente, las aplicaciones web vuelven a mostrar la página con un mensaje de error:

[!code-csharp[](validation/sample_snapshot/Create.cshtml.cs?name=snippet&highlight=3-6)]

Los controladores de Web API no tienen que comprobar `ModelState.IsValid` si tienen el atributo `[ApiController]`. En ese caso, se devuelve una respuesta HTTP 400 automática que contiene los detalles del problema cuando el estado del modelo no es válido. Para obtener más información, consulte [Respuestas HTTP 400 automáticas](xref:web-api/index#automatic-http-400-responses).

## <a name="rerun-validation"></a>Nueva ejecución de la validación

La validación es automática, pero tal vez le interese repetirla manualmente. Por ejemplo, tal vez haya calculado el valor de una propiedad y quiera volver a ejecutar la validación después de establecer la propiedad en el valor calculado. Para volver a ejecutar la validación, llame al método `TryValidateModel`, como se muestra aquí:

[!code-csharp[](validation/sample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a>Atributos de validación

Los atributos de validación permiten especificar reglas de validación para las propiedades del modelo. En el ejemplo siguiente de la [aplicación de ejemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/validation/sample) se muestra una clase de modelo anotada con atributos de validación. El atributo `[ClassicMovie]` es un atributo de validación personalizado y los demás están integrados. (No se muestra `[ClassicMovie2]`, que indica una manera alternativa de implementar un atributo personalizado).

[!code-csharp[](validation/sample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a>Atributos integrados

Estos son algunos de los atributos de validación integrados:

* `[CreditCard]`: valida que la propiedad tenga formato de tarjeta de crédito.
* `[Compare]`: valida que dos propiedades de un modelo coincidan.
* `[EmailAddress]`: valida que la propiedad tenga formato de correo electrónico.
* `[Phone]`: valida que la propiedad tenga formato de número de teléfono.
* `[Range]`: valida que el valor de propiedad se encuentre dentro de un intervalo especificado.
* `[RegularExpression]`: valida que el valor de propiedad coincida con una expresión regular especificada.
* `[Required]`: valida que el campo no sea NULL. Vea [Atributo [Required]](#required-attribute) para obtener más información sobre el comportamiento de este atributo.
* `[StringLength]`: valida que un valor de propiedad de cadena no supere un límite de longitud especificado.
* `[Url]`: valida que la propiedad tenga un formato de URL.
* `[Remote]`: valida la entrada en el cliente mediante una llamada a un método de acción en el servidor. Vea [Atributo [Remote]](#remote-attribute) para obtener más información sobre el comportamiento de este atributo.

En el espacio de nombres [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) encontrará una lista completa de atributos de validación.

### <a name="error-messages"></a>Mensajes de error

Los atributos de validación permiten especificar el mensaje de error que se mostrará para una entrada no válida. Por ejemplo:

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

Internamente, los atributos llaman a `String.Format` con un marcador de posición para el nombre de campo y, en ocasiones, marcadores de posición adicionales. Por ejemplo:

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

Cuando se aplica a una propiedad `Name`, el mensaje de error creado por el código anterior sería "La longitud del nombre debe estar entre 6 y 8".

Para averiguar qué parámetros se pasan a `String.Format` para el mensaje de error de un atributo determinado, vea el [código fuente de DataAnnotations](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).

## <a name="required-attribute"></a>Atributo [Required]

De forma predeterminada, el sistema de validación trata las propiedades o los parámetros que no aceptan valores NULL como si tuvieran un atributo `[Required]`. Los [tipos de valor](/dotnet/csharp/language-reference/keywords/value-types) como `decimal` y `int` no aceptan valores NULL.

### <a name="required-validation-on-the-server"></a>Validación de [Required] en el servidor

En el servidor, si la propiedad es NULL, se considera que falta un valor requerido. Un campo que no acepta valores NULL siempre es válido, y el mensaje de error del atributo [Required] no se muestra nunca.

Aun así, el enlace de modelos para una propiedad que no acepta valores NULL podría fallar, lo que genera un mensaje de error como `The value '' is invalid`. Para especificar un mensaje de error personalizado para la validación del lado servidor de tipos que no aceptan valores NULL, tiene las siguientes opciones:

* Haga que el campo acepte valores NULL (por ejemplo, `decimal?` en lugar de `decimal`). Los tipos de valor [Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) se tratan como tipos estándar que aceptan valores NULL.
* Especifique el mensaje de error predeterminado que el enlace de modelos va a usar, como se muestra en el ejemplo siguiente:

  [!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  Para obtener más información sobre los errores de enlace de modelos para los que se pueden establecer mensajes predeterminados, vea <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.

### <a name="required-validation-on-the-client"></a>Validación de [Required] en el cliente

Las cadenas y los tipos que no aceptan valores NULL se tratan de forma diferente en el cliente, en comparación con el servidor. En el cliente:

* Un valor se considera presente solo si se especifica una entrada para él. Por lo tanto, la validación del lado cliente controla los tipos que no aceptan valores NULL del mismo modo que los tipos que aceptan valores NULL.
* El método [required](https://jqueryvalidation.org/required-method/) de jQuery Validate considera que un espacio en blanco en un campo de cadena es una entrada válida. La validación del lado servidor considera que un campo de cadena necesario no es válido si solo se especifica un espacio en blanco.

Como se indicó anteriormente, los tipos que no aceptan valores NULL se tratan como si tuvieran un atributo `[Required]`. Esto significa que se obtiene la validación del lado cliente incluso si no se aplica el atributo `[Required]`. Si no usa el atributo, aparecerá un mensaje de error predeterminado. Para especificar un mensaje de error personalizado, use el atributo.

## <a name="remote-attribute"></a>Atributo [Remote]

El atributo `[Remote]` implementa la validación del lado cliente que requiere llamar a un método en el servidor para determinar si la entrada del campo es válida. Por ejemplo, la aplicación podría tener que comprobar si un nombre de usuario ya está en uso.

Para implementar la validación remota:

1. Cree un método de acción para que lo llame JavaScript.  El método [remote](https://jqueryvalidation.org/remote-method/) de jQuery Validate espera una respuesta JSON:

   * `"true"` significa que los datos de entrada son válidos.
   * `"false"`, `undefined` o `null` significan que la entrada no es válida.  Muestre el mensaje de error predeterminado.
   * Cualquier otra cadena significa que la entrada no es válida. Muestre la cadena como un mensaje de error personalizado.

   A continuación encontrará un ejemplo de un método de acción que devuelve un mensaje de error personalizado:

   [!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. En la clase de modelo, anote la propiedad con un atributo `[Remote]` que apunte al método de acción de validación, como se muestra en el ejemplo siguiente:

   [!code-csharp[](validation/sample/Models/User.cs?name=snippet_UserEmailProperty)]

### <a name="additional-fields"></a>Campos adicionales

La propiedad `AdditionalFields` del atributo `[Remote]` permite validar combinaciones de campos con los datos del servidor. Por ejemplo, si el modelo `User` tuviera las propiedades `FirstName` y `LastName`, podría interesarle comprobar que ningún usuario actual tuviera ya ese par de nombres. En el siguiente ejemplo se muestra cómo usar `AdditionalFields`:

[!code-csharp[](validation/sample/Models/User.cs?name=snippet_UserNameProperties)]

`AdditionalFields` podría configurarse explícitamente para las cadenas `"FirstName"` y `"LastName"`, pero, al usar el operador [`nameof`](/dotnet/csharp/language-reference/keywords/nameof), se simplifica la refactorización posterior. El método de acción para esta validación debe aceptar los argumentos de nombre y apellido:

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyName)]

Cuando el usuario escribe un nombre o un apellido, JavaScript realiza una llamada remota para comprobar si ese par de nombres ya existe.

Para validar dos o más campos adicionales, proporciónelos como una lista delimitada por comas. Por ejemplo, para agregar una propiedad `MiddleName` al modelo, establezca el atributo `[Remote]` tal como se muestra en el ejemplo siguiente:

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

`AdditionalFields`, al igual que todos los argumentos de atributo, debe ser una expresión constante. Por lo tanto, no use una [cadena interpolada](/dotnet/csharp/language-reference/keywords/interpolated-strings) ni llame a <xref:System.String.Join*> para inicializar `AdditionalFields`.

## <a name="alternatives-to-built-in-attributes"></a>Alternativas a los atributos integrados

Si necesita una validación que no proporcionan los atributos integrados, puede hacer lo siguiente:

* [Crear atributos personalizados](#custom-attributes)
* [Implementar IValidatableObject](#ivalidatableobject)

## <a name="custom-attributes"></a>Atributos personalizados

Para los escenarios que no se controlan mediante los atributos de validación integrados, puede crear atributos de validación personalizados. Cree una clase que herede de <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> y reemplace el método <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.

El método `IsValid` acepta un objeto denominado *value*, que es la entrada que se va a validar. Una sobrecarga también acepta un objeto `ValidationContext`, que proporciona información adicional, como la instancia del modelo creada por el enlace de modelos.

El siguiente ejemplo valida que la fecha de lanzamiento de una película del género *Classic* no sea posterior a un año especificado. El atributo `[ClassicMovie2]` comprueba primero el género y continúa únicamente si es *Classic*. Para las películas que se identifican como clásicos, comprueba la fecha de lanzamiento a fin de asegurarse de que no sea posterior al límite que se ha pasado al constructor de atributo.

[!code-csharp[](validation/sample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

La variable `movie` del ejemplo anterior representa un objeto `Movie` que contiene los datos del envío del formulario. El método `IsValid` comprueba la fecha y el género. Si la validación es correcta, `IsValid` devuelve un código `ValidationResult.Success`. Si se produce un error de validación, se devuelve un `ValidationResult` con un mensaje de error.

## <a name="ivalidatableobject"></a>IValidatableObject

El ejemplo anterior solo funciona con tipos `Movie`. Otra opción para la validación del nivel de clase consiste en implementar `IValidatableObject` en la clase de modelo, como se muestra en el ejemplo siguiente:

[!code-csharp[](validation/sample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a>Validación de nodo de nivel superior

Los nodos de nivel superior incluyen lo siguiente:

* Parámetros de acción
* Propiedades de controlador
* Parámetros de controlador de página
* Propiedades de modelo de página

Los nodos de nivel superior enlazados al modelo se validan además de la validación de las propiedades del modelo. En el ejemplo siguiente de la aplicación de muestra, el método `VerifyPhone` usa <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> para validar el parámetro de acción `phone`:

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

Los nodos de nivel superior pueden usar <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> con atributos de validación. En el ejemplo siguiente de la aplicación de muestra, el método `CheckAge` especifica que el parámetro `age` debe estar enlazado desde la cadena de consulta al enviar el formulario:

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_CheckAge)]

En la página Comprobar edad (*CheckAge.cshtml*), hay dos formularios. El primer formulario envía un valor `Age` de `99` como una cadena de consulta: `https://localhost:5001/Users/CheckAge?Age=99`.

Al enviar un parámetro `age` con un formato correcto desde la cadena de consulta, el formulario se valida.

El segundo formulario de la página Comprobar edad envía el valor `Age` en el cuerpo de la solicitud, y se produce un error de validación. Se produce un error en el enlace porque el parámetro `age` debe provenir de una cadena de consulta.

Cuando se ejecuta con `CompatibilityVersion.Version_2_1` o versiones posteriores, la validación de nodo de nivel superior está habilitada de forma predeterminada. En caso contrario, la validación del nodo de nivel superior está deshabilitada. La opción predeterminada se puede invalidar si se establece la propiedad <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> en (`Startup.ConfigureServices`), como se muestra aquí:

[!code-csharp[](validation/sample_snapshot/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a>Número máximo de errores

La validación se detiene cuando se alcanza el número máximo de errores (200 de forma predeterminada). Puede configurar este número con el siguiente código en `Startup.ConfigureServices`:

[!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a>Recursividad máxima

<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> recorre el gráfico de objetos del modelo que se está validando. En el caso de los modelos muy profundos o infinitamente recursivos, la validación podría causar un desbordamiento de pila. [MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) proporciona una manera de detener pronto la validación si la recursividad del visitante supera la profundidad configurada. El valor predeterminado de `MvcOptions.MaxValidationDepth` es 200 cuando se ejecuta con `CompatibilityVersion.Version_2_2` o una versión posterior. Para las versiones anteriores, el valor es NULL, lo que significa que no hay restricción de profundidad.

## <a name="automatic-short-circuit"></a>Cortocircuito automático

La validación cortocircuita (se omite) automáticamente si el gráfico de modelo no requiere validación. Entre los objetos para los que el tiempo de ejecución omite la validación se incluyen las colecciones de elementos primitivos (como `byte[]`, `string[]` y `Dictionary<string, string>`) y gráficos de objeto complejo que no tienen los validadores.

## <a name="disable-validation"></a>Deshabilitación de la validación

Para deshabilitar la validación:

1. Cree una implementación de `IObjectModelValidator` que no marque ningún campo como no válido.

   [!code-csharp[](validation/sample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. Agregue el código siguiente a `Startup.ConfigureServices` para reemplazar la implementación predeterminada de `IObjectModelValidator` en el contenedor de inserción de dependencias.

   [!code-csharp[](validation/sample/Startup.cs?name=snippet_DisableValidation)]

Es posible que todavía vea errores de estado del modelo originados en el enlace de modelos.

## <a name="client-side-validation"></a>Validación del lado cliente

La validación del lado cliente impide realizar el envío hasta que el formulario sea válido. El botón Enviar ejecuta JavaScript para enviar el formulario o mostrar mensajes de error.

La validación del lado cliente evita un recorrido de ida y vuelta innecesario en el servidor cuando hay errores de entrada en un formulario. Las siguientes referencias de script de *_Layout.cshtml* y *_ValidationScriptsPartial.cshtml* admiten la validación del lado cliente:

[!code-cshtml[](validation/sample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

El script [Validación discreta de jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) es una biblioteca front-end personalizada de Microsoft que se basa en el conocido complemento [Validación de jQuery](https://jqueryvalidation.org/). Si no usa Validación discreta de jQuery, deberá codificar la misma lógica de validación dos veces: una vez en los atributos de validación del lado servidor en las propiedades del modelo y luego en los scripts del lado cliente. En su lugar, los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) y los [asistentes de HTML](xref:mvc/views/overview) usan los atributos de validación y escriben metadatos de las propiedades del modelo para representar atributos `data-` HTML 5 para los elementos de formulario que necesitan validación. Validación discreta de jQuery analiza los atributos `data-` y pasa la lógica a jQuery Validate. De este modo, la lógica de validación del lado servidor se "copia" de manera eficaz en el cliente. Puede mostrar errores de validación en el cliente mediante el uso de asistentes de etiquetas, como se muestra aquí:

[!code-cshtml[](validation/sample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

Los anteriores asistentes de etiquetas representan el siguiente código HTML.

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

Tenga en cuenta que los atributos `data-` en los resultados HTML corresponden a los atributos de validación para la propiedad `ReleaseDate`. El atributo `data-val-required` contiene un mensaje de error que se muestra si el usuario no rellena el campo de fecha de estreno. La Validación discreta de jQuery pasa este valor al método [`required()`](https://jqueryvalidation.org/required-method/) de la Validación de jQuery, que muestra un mensaje en el elemento **\<span>** que lo acompaña.

La validación del tipo de datos se basa en el tipo .NET de una propiedad, a menos que lo reemplace un atributo `[DataType]`. Los exploradores tienen sus propios mensajes de error de predeterminados, pero el paquete de Validación discreta de jQuery Validate puede invalidar esos mensajes. `[DataType]` Los atributos y las subclases `[EmailAddress]`, como , permiten especificar el mensaje de error.

### <a name="add-validation-to-dynamic-forms"></a>Agregar validación a formularios dinámicos

Validación discreta de jQuery pasa los parámetros y la lógica de validación a jQuery Validate cuando la página se carga por primera vez. Por lo tanto, la validación no funciona automáticamente en los formularios generados dinámicamente. Para habilitar la validación, hay que indicarle a Validación discreta de jQuery que analice el formulario dinámico inmediatamente después de su creación. Por ejemplo, en el código siguiente se configura la validación del lado cliente en un formulario agregado mediante AJAX.

```js
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

El método `$.validator.unobtrusive.parse()` acepta un selector de jQuery para su único argumento. Este método indica a Validación discreta de jQuery que analice los atributos `data-` de formularios dentro de ese selector. Después, los valores de estos atributos se pasan al complemento de jQuery Validate.

### <a name="add-validation-to-dynamic-controls"></a>Agregar validación a controles dinámicos

El método `$.validator.unobtrusive.parse()` funciona en todo el formulario, no en los controles individuales generados dinámicamente, como `<input>` y `<select/>`. Para volver a analizar el formulario, quite los datos de validación que se agregaron cuando el formulario se analizó anteriormente, como se muestra en el ejemplo siguiente:

```js
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a>Validación del lado cliente personalizada

Para personalizar la validación del lado cliente, es necesario generar atributos HTML `data-` que funcionen con un adaptador personalizado de jQuery Validate. El siguiente ejemplo de código de adaptador se escribió para los atributos `ClassicMovie` y `ClassicMovie2` que se introdujeron anteriormente en este artículo:

[!code-javascript[](validation/sample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

Para obtener información sobre cómo escribir adaptadores, vea la [documentación de jQuery Validate](http://jqueryvalidation.org/documentation/).

El uso de un adaptador para un campo determinado se desencadena mediante atributos `data-` que:

* Marcan que el campo está sujeto a validación (`data-val="true"`).
* Identifican un nombre de regla de validación y un texto de mensaje de error (por ejemplo, `data-val-rulename="Error message."`).
* Proporcionan los parámetros adicionales que necesite el validador (por ejemplo, `data-val-rulename-parm1="value"`).

En el ejemplo siguiente se muestran los atributos `data-` para el atributo `ClassicMovie` de la aplicación de ejemplo:

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

Como se indicó anteriormente, los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) y los [asistentes de HTML](xref:mvc/views/overview) usan información procedente de los atributos de validación para representar atributos `data-`. Hay dos opciones para escribir código que dé como resultado la creación de atributos HTML `data-` personalizados:

* Puede crear una clase que derive de `AttributeAdapterBase<TAttribute>` y una clase que implemente `IValidationAttributeAdapterProvider` y, después, registrar el atributo y su adaptador en la inserción de dependencias. Este método sigue el [principio de responsabilidad única](https://wikipedia.org/wiki/Single_responsibility_principle), ya que el código de validación relacionado con servidor y el relacionado con el cliente se encuentran en clases independientes. El adaptador también cuenta con una ventaja: debido a que está registrado en la inserción de dependencias, tiene a su disposición otros servicios de la inserción de dependencias si es necesario.
* Puede implementar `IClientModelValidator` en la clase `ValidationAttribute`. Este método es adecuado si el atributo no realiza ninguna validación en el lado servidor y no necesita ningún servicio de la inserción de dependencias.

### <a name="attributeadapter-for-client-side-validation"></a>AttributeAdapter para la validación del lado cliente

Este método para representar atributos `data-` en HTML es lo que usa el atributo `ClassicMovie` en la aplicación de ejemplo. Para agregar la validación de cliente mediante este método:

1. Cree una clase de adaptador de atributo para el atributo de validación personalizado. Derive la clase de [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2). Cree un método `AddValidation` que agregue atributos `data-` a la salida representada, tal como se muestra en este ejemplo:

   [!code-csharp[](validation/sample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. Cree una clase de proveedor de adaptador que implemente <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>. En el método `GetAttributeAdapter`, pase el atributo personalizado al constructor del adaptador, como se muestra en este ejemplo:

   [!code-csharp[](validation/sample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. Registre el proveedor del adaptador para la inserción de dependencias en `Startup.ConfigureServices`:

   [!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a>IClientModelValidator para la validación del lado cliente

Este método para representar atributos `data-` en HTML es lo que usa el atributo `ClassicMovie2` en la aplicación de ejemplo. Para agregar la validación de cliente mediante este método:

* En el atributo de validación personalizado, implemente la interfaz `IClientModelValidator` y cree un método `AddValidation`. En el método `AddValidation`, agregue atributos `data-` para la validación, como se muestra en el ejemplo siguiente:

  [!code-csharp[](validation/sample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a>Deshabilitación de la validación del lado cliente

El código siguiente deshabilita la validación de cliente en las vistas de MVC:

[!code-csharp[](validation/sample_snapshot/Startup2.cs?name=snippet_DisableClientValidation)]

Esto solo funciona en las vistas de MVC, no en Razor Pages. Otra opción para deshabilitar la validación de cliente consiste en marcar como comentario la referencia a `_ValidationScriptsPartial` en el archivo *.cshtml*.

## <a name="additional-resources"></a>Recursos adicionales

* [Espacio de nombres System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations)
* [Enlace de modelos](model-binding.md)
