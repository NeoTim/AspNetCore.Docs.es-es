---
title: Autenticación y autorización en ASP.NET CoreSignalR
author: bradygaster
description: Aprenda a usar la autenticación y autorización en ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/authn-and-authz
ms.openlocfilehash: 794465ceb69f47ee3d5cc8c100b321cb958d9cfe
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407140"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a><span data-ttu-id="ac955-103">Autenticación y autorización en ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="ac955-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="ac955-104">Por [Andrew Stanton-enfermera](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="ac955-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="ac955-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(cómo descargarlo)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="ac955-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a><span data-ttu-id="ac955-106">Autenticar a los usuarios que se conectan a un SignalR centro</span><span class="sxs-lookup"><span data-stu-id="ac955-106">Authenticate users connecting to a SignalR hub</span></span>

SignalR<span data-ttu-id="ac955-107">se puede usar con la [autenticación de ASP.net Core](xref:security/authentication/identity) para asociar un usuario a cada conexión.</span><span class="sxs-lookup"><span data-stu-id="ac955-107"> can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="ac955-108">En un concentrador, se puede tener acceso a los datos de autenticación desde la propiedad [HubConnectionContext. User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) .</span><span class="sxs-lookup"><span data-stu-id="ac955-108">In a hub, authentication data can be accessed from the [HubConnectionContext.User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="ac955-109">La autenticación permite que el concentrador llame a métodos en todas las conexiones asociadas a un usuario.</span><span class="sxs-lookup"><span data-stu-id="ac955-109">Authentication allows the hub to call methods on all connections associated with a user.</span></span> <span data-ttu-id="ac955-110">Para obtener más información, vea [administrar usuarios y grupos SignalR en ](xref:signalr/groups).</span><span class="sxs-lookup"><span data-stu-id="ac955-110">For more information, see [Manage users and groups in SignalR](xref:signalr/groups).</span></span> <span data-ttu-id="ac955-111">Es posible que varias conexiones estén asociadas a un solo usuario.</span><span class="sxs-lookup"><span data-stu-id="ac955-111">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="ac955-112">El siguiente es un ejemplo del `Startup.Configure` uso de SignalR y la autenticación ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="ac955-112">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="ac955-113">El orden en el que se registran SignalR y ASP.net Core middleware de autenticación es importante.</span><span class="sxs-lookup"><span data-stu-id="ac955-113">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="ac955-114">Llame siempre a `UseAuthentication` antes de `UseSignalR` para que SignalR tenga un usuario en `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="ac955-114">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

::: moniker-end

### <a name="cookie-authentication"></a><span data-ttu-id="ac955-115">Autenticación de cookies</span><span class="sxs-lookup"><span data-stu-id="ac955-115">Cookie authentication</span></span>

<span data-ttu-id="ac955-116">En una aplicación basada en explorador, la autenticación de cookies permite que las credenciales de usuario existentes fluyan automáticamente a SignalR las conexiones.</span><span class="sxs-lookup"><span data-stu-id="ac955-116">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="ac955-117">Cuando se usa el cliente del explorador, no se necesita ninguna configuración adicional.</span><span class="sxs-lookup"><span data-stu-id="ac955-117">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="ac955-118">Si el usuario ha iniciado sesión en la aplicación, la SignalR conexión hereda automáticamente esta autenticación.</span><span class="sxs-lookup"><span data-stu-id="ac955-118">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="ac955-119">Las cookies son una forma específica del explorador de enviar tokens de acceso, pero los clientes que no son de explorador pueden enviarlas.</span><span class="sxs-lookup"><span data-stu-id="ac955-119">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="ac955-120">Al utilizar el [cliente .net](xref:signalr/dotnet-client), la `Cookies` propiedad se puede configurar en la `.WithUrl` llamada para proporcionar una cookie.</span><span class="sxs-lookup"><span data-stu-id="ac955-120">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call to provide a cookie.</span></span> <span data-ttu-id="ac955-121">Sin embargo, el uso de la autenticación de cookies del cliente .NET requiere que la aplicación proporcione una API para intercambiar los datos de autenticación de una cookie.</span><span class="sxs-lookup"><span data-stu-id="ac955-121">However, using cookie authentication from the .NET client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="ac955-122">Autenticación por token de portador</span><span class="sxs-lookup"><span data-stu-id="ac955-122">Bearer token authentication</span></span>

<span data-ttu-id="ac955-123">El cliente puede proporcionar un token de acceso en lugar de usar una cookie.</span><span class="sxs-lookup"><span data-stu-id="ac955-123">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="ac955-124">El servidor valida el token y lo usa para identificar al usuario.</span><span class="sxs-lookup"><span data-stu-id="ac955-124">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="ac955-125">Esta validación se realiza solo cuando se establece la conexión.</span><span class="sxs-lookup"><span data-stu-id="ac955-125">This validation is done only when the connection is established.</span></span> <span data-ttu-id="ac955-126">Durante la vida de la conexión, el servidor no se vuelve a validar automáticamente para comprobar la revocación de tokens.</span><span class="sxs-lookup"><span data-stu-id="ac955-126">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="ac955-127">En el servidor, la autenticación de token de portador se configura mediante el [middleware de portador JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="ac955-127">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="ac955-128">En el cliente de JavaScript, el token se puede proporcionar mediante la opción [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) .</span><span class="sxs-lookup"><span data-stu-id="ac955-128">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

<span data-ttu-id="ac955-129">En el cliente de .NET, hay una propiedad [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) similar que se puede usar para configurar el token:</span><span class="sxs-lookup"><span data-stu-id="ac955-129">In the .NET client, there's a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="ac955-130">Se llama a la función de token de acceso que se proporciona antes de **cada** solicitud HTTP realizada por SignalR .</span><span class="sxs-lookup"><span data-stu-id="ac955-130">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="ac955-131">Si necesita renovar el token para mantener la conexión activa (porque puede expirar durante la conexión), hágalo desde esta función y devuelva el token actualizado.</span><span class="sxs-lookup"><span data-stu-id="ac955-131">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="ac955-132">En las API Web estándar, los tokens de portador se envían en un encabezado HTTP.</span><span class="sxs-lookup"><span data-stu-id="ac955-132">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="ac955-133">Sin embargo, SignalR no puede establecer estos encabezados en exploradores cuando se usan algunos transportes.</span><span class="sxs-lookup"><span data-stu-id="ac955-133">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="ac955-134">Cuando se usan WebSockets y eventos enviados por el servidor, el token se transmite como un parámetro de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="ac955-134">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="ac955-135">Para admitir esto en el servidor, se requiere una configuración adicional:</span><span class="sxs-lookup"><span data-stu-id="ac955-135">To support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> <span data-ttu-id="ac955-136">La cadena de consulta se usa en exploradores al conectarse con WebSockets y eventos enviados por el servidor debido a las limitaciones de la API del explorador.</span><span class="sxs-lookup"><span data-stu-id="ac955-136">The query string is used on browsers when connecting with WebSockets and Server-Sent Events due to browser API limitations.</span></span> <span data-ttu-id="ac955-137">Cuando se usa HTTPS, los valores de cadena de consulta están protegidos por la conexión TLS.</span><span class="sxs-lookup"><span data-stu-id="ac955-137">When using HTTPS, query string values are secured by the TLS connection.</span></span> <span data-ttu-id="ac955-138">Sin embargo, muchos servidores registran valores de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="ac955-138">However, many servers log query string values.</span></span> <span data-ttu-id="ac955-139">Para obtener más información, vea [consideraciones de seguridad SignalR en ASP.net Core ](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="ac955-139">For more information, see [Security considerations in ASP.NET Core SignalR](xref:signalr/security).</span></span> SignalR<span data-ttu-id="ac955-140">utiliza encabezados para transmitir tokens en entornos que los admiten (como los clientes de .NET y Java).</span><span class="sxs-lookup"><span data-stu-id="ac955-140"> uses headers to transmit tokens in environments which support them (such as the .NET and Java clients).</span></span>

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="ac955-141">Cookies frente a tokens de portador</span><span class="sxs-lookup"><span data-stu-id="ac955-141">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="ac955-142">Las cookies son específicas de los exploradores.</span><span class="sxs-lookup"><span data-stu-id="ac955-142">Cookies are specific to browsers.</span></span> <span data-ttu-id="ac955-143">Enviarlos desde otros tipos de clientes agrega complejidad en comparación con el envío de tokens de portador.</span><span class="sxs-lookup"><span data-stu-id="ac955-143">Sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="ac955-144">Por lo tanto, no se recomienda la autenticación de cookies a menos que la aplicación solo necesite autenticar a los usuarios desde el cliente del explorador.</span><span class="sxs-lookup"><span data-stu-id="ac955-144">Consequently, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="ac955-145">La autenticación de token de portador es el enfoque recomendado al usar clientes que no sean el cliente del explorador.</span><span class="sxs-lookup"><span data-stu-id="ac955-145">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="ac955-146">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="ac955-146">Windows authentication</span></span>

<span data-ttu-id="ac955-147">Si la [autenticación de Windows](xref:security/authentication/windowsauth) está configurada en la aplicación, SignalR puede usar esa identidad para proteger los concentradores.</span><span class="sxs-lookup"><span data-stu-id="ac955-147">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="ac955-148">Sin embargo, para enviar mensajes a usuarios individuales, debe agregar un proveedor de ID. de usuario personalizado.</span><span class="sxs-lookup"><span data-stu-id="ac955-148">However, to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="ac955-149">El sistema de autenticación de Windows no proporciona la demanda de "identificador de nombre".</span><span class="sxs-lookup"><span data-stu-id="ac955-149">The Windows authentication system doesn't provide the "Name Identifier" claim.</span></span> SignalR<span data-ttu-id="ac955-150">usa la Claim para determinar el nombre de usuario.</span><span class="sxs-lookup"><span data-stu-id="ac955-150"> uses the claim to determine the user name.</span></span>

<span data-ttu-id="ac955-151">Agregue una nueva clase que implemente `IUserIdProvider` y recupere una de las notificaciones del usuario que se va a usar como identificador.</span><span class="sxs-lookup"><span data-stu-id="ac955-151">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="ac955-152">Por ejemplo, para usar la notificaciones "Name" (que es el nombre de usuario de Windows en el formulario `[Domain]\[Username]` ), cree la clase siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac955-152">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="ac955-153">En lugar de `ClaimTypes.Name` , puede usar cualquier valor de `User` (como el identificador de SID de Windows, etc.).</span><span class="sxs-lookup"><span data-stu-id="ac955-153">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, and so on).</span></span>

> [!NOTE]
> <span data-ttu-id="ac955-154">El valor que elija debe ser único entre todos los usuarios del sistema.</span><span class="sxs-lookup"><span data-stu-id="ac955-154">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="ac955-155">De lo contrario, un mensaje destinado a un usuario podría acabar pasando a otro usuario.</span><span class="sxs-lookup"><span data-stu-id="ac955-155">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="ac955-156">Registre este componente en el `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="ac955-156">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="ac955-157">En el cliente de .NET, la autenticación de Windows debe habilitarse estableciendo la propiedad [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) :</span><span class="sxs-lookup"><span data-stu-id="ac955-157">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="ac955-158">La autenticación de Windows se admite en Internet Explorer y Microsoft Edge, pero no en todos los exploradores.</span><span class="sxs-lookup"><span data-stu-id="ac955-158">Windows authentication is supported in Internet Explorer and Microsoft Edge, but not in all browsers.</span></span> <span data-ttu-id="ac955-159">Por ejemplo, en Chrome y Safari, se produce un error al intentar usar la autenticación de Windows y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ac955-159">For example, in Chrome and Safari, attempting to use Windows authentication and WebSockets fails.</span></span> <span data-ttu-id="ac955-160">Cuando se produce un error en la autenticación de Windows, el cliente intenta revertir a otros transportes que podrían funcionar.</span><span class="sxs-lookup"><span data-stu-id="ac955-160">When Windows authentication fails, the client attempts to fall back to other transports which might work.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="ac955-161">Usar notificaciones para personalizar el control de identidades</span><span class="sxs-lookup"><span data-stu-id="ac955-161">Use claims to customize identity handling</span></span>

<span data-ttu-id="ac955-162">Una aplicación que autentica a los usuarios puede derivar los SignalR ID. de usuario de las notificaciones de usuario.</span><span class="sxs-lookup"><span data-stu-id="ac955-162">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="ac955-163">Para especificar cómo SignalR crea los ID. de usuario, implementa `IUserIdProvider` y registra la implementación.</span><span class="sxs-lookup"><span data-stu-id="ac955-163">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="ac955-164">En el código de ejemplo se muestra cómo usar las notificaciones para seleccionar la dirección de correo electrónico del usuario como la propiedad que identifica.</span><span class="sxs-lookup"><span data-stu-id="ac955-164">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="ac955-165">El valor que elija debe ser único entre todos los usuarios del sistema.</span><span class="sxs-lookup"><span data-stu-id="ac955-165">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="ac955-166">De lo contrario, un mensaje destinado a un usuario podría acabar pasando a otro usuario.</span><span class="sxs-lookup"><span data-stu-id="ac955-166">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="ac955-167">El registro de la cuenta agrega una demanda con `ClaimsTypes.Email` el tipo a la base de datos de identidad de ASP.net.</span><span class="sxs-lookup"><span data-stu-id="ac955-167">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="ac955-168">Registre este componente en su `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ac955-168">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="ac955-169">Autorización para que los usuarios accedan a los concentradores y métodos de concentrador</span><span class="sxs-lookup"><span data-stu-id="ac955-169">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="ac955-170">De forma predeterminada, los usuarios no autenticados pueden llamar a todos los métodos de un concentrador.</span><span class="sxs-lookup"><span data-stu-id="ac955-170">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="ac955-171">Para requerir la autenticación, aplique el atributo [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) al centro:</span><span class="sxs-lookup"><span data-stu-id="ac955-171">To require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="ac955-172">Puede usar los argumentos de constructor y las propiedades del atributo `[Authorize]` para restringir el acceso solo a los usuarios que coincidan con [directivas de autorización](xref:security/authorization/policies) específicas.</span><span class="sxs-lookup"><span data-stu-id="ac955-172">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="ac955-173">Por ejemplo, si tiene una directiva de autorización personalizada denominada, `MyAuthorizationPolicy` puede asegurarse de que solo los usuarios que coincidan con esa Directiva puedan tener acceso al centro mediante el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac955-173">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

<span data-ttu-id="ac955-174">Los métodos de concentrador individuales también pueden tener el `[Authorize]` atributo aplicado.</span><span class="sxs-lookup"><span data-stu-id="ac955-174">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="ac955-175">Si el usuario actual no coincide con la Directiva que se aplica al método, se devuelve un error al autor de la llamada:</span><span class="sxs-lookup"><span data-stu-id="ac955-175">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

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

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a><span data-ttu-id="ac955-176">Usar controladores de autorización para personalizar la autorización del método de concentrador</span><span class="sxs-lookup"><span data-stu-id="ac955-176">Use authorization handlers to customize hub method authorization</span></span>

SignalR<span data-ttu-id="ac955-177">proporciona un recurso personalizado a los controladores de autorización cuando un método de concentrador requiere autorización.</span><span class="sxs-lookup"><span data-stu-id="ac955-177"> provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="ac955-178">El recurso es una instancia de `HubInvocationContext`.</span><span class="sxs-lookup"><span data-stu-id="ac955-178">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="ac955-179">`HubInvocationContext`Incluye `HubCallerContext` , el nombre del método de concentrador que se va a invocar y los argumentos para el método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="ac955-179">The `HubInvocationContext` includes the `HubCallerContext`, the name of the hub method being invoked, and the arguments to the hub method.</span></span>

<span data-ttu-id="ac955-180">Considere el ejemplo de un salón de chat que permite el inicio de sesión en varias organizaciones a través de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="ac955-180">Consider the example of a chat room allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="ac955-181">Cualquier persona con un cuenta de Microsoft puede iniciar sesión en el chat, pero solo los miembros de la organización propietaria deben ser capaces de prohibir a los usuarios o ver el historial de chat de los usuarios.</span><span class="sxs-lookup"><span data-stu-id="ac955-181">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization should be able to ban users or view users' chat histories.</span></span> <span data-ttu-id="ac955-182">Además, es posible que quiera restringir ciertas funciones de determinados usuarios.</span><span class="sxs-lookup"><span data-stu-id="ac955-182">Furthermore, we might want to restrict certain functionality from certain users.</span></span> <span data-ttu-id="ac955-183">El uso de las características actualizadas en ASP.NET Core 3,0 es todo lo posible.</span><span class="sxs-lookup"><span data-stu-id="ac955-183">Using the updated features in ASP.NET Core 3.0, this is entirely possible.</span></span> <span data-ttu-id="ac955-184">Observe cómo `DomainRestrictedRequirement` actúa como un personalizado `IAuthorizationRequirement` .</span><span class="sxs-lookup"><span data-stu-id="ac955-184">Note how the `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="ac955-185">Ahora que `HubInvocationContext` se pasa el parámetro de recurso, la lógica interna puede inspeccionar el contexto en el que se llama al centro y tomar decisiones sobre cómo permitir al usuario ejecutar métodos de concentrador individuales.</span><span class="sxs-lookup"><span data-stu-id="ac955-185">Now that the `HubInvocationContext` resource parameter is being passed in, the internal logic can inspect the context in which the Hub is being called and make decisions on allowing the user to execute individual Hub methods.</span></span>

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

<span data-ttu-id="ac955-186">En `Startup.ConfigureServices` , agregue la nueva Directiva y proporcione el `DomainRestrictedRequirement` requisito personalizado como parámetro para crear la `DomainRestricted` Directiva.</span><span class="sxs-lookup"><span data-stu-id="ac955-186">In `Startup.ConfigureServices`, add the new policy, providing the custom `DomainRestrictedRequirement` requirement as a parameter to create the `DomainRestricted` policy.</span></span>

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

<span data-ttu-id="ac955-187">En el ejemplo anterior, la `DomainRestrictedRequirement` clase es y la `IAuthorizationRequirement` suya propia `AuthorizationHandler` para ese requisito.</span><span class="sxs-lookup"><span data-stu-id="ac955-187">In the preceding example, the `DomainRestrictedRequirement` class is both an `IAuthorizationRequirement` and its own `AuthorizationHandler` for that requirement.</span></span> <span data-ttu-id="ac955-188">Es aceptable dividir estos dos componentes en clases independientes para separar los problemas.</span><span class="sxs-lookup"><span data-stu-id="ac955-188">It's acceptable to split these two components into separate classes to separate concerns.</span></span> <span data-ttu-id="ac955-189">Una ventaja del enfoque del ejemplo es que no es necesario insertar `AuthorizationHandler` durante el inicio, ya que el requisito y el controlador son los mismos.</span><span class="sxs-lookup"><span data-stu-id="ac955-189">A benefit of the example's approach is there's no need to inject the `AuthorizationHandler` during startup, as the requirement and the handler are the same thing.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ac955-190">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="ac955-190">Additional resources</span></span>

* [<span data-ttu-id="ac955-191">Autenticación de token de portador en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac955-191">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="ac955-192">Autorización basada en recursos</span><span class="sxs-lookup"><span data-stu-id="ac955-192">Resource-based Authorization</span></span>](xref:security/authorization/resourcebased)
