---
title: Protección de un ASP.NET Core Blazor aplicación hospedada en webassembly con Identity Server
author: guardrex
description: Para crear una nueva aplicación hospedada en Blazor con autenticación desde dentro de Visual Studio que usa un back-end de [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 6c7942a827d88a620e6f295af3f523c23f4b3890
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219056"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a>Protección de un ASP.NET Core Blazor aplicación hospedada en webassembly con Identity Server

Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Para crear una nueva Blazor aplicación hospedada en Visual Studio que usa [IdentityServer](https://identityserver.io/) para autenticar a los usuarios y las llamadas a la API:

1. Use Visual Studio para crear una nueva aplicación **Blazor Webassembly** . Para más información, consulte <xref:blazor/get-started>.
1. En el cuadro de diálogo **crear una nueva aplicación de Blazor** , seleccione **cambiar** en la sección **autenticación** .
1. Seleccione **cuentas de usuario individuales** seguidos de **Aceptar**.
1. Active la casilla **ASP.net Core hospedado** en la sección **avanzadas** .
1. Seleccione el botón **Crear**.

Para crear la aplicación en un shell de comandos, ejecute el siguiente comando:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso (por ejemplo, `-o BlazorSample`). El nombre de la carpeta también se convierte en parte del nombre del proyecto.

## <a name="server-app-configuration"></a>Configuración de la aplicación de servidor

En las secciones siguientes se describen las adiciones al proyecto cuando se incluye compatibilidad con la autenticación.

### <a name="startup-class"></a>Clase Startup

La clase `Startup` tiene las siguientes adiciones:

* En `Startup.ConfigureServices`:

  * Identidad con la interfaz de usuario predeterminada:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer con un método auxiliar de <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> adicional que configura algunas convenciones de ASP.NET Core predeterminadas en la parte superior de IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Autenticación con un método auxiliar de <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> adicional que configura la aplicación para validar los tokens JWT generados por IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* En `Startup.Configure`:

  * El middleware de autenticación responsable de validar las credenciales de solicitud y establecer el usuario en el contexto de la solicitud:

    ```csharp
    app.UseAuthentication();
    ```

  * El middleware IdentityServer que expone los puntos de conexión de Open ID Connect (OIDC):

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

El método auxiliar <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> configura [IdentityServer](https://identityserver.io/) para los escenarios de ASP.net Core. IdentityServer es un marco de trabajo eficaz y extensible para controlar los problemas de seguridad de las aplicaciones. IdentityServer expone una complejidad innecesaria para los escenarios más comunes. Por lo tanto, se proporciona un conjunto de convenciones y opciones de configuración que consideramos un buen punto de partida. Una vez que cambie la autenticación, toda la potencia de IdentityServer sigue estando disponible para personalizar la autenticación para adaptarse a los requisitos de una aplicación.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

El método auxiliar <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> configura un esquema de directiva para la aplicación como el controlador de autenticación predeterminado. La Directiva está configurada para permitir que la identidad controle todas las solicitudes enrutadas a cualquier subruta en el espacio de la dirección URL de identidad `/Identity`. El <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> controla todas las demás solicitudes. Además, este método:

* Registra un recurso de API de `{APPLICATION NAME}API` con IdentityServer con un ámbito predeterminado de `{APPLICATION NAME}API`.
* Configura el middleware del token de portador de JWT para validar los tokens emitidos por IdentityServer para la aplicación.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

En el `WeatherForecastController` (*Controllers/WeatherForecastController. CS*), el atributo de [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) se aplica a la clase. El atributo indica que el usuario debe estar autorizado en función de la directiva predeterminada para tener acceso al recurso. La Directiva de autorización predeterminada está configurada para usar el esquema de autenticación predeterminado, que se configura mediante <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> al esquema de directiva mencionado anteriormente. El método auxiliar configura <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> como el controlador predeterminado para las solicitudes a la aplicación.

### <a name="applicationdbcontext"></a>ApplicationDbContext

En el `ApplicationDbContext` (*Data/ApplicationDbContext. CS*), se usa la misma <xref:Microsoft.EntityFrameworkCore.DbContext> en la identidad, con la excepción de que extiende <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> para incluir el esquema de IdentityServer. La clase <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> se deriva de la clase <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.

Para obtener el control total del esquema de la base de datos, herede de una de las clases de identidad <xref:Microsoft.EntityFrameworkCore.DbContext> disponibles y configure el contexto para incluir el esquema de identidad llamando a `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` en el método `OnModelCreating`.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

En el `OidcConfigurationController` (*Controllers/OidcConfigurationController. CS*), el punto de conexión del cliente se aprovisiona para servir los parámetros de OIDC.

### <a name="app-settings-files"></a>Archivos de configuración de la aplicación

En el archivo de configuración de la aplicación (*appSettings. JSON*) en la raíz del proyecto, en la sección `IdentityServer` se describe la lista de clientes configurados. En el ejemplo siguiente, hay un solo cliente. El nombre de cliente se corresponde con el nombre de la aplicación y se asigna por Convención al parámetro `ClientId` de OAuth. El perfil indica el tipo de aplicación que se está configurando. El perfil se usa internamente para controlar las convenciones que simplifican el proceso de configuración del servidor. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

En el archivo de configuración de la aplicación del entorno de desarrollo (*appSettings. Development. JSON*) en la raíz del proyecto, la sección `IdentityServer` describe la clave que se usa para firmar los tokens. <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a>Configuración de la aplicación cliente

### <a name="authentication-package"></a>Paquete de autenticación

Cuando se crea una aplicación para usar cuentas de usuario individuales (`Individual`), la aplicación recibe automáticamente una referencia de paquete para el paquete de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` en el archivo de proyecto de la aplicación. El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.

Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Reemplace `{VERSION}` en la referencia de paquete anterior por la versión del paquete de `Microsoft.AspNetCore.Blazor.Templates` que se muestra en el artículo <xref:blazor/get-started>.

### <a name="api-authorization-support"></a>Compatibilidad con la autorización de API

La compatibilidad con la autenticación de usuarios está conectada en el contenedor de servicios mediante el método de extensión proporcionado en el paquete de `Microsoft.AspNetCore.Components.WebAssembly.Authentication`. Este método configura todos los servicios necesarios para que la aplicación interactúe con el sistema de autorización existente.

```csharp
builder.Services.AddApiAuthorization();
```

De forma predeterminada, carga la configuración de la aplicación por Convención desde `_configuration/{client-id}`. Por Convención, el identificador de cliente se establece en el nombre de ensamblado de la aplicación. Esta dirección URL se puede cambiar para que apunte a un punto de conexión independiente mediante una llamada a la sobrecarga con opciones.

### <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Componente de aplicación

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

El componente de `LoginDisplay` (*Shared/LoginDisplay. Razor*) se representa en el componente `MainLayout` (*Shared/MainLayout. Razor*) y administra los comportamientos siguientes:

* Para usuarios autenticados:
  * Muestra el nombre de usuario actual.
  * Proporciona un vínculo a la página de Perfil de usuario de ASP.NET Core identidad.
  * Ofrece un botón para cerrar la sesión de la aplicación.
* Para usuarios anónimos:
  * Ofrece la opción de registro.
  * Ofrece la opción de iniciar sesión.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a>Componente de autenticación

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Componente FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Ejecución la aplicación

Ejecute la aplicación desde el proyecto de servidor. Al usar Visual Studio, seleccione el proyecto de servidor en **Explorador de soluciones** y seleccione el botón **Ejecutar** en la barra de herramientas o inicie la aplicación en el menú **depurar** .

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]
