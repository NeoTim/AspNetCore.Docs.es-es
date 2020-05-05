---
title: ASP.NET Core 2,1 MVC SameSite ejemplo de cookie
author: rick-anderson
description: ASP.NET Core 2,1 MVC SameSite ejemplo de cookie
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/samesite/mvc21
ms.openlocfilehash: 6a53c0d3c0a314c4137f071cf50062182b654658
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777311"
---
# <a name="aspnet-core-21-mvc-samesite-cookie-sample"></a><span data-ttu-id="a1bce-103">ASP.NET Core 2,1 MVC SameSite ejemplo de cookie</span><span class="sxs-lookup"><span data-stu-id="a1bce-103">ASP.NET Core 2.1 MVC SameSite cookie sample</span></span>

<span data-ttu-id="a1bce-104">ASP.NET Core 2,1 tiene compatibilidad integrada con el atributo [SameSite](https://www.owasp.org/index.php/SameSite) , pero se escribió en el estándar original.</span><span class="sxs-lookup"><span data-stu-id="a1bce-104">ASP.NET Core 2.1 has built-in support for the [SameSite](https://www.owasp.org/index.php/SameSite) attribute, but it was written to the original standard.</span></span> <span data-ttu-id="a1bce-105">El [comportamiento revisado](https://github.com/dotnet/aspnetcore/issues/8212) ha cambiado el significado de `SameSite.None` para emitir el atributo sameSite con un valor de `None`, en lugar de no emitir el valor.</span><span class="sxs-lookup"><span data-stu-id="a1bce-105">The [patched behavior](https://github.com/dotnet/aspnetcore/issues/8212) changed the meaning of `SameSite.None` to emit the sameSite attribute with a value of `None`, rather than not emit the value at all.</span></span> <span data-ttu-id="a1bce-106">Si desea no emitir el valor, puede establecer la `SameSite` propiedad de una cookie en-1.</span><span class="sxs-lookup"><span data-stu-id="a1bce-106">If you want to not emit the value you can set the `SameSite` property on a cookie to -1.</span></span>

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a><span data-ttu-id="a1bce-107">Escribir el atributo SameSite</span><span class="sxs-lookup"><span data-stu-id="a1bce-107">Writing the SameSite attribute</span></span>

<span data-ttu-id="a1bce-108">A continuación se describe un ejemplo de cómo escribir un atributo SameSite en una cookie:</span><span class="sxs-lookup"><span data-stu-id="a1bce-108">Following is an example of how to write a SameSite attribute on a cookie:</span></span>

```c#
var cookieOptions = new CookieOptions
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the cookie to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the cookie to the response cookie collection
Response.Cookies.Append(CookieName, "cookieValue", cookieOptions);
```

## <a name="setting-cookie-authentication-and-session-state-cookies"></a><span data-ttu-id="a1bce-109">Establecer cookies de autenticación de cookies y de estado de sesión</span><span class="sxs-lookup"><span data-stu-id="a1bce-109">Setting Cookie Authentication and Session State cookies</span></span>

<span data-ttu-id="a1bce-110">Autenticación de cookies, estado de sesión y [otros componentes](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) establezca sus opciones de sameSite a través de las opciones de cookie, por ejemplo</span><span class="sxs-lookup"><span data-stu-id="a1bce-110">Cookie authentication, session state and [various other components](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) set their sameSite options via Cookie options, for example</span></span>

```c#
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.Cookie.SameSite = SameSiteMode.None;
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        options.Cookie.IsEssential = true;
    });

services.AddSession(options =>
{
    options.Cookie.SameSite = SameSiteMode.None;
    options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
    options.Cookie.IsEssential = true;
});
```

<span data-ttu-id="a1bce-111">En el código anterior, la autenticación de cookies y el estado de sesión establecen `None`el atributo sameSite en, emitiendo `None` el atributo con un valor y establecen también el atributo Secure en true.</span><span class="sxs-lookup"><span data-stu-id="a1bce-111">In the preceding code, both cookie authentication and session state set their sameSite attribute to `None`, emitting the attribute with a `None` value, and also set the Secure attribute to true.</span></span>

### <a name="run-the-sample"></a><span data-ttu-id="a1bce-112">Ejecución del ejemplo</span><span class="sxs-lookup"><span data-stu-id="a1bce-112">Run the sample</span></span>

<span data-ttu-id="a1bce-113">Si ejecuta el [proyecto de ejemplo](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), cargue el depurador del explorador en la página inicial y úselo para ver la colección de cookies para el sitio.</span><span class="sxs-lookup"><span data-stu-id="a1bce-113">If you run the [sample project](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), load your browser debugger on the initial page and use it to view the cookie collection for the site.</span></span> <span data-ttu-id="a1bce-114">Para ello, en Edge y Chrome, `F12` Presione la `Application` tecla TAB y haga clic en la dirección URL `Cookies` del sitio en `Storage` la opción de la sección.</span><span class="sxs-lookup"><span data-stu-id="a1bce-114">To do so in Edge and Chrome press `F12` then select the `Application` tab and click the site URL under the `Cookies` option in the `Storage` section.</span></span>

![Lista de cookies del depurador del explorador](BrowserDebugger.png)

<span data-ttu-id="a1bce-116">Puede ver en la imagen anterior que la cookie creada por el ejemplo al hacer clic en el botón "crear cookie de SameSite" tiene un valor de atributo `Lax`SameSite de, que coincide con el valor establecido en el [código de ejemplo](#sampleCode).</span><span class="sxs-lookup"><span data-stu-id="a1bce-116">You can see from the image above that the cookie created by the sample when you click the "Create SameSite Cookie" button has a SameSite attribute value of `Lax`, matching the value set in the [sample code](#sampleCode).</span></span>

## <a name="intercepting-cookies"></a><a name="interception"></a><span data-ttu-id="a1bce-117">Interceptar cookies</span><span class="sxs-lookup"><span data-stu-id="a1bce-117">Intercepting cookies</span></span>

<span data-ttu-id="a1bce-118">Para interceptar las cookies, para ajustar el valor NONE según su compatibilidad en el agente del explorador del usuario, debe usar `CookiePolicy` el middleware.</span><span class="sxs-lookup"><span data-stu-id="a1bce-118">In order to intercept cookies, to adjust the none value according to its support in the user's browser agent you must use the `CookiePolicy` middleware.</span></span> <span data-ttu-id="a1bce-119">Debe colocarse en la canalización de solicitudes HTTP **antes** que los componentes que escriben cookies y se configuran en `ConfigureServices()`.</span><span class="sxs-lookup"><span data-stu-id="a1bce-119">This must be placed into the http request pipeline **before** any components that write cookies and configured within `ConfigureServices()`.</span></span>

<span data-ttu-id="a1bce-120">Para insertarlo en el uso `app.UseCookiePolicy()` de la canalización en el `Configure(IApplicationBuilder, IHostingEnvironment)` método de [Startup.CS](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span><span class="sxs-lookup"><span data-stu-id="a1bce-120">To insert it into the pipeline use `app.UseCookiePolicy()` in the `Configure(IApplicationBuilder, IHostingEnvironment)` method in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span></span> <span data-ttu-id="a1bce-121">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a1bce-121">For example:</span></span>

```c#
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
       app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

<span data-ttu-id="a1bce-122">Después, en `ConfigureServices(IServiceCollection services)` la Directiva de configuración de cookies para llamar a una clase auxiliar cuando se anexan o se eliminan cookies.</span><span class="sxs-lookup"><span data-stu-id="a1bce-122">Then in the `ConfigureServices(IServiceCollection services)` configure the cookie policy to call out to a helper class when cookies are appended or deleted.</span></span> <span data-ttu-id="a1bce-123">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a1bce-123">For example:</span></span>

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppendCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
        options.OnDeleteCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
    });
}

private void CheckSameSite(HttpContext httpContext, CookieOptions options)
{
    if (options.SameSite == SameSiteMode.None)
    {
        var userAgent = httpContext.Request.Headers["User-Agent"].ToString();
        if (SameSite.BrowserDetection.DisallowsSameSiteNone(userAgent))
        {
            options.SameSite = (SameSiteMode)(-1);
        }
    }
}
```

<span data-ttu-id="a1bce-124">La función `CheckSameSite(HttpContext, CookieOptions)`auxiliar:</span><span class="sxs-lookup"><span data-stu-id="a1bce-124">The helper function `CheckSameSite(HttpContext, CookieOptions)`:</span></span>

* <span data-ttu-id="a1bce-125">Se llama a cuando se anexan cookies a la solicitud o se eliminan de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="a1bce-125">Is called when cookies are appended to the request or deleted from the request.</span></span>
* <span data-ttu-id="a1bce-126">Comprueba si la `SameSite` propiedad está establecida en `None`.</span><span class="sxs-lookup"><span data-stu-id="a1bce-126">Checks to see if the `SameSite` property is set to `None`.</span></span>
* <span data-ttu-id="a1bce-127">Si `SameSite` se establece en `None` y se sabe que el agente de usuario actual no admite el valor del atributo none.</span><span class="sxs-lookup"><span data-stu-id="a1bce-127">If `SameSite` is set to `None` and the current user agent is known to not support the none attribute value.</span></span> <span data-ttu-id="a1bce-128">La comprobación se realiza mediante la clase [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) :</span><span class="sxs-lookup"><span data-stu-id="a1bce-128">The check is done using the [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) class:</span></span>
  * <span data-ttu-id="a1bce-129">Establece `SameSite` para que no emita el valor estableciendo la propiedad en.`(SameSiteMode)(-1)`</span><span class="sxs-lookup"><span data-stu-id="a1bce-129">Sets `SameSite` to not emit the value by setting the property to `(SameSiteMode)(-1)`</span></span>

## <a name="targeting-net-framework"></a><span data-ttu-id="a1bce-130">Destinar .NET Framework</span><span class="sxs-lookup"><span data-stu-id="a1bce-130">Targeting .NET Framework</span></span>

<span data-ttu-id="a1bce-131">ASP.NET Core y System. Web (ASP.NET clásico) tienen implementaciones independientes de SameSite.</span><span class="sxs-lookup"><span data-stu-id="a1bce-131">ASP.NET Core and System.Web (ASP.NET Classic) have independent implementations of SameSite.</span></span> <span data-ttu-id="a1bce-132">Las revisiones de SameSite KB para .NET Framework no son necesarias si se usa ASP.NET Core y el requisito de versión mínima del marco de trabajo de System. Web SameSite (.NET 4.7.2) se aplican a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a1bce-132">The SameSite KB patches for .NET Framework are not required if using ASP.NET Core nor does the System.Web SameSite minimum framework version requirement (.NET 4.7.2) apply to ASP.NET Core.</span></span>

<span data-ttu-id="a1bce-133">ASP.NET Core en .NET requiere la actualización de las dependencias de paquetes Nuget para obtener las correcciones correspondientes.</span><span class="sxs-lookup"><span data-stu-id="a1bce-133">ASP.NET Core on .NET requires updating nuget package dependencies to get the appropriate fixes.</span></span>

<span data-ttu-id="a1bce-134">Para obtener los cambios de ASP.NET Core para .NET Framework Asegúrese de que tiene una referencia directa a las versiones y los paquetes revisados (2.1.14 o versiones posteriores 2,1).</span><span class="sxs-lookup"><span data-stu-id="a1bce-134">To get the ASP.NET Core changes for .NET Framework ensure that you have a direct reference to the patched packages and versions (2.1.14 or later 2.1 versions).</span></span>

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a><span data-ttu-id="a1bce-135">Más información</span><span class="sxs-lookup"><span data-stu-id="a1bce-135">More Information</span></span>
 
<span data-ttu-id="a1bce-136">[Actualizaciones](https://www.chromium.org/updates/same-site)
de Chrome[ASP.net Core documentación](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
de SameSite[ASP.net Core 2,1 SameSite cambio de anuncio](https://github.com/dotnet/aspnetcore/issues/8212)</span><span class="sxs-lookup"><span data-stu-id="a1bce-136">[Chrome Updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite Documentation](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite Change Announcement](https://github.com/dotnet/aspnetcore/issues/8212)</span></span>