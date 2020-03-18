---
title: Proteja un ASP.NET Core Blazor aplicación hospedada en webassembly con Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 12e09cf7e27f85473d84f42564d13e1c0ed5dff1
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434452"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a>Proteja un ASP.NET Core Blazor aplicación hospedada en webassembly con Azure Active Directory B2C

Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

En este artículo se describe cómo crear una aplicación independiente Blazor webassembly que usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para la autenticación.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Registrar aplicaciones en AAD B2C y crear una solución

### <a name="create-a-tenant"></a>Creación de un inquilino

Siga las instrucciones de [Tutorial: creación de un inquilino de Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) para crear un inquilino de AAD B2C y registre la siguiente información:

* AAD B2C instancia (por ejemplo, `https://contoso.b2clogin.com/`, que incluye la barra diagonal final)
* AAD B2C dominio del inquilino (por ejemplo, `contoso.onmicrosoft.com`)

### <a name="register-a-server-api-app"></a>Registrar una aplicación de API de servidor

Siga las instrucciones de [Tutorial: registro de una aplicación en Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) para registrar una aplicación de AAD para la *aplicación de API de servidor* en el área **Azure Active Directory** > **registros de aplicaciones** del Azure Portal:

1. Seleccione **Nuevo registro**.
1. Proporcione un **nombre** para la aplicación (por ejemplo, **Blazor Server AAD B2C**).
1. En **tipos de cuenta compatibles**, seleccione **cuentas en cualquier directorio de la organización o cualquier proveedor de identidades. Para autenticar a los usuarios con Azure AD B2C.** (multiinquilino) para esta experiencia.
1. La *aplicación de API de servidor* no requiere un **URI de redirección** en este escenario, por lo que deje la lista desplegable establecida en **Web** y no escriba un URI de redirección.
1. Confirme que **los permisos** > **conceder permisos de administrador a OpenID y offline_access** está habilitado.
1. Seleccione **Registrar**.

En **exponer una API**:

1. Seleccione **Agregar un ámbito**.
1. Seleccione **Guardar y continuar**.
1. Proporcione un **nombre de ámbito** (por ejemplo, `API.Access`).
1. Proporcione un **nombre para mostrar del consentimiento del administrador** (por ejemplo, `Access API`).
1. Proporcione una **Descripción del consentimiento del administrador** (por ejemplo, `Allows the app to access server app API endpoints.`).
1. Confirme que el **Estado** está establecido en **habilitado**.
1. Seleccione la opción **Agregar un ámbito**.

Registre la siguiente información:

* *Aplicación de API de servidor* IDENTIFICADOR de aplicación (ID. de cliente) (por ejemplo, `11111111-1111-1111-1111-111111111111`)
* IDENTIFICADOR de directorio (ID. de inquilino) (por ejemplo, `222222222-2222-2222-2222-222222222222`)
* *Aplicación de API de servidor* URI de ID. de aplicación (por ejemplo, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, el valor predeterminado del Azure Portal podría ser el ID. de cliente)
* Ámbito predeterminado (por ejemplo, `API.Access`)

### <a name="register-a-client-app"></a>Registrar una aplicación de cliente

Siga las instrucciones de [Tutorial: registro de una aplicación en Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) de nuevo para registrar una aplicación de AAD para la *aplicación cliente* en el área **Azure Active Directory** > **registros de aplicaciones** del Azure Portal:

1. Seleccione **Nuevo registro**.
1. Proporcione un **nombre** para la aplicación (por ejemplo, **Blazor cliente AAD B2C**).
1. En **tipos de cuenta compatibles**, seleccione **cuentas en cualquier directorio de la organización o cualquier proveedor de identidades. Para autenticar a los usuarios con Azure AD B2C.** (multiinquilino) para esta experiencia.
1. Deje la lista desplegable **URI de redirección** establecida en **Web**y proporcione un URI de redireccionamiento de `https://localhost:5001/authentication/login-callback`.
1. Confirme que **los permisos** > **conceder permisos de administrador a OpenID y offline_access** está habilitado.
1. Seleccione **Registrar**.

En **autenticación** > **configuraciones de plataforma** > **Web**:

1. Confirme que el **URI de redirección** de `https://localhost:5001/authentication/login-callback` está presente.
1. En **concesión implícita**, active las casillas de verificación de **tokens de acceso** y **tokens de identificador**.
1. Los valores predeterminados restantes de la aplicación son aceptables para esta experiencia.
1. Seleccione el botón **Guardar**.

En **permisos de API**:

1. Confirme que la aplicación tiene **Microsoft Graph** > **usuario.** permiso de lectura.
1. Seleccione **Agregar un permiso** seguido de **mis API**.
1. Seleccione la *aplicación de API de servidor* en la columna **nombre** (por ejemplo, **Blazor Server AAD B2C**).
1. Abra la lista de **API** .
1. Habilite el acceso a la API (por ejemplo, `API.Access`).
1. Seleccione **Agregar permisos**.
1. Seleccione el botón **conceder contenido de administración para {nombre de inquilino}** . Seleccione **Sí** para confirmar la acción.

En **Home** > **Azure ad B2C** > los **flujos de usuario**:

[Creación de un flujo de usuario de inicio de sesión e inicio de sesión](/azure/active-directory-b2c/tutorial-create-user-flows)

Como mínimo, seleccione el atributo de usuario **notificaciones de aplicación** > **nombre para mostrar** para rellenar el `context.User.Identity.Name` en el componente `LoginDisplay` (*Shared/LoginDisplay. Razor*).

Registre la siguiente información:

* Registre el identificador de aplicación de la aplicación *cliente* (identificador de cliente) (por ejemplo, `33333333-3333-3333-3333-333333333333`).
* Registre el nombre del flujo de usuario de inicio de sesión y de registro creado para la aplicación (por ejemplo, `B2C_1_signupsignin`).

### <a name="create-the-app"></a>Creación de la aplicación

Reemplace los marcadores de posición en el siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso (por ejemplo, `-o BlazorSample`). El nombre de la carpeta también se convierte en parte del nombre del proyecto.

## <a name="server-app-configuration"></a>Configuración de la aplicación de servidor

*Esta sección pertenece a la aplicación de **servidor** de la solución.*

### <a name="authentication-package"></a>Paquete de autenticación

La compatibilidad para autenticar y autorizar llamadas a ASP.NET Core API Web la proporciona el `Microsoft.AspNetCore.Authentication.AzureAD.UI`:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a>Compatibilidad con el servicio de autenticación

El método `AddAuthentication` configura los servicios de autenticación dentro de la aplicación y configura el controlador de portador JWT como el método de autenticación predeterminado. El método `AddAzureADBearer` configura los parámetros específicos en el controlador de portador JWT necesario para validar los tokens emitidos por el Azure Active Directory:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication` y `UseAuthorization` Asegúrese de que:

* La aplicación intenta analizar y validar los tokens en las solicitudes entrantes.
* Se produce un error en cualquier solicitud que intente obtener acceso a un recurso protegido sin credenciales adecuadas.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a>Configuración de la aplicación

El archivo *appSettings. JSON* contiene las opciones para configurar el controlador de portador JWT que se usa para validar los tokens de acceso.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a>Controlador de WeatherForecast

El controlador WeatherForecast (*Controllers/WeatherForecastController. CS*) expone una API protegida con el `[Authorize]` atributo que se aplica al controlador. Es **importante** comprender esto:

* El `[Authorize]` atributo de este controlador de API es lo único que protege esta API frente al acceso no autorizado.
* El atributo `[Authorize]` usado en la aplicación webassembly de Blazor solo sirve como sugerencia a la aplicación de que el usuario debe estar autorizado para que la aplicación funcione correctamente.

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>Configuración de la aplicación cliente

*Esta sección pertenece a la aplicación **cliente** de la solución.*

### <a name="authentication-package"></a>Paquete de autenticación

Cuando se crea una aplicación para usar una cuenta de B2C individual (`IndividualB2C`), la aplicación recibe automáticamente una referencia de paquete para la [biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`). El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.

Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Reemplace `{VERSION}` en la referencia de paquete anterior por la versión del paquete de `Microsoft.AspNetCore.Blazor.Templates` que se muestra en el artículo <xref:blazor/get-started>.

El paquete de `Microsoft.Authentication.WebAssembly.Msal` agrega de manera transitiva el paquete de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` a la aplicación.

### <a name="authentication-service-support"></a>Compatibilidad con el servicio de autenticación

La compatibilidad con la autenticación de usuarios se registra en el contenedor de servicios con el método de extensión `AddMsalAuthentication` proporcionado por el paquete de `Microsoft.Authentication.WebAssembly.Msal`. Este método configura todos los servicios necesarios para que la aplicación interactúe con el proveedor de identidades (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{DEFAULT SCOPE}");
});
```

El método `AddMsalAuthentication` acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación. Los valores necesarios para configurar la aplicación pueden obtenerse a partir de la configuración de AAD de Azure portal cuando se registra la aplicación.

La plantilla Blazor webassembly configura automáticamente la aplicación para solicitar un token de acceso para una API segura para el ámbito predeterminado proporcionado al comando `dotnet new` (`{APP ID URI}/{DEFAULT SCOPE}`).

Los ámbitos de token de acceso predeterminados representan la lista de ámbitos de token de acceso que son:

* Se incluye de forma predeterminada en la solicitud de inicio de sesión.
* Se usa para aprovisionar un token de acceso inmediatamente después de la autenticación.

Todos los ámbitos deben pertenecer a la misma aplicación por reglas de Azure Active Directory. Se pueden agregar ámbitos adicionales para las aplicaciones de API adicionales según sea necesario:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{SCOPE}");
});
```

### <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

### <a name="app-component"></a>Componente de aplicación

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Componente de autenticación

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Componente FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Ejecución la aplicación

Ejecute la aplicación desde el proyecto de servidor. Al usar Visual Studio, seleccione el proyecto de servidor en **Explorador de soluciones** y seleccione el botón **Ejecutar** en la barra de herramientas o inicie la aplicación en el menú **depurar** .

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionales

* <xref:security/authentication/azure-ad-b2c>
* [Tutorial: Creación de un inquilino de Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
