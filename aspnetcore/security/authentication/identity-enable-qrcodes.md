---
title: Habilitar la generación de código QR para las aplicaciones de TOTP Authenticator en ASP.NET Core
author: rick-anderson
description: Descubra cómo habilitar la generación de código QR para las aplicaciones de TOTP Authenticator que funcionan con la autenticación de dos factores ASP.NET Core.
ms.author: riande
ms.date: 08/14/2018
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: a7fdc86b3fe94e714e5147c89a32fce13757d1c1
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654161"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a>Habilitar la generación de código QR para las aplicaciones de TOTP Authenticator en ASP.NET Core

::: moniker range="<= aspnetcore-2.0"

Los códigos QR requieren ASP.NET Core 2,0 o posterior.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

ASP.NET Core incluye compatibilidad con las aplicaciones de autenticador para la autenticación individual. Dos aplicaciones de autenticador de autenticación (2FA) de factor, con una duración definida por única vez contraseña algoritmo (TOTP), son el enfoque para 2FA recomendado en el sector. 2FA uso TOTP es preferible a SMS 2FA. Una aplicación autenticadora proporciona un código de 6 a 8 dígitos que los usuarios deben escribir después de confirmar su nombre de usuario y contraseña. Normalmente, una aplicación autenticadora se instala en un smartphone.

Las plantillas de aplicación Web de ASP.NET Core admiten autenticadores, pero no proporcionan compatibilidad con la generación de QRCode. Los generadores de QRCode facilitan la configuración de 2FA. Este documento le guiará a través de la adición de la generación de [código QR](https://wikipedia.org/wiki/QR_code) a la página de configuración de 2FA.

La autenticación en dos fases no se realiza mediante un proveedor de autenticación externo, como [Google](xref:security/authentication/google-logins) o [Facebook](xref:security/authentication/facebook-logins). Los inicios de sesión externos están protegidos por cualquier mecanismo que proporcione el proveedor de inicio de sesión externo. Considere, por ejemplo, que el proveedor de autenticación de [Microsoft](xref:security/authentication/microsoft-logins) requiere una clave de hardware u otro enfoque 2FA. Si las plantillas predeterminadas exigen "local" 2FA, los usuarios deberán cumplir dos enfoques de 2FA, lo que no es un escenario de uso frecuente.

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a>Agregar códigos QR a la página de configuración de 2FA

En estas instrucciones se usa *qrcode. js* del repositorio https://davidshimjs.github.io/qrcodejs/.

* Descargue la [biblioteca de JavaScript de qrcode. js](https://davidshimjs.github.io/qrcodejs/) en la carpeta `wwwroot\lib` del proyecto.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* Siga las instrucciones de la [identidad de scaffolding](xref:security/authentication/scaffold-identity) para generar */areas/Identity/pages/Account/Manage/EnableAuthenticator.cshtml*.
* En */areas/Identity/pages/Account/Manage/EnableAuthenticator.cshtml*, busque la sección `Scripts` al final del archivo:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* En *pages/Account/Manage/EnableAuthenticator. cshtml* (Razor pages) o *views/Manage/EnableAuthenticator. cshtml* (MVC), busque la sección `Scripts` al final del archivo:

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* Actualice la sección `Scripts` para agregar una referencia a la biblioteca de `qrcodejs` que ha agregado y una llamada para generar el código QR. Debe tener el siguiente aspecto:

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="~/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* Elimine el párrafo que le vincula a estas instrucciones.

Ejecute la aplicación y asegúrese de que puede examinar el código QR y validar el código que el autenticador demuestra.

## <a name="change-the-site-name-in-the-qr-code"></a>Cambiar el nombre del sitio en el código QR

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

El nombre del sitio en el código QR se toma del nombre del proyecto elegido al crear inicialmente el proyecto. Puede cambiarlo buscando el método `GenerateQrCodeUri(string email, string unformattedKey)` en */areas/Identity/pages/Account/Manage/EnableAuthenticator.cshtml.CS*.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

El nombre del sitio en el código QR se toma del nombre del proyecto elegido al crear inicialmente el proyecto. Puede cambiarlo si busca el método `GenerateQrCodeUri(string email, string unformattedKey)` en el archivo *pages/Account/Manage/EnableAuthenticator. cshtml. CS* (Razor pages) o el archivo *Controllers/ManageController. CS* (MVC).

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

El código predeterminado de la plantilla tiene el siguiente aspecto:

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenticatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

El segundo parámetro de la llamada a `string.Format` es el nombre del sitio, tomado del nombre de la solución. Se puede cambiar a cualquier valor, pero siempre debe ser una dirección URL codificada.

## <a name="using-a-different-qr-code-library"></a>Usar una biblioteca de códigos QR diferente

Puede reemplazar la biblioteca de códigos QR por su biblioteca preferida. El código HTML contiene un elemento `qrCode` en el que puede colocar un código QR según el mecanismo que proporcione la biblioteca.

La dirección URL con el formato correcto para el código QR está disponible en:

* `AuthenticatorUri` propiedad del modelo.
* `data-url` propiedad en el elemento `qrCodeData`.

## <a name="totp-client-and-server-time-skew"></a>Desfase de tiempo de servidor y cliente de TOTP

La autenticación TOTP (contraseña de un solo uso) depende del servidor y del dispositivo autenticador con una hora precisa. Los tokens solo duran 30 segundos. Si se producen errores en los inicios de sesión de TOTP 2FA, compruebe que la hora del servidor es correcta y, preferiblemente, se ha sincronizado con un servicio NTP preciso.

::: moniker-end
