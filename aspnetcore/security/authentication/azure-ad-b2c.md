---
title: Autenticación en la nube con Azure Active Directory B2C en ASP.NET Core
author: camsoper
description: Descubra cómo configurar la autenticación de Azure Active Directory B2C con ASP.NET Core.
ms.author: casoper
ms.custom: mvc
ms.date: 01/21/2019
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: 136fa47788456492a9a7fe6d9d9e5996c13e8c20
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653621"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a>Autenticación en la nube con Azure Active Directory B2C en ASP.NET Core

Por [Cam Soper](https://twitter.com/camsoper)

[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure ad B2C) es una solución de administración de identidades en la nube para aplicaciones web y móviles. El servicio proporciona autenticación para las aplicaciones hospedadas en la nube y locales. Tipos de autenticación incluyen cuentas individuales, las cuentas de redes sociales y federados cuentas de empresa. Además, Azure AD B2C puede proporcionar autenticación multifactor con la configuración mínima.

> [!TIP]
> Azure Active Directory (Azure AD) y Azure AD B2C son ofertas de producto independiente. Un inquilino de Azure AD representa una organización, mientras que un inquilino de Azure AD B2C representa una colección de identidades para su uso con las aplicaciones de confianza. Para obtener más información, consulte [Azure ad B2C: preguntas más frecuentes (p + f)](/azure/active-directory-b2c/active-directory-b2c-faqs).

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de un inquilino de Azure Active Directory B2C
> * Registrar una aplicación en Azure AD B2C
> * Usar Visual Studio para crear una aplicación Web de ASP.NET Core configurada para usar el inquilino de Azure AD B2C para la autenticación
> * Configuración de directivas que controlan el comportamiento del inquilino de Azure AD B2C

## <a name="prerequisites"></a>Prerequisites

Se requiere para este tutorial lo siguiente:

* [Microsoft Azure suscripción](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a>Crear al inquilino de Azure Active Directory B2C

Cree un inquilino de Azure Active Directory B2C [como se describe en la documentación de](/azure/active-directory-b2c/active-directory-b2c-get-started). Cuando se le solicite, asociar al inquilino con una suscripción de Azure es opcional para este tutorial.

## <a name="register-the-app-in-azure-ad-b2c"></a>Registrar la aplicación en Azure AD B2C

En el inquilino de Azure AD B2C recién creado, registre la aplicación con [los pasos de la documentación](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) en la sección **registrar una aplicación web** . Detenga en la sección **creación de un secreto de cliente de aplicación web** . No es necesario un secreto de cliente para este tutorial. 

Use los valores siguientes:

| Configuración                       | Value                     | Notas                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nombre**                      | *&lt;nombre de aplicación&gt;*        | Escriba un **nombre** para la aplicación que describa la aplicación a los consumidores.                                                                                                                                 |
| **Incluir aplicación web o API web** | Sí                       |                                                                                                                                                                                                    |
| **Permitir flujo implícito**       | Sí                       |                                                                                                                                                                                                    |
| **URL de respuesta**                 | `https://localhost:44300/signin-oidc` | Las direcciones URL de respuesta son puntos de conexión en los que Azure AD B2C devolverá los tokens que su aplicación solicite. Visual Studio proporciona la dirección URL de respuesta que se va a usar. Por ahora, escriba `https://localhost:44300/signin-oidc` para completar el formulario. |
| **URI de id. de aplicación**                | Déjelo en blanco               | No es necesario para este tutorial.                                                                                                                                                                    |
| **Incluir cliente nativo**     | No                        |                                                                                                                                                                                                    |

> [!WARNING]
> Si va a configurar una dirección URL de respuesta que no sea localhost, tenga en cuenta las [restricciones en lo que se permite en la lista de direcciones URL de respuesta](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application). 

Una vez registrada la aplicación, se muestra la lista de aplicaciones del inquilino. Seleccione la aplicación que acaba de registrar. Seleccione el icono de **copia** situado a la derecha del campo ID. de **aplicación** para copiarlo en el portapapeles.

En este momento, no se puede configurar nada más en el inquilino de Azure AD B2C, pero deje abierta la ventana del explorador. Después de crear la aplicación ASP.NET Core se crea una configuración.

## <a name="create-an-aspnet-core-app-in-visual-studio"></a>Creación de una aplicación de ASP.NET Core en Visual Studio

La plantilla de aplicación Web de Visual Studio puede configurarse para usar al inquilino de Azure AD B2C para la autenticación.

En Visual Studio:

1. Cree una aplicación web de ASP.NET Core. 
2. Seleccione **aplicación web** en la lista de plantillas.
3. Seleccione el botón **cambiar autenticación** .
    
    ![Botón de la autenticación de cambio](./azure-ad-b2c/_static/changeauth.png)

4. En el cuadro de diálogo **cambiar autenticación** , seleccione **cuentas de usuario individuales**y, a continuación, seleccione **conectarse a un almacén de usuario existente en la nube** en la lista desplegable. 
    
    ![Cuadro de diálogo de autenticación de cambio](./azure-ad-b2c/_static/changeauthdialog.png)

5. Complete el formulario con los siguientes valores:
    
    | Configuración                       | Value                                                 |
    |-------------------------------|-------------------------------------------------------|
    | **Nombre de dominio**               | *&lt;el nombre de dominio del inquilino de B2C&gt;*          |
    | **Identificador de aplicación**            | *&lt;pegar el identificador de la aplicación del portapapeles&gt;* |
    | **Ruta de devolución de llamada**             | *&lt;usar el valor predeterminado&gt;*                       |
    | **Directiva de registro o de inicio de sesión** | `B2C_1_SiUpIn`                                        |
    | **Restablecer Directiva de contraseñas**     | `B2C_1_SSPR`                                          |
    | **Editar Directiva de perfil**       | *&lt;dejar en blanco&gt;*                                 |
    
    Seleccione el vínculo **copiar** junto a **URI de respuesta** para copiar el URI de respuesta en el portapapeles. Seleccione **Aceptar** para cerrar el cuadro de diálogo **cambiar autenticación** . Seleccione **Aceptar** para crear la aplicación Web.

## <a name="finish-the-b2c-app-registration"></a>Finalizar el registro de la aplicación B2C

Vuelva a la ventana del explorador con las propiedades de la aplicación B2C todavía abiertas. Cambie la **dirección URL de respuesta** temporal especificada anteriormente al valor copiado desde Visual Studio. Seleccione **Guardar** en la parte superior de la ventana.

> [!TIP]
> Si no ha copiado la dirección URL de respuesta, use la dirección HTTPS de la pestaña depurar en las propiedades del proyecto web y Anexe el valor **CallbackPath** de *appSettings. JSON*.

## <a name="configure-policies"></a>Configurar directivas

Siga los pasos de la documentación de Azure AD B2C para [crear una directiva de registro o de inicio de sesión](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions)y, a continuación, [cree una directiva de restablecimiento de contraseña](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions). Use los valores de ejemplo proporcionados en la documentación para los **proveedores de identidades**, **los atributos de registro**y las **notificaciones**de la aplicación. El uso del botón **Ejecutar ahora** para probar las directivas tal y como se describe en la documentación es opcional.

> [!WARNING]
> Asegúrese de que los nombres de las directivas sean exactamente como se describen en la documentación, ya que esas directivas se usaron en el cuadro de diálogo **cambiar autenticación** en Visual Studio. Los nombres de Directiva se pueden comprobar en *appSettings. JSON*.

## <a name="configure-the-underlying-openidconnectoptionsjwtbearercookie-options"></a>Configurar las opciones de OpenIdConnectOptions/JwtBearer/cookies subyacentes

Para configurar las opciones subyacentes directamente, use la constante de esquema adecuada en `Startup.ConfigureServices`:

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<CookieAuthenticationOptions>(
    AzureAD[B2C]Defaults.CookieScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a>Ejecución la aplicación

En Visual Studio, presione **F5** para compilar y ejecutar la aplicación. Una vez iniciada la aplicación Web, seleccione **Aceptar** para aceptar el uso de cookies (si se le solicita) y, a continuación, seleccione **iniciar sesión**.

![Inicio de sesión en la aplicación](./azure-ad-b2c/_static/signin.png)

El explorador redirige al inquilino de Azure AD B2C. Inicie sesión con una cuenta existente (si se creó una prueba de las directivas) o seleccione **registrarse ahora** para crear una cuenta nueva. El vínculo **¿olvidó su contraseña?** se usa para restablecer una contraseña olvidada.

![Inicio de sesión de Azure AD B2C](./azure-ad-b2c/_static/b2csts.png)

Después de iniciar sesión correctamente, el explorador se redirige a la aplicación Web.

![Correcto](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a:

> [!div class="checklist"]
> * Creación de un inquilino de Azure Active Directory B2C
> * Registrar una aplicación en Azure AD B2C
> * Usar Visual Studio para crear una aplicación Web de ASP.NET Core configurada para usar el inquilino de Azure AD B2C para la autenticación
> * Configuración de directivas que controlan el comportamiento del inquilino de Azure AD B2C

Ahora que la aplicación ASP.NET Core está configurada para usar Azure AD B2C para la autenticación, se puede usar el [atributo Authorize](xref:security/authorization/simple) para proteger la aplicación. Siga desarrollando su aplicación aprendiendo a:

* [Personalización de la interfaz de usuario de Azure ad B2C](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).
* [Configurar los requisitos de complejidad de la contraseña](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).
* [Habilitar multi-factor Authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).
* Configure proveedores de identidades adicionales, como [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)y otros.
* [Use el Graph API Azure ad](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) para recuperar información de usuario adicional, como la pertenencia a grupos, del inquilino de Azure ad B2C.
* [Protección de una API web ASP.net Core mediante Azure ad B2C](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/).
* [Llame a una API Web de .net desde una aplicación Web .net mediante Azure ad B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).