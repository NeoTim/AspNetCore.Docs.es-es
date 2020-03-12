---
title: La autenticación de Facebook, Google y proveedores externos sin ASP.NET Core identidad
author: rick-anderson
description: Explicación del uso de Facebook, Google, Twitter, etc. autenticación de usuarios de cuentas sin ASP.NET Core identidad.
ms.author: riande
ms.date: 12/10/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: b30ce7055b35b721c7fb83b61a328200d6a136b1
ms.sourcegitcommit: 3ca4a2235a8129def9e480d0a6ad54cc856920ec
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "79025396"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a>Usar la autenticación de proveedor de inicio de sesión social sin ASP.NET Core identidad

Por [Kirk Larkin](https://twitter.com/serpent5) y [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

en <xref:security/authentication/social/index> se describe cómo permitir a los usuarios iniciar sesión con OAuth 2,0 con las credenciales de proveedores de autenticación externos. El enfoque descrito en ese tema incluye ASP.NET Core identidad como proveedor de autenticación.

Este ejemplo muestra cómo utilizar un proveedor de autenticación externo **sin** ASP.net Core identidad. Esto resulta útil para las aplicaciones que no requieren todas las características de ASP.NET Core identidad, pero que aún requieren la integración con un proveedor de autenticación externo de confianza.

Este ejemplo utiliza la [autenticación de Google](xref:security/authentication/google-logins) para autenticar a los usuarios. El uso de la autenticación de Google desplaza muchas de las complejidades de la administración del proceso de inicio de sesión en Google. Para integrar con otro proveedor de autenticación externo, vea los temas siguientes:

* [Autenticación con Facebook](xref:security/authentication/facebook-logins)
* [Autenticación con Microsoft](xref:security/authentication/microsoft-logins)
* [Autenticación con Twitter](xref:security/authentication/twitter-logins)
* [Otros proveedores](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Configuración

En el método `ConfigureServices`, configure los esquemas de autenticación de la aplicación con los métodos <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>y <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*>:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

La llamada a <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> establece el <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>de la aplicación. El `DefaultScheme` es el esquema predeterminado utilizado por los siguientes métodos de extensión de autenticación de `HttpContext`:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Al establecer la `DefaultScheme` de la aplicación en [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies"), se configura la aplicación para que use cookies como esquema predeterminado para estos métodos de extensión. Al establecer la <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> de la aplicación en [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google"), se configura la aplicación para usar Google como esquema predeterminado para las llamadas a `ChallengeAsync`. `DefaultChallengeScheme` invalida `DefaultScheme`. Vea <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> de propiedades adicionales que invalidan `DefaultScheme` cuando se establecen.

En `Startup.Configure`, llame a `UseAuthentication` y `UseAuthorization` entre las llamadas a `UseRouting` y `UseEndpoints`. Esto establece la propiedad `HttpContext.User` y ejecuta el middleware de autorización para las solicitudes:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

Para obtener más información sobre los esquemas de autenticación, vea [conceptos de autenticación](xref:security/authentication/index#authentication-concepts). Para obtener más información acerca de la autenticación de cookies, consulte <xref:security/authentication/cookie>.

## <a name="apply-authorization"></a>Aplicar autorización

Pruebe la configuración de autenticación de la aplicación aplicando el `AuthorizeAttribute` atributo a un controlador, acción o página. El código siguiente limita el acceso a la página de *privacidad* a los usuarios que se han autenticado:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Cerrar sesión

Para cerrar la sesión del usuario actual y eliminar su cookie, llame a [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*). En el código siguiente se agrega un controlador de página de `Logout` a la página de *Índice* :

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Observe que la llamada a `SignOutAsync` no especifica un esquema de autenticación. La `DefaultScheme` de la aplicación de `CookieAuthenticationDefaults.AuthenticationScheme` se usa como retroceso.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

en <xref:security/authentication/social/index> se describe cómo permitir a los usuarios iniciar sesión con OAuth 2,0 con las credenciales de proveedores de autenticación externos. El enfoque descrito en ese tema incluye ASP.NET Core identidad como proveedor de autenticación.

Este ejemplo muestra cómo utilizar un proveedor de autenticación externo **sin** ASP.net Core identidad. Esto resulta útil para las aplicaciones que no requieren todas las características de ASP.NET Core identidad, pero que aún requieren la integración con un proveedor de autenticación externo de confianza.

Este ejemplo utiliza la [autenticación de Google](xref:security/authentication/google-logins) para autenticar a los usuarios. El uso de la autenticación de Google desplaza muchas de las complejidades de la administración del proceso de inicio de sesión en Google. Para integrar con otro proveedor de autenticación externo, vea los temas siguientes:

* [Autenticación con Facebook](xref:security/authentication/facebook-logins)
* [Autenticación con Microsoft](xref:security/authentication/microsoft-logins)
* [Autenticación con Twitter](xref:security/authentication/twitter-logins)
* [Otros proveedores](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Configuración

En el método `ConfigureServices`, configure los esquemas de autenticación de la aplicación con los métodos `AddAuthentication`, `AddCookie`y `AddGoogle`:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

La llamada a [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) establece el [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)de la aplicación. El `DefaultScheme` es el esquema predeterminado utilizado por los siguientes métodos de extensión de autenticación de `HttpContext`:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Al establecer la `DefaultScheme` de la aplicación en [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies"), se configura la aplicación para que use cookies como esquema predeterminado para estos métodos de extensión. Al establecer la <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> de la aplicación en [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google"), se configura la aplicación para usar Google como esquema predeterminado para las llamadas a `ChallengeAsync`. `DefaultChallengeScheme` invalida `DefaultScheme`. Vea <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> de propiedades adicionales que invalidan `DefaultScheme` cuando se establecen.

En el método `Configure`, llame al método `UseAuthentication` para invocar el middleware de autenticación que establece la propiedad `HttpContext.User`. Llame al método `UseAuthentication` antes de llamar a `UseMvcWithDefaultRoute` o `UseMvc`:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

Para obtener más información sobre los esquemas de autenticación, vea [conceptos de autenticación](xref:security/authentication/index#authentication-concepts). Para obtener más información acerca de la autenticación de cookies, consulte <xref:security/authentication/cookie>.

## <a name="apply-authorization"></a>Aplicar autorización

Pruebe la configuración de autenticación de la aplicación aplicando el `AuthorizeAttribute` atributo a un controlador, acción o página. El código siguiente limita el acceso a la página de *privacidad* a los usuarios que se han autenticado:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Cerrar sesión

Para cerrar la sesión del usuario actual y eliminar su cookie, llame a [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*). En el código siguiente se agrega un controlador de página de `Logout` a la página de *Índice* :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Observe que la llamada a `SignOutAsync` no especifica un esquema de autenticación. La `DefaultScheme` de la aplicación de `CookieAuthenticationDefaults.AuthenticationScheme` se usa como retroceso.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
