---
title: SameSite
author: rick-anderson
description: Obtenga información sobre cómo usar para SameSite cookies en ASP.NET Core
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2019
uid: security/samesite
ms.openlocfilehash: 91c69c4caf0644c1f40750233175ddb4c02d7cfe
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717440"
---
# <a name="working-with-samesite-cookies-in-aspnet-core"></a>Trabajar con cookies de SameSite en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

[SameSite](https://tools.ietf.org/html/draft-west-first-party-cookies-07) es un borrador de [IETF](https://ietf.org/about/) diseñado para proporcionar protección contra los ataques de falsificación de solicitud entre sitios (CSRF). El [borrador de SameSite 2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00):

* Trata las cookies como `SameSite=Lax` de forma predeterminada.
* Indica que las cookies que imponen explícitamente `SameSite=None` para habilitar la entrega entre sitios se deben marcar como `Secure`.

`Lax` funciona para la mayoría de las cookies de aplicación. Algunas formas de autenticación como [OpenID Connect](https://openid.net/connect/) (OIDC) y [WS-Federation](https://auth0.com/docs/protocols/ws-fed) tienen como valor predeterminado el envío de redirecciones basadas en post. Las redirecciones basadas en POST desencadenan las protecciones del explorador de SameSite, por lo que SameSite está deshabilitado para estos componentes. La mayoría de los inicios de sesión de [OAuth](https://oauth.net/) no se ven afectados debido a las diferencias en el modo en que fluye la solicitud.

El parámetro `None` causa problemas de compatibilidad con los clientes que implementaron el estándar de borrador anterior 2016 (por ejemplo, iOS 12). Consulte [compatibilidad con exploradores anteriores](#sob) en este documento.

Cada componente de ASP.NET Core que emite cookies debe decidir si SameSite es adecuado.

## <a name="api-usage-with-samesite"></a>Uso de la API con SameSite

[HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) tiene como valor predeterminado `Unspecified`, lo que significa que no se ha agregado ningún atributo SameSite a la cookie y el cliente usará su comportamiento predeterminado (LAX para los nuevos exploradores, ninguno para los antiguos). En el código siguiente se muestra cómo cambiar el valor de SameSite de la cookie a `SameSiteMode.Lax`:

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

Todos los componentes de ASP.NET Core que emiten cookies reemplazan a los valores predeterminados anteriores con la configuración adecuada para sus escenarios. Los valores predeterminados anteriores invalidados no han cambiado.

| Componente | ellas | Predeterminado |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [SessionOptions. cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [CookieTempDataProviderOptions. cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [AntiforgeryOptions. cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [Autenticación de cookies](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [CookieAuthenticationOptions. cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [TwitterOptions.StateCookie](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions. CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None` |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [OpenIdConnectOptions.NonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

ASP.NET Core 3,1 y versiones posteriores proporcionan la siguiente compatibilidad con SameSite:

* Vuelve a definir el comportamiento de `SameSiteMode.None` que se van a emitir `SameSite=None`
* Agrega un nuevo valor `SameSiteMode.Unspecified` para omitir el atributo SameSite.
* Todas las API de cookies tienen como valor predeterminado `Unspecified`. Algunos componentes que usan cookies establecen valores más específicos de sus escenarios. Vea la tabla anterior para obtener ejemplos.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

En ASP.NET Core 3,0 y versiones posteriores, se cambiaron los valores predeterminados de SameSite para evitar conflictos con los valores predeterminados de cliente incoherentes. Las siguientes API han cambiado el valor predeterminado de `SameSiteMode.Lax ` a `-1` para evitar la emisión de un atributo SameSite para estas cookies:

* <xref:Microsoft.AspNetCore.Http.CookieOptions> usa con [HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)
* <xref:Microsoft.AspNetCore.Http.CookieBuilder> usar como generador de `CookieOptions`
* [CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a>Historial y cambios

La compatibilidad con SameSite se implementó por primera vez en ASP.NET Core en 2,0 con el [borrador estándar de 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1). El estándar 2016 era participación. ASP.NET Core participar estableciendo varias cookies en `Lax` de forma predeterminada. Después de encontrar varios [problemas](https://github.com/aspnet/Announcements/issues/318) con la autenticación, se [deshabilitó](https://github.com/aspnet/Announcements/issues/348)la mayor parte del uso de SameSite.

Las revisiones se emitieron en noviembre de 2019 para actualizar desde el estándar 2016 al estándar 2019. El [borrador 2019 de la especificación SameSite](https://github.com/aspnet/Announcements/issues/390):

* **No** es compatible con las versiones anteriores del borrador 2016. Para obtener más información, consulte [compatibilidad con exploradores anteriores](#sob) en este documento.
* Especifica que las cookies se tratan como `SameSite=Lax` de forma predeterminada.
* Especifica las cookies que validan explícitamente `SameSite=None` para habilitar la entrega entre sitios debe marcarse como `Secure`. `None` es una nueva entrada para rechazarla.
* Es compatible con las revisiones emitidas para ASP.NET Core 2,1, 2,2 y 3,0. ASP.NET Core 3,1 tiene compatibilidad adicional con SameSite.
* Está programado para que [Chrome](https://chromestatus.com/feature/5088147346030592) lo habilite de forma predeterminada en [febrero de 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html). Los exploradores empezaron a pasar a este estándar en 2019.

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a>API afectadas por el cambio del estándar de borrador de 2016 SameSite al borrador estándar de 2019

* [Http. SameSiteMode](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [CookieOptions. SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [CookieBuilder. SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a>Compatibilidad con exploradores más antiguos

El estándar 2016 SameSite impediba que los valores desconocidos se deben tratar como valores `SameSite=Strict`. Las aplicaciones a las que se accede desde exploradores más antiguos que admiten el estándar 2016 SameSite se pueden interrumpir cuando obtienen una propiedad SameSite con un valor de `None`. Las aplicaciones web deben implementar la detección del explorador si pretenden admitir exploradores más antiguos. ASP.NET Core no implementa la detección del explorador porque los valores de los agentes de usuario son muy volátiles y cambian con frecuencia. Un punto de extensión de <xref:Microsoft.AspNetCore.CookiePolicy> permite conectar la lógica específica del agente de usuario.

En `Startup.Configure`, agregue código que llame a <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> antes de llamar a <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> o a *cualquier* método que escriba cookies:

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

En `Startup.ConfigureServices`, agregue código similar al siguiente:

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

En el ejemplo anterior, `MyUserAgentDetectionLib.DisallowsSameSiteNone` es una biblioteca suministrada por el usuario que detecta si el agente de usuario no es compatible con SameSite `None`:

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

En el código siguiente se muestra un método `DisallowsSameSiteNone` de ejemplo:

> [!WARNING]
> El código siguiente es solo para la demostración:
> * No debe considerarse como completa.
> * No se mantiene ni se admite.

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a>Probar aplicaciones para problemas de SameSite

Las aplicaciones que interactúan con sitios remotos, como a través de un inicio de sesión de terceros, deben:

* Pruebe la interacción en varios exploradores.
* Aplique la [detección y la mitigación del explorador de CookiePolicy](#sob) descritas en este documento.

Pruebe las aplicaciones web con una versión de cliente que pueda participar en el nuevo comportamiento de SameSite. Chrome, Firefox y el borde de cromo tienen nuevas marcas de características opcionales que se pueden usar para las pruebas. Una vez que la aplicación aplica las revisiones de SameSite, probarla con versiones de cliente anteriores, especialmente Safari. Para obtener más información, consulte [compatibilidad con exploradores anteriores](#sob) en este documento.

### <a name="test-with-chrome"></a>Prueba con Chrome

Chrome 78 + ofrece resultados engañosos porque tiene una mitigación temporal en contexto. La mitigación temporal de Chrome 78 + permite cookies con menos de dos minutos de antigüedad. Chrome 76 o 77 con las marcas de prueba adecuadas habilitadas proporcionan resultados más precisos. Para probar el nuevo comportamiento de SameSite, cambie `chrome://flags/#same-site-by-default-cookies` a **habilitado**. Las versiones anteriores de Chrome (75 e inferiores) se muestran como erróneas con el nuevo valor de `None`. Consulte [compatibilidad con exploradores anteriores](#sob) en este documento.

Google no hace que las versiones anteriores de Chrome estén disponibles. Siga las instrucciones de [Descargar cromo](https://www.chromium.org/getting-involved/download-chromium) para probar versiones anteriores de Chrome. **No** Descargue Chrome desde los vínculos que se proporcionan al buscar versiones anteriores de Chrome.

* [Cromo 76 Win64](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [Cromo 74 Win64](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

### <a name="test-with-safari"></a>Prueba con Safari

Safari 12 implementó estrictamente el borrador anterior y produce un error cuando el nuevo valor de `None` está en una cookie. `None` se evita a través del código de detección del explorador que [admite exploradores anteriores](#sob) en este documento. Pruebe los inicios de sesión de estilo de SO basados en Safari 12, Safari 13 y WebKit con MSAL, ADAL o cualquier biblioteca que use. El problema depende de la versión del sistema operativo subyacente. OSX Mojave (10,14) e iOS 12 se sabe que tienen problemas de compatibilidad con el nuevo comportamiento de SameSite. Al actualizar el sistema operativo a OSX Catalina (10,15) o iOS 13 se corrige el problema. Safari no tiene actualmente una marca de participación para probar el nuevo comportamiento de la especificación.

### <a name="test-with-firefox"></a>Prueba con Firefox

La compatibilidad con Firefox para el nuevo estándar se puede probar en la versión 68 + al optar por la `about:config` página con la marca de características `network.cookie.sameSite.laxByDefault`. No ha habido informes de problemas de compatibilidad con versiones anteriores de Firefox.

### <a name="test-with-edge-browser"></a>Probar con el explorador Edge

Edge es compatible con el estándar SameSite antiguo. La versión perimetral 44 no tiene ningún problema de compatibilidad conocido con el nuevo estándar.

### <a name="test-with-edge-chromium"></a>Prueba con borde (cromo)

Las marcas SameSite se establecen en la página `edge://flags/#same-site-by-default-cookies`. No se detectaron problemas de compatibilidad con el cromo perimetral.

### <a name="test-with-electron"></a>Prueba con electrones

Las versiones de electrones incluyen versiones anteriores de cromo. Por ejemplo, la versión de electrones utilizada por los equipos es cromo 66, que exhibe el comportamiento anterior. Debe realizar sus propias pruebas de compatibilidad con la versión de electrones que usa el producto. Consulte [compatibilidad con exploradores anteriores](#sob) en la sección siguiente.

## <a name="additional-resources"></a>Recursos adicionales

* [Blog de cromo: desarrolladores: Prepárese para New SameSite = None; Configuración de cookies seguras](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [Explicación de las cookies de SameSite](https://web.dev/samesite-cookies-explained/)
