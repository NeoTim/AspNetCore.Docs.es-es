---
title: Aplicación de HTTPS en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo requerir HTTPS/TLS en una aplicación Web de ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2019
uid: security/enforcing-ssl
ms.openlocfilehash: 82cd2e52f3bd929682b9eae24611ad04fd9f8682
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317366"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="2570a-103">Aplicación de HTTPS en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2570a-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="2570a-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2570a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2570a-105">En este documento se muestra cómo:</span><span class="sxs-lookup"><span data-stu-id="2570a-105">This document shows how to:</span></span>

* <span data-ttu-id="2570a-106">Requiere HTTPS para todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="2570a-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="2570a-107">Redirija todas las solicitudes HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2570a-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="2570a-108">Ninguna API puede impedir que un cliente envíe datos confidenciales en la primera solicitud.</span><span class="sxs-lookup"><span data-stu-id="2570a-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="2570a-109">Proyectos de API</span><span class="sxs-lookup"><span data-stu-id="2570a-109">API projects</span></span>
>
> <span data-ttu-id="2570a-110">**No** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) en las API Web que reciben información confidencial.</span><span class="sxs-lookup"><span data-stu-id="2570a-110">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="2570a-111">`RequireHttpsAttribute` usa códigos de Estado HTTP para redirigir exploradores de HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2570a-111">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="2570a-112">Es posible que los clientes de API no conozcan o cumplan las redirecciones de HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2570a-112">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="2570a-113">Estos clientes pueden enviar información a través de HTTP.</span><span class="sxs-lookup"><span data-stu-id="2570a-113">Such clients may send information over HTTP.</span></span> <span data-ttu-id="2570a-114">Las API Web deben:</span><span class="sxs-lookup"><span data-stu-id="2570a-114">Web APIs should either:</span></span>
>
> * <span data-ttu-id="2570a-115">No escuche en HTTP.</span><span class="sxs-lookup"><span data-stu-id="2570a-115">Not listen on HTTP.</span></span>
> * <span data-ttu-id="2570a-116">Cierre la conexión con el código de estado 400 (solicitud incorrecta) y no atienda la solicitud.</span><span class="sxs-lookup"><span data-stu-id="2570a-116">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
>
> ## <a name="hsts-and-api-projects"></a><span data-ttu-id="2570a-117">Proyectos de HSTS y API</span><span class="sxs-lookup"><span data-stu-id="2570a-117">HSTS and API projects</span></span>
>
> <span data-ttu-id="2570a-118">Los proyectos de API predeterminados no incluyen [HSTS](#hsts) porque HSTS suele ser una instrucción solo del explorador.</span><span class="sxs-lookup"><span data-stu-id="2570a-118">The default API projects don't include [HSTS](#hsts) because HSTS is generally a browser only instruction.</span></span> <span data-ttu-id="2570a-119">Otros llamadores, como las aplicaciones de escritorio o de teléfono, **no** obedecen a la instrucción.</span><span class="sxs-lookup"><span data-stu-id="2570a-119">Other callers, such as phone or desktop apps, do **not** obey the instruction.</span></span> <span data-ttu-id="2570a-120">Incluso dentro de los exploradores, una única llamada autenticada a una API sobre HTTP tiene riesgos en las redes no seguras.</span><span class="sxs-lookup"><span data-stu-id="2570a-120">Even within browsers, a single authenticated call to an API over HTTP has risks on insecure networks.</span></span> <span data-ttu-id="2570a-121">El enfoque seguro consiste en configurar proyectos de API para que solo escuchen y respondan a través de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2570a-121">The secure approach is to configure API projects to only listen to and respond over HTTPS.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="2570a-122">Proyectos de API</span><span class="sxs-lookup"><span data-stu-id="2570a-122">API projects</span></span>
>
> <span data-ttu-id="2570a-123">**No** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) en las API Web que reciben información confidencial.</span><span class="sxs-lookup"><span data-stu-id="2570a-123">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="2570a-124">`RequireHttpsAttribute` usa códigos de Estado HTTP para redirigir exploradores de HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2570a-124">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="2570a-125">Es posible que los clientes de API no conozcan o cumplan las redirecciones de HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2570a-125">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="2570a-126">Estos clientes pueden enviar información a través de HTTP.</span><span class="sxs-lookup"><span data-stu-id="2570a-126">Such clients may send information over HTTP.</span></span> <span data-ttu-id="2570a-127">Las API Web deben:</span><span class="sxs-lookup"><span data-stu-id="2570a-127">Web APIs should either:</span></span>
>
> * <span data-ttu-id="2570a-128">No escuche en HTTP.</span><span class="sxs-lookup"><span data-stu-id="2570a-128">Not listen on HTTP.</span></span>
> * <span data-ttu-id="2570a-129">Cierre la conexión con el código de estado 400 (solicitud incorrecta) y no atienda la solicitud.</span><span class="sxs-lookup"><span data-stu-id="2570a-129">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>

::: moniker-end

## <a name="require-https"></a><span data-ttu-id="2570a-130">Requisito de HTTPS</span><span class="sxs-lookup"><span data-stu-id="2570a-130">Require HTTPS</span></span>

<span data-ttu-id="2570a-131">Se recomienda que las aplicaciones Web de producción ASP.NET Core usen:</span><span class="sxs-lookup"><span data-stu-id="2570a-131">We recommend that production ASP.NET Core web apps use:</span></span>

* <span data-ttu-id="2570a-132">Middleware de redirección de HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) para redirigir las solicitudes HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2570a-132">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="2570a-133">Middleware de HSTS ([UseHsts](#http-strict-transport-security-protocol-hsts)) para enviar encabezados del Protocolo de seguridad de transporte estricto (HSTS) http a los clientes.</span><span class="sxs-lookup"><span data-stu-id="2570a-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="2570a-134">Las aplicaciones implementadas en una configuración de proxy inverso permiten que el proxy controle la seguridad de conexión (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2570a-134">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="2570a-135">Si el proxy también controla el redireccionamiento de HTTPS, no es necesario usar el middleware de redirección de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2570a-135">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="2570a-136">Si el servidor proxy también controla la escritura de encabezados HSTS (por ejemplo, la [compatibilidad con HSTS nativa en IIS 10,0 (1709) o posterior](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), la aplicación no requiere el middleware HSTS.</span><span class="sxs-lookup"><span data-stu-id="2570a-136">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="2570a-137">Para obtener más información, consulte no [participar en https/HSTS en la creación de proyectos](#opt-out-of-httpshsts-on-project-creation).</span><span class="sxs-lookup"><span data-stu-id="2570a-137">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="2570a-138">UseHttpsRedirection</span><span class="sxs-lookup"><span data-stu-id="2570a-138">UseHttpsRedirection</span></span>

<span data-ttu-id="2570a-139">En el código siguiente se llama a `UseHttpsRedirection` en la clase `Startup`:</span><span class="sxs-lookup"><span data-stu-id="2570a-139">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

<span data-ttu-id="2570a-140">Código resaltado anterior:</span><span class="sxs-lookup"><span data-stu-id="2570a-140">The preceding highlighted code:</span></span>

* <span data-ttu-id="2570a-141">Usa el valor predeterminado [HttpsRedirectionOptions. RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span><span class="sxs-lookup"><span data-stu-id="2570a-141">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="2570a-142">Usa el valor predeterminado de [HttpsRedirectionOptions. HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (NULL) a menos que sea invalidado por la variable de entorno `ASPNETCORE_HTTPS_PORT` o [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span><span class="sxs-lookup"><span data-stu-id="2570a-142">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="2570a-143">Se recomienda el uso de redirecciones temporales en lugar de redireccionamientos permanentes.</span><span class="sxs-lookup"><span data-stu-id="2570a-143">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="2570a-144">El almacenamiento en caché de vínculos puede producir un comportamiento inestable en entornos de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="2570a-144">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="2570a-145">Si prefiere enviar un código de estado de redirección permanente cuando la aplicación se encuentra en un entorno que no es de desarrollo, consulte la sección [configuración de redirecciones permanentes en producción](#configure-permanent-redirects-in-production) .</span><span class="sxs-lookup"><span data-stu-id="2570a-145">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="2570a-146">Se recomienda usar [HSTS](#http-strict-transport-security-protocol-hsts) para indicar a los clientes que solo se deben enviar solicitudes de recursos seguros a la aplicación (solo en producción).</span><span class="sxs-lookup"><span data-stu-id="2570a-146">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="2570a-147">Configuración de Puerto</span><span class="sxs-lookup"><span data-stu-id="2570a-147">Port configuration</span></span>

<span data-ttu-id="2570a-148">Un puerto debe estar disponible para que el middleware redirija una solicitud no segura a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2570a-148">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="2570a-149">Si no hay ningún puerto disponible:</span><span class="sxs-lookup"><span data-stu-id="2570a-149">If no port is available:</span></span>

* <span data-ttu-id="2570a-150">No se produce la redirección a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2570a-150">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="2570a-151">El middleware registra la advertencia "no se pudo determinar el puerto https para la redirección".</span><span class="sxs-lookup"><span data-stu-id="2570a-151">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="2570a-152">Especifique el Puerto HTTPS mediante cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="2570a-152">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="2570a-153">Establezca [HttpsRedirectionOptions. HttpsPort](#options).</span><span class="sxs-lookup"><span data-stu-id="2570a-153">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="2570a-154">Establezca la [configuración de host](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port)de `https_port`:</span><span class="sxs-lookup"><span data-stu-id="2570a-154">Set the `https_port` [host setting](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port):</span></span>

  * <span data-ttu-id="2570a-155">En configuración de host.</span><span class="sxs-lookup"><span data-stu-id="2570a-155">In host configuration.</span></span>
  * <span data-ttu-id="2570a-156">Estableciendo el `ASPNETCORE_HTTPS_PORT` variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="2570a-156">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="2570a-157">Agregando una entrada de nivel superior en *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="2570a-157">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* <span data-ttu-id="2570a-158">Indique un puerto con el esquema seguro mediante la [variable de entorno ASPNETCORE_URLS](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls).</span><span class="sxs-lookup"><span data-stu-id="2570a-158">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls).</span></span> <span data-ttu-id="2570a-159">La variable de entorno configura el servidor.</span><span class="sxs-lookup"><span data-stu-id="2570a-159">The environment variable configures the server.</span></span> <span data-ttu-id="2570a-160">El middleware detecta indirectamente el Puerto HTTPS a través de <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="2570a-160">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="2570a-161">Este enfoque no funciona en las implementaciones de proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="2570a-161">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* <span data-ttu-id="2570a-162">Establezca la [configuración de host](xref:fundamentals/host/web-host#https-port)de `https_port`:</span><span class="sxs-lookup"><span data-stu-id="2570a-162">Set the `https_port` [host setting](xref:fundamentals/host/web-host#https-port):</span></span>

  * <span data-ttu-id="2570a-163">En configuración de host.</span><span class="sxs-lookup"><span data-stu-id="2570a-163">In host configuration.</span></span>
  * <span data-ttu-id="2570a-164">Estableciendo el `ASPNETCORE_HTTPS_PORT` variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="2570a-164">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="2570a-165">Agregando una entrada de nivel superior en *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="2570a-165">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* <span data-ttu-id="2570a-166">Indique un puerto con el esquema seguro mediante la [variable de entorno ASPNETCORE_URLS](xref:fundamentals/host/web-host#server-urls).</span><span class="sxs-lookup"><span data-stu-id="2570a-166">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](xref:fundamentals/host/web-host#server-urls).</span></span> <span data-ttu-id="2570a-167">La variable de entorno configura el servidor.</span><span class="sxs-lookup"><span data-stu-id="2570a-167">The environment variable configures the server.</span></span> <span data-ttu-id="2570a-168">El middleware detecta indirectamente el Puerto HTTPS a través de <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="2570a-168">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="2570a-169">Este enfoque no funciona en las implementaciones de proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="2570a-169">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

* <span data-ttu-id="2570a-170">En desarrollo, establezca una dirección URL HTTPS en *launchsettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="2570a-170">In development, set an HTTPS URL in *launchsettings.json*.</span></span> <span data-ttu-id="2570a-171">Habilite HTTPS cuando se use IIS Express.</span><span class="sxs-lookup"><span data-stu-id="2570a-171">Enable HTTPS when IIS Express is used.</span></span>

* <span data-ttu-id="2570a-172">Configure un punto de conexión de dirección URL HTTPS para una implementación perimetral de acceso público del servidor [Kestrel](xref:fundamentals/servers/kestrel) o [http. sys](xref:fundamentals/servers/httpsys) .</span><span class="sxs-lookup"><span data-stu-id="2570a-172">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) server or [HTTP.sys](xref:fundamentals/servers/httpsys) server.</span></span> <span data-ttu-id="2570a-173">La aplicación solo usa **un puerto https** .</span><span class="sxs-lookup"><span data-stu-id="2570a-173">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="2570a-174">El middleware detecta el puerto a través de <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="2570a-174">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="2570a-175">Cuando una aplicación se ejecuta en una configuración de proxy inverso, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> no está disponible.</span><span class="sxs-lookup"><span data-stu-id="2570a-175">When an app is run in a reverse proxy configuration, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> isn't available.</span></span> <span data-ttu-id="2570a-176">Establezca el puerto con uno de los otros enfoques descritos en esta sección.</span><span class="sxs-lookup"><span data-stu-id="2570a-176">Set the port using one of the other approaches described in this section.</span></span>

### <a name="edge-deployments"></a><span data-ttu-id="2570a-177">Implementaciones perimetrales</span><span class="sxs-lookup"><span data-stu-id="2570a-177">Edge deployments</span></span> 

<span data-ttu-id="2570a-178">Cuando Kestrel o HTTP. sys se usa como un servidor perimetral de acceso público, se debe configurar Kestrel o HTTP. sys para que escuche en ambos:</span><span class="sxs-lookup"><span data-stu-id="2570a-178">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="2570a-179">El puerto seguro al que se redirige el cliente (normalmente, 443 en producción y 5001 en desarrollo).</span><span class="sxs-lookup"><span data-stu-id="2570a-179">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="2570a-180">El puerto no seguro (normalmente, 80 en producción y 5000 en desarrollo).</span><span class="sxs-lookup"><span data-stu-id="2570a-180">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="2570a-181">El cliente debe tener acceso al puerto inseguro para que la aplicación reciba una solicitud no segura y redirija el cliente al puerto seguro.</span><span class="sxs-lookup"><span data-stu-id="2570a-181">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="2570a-182">Para obtener más información, consulte [configuración del punto de conexión de Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) o <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="2570a-182">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="2570a-183">Escenarios de implementación</span><span class="sxs-lookup"><span data-stu-id="2570a-183">Deployment scenarios</span></span>

<span data-ttu-id="2570a-184">Cualquier firewall entre el cliente y el servidor también debe tener puertos de comunicación abiertos para el tráfico.</span><span class="sxs-lookup"><span data-stu-id="2570a-184">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="2570a-185">Si las solicitudes se reenvían en una configuración de proxy inverso, use el [middleware de encabezados reenviados](xref:host-and-deploy/proxy-load-balancer) antes de llamar al middleware de redireccionamiento de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2570a-185">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="2570a-186">El middleware de encabezados reenviados actualiza el `Request.Scheme`, mediante el encabezado de `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="2570a-186">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="2570a-187">El middleware permite que los URI de redirección y otras directivas de seguridad funcionen correctamente.</span><span class="sxs-lookup"><span data-stu-id="2570a-187">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="2570a-188">Cuando no se usa middleware de encabezados reenviados, es posible que la aplicación de back-end no reciba el esquema correcto y acabe en un bucle de redirección.</span><span class="sxs-lookup"><span data-stu-id="2570a-188">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="2570a-189">Un mensaje de error común del usuario final es que se han producido demasiadas redirecciones.</span><span class="sxs-lookup"><span data-stu-id="2570a-189">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="2570a-190">Al implementar en Azure App Service, siga las instrucciones de [Tutorial: enlazar un certificado SSL personalizado existente a Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="2570a-190">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="2570a-191">Opciones</span><span class="sxs-lookup"><span data-stu-id="2570a-191">Options</span></span>

<span data-ttu-id="2570a-192">El siguiente código resaltado llama a [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) para configurar las opciones de middleware:</span><span class="sxs-lookup"><span data-stu-id="2570a-192">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


<span data-ttu-id="2570a-193">Solo es necesario llamar a `AddHttpsRedirection` para cambiar los valores de `HttpsPort` o `RedirectStatusCode`.</span><span class="sxs-lookup"><span data-stu-id="2570a-193">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="2570a-194">Código resaltado anterior:</span><span class="sxs-lookup"><span data-stu-id="2570a-194">The preceding highlighted code:</span></span>

* <span data-ttu-id="2570a-195">Establece [HttpsRedirectionOptions. RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) en <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, que es el valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="2570a-195">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="2570a-196">Utilice los campos de la clase <xref:Microsoft.AspNetCore.Http.StatusCodes> para las asignaciones a `RedirectStatusCode`.</span><span class="sxs-lookup"><span data-stu-id="2570a-196">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="2570a-197">Establece el Puerto HTTPS en 5001.</span><span class="sxs-lookup"><span data-stu-id="2570a-197">Sets the HTTPS port to 5001.</span></span> <span data-ttu-id="2570a-198">El valor predeterminado es 443.</span><span class="sxs-lookup"><span data-stu-id="2570a-198">The default value is 443.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="2570a-199">Configuración de redirecciones permanentes en producción</span><span class="sxs-lookup"><span data-stu-id="2570a-199">Configure permanent redirects in production</span></span>

<span data-ttu-id="2570a-200">De forma predeterminada, el middleware envía un [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) con todos los redireccionamientos.</span><span class="sxs-lookup"><span data-stu-id="2570a-200">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="2570a-201">Si prefiere enviar un código de estado de redirección permanente cuando la aplicación se encuentra en un entorno que no es de desarrollo, ajuste la configuración de las opciones de middleware en una comprobación condicional para un entorno que no sea de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="2570a-201">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2570a-202">Al configurar servicios en *Startup.CS*:</span><span class="sxs-lookup"><span data-stu-id="2570a-202">When configuring services in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="2570a-203">Al configurar servicios en *Startup.CS*:</span><span class="sxs-lookup"><span data-stu-id="2570a-203">When configuring services in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="2570a-204">Enfoque alternativo de middleware de redirección de HTTPS</span><span class="sxs-lookup"><span data-stu-id="2570a-204">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="2570a-205">Una alternativa al uso del middleware de redirección de HTTPS (`UseHttpsRedirection`) es usar el middleware de reescritura de direcciones URL (`AddRedirectToHttps`).</span><span class="sxs-lookup"><span data-stu-id="2570a-205">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="2570a-206">`AddRedirectToHttps` también puede establecer el código de estado y el puerto cuando se ejecuta la redirección.</span><span class="sxs-lookup"><span data-stu-id="2570a-206">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="2570a-207">Para obtener más información, vea [middleware de reescritura de direcciones URL](xref:fundamentals/url-rewriting).</span><span class="sxs-lookup"><span data-stu-id="2570a-207">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="2570a-208">Al redirigir a HTTPS sin necesidad de reglas de redirección adicionales, se recomienda usar el middleware de redireccionamiento de HTTPS (`UseHttpsRedirection`) que se describe en este tema.</span><span class="sxs-lookup"><span data-stu-id="2570a-208">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="2570a-209">Protocolo de seguridad de transporte estricto HTTP (HSTS)</span><span class="sxs-lookup"><span data-stu-id="2570a-209">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="2570a-210">Por [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), la [seguridad de transporte estricta http (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) es una mejora de seguridad opcional que se especifica mediante una aplicación Web mediante el uso de un encabezado de respuesta.</span><span class="sxs-lookup"><span data-stu-id="2570a-210">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="2570a-211">Cuando un [explorador que admite HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) recibe este encabezado:</span><span class="sxs-lookup"><span data-stu-id="2570a-211">When a [browser that supports HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) receives this header:</span></span>

* <span data-ttu-id="2570a-212">El explorador almacena la configuración del dominio que evita el envío de cualquier comunicación a través de HTTP.</span><span class="sxs-lookup"><span data-stu-id="2570a-212">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="2570a-213">El explorador fuerza toda la comunicación a través de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2570a-213">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="2570a-214">El explorador impide que el usuario Use certificados no confiables o no válidos.</span><span class="sxs-lookup"><span data-stu-id="2570a-214">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="2570a-215">El explorador deshabilita los mensajes que permiten a un usuario confiar temporalmente en este tipo de certificado.</span><span class="sxs-lookup"><span data-stu-id="2570a-215">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="2570a-216">Dado que el cliente exige HSTS, tiene algunas limitaciones:</span><span class="sxs-lookup"><span data-stu-id="2570a-216">Because HSTS is enforced by the client it has some limitations:</span></span>

* <span data-ttu-id="2570a-217">El cliente debe admitir HSTS.</span><span class="sxs-lookup"><span data-stu-id="2570a-217">The client must support HSTS.</span></span>
* <span data-ttu-id="2570a-218">HSTS requiere al menos una solicitud HTTPS correcta para establecer la Directiva HSTS.</span><span class="sxs-lookup"><span data-stu-id="2570a-218">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="2570a-219">La aplicación debe comprobar cada solicitud HTTP y redirigir o rechazar la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="2570a-219">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="2570a-220">ASP.NET Core 2,1 y versiones posteriores implementan HSTS con el método de extensión `UseHsts`.</span><span class="sxs-lookup"><span data-stu-id="2570a-220">ASP.NET Core 2.1 and later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="2570a-221">El código siguiente llama a `UseHsts` cuando la aplicación no está en [modo de desarrollo](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="2570a-221">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

<span data-ttu-id="2570a-222">`UseHsts` no se recomienda en el desarrollo porque los exploradores pueden almacenar en memoria caché los valores de HSTS.</span><span class="sxs-lookup"><span data-stu-id="2570a-222">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="2570a-223">De forma predeterminada, `UseHsts` excluye la dirección de bucle invertido local.</span><span class="sxs-lookup"><span data-stu-id="2570a-223">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="2570a-224">En el caso de los entornos de producción que implementan HTTPS por primera vez, establezca [HstsOptions. MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) inicial en un valor pequeño utilizando uno de los métodos de <xref:System.TimeSpan>.</span><span class="sxs-lookup"><span data-stu-id="2570a-224">For production environments that are implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="2570a-225">Establezca el valor de horas en no más de un día único en caso de que necesite revertir la infraestructura HTTPS a HTTP.</span><span class="sxs-lookup"><span data-stu-id="2570a-225">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="2570a-226">Después de estar seguro de la sostenibilidad de la configuración de HTTPS, aumente el valor de HSTS Max-Age. un valor utilizado comúnmente es un año.</span><span class="sxs-lookup"><span data-stu-id="2570a-226">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS max-age value; a commonly used value is one year.</span></span>

<span data-ttu-id="2570a-227">El código siguiente:</span><span class="sxs-lookup"><span data-stu-id="2570a-227">The following code:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* <span data-ttu-id="2570a-228">Establece el parámetro preload del encabezado STRICT-Transport-Security.</span><span class="sxs-lookup"><span data-stu-id="2570a-228">Sets the preload parameter of the Strict-Transport-Security header.</span></span> <span data-ttu-id="2570a-229">La precarga no forma parte de la [especificación RFC HSTS](https://tools.ietf.org/html/rfc6797), pero es compatible con los exploradores Web para precargar sitios de HSTS en la instalación nueva.</span><span class="sxs-lookup"><span data-stu-id="2570a-229">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="2570a-230">Para más información, vea [https://hstspreload.org/](https://hstspreload.org/).</span><span class="sxs-lookup"><span data-stu-id="2570a-230">See [https://hstspreload.org/](https://hstspreload.org/) for more information.</span></span>
* <span data-ttu-id="2570a-231">Habilita [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), que aplica la Directiva HSTS para hospedar subdominios.</span><span class="sxs-lookup"><span data-stu-id="2570a-231">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="2570a-232">Establece explícitamente el parámetro Max-Age del encabezado STRICT-Transport-Security en 60 días.</span><span class="sxs-lookup"><span data-stu-id="2570a-232">Explicitly sets the max-age parameter of the Strict-Transport-Security header to 60 days.</span></span> <span data-ttu-id="2570a-233">Si no se establece, el valor predeterminado es 30 días.</span><span class="sxs-lookup"><span data-stu-id="2570a-233">If not set, defaults to 30 days.</span></span> <span data-ttu-id="2570a-234">Para obtener más información, vea la [Directiva Max-Age](https://tools.ietf.org/html/rfc6797#section-6.1.1) .</span><span class="sxs-lookup"><span data-stu-id="2570a-234">See the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1) for more information.</span></span>
* <span data-ttu-id="2570a-235">Agrega `example.com` a la lista de hosts que se van a excluir.</span><span class="sxs-lookup"><span data-stu-id="2570a-235">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="2570a-236">`UseHsts` excluye los siguientes hosts de bucle invertido:</span><span class="sxs-lookup"><span data-stu-id="2570a-236">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="2570a-237">`localhost`: la dirección de bucle invertido IPv4.</span><span class="sxs-lookup"><span data-stu-id="2570a-237">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="2570a-238">`127.0.0.1`: la dirección de bucle invertido IPv4.</span><span class="sxs-lookup"><span data-stu-id="2570a-238">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="2570a-239">`[::1]`: dirección de bucle invertido IPv6.</span><span class="sxs-lookup"><span data-stu-id="2570a-239">`[::1]` : The IPv6 loopback address.</span></span>

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="2570a-240">No participar en HTTPS/HSTS en la creación de proyectos</span><span class="sxs-lookup"><span data-stu-id="2570a-240">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="2570a-241">En algunos escenarios de servicio de back-end en los que la seguridad de la conexión se controla en el perímetro de acceso público de la red, no es necesario configurar la seguridad de conexión en cada nodo.</span><span class="sxs-lookup"><span data-stu-id="2570a-241">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="2570a-242">Las aplicaciones web que se generan a partir de las plantillas en Visual Studio o desde el comando [dotnet New](/dotnet/core/tools/dotnet-new) habilitan la [redirección de https](#require-https) y [HSTS](#http-strict-transport-security-protocol-hsts).</span><span class="sxs-lookup"><span data-stu-id="2570a-242">Web apps that are generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="2570a-243">En el caso de las implementaciones que no requieren estos escenarios, puede optar por HTTPS/HSTS cuando la aplicación se crea a partir de la plantilla.</span><span class="sxs-lookup"><span data-stu-id="2570a-243">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="2570a-244">Para no participar en HTTPS/HSTS:</span><span class="sxs-lookup"><span data-stu-id="2570a-244">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2570a-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2570a-245">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="2570a-246">Desactive la casilla **configurar para https** .</span><span class="sxs-lookup"><span data-stu-id="2570a-246">Uncheck the **Configure for HTTPS** check box.</span></span>

::: moniker range=">= aspnetcore-3.0"

![Cuadro de diálogo Nueva ASP.NET Core aplicación web que muestra la casilla de verificación configurar para HTTPS no seleccionada.](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![Cuadro de diálogo Nueva ASP.NET Core aplicación web que muestra la casilla de verificación configurar para HTTPS no seleccionada.](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2570a-249">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="2570a-249">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="2570a-250">Use la opción `--no-https`.</span><span class="sxs-lookup"><span data-stu-id="2570a-250">Use the `--no-https` option.</span></span> <span data-ttu-id="2570a-251">Por ejemplo</span><span class="sxs-lookup"><span data-stu-id="2570a-251">For example</span></span>

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="2570a-252">Confíe en el certificado de desarrollo de ASP.NET Core HTTPS en Windows y macOS</span><span class="sxs-lookup"><span data-stu-id="2570a-252">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="2570a-253">El SDK de .NET Core incluye un certificado de desarrollo de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2570a-253">The .NET Core SDK includes an HTTPS development certificate.</span></span> <span data-ttu-id="2570a-254">El certificado se instala como parte de la experiencia de primera ejecución.</span><span class="sxs-lookup"><span data-stu-id="2570a-254">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="2570a-255">Por ejemplo, `dotnet --info` produce una salida similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="2570a-255">For example, `dotnet --info` produces output similar to the following:</span></span>

```text
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="2570a-256">Al instalar el SDK de .NET Core se instala el certificado de desarrollo HTTPS de ASP.NET Core en el almacén de certificados local del usuario.</span><span class="sxs-lookup"><span data-stu-id="2570a-256">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="2570a-257">El certificado se ha instalado, pero no es de confianza.</span><span class="sxs-lookup"><span data-stu-id="2570a-257">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="2570a-258">Para confiar en el certificado, realice el paso de una vez para ejecutar la herramienta dotnet `dev-certs`:</span><span class="sxs-lookup"><span data-stu-id="2570a-258">To trust the certificate perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="2570a-259">El siguiente comando proporciona ayuda sobre la herramienta `dev-certs`:</span><span class="sxs-lookup"><span data-stu-id="2570a-259">The following command provides help on the `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="2570a-260">Cómo configurar un certificado de desarrollador para Docker</span><span class="sxs-lookup"><span data-stu-id="2570a-260">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="2570a-261">Consulte [este problema de GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/6199).</span><span class="sxs-lookup"><span data-stu-id="2570a-261">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6199).</span></span>

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a><span data-ttu-id="2570a-262">Confiar en el certificado HTTPS del subsistema de Windows para Linux</span><span class="sxs-lookup"><span data-stu-id="2570a-262">Trust HTTPS certificate from Windows Subsystem for Linux</span></span>

<span data-ttu-id="2570a-263">El subsistema de Windows para Linux (WSL) genera un certificado autofirmado HTTPS. Para configurar el almacén de certificados de Windows para que confíe en el certificado WSL:</span><span class="sxs-lookup"><span data-stu-id="2570a-263">The Windows Subsystem for Linux (WSL) generates a HTTPS self-signed cert. To configure the Windows certificate store to trust the WSL certificate:</span></span>

* <span data-ttu-id="2570a-264">Ejecute el siguiente comando para exportar el certificado generado por WSL: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span><span class="sxs-lookup"><span data-stu-id="2570a-264">Run the following command to export the WSL generated certificate: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span></span>
* <span data-ttu-id="2570a-265">En una ventana de WSL, ejecute el siguiente comando: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span><span class="sxs-lookup"><span data-stu-id="2570a-265">In a WSL window, run the following command: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span></span>

  <span data-ttu-id="2570a-266">El comando anterior establece las variables de entorno, por lo que Linux usa el certificado de confianza de Windows.</span><span class="sxs-lookup"><span data-stu-id="2570a-266">The preceding command sets the environment variables so Linux uses the Windows trusted certificate.</span></span>

## <a name="troubleshoot-certificate-problems"></a><span data-ttu-id="2570a-267">Solucionar problemas de certificados</span><span class="sxs-lookup"><span data-stu-id="2570a-267">Troubleshoot certificate problems</span></span>

<span data-ttu-id="2570a-268">En esta sección se proporciona ayuda cuando se ha [instalado y se confía](#trust)en el certificado de desarrollo de ASP.net Core https, pero sigue teniendo en cuenta las advertencias del explorador de que el certificado no es de confianza.</span><span class="sxs-lookup"><span data-stu-id="2570a-268">This section provides help when the ASP.NET Core HTTPS development certificate has been [installed and trusted](#trust), but you still have browser warnings that the certificate is not trusted.</span></span> <span data-ttu-id="2570a-269">[Kestrel](xref:fundamentals/servers/kestrel)usa el certificado de desarrollo de https ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="2570a-269">The ASP.NET Core HTTPS development certificate is used by [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

### <a name="all-platforms---certificate-not-trusted"></a><span data-ttu-id="2570a-270">Todas las plataformas: el certificado no es de confianza</span><span class="sxs-lookup"><span data-stu-id="2570a-270">All platforms - certificate not trusted</span></span>

<span data-ttu-id="2570a-271">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="2570a-271">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="2570a-272">Cierre todas las instancias del explorador abiertas.</span><span class="sxs-lookup"><span data-stu-id="2570a-272">Close any browser instances open.</span></span> <span data-ttu-id="2570a-273">Abra una nueva ventana del explorador en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2570a-273">Open a new browser window to app.</span></span> <span data-ttu-id="2570a-274">Los exploradores almacenan en caché la confianza de certificados.</span><span class="sxs-lookup"><span data-stu-id="2570a-274">Certificate trust is cached by browsers.</span></span>

<span data-ttu-id="2570a-275">Los comandos anteriores solucionan la mayoría de los problemas de confianza del explorador.</span><span class="sxs-lookup"><span data-stu-id="2570a-275">The preceding commands solve most browser trust issues.</span></span> <span data-ttu-id="2570a-276">Si el explorador sigue sin confiar en el certificado, siga las sugerencias específicas de la plataforma que se indican a continuación.</span><span class="sxs-lookup"><span data-stu-id="2570a-276">If the browser is still not trusting the certificate, follow the platform specific suggestions that follow.</span></span>

### <a name="docker---certificate-not-trusted"></a><span data-ttu-id="2570a-277">Docker: certificado no confiable</span><span class="sxs-lookup"><span data-stu-id="2570a-277">Docker - certificate not trusted</span></span>

* <span data-ttu-id="2570a-278">Elimine la carpeta *C:\Users\{usuario} \AppData\Roaming\ASP.NET\Https*</span><span class="sxs-lookup"><span data-stu-id="2570a-278">Delete the *C:\Users\{USER}\AppData\Roaming\ASP.NET\Https* folder.</span></span>
* <span data-ttu-id="2570a-279">Limpie la solución.</span><span class="sxs-lookup"><span data-stu-id="2570a-279">Clean the solution.</span></span> <span data-ttu-id="2570a-280">Elimine las carpetas *bin* y *obj*.</span><span class="sxs-lookup"><span data-stu-id="2570a-280">Delete the *bin* and *obj* folders.</span></span>
* <span data-ttu-id="2570a-281">Reinicie la herramienta de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="2570a-281">Restart the development tool.</span></span> <span data-ttu-id="2570a-282">Por ejemplo, Visual Studio, Visual Studio Code o Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="2570a-282">For example, Visual Studio, Visual Studio Code, or Visual Studio for Mac.</span></span>

### <a name="windows---certificate-not-trusted"></a><span data-ttu-id="2570a-283">Windows-certificado no confiable</span><span class="sxs-lookup"><span data-stu-id="2570a-283">Windows - certificate not trusted</span></span>

* <span data-ttu-id="2570a-284">Compruebe los certificados en el almacén de certificados.</span><span class="sxs-lookup"><span data-stu-id="2570a-284">Check the certificates in the certificate store.</span></span> <span data-ttu-id="2570a-285">Debe haber un certificado de `localhost` con el `ASP.NET Core HTTPS development certificate` nombre descriptivo en `Current User > Personal > Certificates` y `Current User > Trusted root certification authorities > Certificates`</span><span class="sxs-lookup"><span data-stu-id="2570a-285">There should be a `localhost` certificate with the `ASP.NET Core HTTPS development certificate` friendly name both under `Current User > Personal > Certificates` and `Current User > Trusted root certification authorities > Certificates`</span></span>
* <span data-ttu-id="2570a-286">Quite todos los certificados encontrados de las entidades de certificación personales y de confianza.</span><span class="sxs-lookup"><span data-stu-id="2570a-286">Remove all the found certificates from both Personal and Trusted root certification authorities.</span></span> <span data-ttu-id="2570a-287">**No** Quite el certificado de host local IIS Express.</span><span class="sxs-lookup"><span data-stu-id="2570a-287">Do **not** remove the IIS Express localhost certificate.</span></span>
* <span data-ttu-id="2570a-288">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="2570a-288">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="2570a-289">Cierre todas las instancias del explorador abiertas.</span><span class="sxs-lookup"><span data-stu-id="2570a-289">Close any browser instances open.</span></span> <span data-ttu-id="2570a-290">Abra una nueva ventana del explorador en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2570a-290">Open a new browser window to app.</span></span>

### <a name="os-x---certificate-not-trusted"></a><span data-ttu-id="2570a-291">OS X: certificado no confiable</span><span class="sxs-lookup"><span data-stu-id="2570a-291">OS X - certificate not trusted</span></span>

* <span data-ttu-id="2570a-292">Abra el acceso a llaves.</span><span class="sxs-lookup"><span data-stu-id="2570a-292">Open KeyChain Access.</span></span>
* <span data-ttu-id="2570a-293">Seleccione la cadena de claves del sistema.</span><span class="sxs-lookup"><span data-stu-id="2570a-293">Select the System keychain.</span></span>
* <span data-ttu-id="2570a-294">Compruebe la presencia de un certificado localhost.</span><span class="sxs-lookup"><span data-stu-id="2570a-294">Check for the presence of a localhost certificate.</span></span>
* <span data-ttu-id="2570a-295">Compruebe que contiene un símbolo de `+` en el icono para indicar su confianza para todos los usuarios.</span><span class="sxs-lookup"><span data-stu-id="2570a-295">Check that it contains a `+` symbol on the icon to indicate its trusted for all users.</span></span>
* <span data-ttu-id="2570a-296">Quite el certificado de la cadena de claves del sistema.</span><span class="sxs-lookup"><span data-stu-id="2570a-296">Remove the certificate from the system keychain.</span></span>
* <span data-ttu-id="2570a-297">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="2570a-297">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="2570a-298">Cierre todas las instancias del explorador abiertas.</span><span class="sxs-lookup"><span data-stu-id="2570a-298">Close any browser instances open.</span></span> <span data-ttu-id="2570a-299">Abra una nueva ventana del explorador en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2570a-299">Open a new browser window to app.</span></span>

<span data-ttu-id="2570a-300">Vea [error de https mediante IIS Express (ASPNET/AspNetCore #16892)](https://github.com/aspnet/AspNetCore/issues/16892) para solucionar problemas de certificados con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2570a-300">See [HTTPS Error using IIS Express (aspnet/AspNetCore #16892)](https://github.com/aspnet/AspNetCore/issues/16892) for troubleshooting certificate issues with Visual Studio.</span></span>

### <a name="iis-express-ssl-certificate-used-with-visual-studio"></a><span data-ttu-id="2570a-301">IIS Express certificado SSL usado con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2570a-301">IIS Express SSL certificate used with Visual Studio</span></span>

<span data-ttu-id="2570a-302">Para solucionar problemas con el certificado de IIS Express, seleccione **reparar** en el instalador de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2570a-302">To fix problems with the IIS Express certificate, select **Repair** from the Visual Studio installer.</span></span>

## <a name="additional-information"></a><span data-ttu-id="2570a-303">Información adicional</span><span class="sxs-lookup"><span data-stu-id="2570a-303">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="2570a-304">Host ASP.NET Core en Linux con la configuración de Apache: HTTPS</span><span class="sxs-lookup"><span data-stu-id="2570a-304">Host ASP.NET Core on Linux with Apache: HTTPS configuration</span></span>](xref:host-and-deploy/linux-apache#https-configuration)
* [<span data-ttu-id="2570a-305">Host ASP.NET Core en Linux con NGINX: configuración de HTTPS</span><span class="sxs-lookup"><span data-stu-id="2570a-305">Host ASP.NET Core on Linux with Nginx: HTTPS configuration</span></span>](xref:host-and-deploy/linux-nginx#https-configuration)
* [<span data-ttu-id="2570a-306">Procedimientos para configurar SSL en IIS</span><span class="sxs-lookup"><span data-stu-id="2570a-306">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="2570a-307">Compatibilidad con el explorador de OWASP HSTS</span><span class="sxs-lookup"><span data-stu-id="2570a-307">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
