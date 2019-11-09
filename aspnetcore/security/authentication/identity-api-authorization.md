---
title: Introducción a la autenticación para aplicaciones de una sola página en ASP.NET Core
author: javiercn
description: Use Identity con una aplicación de una sola página hospedada en una aplicación ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: f58d92634ce1ef6110533d56c40b7520dda90514
ms.sourcegitcommit: 4818385c3cfe0805e15138a2c1785b62deeaab90
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2019
ms.locfileid: "73897049"
---
# <a name="authentication-and-authorization-for-spas"></a>Autenticación y autorización para spa

ASP.NET Core 3,0 o posterior ofrece autenticación en aplicaciones de una sola página (Spa) mediante la compatibilidad con la autorización de la API. ASP.NET Core identidad para autenticar y almacenar usuarios se combina con [IdentityServer](https://identityserver.io/) para implementar Open ID Connect.

Se ha agregado un parámetro de autenticación a las plantillas de proyecto **angular** y **reAct** que es similar al parámetro de autenticación de la **aplicación web (modelo de vista de modelos)** (MVC) y **aplicación web** (Razor pages) plantillas de proyecto. Los valores de parámetro permitidos son **None** y **individual**. En este momento, la plantilla de proyecto **reAct. js y Redux** no admite el parámetro de autenticación.

## <a name="create-an-app-with-api-authorization-support"></a>Creación de una aplicación con compatibilidad con la autorización de API

Se puede usar la autenticación y autorización de usuario con angular y reAct Spa. Abra un shell de comandos y ejecute el siguiente comando:

**Angular**:

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

**ReAct**:

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

El comando anterior crea una aplicación ASP.NET Core con un directorio *ClientApp* que contiene el Spa.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>Descripción general de los componentes de ASP.NET Core de la aplicación

En las secciones siguientes se describen las adiciones al proyecto cuando se incluye compatibilidad con la autenticación:

### <a name="startup-class"></a>Startup (clase)

La clase `Startup` tiene las siguientes adiciones:

* Dentro del método `Startup.ConfigureServices`:
  * Identidad con la interfaz de usuario predeterminada:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer con un método auxiliar de `AddApiAuthorization` adicional que configura algunas convenciones de ASP.NET Core predeterminadas en la parte superior de IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Autenticación con un método auxiliar de `AddIdentityServerJwt` adicional que configura la aplicación para validar los tokens JWT generados por IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* Dentro del método `Startup.Configure`:
  * El middleware de autenticación responsable de validar las credenciales de solicitud y establecer el usuario en el contexto de la solicitud:

    ```csharp
    app.UseAuthentication();
    ```

  * El middleware IdentityServer que expone los puntos de conexión de Open ID Connect:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

Este método auxiliar configura IdentityServer para usar la configuración admitida. IdentityServer es un marco de trabajo eficaz y extensible para controlar los problemas de seguridad de las aplicaciones. Al mismo tiempo, esto expone una complejidad innecesaria para los escenarios más comunes. Por lo tanto, se le proporciona un conjunto de convenciones y opciones de configuración que se consideran un buen punto de partida. Una vez que cambie la autenticación, toda la capacidad de IdentityServer sigue estando disponible para personalizar la autenticación para satisfacer sus necesidades.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

Este método auxiliar configura un esquema de directiva para la aplicación como el controlador de autenticación predeterminado. La Directiva está configurada para permitir que la identidad controle todas las solicitudes enrutadas a cualquier subruta en el espacio de la dirección URL de identidad "/Identity". El `JwtBearerHandler` controla todas las demás solicitudes. Además, este método registra un recurso de API de `<<ApplicationName>>API` con IdentityServer con un ámbito predeterminado de `<<ApplicationName>>API` y configura el middleware de token de portador JWT para validar los tokens emitidos por IdentityServer para la aplicación.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

En el archivo *Controllers\WeatherForecastController.CS* , observe el atributo `[Authorize]` aplicado a la clase que indica que el usuario debe ser autorizado en función de la directiva predeterminada para tener acceso al recurso. La Directiva de autorización predeterminada está configurada para usar el esquema de autenticación predeterminado, que se configura mediante `AddIdentityServerJwt` al esquema de directivas mencionado anteriormente, lo que hace que el `JwtBearerHandler` configurado por este método auxiliar sea el controlador predeterminado para las solicitudes a la aplicación.

### <a name="applicationdbcontext"></a>ApplicationDbContext

En el archivo *Data\ApplicationDbContext.CS* , observe que se usa el mismo `DbContext` en la identidad, con la excepción de que extiende `ApiAuthorizationDbContext` (una clase más derivada de `IdentityDbContext`) para incluir el esquema de IdentityServer.

Para obtener el control total del esquema de la base de datos, herede de una de las clases de identidad `DbContext` disponibles y configure el contexto para incluir el esquema de identidad llamando a `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` en el método `OnModelCreating`.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

En el archivo *Controllers\OidcConfigurationController.CS* , observe el punto de conexión aprovisionado para atender los parámetros OIDC que el cliente necesita usar.

### <a name="appsettingsjson"></a>appsettings.json

En el archivo *appSettings. JSON* de la raíz del proyecto, hay una nueva sección `IdentityServer` que describe la lista de clientes configurados. En el ejemplo siguiente, hay un solo cliente. El nombre de cliente se corresponde con el nombre de la aplicación y se asigna por Convención al parámetro `ClientId` de OAuth. El perfil indica el tipo de aplicación que se está configurando. Se usa internamente para controlar las convenciones que simplifican el proceso de configuración del servidor. Hay varios perfiles disponibles, como se explica en la sección [perfiles de aplicación](#application-profiles) .

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appSettings. Desarrollo. JSON

En el *appSettings. Archivo Development. JSON* de la raíz del proyecto, hay una sección `IdentityServer` que describe la clave que se usa para firmar los tokens. Al realizar la implementación en producción, es necesario aprovisionar e implementar una clave junto con la aplicación, como se explica en la sección [implementación en producción](#deploy-to-production) .

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>Descripción general de la aplicación angular

La compatibilidad con la autenticación y la autorización de API en la plantilla de angular reside en su propio módulo angular en el directorio *ClientApp\src\api-Authorization* El módulo se compone de los siguientes elementos:

* 3 componentes:
  * *login. Component. ts*: controla el flujo de inicio de sesión de la aplicación.
  * *logout. Component. ts*: controla el flujo de cierre de sesión de la aplicación.
  * *login-menu. Component. ts*: un widget que muestra uno de los siguientes conjuntos de vínculos:
    * Los vínculos de administración de perfiles de usuario y cierre de sesión cuando el usuario está autenticado.
    * Los vínculos registro e inicio de sesión cuando el usuario no está autenticado.
* Un `AuthorizeGuard` de protección de rutas que se puede Agregar a las rutas y requiere que un usuario se autentique antes de visitar la ruta.
* `AuthorizeInterceptor` del interceptor de HTTP que asocia el token de acceso a las solicitudes HTTP salientes que se dirigen a la API cuando se autentica el usuario.
* `AuthorizeService` de servicio que controla los detalles de nivel inferior del proceso de autenticación y expone información sobre el usuario autenticado al resto de la aplicación para su consumo.
* Un módulo angular que define rutas asociadas a las partes de autenticación de la aplicación. Expone el componente de menú Inicio de sesión, el interceptor, la protección y el servicio para su consumo desde el resto de la aplicación.

## <a name="general-description-of-the-react-app"></a>Descripción general de la aplicación reAct

La compatibilidad con la autenticación y la autorización de API en la plantilla reAct reside en el directorio *ClientApp\src\components\api-Authorization* Se compone de los siguientes elementos:

* 4 componentes:
  * *Login. js*: controla el flujo de inicio de sesión de la aplicación.
  * *Logout. js*: controla el flujo de cierre de sesión de la aplicación.
  * *LoginMenu. js*: un widget que muestra uno de los siguientes conjuntos de vínculos:
    * Los vínculos de administración de perfiles de usuario y cierre de sesión cuando el usuario está autenticado.
    * Los vínculos registro e inicio de sesión cuando el usuario no está autenticado.
  * *AuthorizeRoute. js*: componente de ruta que requiere que un usuario se autentique antes de representar el componente indicado en el parámetro `Component`.
* Instancia de `authService` exportada de la clase `AuthorizeService` que controla los detalles de nivel inferior del proceso de autenticación y expone información sobre el usuario autenticado al resto de la aplicación para su consumo.

Ahora que ha visto los componentes principales de la solución, puede profundizar en los escenarios individuales de la aplicación.

## <a name="require-authorization-on-a-new-api"></a>Requerir autorización en una nueva API

De forma predeterminada, el sistema está configurado para requerir fácilmente la autorización para las nuevas API. Para ello, cree un nuevo controlador y agregue el `[Authorize]` atributo a la clase de controlador o a cualquier acción dentro del controlador.

## <a name="customize-the-api-authentication-handler"></a>Personalización del controlador de autenticación de API

Para personalizar la configuración del controlador de JWT de la API, configure su <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instancia:

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

El controlador JWT de la API genera eventos que permiten controlar el proceso de autenticación mediante `JwtBearerEvents`. Para proporcionar compatibilidad con la autorización de API, `AddIdentityServerJwt` registra sus propios controladores de eventos.

Para personalizar el control de un evento, ajuste el controlador de eventos existente con lógica adicional, según sea necesario. Por ejemplo:

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

En el código anterior, el controlador de eventos `OnTokenValidated` se reemplaza con una implementación personalizada. Esta implementación:

1. Llama a la implementación original proporcionada por la compatibilidad con la autorización de la API.
1. Ejecute su propia lógica personalizada.

## <a name="protect-a-client-side-route-angular"></a>Protección de una ruta de lado cliente (angular)

La protección de una ruta del lado cliente se realiza mediante la adición de la protección de autorización a la lista de protecciones que se deben ejecutar al configurar una ruta. Por ejemplo, puede ver cómo se configura la ruta de `fetch-data` en el módulo de angular de la aplicación principal:

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

Es importante mencionar que la protección de una ruta no protege el punto de conexión real (que requiere que se le aplique un `[Authorize]` atributo), sino que solo impide que el usuario navegue hasta la ruta de cliente determinada cuando no se autentica.

## <a name="authenticate-api-requests-angular"></a>Autenticar solicitudes de API (angular)

La autenticación de las solicitudes a las API hospedadas junto a la aplicación se realiza automáticamente mediante el uso del interceptor de cliente HTTP definido por la aplicación.

## <a name="protect-a-client-side-route-react"></a>Proteger una ruta del lado cliente (reAct)

Proteja una ruta del lado cliente mediante el componente de `AuthorizeRoute` en lugar del componente de `Route` sin formato. Por ejemplo, observe cómo se configura la ruta de `fetch-data` dentro del componente `App`:

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

Protección de una ruta:

* No protege el punto de conexión real (que aún requiere un atributo `[Authorize]` aplicado).
* Solo impide que el usuario navegue hasta la ruta del lado cliente determinada cuando no se autentica.

## <a name="authenticate-api-requests-react"></a>Autenticar solicitudes de API (reAct)

La autenticación de solicitudes con reAct se realiza mediante la importación en primer lugar de la instancia de `authService` de la `AuthorizeService`. El token de acceso se recupera del `authService` y se adjunta a la solicitud, como se muestra a continuación. En los componentes de reAct, este trabajo se realiza normalmente en el método de ciclo de vida de `componentDidMount` o como resultado de alguna interacción del usuario.

### <a name="import-the-authservice-into-your-component"></a>Importar la authService en el componente

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>Recuperar y adjuntar el token de acceso a la respuesta

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a>Implementación en producción

Para implementar la aplicación en producción, se deben aprovisionar los siguientes recursos:

* Una base de datos para almacenar las cuentas de usuario de identidad y las concesiones de IdentityServer.
* Un certificado de producción que se utilizará para firmar los tokens.
  * No hay requisitos específicos para este certificado; puede ser un certificado autofirmado o un certificado aprovisionado a través de una entidad de certificación.
  * Se puede generar a través de herramientas estándar como PowerShell o OpenSSL.
  * Se puede instalar en el almacén de certificados en los equipos de destino o implementarse como un archivo *. pfx* con una contraseña segura.

### <a name="example-deploy-to-azure-websites"></a>Ejemplo: implementación en Azure websites

En esta sección se describe la implementación de la aplicación en sitios web de Azure mediante un certificado almacenado en el almacén de certificados. Para modificar la aplicación para cargar un certificado desde el almacén de certificados, el plan de App Service debe estar al menos en el nivel estándar al configurar en un paso posterior. En el archivo *appSettings. JSON* de la aplicación, modifique la sección `IdentityServer` para incluir los detalles de la clave:

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* La propiedad Name en el certificado corresponde al sujeto distintivo del certificado.
* La ubicación del almacén representa dónde se debe cargar el certificado (`CurrentUser` o `LocalMachine`).
* El nombre del almacén representa el nombre del almacén de certificados donde se almacena el certificado. En este caso, apunta al almacén de usuarios personales.

Para implementar en Azure websites, implemente la aplicación siguiendo los pasos descritos en [implementación de la aplicación en Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) para crear los recursos de Azure necesarios e implementar la aplicación en producción.

Después de seguir las instrucciones anteriores, la aplicación se implementa en Azure pero todavía no es funcional. Todavía es necesario configurar el certificado usado por la aplicación. Busque la huella digital del certificado que se va a usar y siga los pasos descritos en [carga de certificados](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).

Aunque estos pasos mencionan SSL, hay una sección de **certificados privados** en el portal donde puede cargar el certificado aprovisionado para usarlo con la aplicación.

Después de este paso, reinicie la aplicación y debe ser funcional.

## <a name="other-configuration-options"></a>Otras opciones de configuración

La compatibilidad con la autorización de API se basa en IdentityServer con un conjunto de convenciones, valores predeterminados y mejoras para simplificar la experiencia de spa. No es necesario decir que toda la capacidad de IdentityServer está disponible en segundo plano si las integraciones de ASP.NET Core no cubren su escenario. La compatibilidad con ASP.NET Core se centra en las aplicaciones de "primera parte", donde se crean e implementan todas las aplicaciones. Como tal, no se ofrece soporte técnico para elementos como el consentimiento o la Federación. Para esos escenarios, use IdentityServer y siga su documentación.

### <a name="application-profiles"></a>Perfiles de aplicación

Los perfiles de aplicación son configuraciones predefinidas para aplicaciones que definen aún más sus parámetros. En este momento, se admiten los siguientes perfiles:

* `IdentityServerSPA`: representa un SPA hospedado junto con IdentityServer como una sola unidad.
  * El `redirect_uri` tiene como valor predeterminado `/authentication/login-callback`.
  * El `post_logout_redirect_uri` tiene como valor predeterminado `/authentication/logout-callback`.
  * El conjunto de ámbitos incluye el `openid`, `profile`y cada ámbito definido para las API de la aplicación.
  * El conjunto de tipos de respuesta OIDC permitidos es `id_token token` o cada uno de ellos de forma individual (`id_token`, `token`).
  * El modo de respuesta permitido es `fragment`.
* `SPA`: representa un SPA que no está hospedado con IdentityServer.
  * El conjunto de ámbitos incluye el `openid`, `profile`y cada ámbito definido para las API de la aplicación.
  * El conjunto de tipos de respuesta OIDC permitidos es `id_token token` o cada uno de ellos de forma individual (`id_token`, `token`).
  * El modo de respuesta permitido es `fragment`.
* `IdentityServerJwt`: representa una API que se hospeda junto con IdentityServer.
  * La aplicación está configurada para tener un solo ámbito que tenga como valor predeterminado el nombre de la aplicación.
* `API`: representa una API que no está hospedada con IdentityServer.
  * La aplicación está configurada para tener un solo ámbito que tenga como valor predeterminado el nombre de la aplicación.

### <a name="configuration-through-appsettings"></a>Configuración a través de AppSettings

Configure las aplicaciones a través del sistema de configuración agregándolas a la lista de `Clients` o `Resources`.

Configure la propiedad `redirect_uri` y `post_logout_redirect_uri` de cada cliente, tal y como se muestra en el ejemplo siguiente:

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

Al configurar recursos, puede configurar los ámbitos para el recurso, tal y como se muestra a continuación:

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a>Configuración mediante código

También puede configurar los clientes y los recursos a través del código mediante una sobrecarga de `AddApiAuthorization` que realiza una acción para configurar las opciones.

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
