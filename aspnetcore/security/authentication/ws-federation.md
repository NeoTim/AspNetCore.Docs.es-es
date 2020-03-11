---
title: Autenticación de usuarios con WS-Federation en ASP.NET Core
author: chlowell
description: En este tutorial se muestra cómo usar WS-Federation en una aplicación ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
uid: security/authentication/ws-federation
ms.openlocfilehash: d82421a14ede6cb6b01ef59f233bb2eba6b56aec
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651335"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a><span data-ttu-id="c1327-103">Autenticación de usuarios con WS-Federation en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c1327-103">Authenticate users with WS-Federation in ASP.NET Core</span></span>

<span data-ttu-id="c1327-104">En este tutorial se muestra cómo permitir a los usuarios iniciar sesión con un proveedor de autenticación de WS-Federation como Servicios de federación de Active Directory (AD FS) (ADFS) o [Azure Active Directory](/azure/active-directory/) (AAD).</span><span class="sxs-lookup"><span data-stu-id="c1327-104">This tutorial demonstrates how to enable users to sign in with a WS-Federation authentication provider like Active Directory Federation Services (ADFS) or [Azure Active Directory](/azure/active-directory/) (AAD).</span></span> <span data-ttu-id="c1327-105">Usa la aplicación de ejemplo ASP.NET Core 2,0 descrita en [Facebook, Google y la autenticación de proveedor externo](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="c1327-105">It uses the ASP.NET Core 2.0 sample app described in [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="c1327-106">En el caso de las aplicaciones ASP.NET Core 2,0, [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)proporciona compatibilidad con WS-Federation.</span><span class="sxs-lookup"><span data-stu-id="c1327-106">For ASP.NET Core 2.0 apps, WS-Federation support is provided by [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span></span> <span data-ttu-id="c1327-107">Este componente se traslada de [Microsoft. Owin. Security. WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) y comparte muchos de los mecanismos de ese componente.</span><span class="sxs-lookup"><span data-stu-id="c1327-107">This component is ported from [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) and shares many of that component's mechanics.</span></span> <span data-ttu-id="c1327-108">Sin embargo, los componentes se diferencian en un par de formas importantes.</span><span class="sxs-lookup"><span data-stu-id="c1327-108">However, the components differ in a couple of important ways.</span></span>

<span data-ttu-id="c1327-109">De forma predeterminada, el nuevo middleware:</span><span class="sxs-lookup"><span data-stu-id="c1327-109">By default, the new middleware:</span></span>

* <span data-ttu-id="c1327-110">No permite inicios de sesión no solicitados.</span><span class="sxs-lookup"><span data-stu-id="c1327-110">Doesn't allow unsolicited logins.</span></span> <span data-ttu-id="c1327-111">Esta característica del protocolo WS-Federation es vulnerable a ataques de XSRF.</span><span class="sxs-lookup"><span data-stu-id="c1327-111">This feature of the WS-Federation protocol is vulnerable to XSRF attacks.</span></span> <span data-ttu-id="c1327-112">Sin embargo, se puede habilitar con la opción `AllowUnsolicitedLogins`.</span><span class="sxs-lookup"><span data-stu-id="c1327-112">However, it can be enabled with the `AllowUnsolicitedLogins` option.</span></span>
* <span data-ttu-id="c1327-113">No comprueba los mensajes de inicio de sesión de todos los formularios.</span><span class="sxs-lookup"><span data-stu-id="c1327-113">Doesn't check every form post for sign-in messages.</span></span> <span data-ttu-id="c1327-114">Solo se comprueban los inicios de sesión de las solicitudes a los `CallbackPath`. `CallbackPath` tiene como valor predeterminado `/signin-wsfed`, pero se puede cambiar a través de la propiedad [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) heredada de la clase [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) .</span><span class="sxs-lookup"><span data-stu-id="c1327-114">Only requests to the `CallbackPath` are checked for sign-ins. `CallbackPath` defaults to `/signin-wsfed` but can be changed via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) class.</span></span> <span data-ttu-id="c1327-115">Esta ruta de acceso se puede compartir con otros proveedores de autenticación habilitando la opción [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) .</span><span class="sxs-lookup"><span data-stu-id="c1327-115">This path can be shared with other authentication providers by enabling the [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) option.</span></span>

## <a name="register-the-app-with-active-directory"></a><span data-ttu-id="c1327-116">Registrar la aplicación con Active Directory</span><span class="sxs-lookup"><span data-stu-id="c1327-116">Register the app with Active Directory</span></span>

### <a name="active-directory-federation-services"></a><span data-ttu-id="c1327-117">Servicios de federación de Active Directory</span><span class="sxs-lookup"><span data-stu-id="c1327-117">Active Directory Federation Services</span></span>

* <span data-ttu-id="c1327-118">Abra el **Asistente para agregar relación de confianza para usuario autenticado** desde la consola de administración de ADFS:</span><span class="sxs-lookup"><span data-stu-id="c1327-118">Open the server's **Add Relying Party Trust Wizard** from the ADFS Management console:</span></span>

![Asistente para agregar relación de confianza para usuario autenticado: Página principal](ws-federation/_static/AdfsAddTrust.png)

* <span data-ttu-id="c1327-120">Elija escribir los datos manualmente:</span><span class="sxs-lookup"><span data-stu-id="c1327-120">Choose to enter data manually:</span></span>

![Asistente para agregar relación de confianza para usuario autenticado: seleccionar origen de datos](ws-federation/_static/AdfsSelectDataSource.png)

* <span data-ttu-id="c1327-122">Escriba un nombre para mostrar para el usuario de confianza.</span><span class="sxs-lookup"><span data-stu-id="c1327-122">Enter a display name for the relying party.</span></span> <span data-ttu-id="c1327-123">El nombre no es importante para la aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1327-123">The name isn't important to the ASP.NET Core app.</span></span>

* <span data-ttu-id="c1327-124">[Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) carece de compatibilidad con el cifrado de tokens, por lo que no configure un certificado de cifrado de tokens:</span><span class="sxs-lookup"><span data-stu-id="c1327-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) lacks support for token encryption, so don't configure a token encryption certificate:</span></span>

![Asistente para agregar relación de confianza para usuario autenticado: configurar certificado](ws-federation/_static/AdfsConfigureCert.png)

* <span data-ttu-id="c1327-126">Habilite la compatibilidad con el protocolo pasivo de WS-Federation mediante la dirección URL de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c1327-126">Enable support for WS-Federation Passive protocol, using the app's URL.</span></span> <span data-ttu-id="c1327-127">Compruebe que el puerto sea correcto para la aplicación:</span><span class="sxs-lookup"><span data-stu-id="c1327-127">Verify the port is correct for the app:</span></span>

![Asistente para agregar relación de confianza para usuario autenticado: configurar URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> <span data-ttu-id="c1327-129">Debe ser una dirección URL HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c1327-129">This must be an HTTPS URL.</span></span> <span data-ttu-id="c1327-130">IIS Express puede proporcionar un certificado autofirmado al hospedar la aplicación durante el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="c1327-130">IIS Express can provide a self-signed certificate when hosting the app during development.</span></span> <span data-ttu-id="c1327-131">Kestrel requiere la configuración manual del certificado.</span><span class="sxs-lookup"><span data-stu-id="c1327-131">Kestrel requires manual certificate configuration.</span></span> <span data-ttu-id="c1327-132">Consulte la [documentación de Kestrel](xref:fundamentals/servers/kestrel) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="c1327-132">See the [Kestrel documentation](xref:fundamentals/servers/kestrel) for more details.</span></span>

* <span data-ttu-id="c1327-133">Haga clic en **siguiente** a través del resto del asistente y **cierre** al final.</span><span class="sxs-lookup"><span data-stu-id="c1327-133">Click **Next** through the rest of the wizard and **Close** at the end.</span></span>

* <span data-ttu-id="c1327-134">ASP.NET Core identidad requiere una demanda de **identificador de nombre** .</span><span class="sxs-lookup"><span data-stu-id="c1327-134">ASP.NET Core Identity requires a **Name ID** claim.</span></span> <span data-ttu-id="c1327-135">Agregue uno en el cuadro de diálogo **editar reglas de notificaciones** :</span><span class="sxs-lookup"><span data-stu-id="c1327-135">Add one from the **Edit Claim Rules** dialog:</span></span>

![Editar reglas de notificaciones](ws-federation/_static/EditClaimRules.png)

* <span data-ttu-id="c1327-137">En el **Asistente para agregar regla de notificación de transformación**, deje seleccionada la plantilla predeterminada **Enviar atributos LDAP como notificaciones** y haga clic en **siguiente**.</span><span class="sxs-lookup"><span data-stu-id="c1327-137">In the **Add Transform Claim Rule Wizard**, leave the default **Send LDAP Attributes as Claims** template selected, and click **Next**.</span></span> <span data-ttu-id="c1327-138">Agregue una regla que asigne el atributo LDAP **Sam-Account-Name** a la notificaciones salientes de **ID. de nombre** :</span><span class="sxs-lookup"><span data-stu-id="c1327-138">Add a rule mapping the **SAM-Account-Name** LDAP attribute to the **Name ID** outgoing claim:</span></span>

![Asistente para agregar regla de notificaciones de transformación: configurar regla de notificaciones](ws-federation/_static/AddTransformClaimRule.png)

* <span data-ttu-id="c1327-140">Haga clic en **finalizar** > **Aceptar** en la ventana **editar reglas de notificaciones** .</span><span class="sxs-lookup"><span data-stu-id="c1327-140">Click **Finish** > **OK** in the **Edit Claim Rules** window.</span></span>

### <a name="azure-active-directory"></a><span data-ttu-id="c1327-141">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="c1327-141">Azure Active Directory</span></span>

* <span data-ttu-id="c1327-142">Vaya a la hoja registros de aplicaciones del inquilino de AAD.</span><span class="sxs-lookup"><span data-stu-id="c1327-142">Navigate to the AAD tenant's app registrations blade.</span></span> <span data-ttu-id="c1327-143">Haga clic en **nuevo registro de aplicaciones**:</span><span class="sxs-lookup"><span data-stu-id="c1327-143">Click **New application registration**:</span></span>

![Azure Active Directory: Registros de aplicaciones](ws-federation/_static/AadNewAppRegistration.png)

* <span data-ttu-id="c1327-145">Escriba un nombre para el registro de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c1327-145">Enter a name for the app registration.</span></span> <span data-ttu-id="c1327-146">Esto no es importante para la aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1327-146">This isn't important to the ASP.NET Core app.</span></span>
* <span data-ttu-id="c1327-147">Escriba la dirección URL en la que escucha la aplicación como la **dirección URL de inicio de sesión**:</span><span class="sxs-lookup"><span data-stu-id="c1327-147">Enter the URL the app listens on as the **Sign-on URL**:</span></span>

![Azure Active Directory: creación del registro de aplicaciones](ws-federation/_static/AadCreateAppRegistration.png)

* <span data-ttu-id="c1327-149">Haga clic en **extremos** y anote la dirección URL del **documento de metadatos de Federación** .</span><span class="sxs-lookup"><span data-stu-id="c1327-149">Click **Endpoints** and note the **Federation Metadata Document** URL.</span></span> <span data-ttu-id="c1327-150">Este es el `MetadataAddress`del middleware de WS-Federation:</span><span class="sxs-lookup"><span data-stu-id="c1327-150">This is the WS-Federation middleware's `MetadataAddress`:</span></span>

![Azure Active Directory: puntos de conexión](ws-federation/_static/AadFederationMetadataDocument.png)

* <span data-ttu-id="c1327-152">Navegue al nuevo registro de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="c1327-152">Navigate to the new app registration.</span></span> <span data-ttu-id="c1327-153">Haga clic en **configuración** > **propiedades** y anote el **URI del ID**. de aplicación.</span><span class="sxs-lookup"><span data-stu-id="c1327-153">Click **Settings** > **Properties** and make note of the **App ID URI**.</span></span> <span data-ttu-id="c1327-154">Este es el `Wtrealm`del middleware de WS-Federation:</span><span class="sxs-lookup"><span data-stu-id="c1327-154">This is the WS-Federation middleware's `Wtrealm`:</span></span>

![Azure Active Directory: propiedades de registro de aplicaciones](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a><span data-ttu-id="c1327-156">Usar WS-Federation sin ASP.NET Core identidad</span><span class="sxs-lookup"><span data-stu-id="c1327-156">Use WS-Federation without ASP.NET Core Identity</span></span>

<span data-ttu-id="c1327-157">El middleware de WS-Federation se puede usar sin identidad.</span><span class="sxs-lookup"><span data-stu-id="c1327-157">The WS-Federation middleware can be used without Identity.</span></span> <span data-ttu-id="c1327-158">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c1327-158">For example:</span></span>
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a><span data-ttu-id="c1327-159">Agregar WS-Federation como proveedor de inicio de sesión externo para ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="c1327-159">Add WS-Federation as an external login provider for ASP.NET Core Identity</span></span>

* <span data-ttu-id="c1327-160">Agregue una dependencia de [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) al proyecto.</span><span class="sxs-lookup"><span data-stu-id="c1327-160">Add a dependency on [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) to the project.</span></span>
* <span data-ttu-id="c1327-161">Agregue WS-Federation a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c1327-161">Add WS-Federation to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a><span data-ttu-id="c1327-162">Inicio de sesión con WS-Federation</span><span class="sxs-lookup"><span data-stu-id="c1327-162">Log in with WS-Federation</span></span>

<span data-ttu-id="c1327-163">Vaya a la aplicación y haga clic en el vínculo **iniciar sesión** en el encabezado de navegación.</span><span class="sxs-lookup"><span data-stu-id="c1327-163">Browse to the app and click the **Log in** link in the nav header.</span></span> <span data-ttu-id="c1327-164">Hay una opción para iniciar sesión con WsFederation: ![página de inicio de sesión](ws-federation/_static/WsFederationButton.png)</span><span class="sxs-lookup"><span data-stu-id="c1327-164">There's an option to log in with WsFederation: ![Log in page](ws-federation/_static/WsFederationButton.png)</span></span>

<span data-ttu-id="c1327-165">Con ADFS como proveedor, el botón redirige a una página de inicio de sesión de ADFS: ![página de inicio de sesión de ADFS](ws-federation/_static/AdfsLoginPage.png)</span><span class="sxs-lookup"><span data-stu-id="c1327-165">With ADFS as the provider, the button redirects to an ADFS sign-in page: ![ADFS sign-in page](ws-federation/_static/AdfsLoginPage.png)</span></span>

<span data-ttu-id="c1327-166">Con Azure Active Directory como proveedor, el botón redirige a una página de inicio de sesión de AAD: ![página de inicio de sesión de AAD](ws-federation/_static/AadSignIn.png)</span><span class="sxs-lookup"><span data-stu-id="c1327-166">With Azure Active Directory as the provider, the button redirects to an AAD sign-in page: ![AAD sign-in page](ws-federation/_static/AadSignIn.png)</span></span>

<span data-ttu-id="c1327-167">Un inicio de sesión correcto para un nuevo usuario redirige a la página de registro del usuario de la aplicación: ![página registrar](ws-federation/_static/Register.png)</span><span class="sxs-lookup"><span data-stu-id="c1327-167">A successful sign-in for a new user redirects to the app's user registration page: ![Register page](ws-federation/_static/Register.png)</span></span>