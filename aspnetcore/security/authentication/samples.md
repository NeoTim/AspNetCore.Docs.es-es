---
title: Ejemplos de autenticación para ASP.NET Core
author: rick-anderson
description: Proporciona vínculos a los ejemplos de autenticación en el repositorio de ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/samples
ms.openlocfilehash: 7cd0fe60d7917abda7d8ac0e071deca13a4136ce
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776557"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="2eadf-103">Ejemplos de autenticación para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2eadf-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="2eadf-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2eadf-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2eadf-105">El [repositorio ASP.net Core](https://github.com/dotnet/AspNetCore) contiene los siguientes ejemplos de autenticación en la carpeta *AspNetCore/src/Security/samples* :</span><span class="sxs-lookup"><span data-stu-id="2eadf-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="2eadf-106">Transformación de notificaciones</span><span class="sxs-lookup"><span data-stu-id="2eadf-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="2eadf-107">Autenticación de cookies</span><span class="sxs-lookup"><span data-stu-id="2eadf-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [<span data-ttu-id="2eadf-108">Proveedor de directivas personalizadas: IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="2eadf-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="2eadf-109">Opciones y esquemas de autenticación dinámica</span><span class="sxs-lookup"><span data-stu-id="2eadf-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="2eadf-110">[Notificaciones externas](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="2eadf-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="2eadf-111">Selección entre cookies y otro esquema de autenticación en función de la solicitud</span><span class="sxs-lookup"><span data-stu-id="2eadf-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="2eadf-112">Restringe el acceso a los archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="2eadf-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="2eadf-113">Ejecución de las muestras</span><span class="sxs-lookup"><span data-stu-id="2eadf-113">Run the samples</span></span>

* <span data-ttu-id="2eadf-114">Seleccione una [rama](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="2eadf-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="2eadf-115">Por ejemplo: `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="2eadf-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="2eadf-116">Clone o descargue el [repositorio de ASP.net Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="2eadf-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="2eadf-117">Compruebe que ha instalado la versión [SDK de .net Core](https://dotnet.microsoft.com/download/dotnet-core) que coincide con el clon del repositorio de ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="2eadf-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="2eadf-118">Navegue a un ejemplo en *AspNetCore/src/Security/samples* y ejecute el ejemplo `dotnet run`con.</span><span class="sxs-lookup"><span data-stu-id="2eadf-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2eadf-119">El [repositorio ASP.net Core](https://github.com/dotnet/AspNetCore) contiene los siguientes ejemplos de autenticación en la carpeta *AspNetCore/src/Security/samples* :</span><span class="sxs-lookup"><span data-stu-id="2eadf-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="2eadf-120">Transformación de notificaciones</span><span class="sxs-lookup"><span data-stu-id="2eadf-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="2eadf-121">Autenticación de cookies</span><span class="sxs-lookup"><span data-stu-id="2eadf-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="2eadf-122">Proveedor de directivas personalizadas: IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="2eadf-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="2eadf-123">Opciones y esquemas de autenticación dinámica</span><span class="sxs-lookup"><span data-stu-id="2eadf-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="2eadf-124">[Notificaciones externas](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="2eadf-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="2eadf-125">Selección entre cookies y otro esquema de autenticación en función de la solicitud</span><span class="sxs-lookup"><span data-stu-id="2eadf-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="2eadf-126">Restringe el acceso a los archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="2eadf-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="2eadf-127">Ejecución de las muestras</span><span class="sxs-lookup"><span data-stu-id="2eadf-127">Run the samples</span></span>

* <span data-ttu-id="2eadf-128">Seleccione una [rama](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="2eadf-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="2eadf-129">Por ejemplo: `release/2.2`</span><span class="sxs-lookup"><span data-stu-id="2eadf-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="2eadf-130">Clone o descargue el [repositorio de ASP.net Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="2eadf-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="2eadf-131">Compruebe que ha instalado la versión [SDK de .net Core](https://dotnet.microsoft.com/download/dotnet-core) que coincide con el clon del repositorio de ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="2eadf-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="2eadf-132">Navegue a un ejemplo en *AspNetCore/src/Security/samples* y ejecute el ejemplo `dotnet run`con.</span><span class="sxs-lookup"><span data-stu-id="2eadf-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
