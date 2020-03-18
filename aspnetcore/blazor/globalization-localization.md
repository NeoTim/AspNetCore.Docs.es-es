---
title: Globalización y localización de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo poner los componentes de Razor a disposición de los usuarios en varias referencias culturales e idiomas.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: aba62fa7b6285c8ba884652694f1ea3e3a66ed18
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78644897"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a>Globalización y localización de Blazor de ASP.NET Core

Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)

Los componentes de Razor se pueden poner a disposición de los usuarios en varias referencias culturales e idiomas. Estos son los escenarios de globalización y localización de .NET disponibles:

* Sistema de recursos de .NET
* Formato de fecha y número específico de la referencia cultural

Actualmente se admite un conjunto limitado de escenarios de localización de ASP.NET Core:

* `IStringLocalizer<>` *se admite* en aplicaciones de Blazor.
* `IHtmlLocalizer<>`, `IViewLocalizer<>` y la localización de anotaciones de datos son escenarios de MVC de ASP.NET Core y **no se admiten** en aplicaciones de Blazor.

Para obtener más información, vea <xref:fundamentals/localization>.

## <a name="globalization"></a>Globalización

La funcionalidad `@bind` de Blazor realiza formatos y analiza valores para mostrarlos en función de la referencia cultural actual del usuario.

Se puede acceder a la referencia cultural actual desde la propiedad <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>.

[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) se utiliza con los siguientes tipos de campo (`<input type="{TYPE}" />`):

* `date`
* `number`

Los tipos de campo anteriores:

* Se muestran según sus reglas de formato basado en explorador correspondientes.
* No pueden contener texto de forma libre.
* Proporcionar características de interacción del usuario en función de la implementación del explorador.

Los siguientes tipos de campo tienen requisitos de formato específicos y no se admiten actualmente en Blazor, ya que no son compatibles con todos los exploradores principales:

* `datetime-local`
* `month`
* `week`

`@bind` admite el parámetro `@bind:culture` para proporcionar un elemento <xref:System.Globalization.CultureInfo?displayProperty=fullName> para analizar y dar formato a un valor. No se recomienda especificar una referencia cultural cuando se usen los tipos de campo `date` y `number`. `date` y `number` tienen compatibilidad integrada con Blazor que proporciona la referencia cultural necesaria.

## <a name="localization"></a>Localización

Las aplicaciones de servidor Blazor se localizan usando un [middleware de localización](xref:fundamentals/localization#localization-middleware). El middleware selecciona la referencia cultural adecuada según los usuarios que solicitan recursos de la aplicación.

La referencia cultural se puede establecer con uno de los siguientes métodos:

* [Cookies](#cookies)
* [Especificando una interfaz de usuario para elegir la referencia cultural](#provide-ui-to-choose-the-culture)

Para obtener más información y ejemplos, vea <xref:fundamentals/localization>.

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a>Configuración del enlazador para la internacionalización (WebAssembly de Blazor)

De forma predeterminada, la configuración del enlazador de Blazor para aplicaciones WebAssembly de Blazor quita información de internacionalización, excepto para las configuraciones regionales solicitadas de forma explícita. Para obtener más información e instrucciones sobre cómo controlar el comportamiento del enlazador, vea <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.

### <a name="cookies"></a>Cookies

Una cookie de referencia cultural de localización puede conservar la referencia cultural del usuario. La cookie se crea a través del método `OnGet` de la página host de la aplicación (*Pages/Host.cshtml.cs*). El middleware de localización lee la cookie en solicitudes posteriores para establecer la referencia cultural del usuario. 

El uso de una cookie garantiza que la conexión WebSocket puede propagar correctamente la referencia cultural. Si los esquemas de localización se basan en la ruta de acceso URL o en la cadena de consulta, puede que el esquema no funcione con WebSockets, por lo que no se podrá conservar la referencia cultural. Por lo tanto, el uso de una cookie de referencia cultural de localización es el método recomendado.

Se puede usar cualquier técnica para asignar una referencia cultural si la referencia cultural se conserva en una cookie de localización. Si la aplicación ya tiene un esquema de localización establecido para ASP.NET Core del lado servidor, siga usando la infraestructura de localización existente de la aplicación y establezca la cookie de cultura de localización en el esquema de la aplicación.

En el siguiente ejemplo se muestra cómo establecer la referencia cultural actual en una cookie que el middleware de localización puede leer. Cree un archivo *Pages/Host.cshtml.cs* con el siguiente contenido en la aplicación de servidor Blazor:

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

La aplicación controla la localización en la siguiente secuencia de eventos:

1. El explorador envía una solicitud HTTP inicial a la aplicación.
1. El middleware de localización asigna la referencia cultural.
1. El método `OnGet` de *_Host.cshtml.cs* conserva la referencia cultural en una cookie como parte de la respuesta.
1. El explorador abre una conexión WebSocket para crear una sesión de servidor Blazor interactiva.
1. El middleware de localización lee la cookie y asigna la referencia cultural.
1. La sesión del servidor Blazor comienza con la referencia cultural correcta.

### <a name="provide-ui-to-choose-the-culture"></a>Especificación de una interfaz de usuario para elegir la referencia cultural

Para especificar una interfaz de usuario que permita a los usuarios seleccionar una referencia cultural, se recomienda usar un *método basado en el redireccionamiento*. El proceso es similar a lo que ocurre en una aplicación web cuando un usuario intenta acceder a un recurso seguro; esto es, se redirige al usuario a una página de inicio de sesión y, después, se le redirige nuevamente de vuelta al recurso original. 

La aplicación conserva la referencia cultural seleccionada por el usuario a través de un redireccionamiento a un controlador. Este controlador establece la referencia cultural seleccionada del usuario en una cookie y redirige al usuario de nuevo al URI original.

Establezca un punto de conexión HTTP en el servidor para establecer la referencia cultural seleccionada del usuario en una cookie y realizar el redireccionamiento al URI original:

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> Use el resultado de la acción `LocalRedirect` para evitar ataques de redireccionamiento abierto. Para obtener más información, vea <xref:security/preventing-open-redirects>.

El siguiente componente muestra un ejemplo sobre cómo realizar el redireccionamiento inicial cuando el usuario selecciona una referencia cultural:

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/localization>
