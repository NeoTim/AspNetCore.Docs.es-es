---
title: Reglamento de protección de datos generales (RGPD) se admiten en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo obtener acceso a los puntos de extensión de RGPD en una aplicación web ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/29/2018
uid: security/gdpr
ms.openlocfilehash: c5c13dbd1006d10aba0f54b0b9d72b527ee98945
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894112"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="02ee7-103">Soporte técnico del Reglamento General de protección de datos (RGPD) de la UE en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="02ee7-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="02ee7-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="02ee7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="02ee7-105">ASP.NET Core proporciona las API y plantillas para ayudar a cumplir algunos de los [Reglamento General de protección de datos (RGPD) de la UE](https://www.eugdpr.org/) requisitos:</span><span class="sxs-lookup"><span data-stu-id="02ee7-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements:</span></span>

* <span data-ttu-id="02ee7-106">Las plantillas de proyecto incluyen puntos de extensión y auxiliar marcado que se puede reemplazar con la privacidad y la directiva de uso de cookies.</span><span class="sxs-lookup"><span data-stu-id="02ee7-106">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="02ee7-107">Una característica de consentimiento de cookie permite pedir consentimiento (y realizar un seguimiento) de los usuarios para almacenar información personal.</span><span class="sxs-lookup"><span data-stu-id="02ee7-107">A cookie consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="02ee7-108">Si un usuario no ha dado su consentimiento para la recopilación de datos y la aplicación tiene [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) establecido en `true`, las cookies no esenciales no se envían al explorador.</span><span class="sxs-lookup"><span data-stu-id="02ee7-108">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) set to `true`, non-essential cookies aren't sent to the browser.</span></span>
* <span data-ttu-id="02ee7-109">Las cookies se pueden marcar como esenciales.</span><span class="sxs-lookup"><span data-stu-id="02ee7-109">Cookies can be marked as essential.</span></span> <span data-ttu-id="02ee7-110">Esencial cookies se envían al explorador incluso cuando el usuario no ha dado su consentimiento y seguimiento está deshabilitado.</span><span class="sxs-lookup"><span data-stu-id="02ee7-110">Essential cookies are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="02ee7-111">[Las cookies de TempData y Session](#tempdata) no funcionan cuando se deshabilita el seguimiento.</span><span class="sxs-lookup"><span data-stu-id="02ee7-111">[TempData and Session cookies](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="02ee7-112">El [administrar identidades](#pd) página proporciona un vínculo para descargar y eliminar datos de usuario.</span><span class="sxs-lookup"><span data-stu-id="02ee7-112">The [Identity manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="02ee7-113">El [aplicación de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) permite probar la mayoría de los puntos de extensión de RGPD y las API agregadas a las plantillas de ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="02ee7-113">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="02ee7-114">Consulte la [Léame](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) archivo para las pruebas de instrucciones.</span><span class="sxs-lookup"><span data-stu-id="02ee7-114">See the [ReadMe](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="02ee7-115">[Vea o descargue el código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="02ee7-115">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="02ee7-116">Compatibilidad con GDPR de ASP.NET Core en el código de plantilla generada</span><span class="sxs-lookup"><span data-stu-id="02ee7-116">ASP.NET Core GDPR support in template generated code</span></span>

<span data-ttu-id="02ee7-117">Las páginas de Razor y MVC los proyectos creados con las plantillas de proyecto incluyen la compatibilidad de RGPD siguiente:</span><span class="sxs-lookup"><span data-stu-id="02ee7-117">Razor Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="02ee7-118">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) y [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) se establecen en `Startup`.</span><span class="sxs-lookup"><span data-stu-id="02ee7-118">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) are set in `Startup`.</span></span>
* <span data-ttu-id="02ee7-119">The *_CookieConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="02ee7-119">The *_CookieConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span>
* <span data-ttu-id="02ee7-120">El *Pages/Privacy.cshtml* página o *Views/Home/Privacy.cshtml* vista proporciona una página para detallar política de privacidad de su sitio.</span><span class="sxs-lookup"><span data-stu-id="02ee7-120">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="02ee7-121">El *_CookieConsentPartial.cshtml* archivo genera un vínculo a la página de privacidad.</span><span class="sxs-lookup"><span data-stu-id="02ee7-121">The *_CookieConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="02ee7-122">Las aplicaciones creadas con cuentas de usuario individuales, la página de administración proporciona vínculos para descargar y eliminar [personal del usuario](#pd).</span><span class="sxs-lookup"><span data-stu-id="02ee7-122">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a><span data-ttu-id="02ee7-123">CookiePolicyOptions y UseCookiePolicy</span><span class="sxs-lookup"><span data-stu-id="02ee7-123">CookiePolicyOptions and UseCookiePolicy</span></span>

<span data-ttu-id="02ee7-124">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) se inicializan en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="02ee7-124">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="02ee7-125">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) se denomina en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="02ee7-125">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="cookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="02ee7-126">_CookieConsentPartial.cshtml partial view</span><span class="sxs-lookup"><span data-stu-id="02ee7-126">_CookieConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="02ee7-127">El *_CookieConsentPartial.cshtml* vista parcial:</span><span class="sxs-lookup"><span data-stu-id="02ee7-127">The *_CookieConsentPartial.cshtml* partial view:</span></span>

[!code-html[](gdpr/sample/RP/Pages/Shared/_CookieConsentPartial.cshtml)]

<span data-ttu-id="02ee7-128">Este parcial:</span><span class="sxs-lookup"><span data-stu-id="02ee7-128">This partial:</span></span>

* <span data-ttu-id="02ee7-129">Obtiene el estado de seguimiento para el usuario.</span><span class="sxs-lookup"><span data-stu-id="02ee7-129">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="02ee7-130">Si la aplicación está configurada para requerir consentimiento, el usuario debe dar su consentimiento antes de que pueden realizar el seguimiento de las cookies.</span><span class="sxs-lookup"><span data-stu-id="02ee7-130">If the app is configured to require consent, the user must consent before cookies can be tracked.</span></span> <span data-ttu-id="02ee7-131">Si se requiere el consentimiento, el panel de consentimiento de la cookie se fija en la parte superior de la barra de navegación creada por el *_Layout.cshtml* archivo.</span><span class="sxs-lookup"><span data-stu-id="02ee7-131">If consent is required, the cookie consent panel is fixed at top of the navigation bar created by the *_Layout.cshtml* file.</span></span>
* <span data-ttu-id="02ee7-132">Proporciona una etiqueta HTML `<p>` usar la directiva de elemento que se va a resumir su privacidad y cookies.</span><span class="sxs-lookup"><span data-stu-id="02ee7-132">Provides an HTML `<p>` element to summarize your privacy and cookie use policy.</span></span>
* <span data-ttu-id="02ee7-133">Proporciona un vínculo a la página de privacidad o vista donde puede detallan la directiva de privacidad de su sitio.</span><span class="sxs-lookup"><span data-stu-id="02ee7-133">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-cookies"></a><span data-ttu-id="02ee7-134">Cookies esenciales</span><span class="sxs-lookup"><span data-stu-id="02ee7-134">Essential cookies</span></span>

<span data-ttu-id="02ee7-135">Si no tiene concedido el consentimiento, solo las cookies de marcado esencial se envían al explorador.</span><span class="sxs-lookup"><span data-stu-id="02ee7-135">If consent has not been given, only cookies marked essential are sent to the browser.</span></span> <span data-ttu-id="02ee7-136">El código siguiente realiza una cookie esenciales:</span><span class="sxs-lookup"><span data-stu-id="02ee7-136">The following code makes a cookie essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

## <a name="tempdata-provider-and-session-state-cookies-are-not-essential"></a><span data-ttu-id="02ee7-137">Cookies de estado de sesión y el proveedor TempData no son esenciales</span><span class="sxs-lookup"><span data-stu-id="02ee7-137">Tempdata provider and session state cookies are not essential</span></span>

<span data-ttu-id="02ee7-138">El [proveedor Tempdata](xref:fundamentals/app-state#tempdata) cookie no es esencial.</span><span class="sxs-lookup"><span data-stu-id="02ee7-138">The [Tempdata provider](xref:fundamentals/app-state#tempdata) cookie isn't essential.</span></span> <span data-ttu-id="02ee7-139">Si se deshabilita el seguimiento, el proveedor Tempdata no funcionará.</span><span class="sxs-lookup"><span data-stu-id="02ee7-139">If tracking is disabled, the Tempdata provider isn't functional.</span></span> <span data-ttu-id="02ee7-140">Para habilitar el proveedor Tempdata cuando se deshabilita el seguimiento, marcar la cookie de TempData como esencial en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="02ee7-140">To enable the Tempdata provider when tracking is disabled, mark the TempData cookie as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP/Startup.cs?name=snippet1)]

<span data-ttu-id="02ee7-141">[Estado de sesión](xref:fundamentals/app-state) las cookies no son esenciales.</span><span class="sxs-lookup"><span data-stu-id="02ee7-141">[Session state](xref:fundamentals/app-state) cookies are not essential.</span></span> <span data-ttu-id="02ee7-142">Estado de sesión no funcionará cuando se deshabilita el seguimiento.</span><span class="sxs-lookup"><span data-stu-id="02ee7-142">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="02ee7-143">El código siguiente hace que las cookies de sesión esenciales:</span><span class="sxs-lookup"><span data-stu-id="02ee7-143">The following code makes session cookies essential:</span></span>

[!code-csharp[](gdpr/sample/RP/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="02ee7-144">Datos personales</span><span class="sxs-lookup"><span data-stu-id="02ee7-144">Personal data</span></span>

<span data-ttu-id="02ee7-145">Las aplicaciones de ASP.NET Core creadas con cuentas de usuario individuales incluyen código para descargar y eliminar datos personales.</span><span class="sxs-lookup"><span data-stu-id="02ee7-145">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="02ee7-146">Seleccione el nombre de usuario y, a continuación, seleccione **datos personales**:</span><span class="sxs-lookup"><span data-stu-id="02ee7-146">Select the user name and then select **Personal data**:</span></span>

![Página de datos personales de administración](gdpr/_static/pd.png)

<span data-ttu-id="02ee7-148">Notas:</span><span class="sxs-lookup"><span data-stu-id="02ee7-148">Notes:</span></span>

* <span data-ttu-id="02ee7-149">Para generar el `Account/Manage` código, vea [Scaffold identidad](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="02ee7-149">To generate the `Account/Manage` code, see [Scaffold Identity](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="02ee7-150">El **eliminar** y **descargar** vínculos solo actúan sobre los datos de identidad predeterminada.</span><span class="sxs-lookup"><span data-stu-id="02ee7-150">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="02ee7-151">Las aplicaciones que crean datos de usuario personalizada deben ampliarse para eliminación y descarga los datos de usuario personalizada.</span><span class="sxs-lookup"><span data-stu-id="02ee7-151">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="02ee7-152">Para obtener más información, consulte [Add, descargar y eliminar datos de usuario personalizada para identidad](xref:security/authentication/add-user-data).</span><span class="sxs-lookup"><span data-stu-id="02ee7-152">For more information, see [Add, download, and delete custom user data to Identity](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="02ee7-153">Guarda los tokens para el usuario que se almacenan en la tabla de base de datos de identidad `AspNetUserTokens` se eliminan cuando el usuario se elimina mediante el comportamiento de eliminación en cascada debido a la [clave externa](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span><span class="sxs-lookup"><span data-stu-id="02ee7-153">Saved tokens for the user that are stored in the Identity database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span></span>
* <span data-ttu-id="02ee7-154">[Autenticación de proveedor externo](xref:security/authentication/social/index), como Facebook y Google, no está disponible antes de que se acepte la directiva.</span><span class="sxs-lookup"><span data-stu-id="02ee7-154">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the cookie policy is accepted.</span></span>

## <a name="encryption-at-rest"></a><span data-ttu-id="02ee7-155">Cifrado en reposo</span><span class="sxs-lookup"><span data-stu-id="02ee7-155">Encryption at rest</span></span>

<span data-ttu-id="02ee7-156">Algunas bases de datos y mecanismos de almacenamiento permiten para el cifrado en reposo.</span><span class="sxs-lookup"><span data-stu-id="02ee7-156">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="02ee7-157">Cifrado en reposo:</span><span class="sxs-lookup"><span data-stu-id="02ee7-157">Encryption at rest:</span></span>

* <span data-ttu-id="02ee7-158">Cifra automáticamente los datos almacenados.</span><span class="sxs-lookup"><span data-stu-id="02ee7-158">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="02ee7-159">Cifra sin configuración, programación u otros trabajos para el software que tiene acceso a los datos.</span><span class="sxs-lookup"><span data-stu-id="02ee7-159">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="02ee7-160">Es la opción más sencilla y segura.</span><span class="sxs-lookup"><span data-stu-id="02ee7-160">Is the easiest and safest option.</span></span>
* <span data-ttu-id="02ee7-161">Permite administrar las claves y cifrado de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="02ee7-161">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="02ee7-162">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="02ee7-162">For example:</span></span>

* <span data-ttu-id="02ee7-163">Microsoft SQL y SQL de Azure proporcionan [cifrado de datos transparente](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span><span class="sxs-lookup"><span data-stu-id="02ee7-163">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="02ee7-164">SQL Azure cifra la base de datos de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="02ee7-164">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="02ee7-165">[Azure Blobs, archivos, Table y Queue Storage se cifran de forma predeterminada](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span><span class="sxs-lookup"><span data-stu-id="02ee7-165">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="02ee7-166">Para las bases de datos que no proporcionan cifrado integrado en reposo, puede usar el cifrado de disco para proporcionar la misma protección.</span><span class="sxs-lookup"><span data-stu-id="02ee7-166">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="02ee7-167">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="02ee7-167">For example:</span></span>

* [<span data-ttu-id="02ee7-168">BitLocker para Windows Server</span><span class="sxs-lookup"><span data-stu-id="02ee7-168">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="02ee7-169">Linux:</span><span class="sxs-lookup"><span data-stu-id="02ee7-169">Linux:</span></span>
  * [<span data-ttu-id="02ee7-170">eCryptfs</span><span class="sxs-lookup"><span data-stu-id="02ee7-170">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="02ee7-171">[EncFS](https://github.com/vgough/encfs).</span><span class="sxs-lookup"><span data-stu-id="02ee7-171">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="02ee7-172">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="02ee7-172">Additional resources</span></span>

* [<span data-ttu-id="02ee7-173">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="02ee7-173">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
