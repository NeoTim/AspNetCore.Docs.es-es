---
title: Autenticación y autorización en ASP.NET Core SignalR
author: bradygaster
description: Aprenda a usar la autenticación y autorización en ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: signalr/authn-and-authz
ms.openlocfilehash: 3b7216bb064ba06a4c909016e1efd4242a64a7ad
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652019"
---
# <a name="authentication-and-authorization-in-aspnet-core-opno-locsignalr"></a>Autenticación y autorización en ASP.NET Core SignalR

Por [Andrew Stanton-enfermera](https://twitter.com/anurse)

[Ver o descargar el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(cómo descargarlo)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-opno-locsignalr-hub"></a>Autenticación de usuarios que se conectan a un concentrador de SignalR

SignalR puede usarse con la [autenticación de ASP.net Core](xref:security/authentication/identity) para asociar un usuario a cada conexión. En un concentrador, se puede tener acceso a los datos de autenticación desde la propiedad [HubConnectionContext. User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) . La autenticación permite que el concentrador llame a métodos en todas las conexiones asociadas a un usuario. Para obtener más información, vea [administrar usuarios y grupos en SignalR](xref:signalr/groups). Es posible que varias conexiones estén asociadas a un solo usuario.

El siguiente es un ejemplo de `Startup.Configure` que usa la autenticación de SignalR y ASP.NET Core:

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> El orden en el que se registran los SignalR y el middleware de autenticación ASP.NET Core. Llame siempre a `UseAuthentication` antes de `UseSignalR` para que SignalR tenga un usuario en el `HttpContext`.

::: moniker-end

### <a name="cookie-authentication"></a>Autenticación de cookies

En una aplicación basada en explorador, la autenticación de cookies permite que las credenciales de usuario existentes fluyan automáticamente a las conexiones SignalR. Cuando se usa el cliente del explorador, no se necesita ninguna configuración adicional. Si el usuario ha iniciado sesión en la aplicación, la conexión de SignalR hereda automáticamente esta autenticación.

Las cookies son una forma específica del explorador de enviar tokens de acceso, pero los clientes que no son de explorador pueden enviarlas. Al utilizar el [cliente .net](xref:signalr/dotnet-client), se puede configurar la propiedad `Cookies` en la llamada `.WithUrl` para proporcionar una cookie. Sin embargo, el uso de la autenticación de cookies del cliente .NET requiere que la aplicación proporcione una API para intercambiar los datos de autenticación de una cookie.

### <a name="bearer-token-authentication"></a>Autenticación de token de portador

El cliente puede proporcionar un token de acceso en lugar de usar una cookie. El servidor valida el token y lo usa para identificar al usuario. Esta validación se realiza solo cuando se establece la conexión. Durante la vida de la conexión, el servidor no se vuelve a validar automáticamente para comprobar la revocación de tokens.

En el servidor, la autenticación de token de portador se configura mediante el [middleware portador de JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

En el cliente de JavaScript, el token se puede proporcionar mediante la opción [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) .

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

En el cliente de .NET, hay una propiedad [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) similar que se puede usar para configurar el token:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> Se llama a la función de token de acceso que se proporciona antes de **cada** solicitud HTTP realizada por SignalR. Si necesita renovar el token para mantener la conexión activa (porque puede expirar durante la conexión), hágalo desde esta función y devuelva el token actualizado.

En las API Web estándar, los tokens de portador se envían en un encabezado HTTP. Sin embargo, SignalR no puede establecer estos encabezados en exploradores cuando se usan algunos transportes. Cuando se usan WebSockets y eventos enviados por el servidor, el token se transmite como un parámetro de cadena de consulta. Para admitir esto en el servidor, se requiere una configuración adicional:

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

> [!NOTE]
> La cadena de consulta se usa en exploradores al conectarse con WebSockets y eventos enviados por el servidor debido a las limitaciones de la API del explorador. Cuando se usa HTTPS, los valores de cadena de consulta están protegidos por la conexión TLS. Sin embargo, muchos servidores registran valores de cadena de consulta. Para obtener más información, vea [consideraciones de seguridad en ASP.NET Core SignalR](xref:signalr/security). SignalR usa encabezados para transmitir tokens en entornos que los admiten (como los clientes de .NET y Java).

### <a name="cookies-vs-bearer-tokens"></a>Cookies frente a tokens de portador 

Las cookies son específicas de los exploradores. Enviarlos desde otros tipos de clientes agrega complejidad en comparación con el envío de tokens de portador. Por lo tanto, no se recomienda la autenticación de cookies a menos que la aplicación solo necesite autenticar a los usuarios desde el cliente del explorador. La autenticación de token de portador es el enfoque recomendado al usar clientes que no sean el cliente del explorador.

### <a name="windows-authentication"></a>Autenticación de Windows

Si la [autenticación de Windows](xref:security/authentication/windowsauth) está configurada en la aplicación, SignalR puede usar esa identidad para proteger los concentradores. Sin embargo, para enviar mensajes a usuarios individuales, debe agregar un proveedor de ID. de usuario personalizado. El sistema de autenticación de Windows no proporciona la demanda de "identificador de nombre". SignalR usa la demanda para determinar el nombre de usuario.

Agregue una nueva clase que implemente `IUserIdProvider` y recupere una de las notificaciones del usuario para usarla como identificador. Por ejemplo, para usar la notificaciones "Name" (que es el nombre de usuario de Windows con el formato `[Domain]\[Username]`), cree la clase siguiente:

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

En lugar de `ClaimTypes.Name`, puede usar cualquier valor del `User` (por ejemplo, el identificador de SID de Windows, etc.).

> [!NOTE]
> El valor que elija debe ser único entre todos los usuarios del sistema. De lo contrario, un mensaje destinado a un usuario podría acabar pasando a otro usuario.

Registre este componente en el método de `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

En el cliente de .NET, la autenticación de Windows debe habilitarse estableciendo la propiedad [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

La autenticación de Windows solo es compatible con el cliente del explorador al usar Microsoft Internet Explorer o Microsoft Edge.

### <a name="use-claims-to-customize-identity-handling"></a>Usar notificaciones para personalizar el control de identidades

Una aplicación que autentica a los usuarios puede derivar SignalR identificadores de usuario de las notificaciones de usuario. Para especificar cómo crea SignalR los identificadores de usuario, implemente `IUserIdProvider` y registre la implementación.

En el código de ejemplo se muestra cómo usar las notificaciones para seleccionar la dirección de correo electrónico del usuario como la propiedad que identifica. 

> [!NOTE]
> El valor que elija debe ser único entre todos los usuarios del sistema. De lo contrario, un mensaje destinado a un usuario podría acabar pasando a otro usuario.

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

El registro de la cuenta agrega una demanda con el tipo `ClaimsTypes.Email` a la base de datos de identidad de ASP.NET.

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

Registre este componente en el `Startup.ConfigureServices`.

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>Autorización para que los usuarios accedan a los concentradores y métodos de concentrador

De forma predeterminada, los usuarios no autenticados pueden llamar a todos los métodos de un concentrador. Para requerir la autenticación, aplique el atributo [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) al centro:

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

Puede usar los argumentos de constructor y las propiedades del atributo `[Authorize]` para restringir el acceso solo a los usuarios que coincidan con [las directivas de autorización](xref:security/authorization/policies)específicas. Por ejemplo, si tiene una directiva de autorización personalizada llamada `MyAuthorizationPolicy` puede asegurarse de que solo los usuarios que coincidan con esa Directiva puedan acceder a la central mediante el código siguiente:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

Los métodos de concentrador individuales pueden tener también el atributo `[Authorize]` aplicado. Si el usuario actual no coincide con la Directiva que se aplica al método, se devuelve un error al autor de la llamada:

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a>Usar controladores de autorización para personalizar la autorización del método de concentrador

SignalR proporciona un recurso personalizado a los controladores de autorización cuando un método de concentrador requiere autorización. El recurso es una instancia de `HubInvocationContext`. El `HubInvocationContext` incluye el `HubCallerContext`, el nombre del método de concentrador que se va a invocar y los argumentos para el método de concentrador.

Considere el ejemplo de un salón de chat que permite el inicio de sesión en varias organizaciones a través de Azure Active Directory. Cualquier persona con un cuenta de Microsoft puede iniciar sesión en el chat, pero solo los miembros de la organización propietaria deben ser capaces de prohibir a los usuarios o ver el historial de chat de los usuarios. Además, es posible que quiera restringir ciertas funciones de determinados usuarios. El uso de las características actualizadas en ASP.NET Core 3,0 es todo lo posible. Observe cómo el `DomainRestrictedRequirement` actúa como `IAuthorizationRequirement`personalizado. Ahora que se está pasando el parámetro de recurso `HubInvocationContext`, la lógica interna puede inspeccionar el contexto en el que se llama al centro y tomar decisiones sobre cómo permitir que el usuario ejecute métodos de concentrador individuales.

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

En `Startup.ConfigureServices`, agregue la nueva Directiva y proporcione el requisito de `DomainRestrictedRequirement` personalizado como parámetro para crear la Directiva de `DomainRestricted`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

En el ejemplo anterior, la clase `DomainRestrictedRequirement` es tanto una `IAuthorizationRequirement` como su propia `AuthorizationHandler` para ese requisito. Es aceptable dividir estos dos componentes en clases independientes para separar los problemas. Una ventaja del enfoque del ejemplo es que no es necesario insertar el `AuthorizationHandler` durante el inicio, ya que el requisito y el controlador son los mismos.

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* [Autenticación de token de portador en ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Autorización basada en recursos](xref:security/authorization/resourcebased)
