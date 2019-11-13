---
title: Introducción a las API de protección de datos en ASP.NET Core
author: rick-anderson
description: Aprenda a usar las API de protección de datos de ASP.NET Core para proteger y desproteger los datos de una aplicación.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- SignalR
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 8c3f3c7fb21434cf335591c41741f0ce868df33e
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963864"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a><span data-ttu-id="4a1e8-103">Introducción a las API de protección de datos en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4a1e8-103">Get started with the Data Protection APIs in ASP.NET Core</span></span>

<a name="security-data-protection-getting-started"></a>

<span data-ttu-id="4a1e8-104">En su forma más sencilla, la protección de datos consta de los siguientes pasos:</span><span class="sxs-lookup"><span data-stu-id="4a1e8-104">At its simplest, protecting data consists of the following steps:</span></span>

1. <span data-ttu-id="4a1e8-105">Cree un protector de datos a partir de un proveedor de protección de datos.</span><span class="sxs-lookup"><span data-stu-id="4a1e8-105">Create a data protector from a data protection provider.</span></span>

2. <span data-ttu-id="4a1e8-106">Llame al método `Protect` con los datos que desea proteger.</span><span class="sxs-lookup"><span data-stu-id="4a1e8-106">Call the `Protect` method with the data you want to protect.</span></span>

3. <span data-ttu-id="4a1e8-107">Llame al método `Unprotect` con los datos que desea volver a convertir en texto sin formato.</span><span class="sxs-lookup"><span data-stu-id="4a1e8-107">Call the `Unprotect` method with the data you want to turn back into plain text.</span></span>

<span data-ttu-id="4a1e8-108">La mayoría de los marcos y modelos de aplicaciones, como ASP.NET Core o SignalR, ya configuran el sistema de protección de datos y lo agregan a un contenedor de servicio al que se accede a través de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="4a1e8-108">Most frameworks and app models, such as ASP.NET Core or SignalR, already configure the data protection system and add it to a service container you access via dependency injection.</span></span> <span data-ttu-id="4a1e8-109">En el ejemplo siguiente se muestra la configuración de un contenedor de servicios para la inserción de dependencias y el registro de la pila de protección de datos, la recepción del proveedor de protección de datos a través de DI, la creación de un protector y la protección de los datos.</span><span class="sxs-lookup"><span data-stu-id="4a1e8-109">The following sample demonstrates configuring a service container for dependency injection and registering the data protection stack, receiving the data protection provider via DI, creating a protector and protecting then unprotecting data.</span></span>

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="4a1e8-110">Al crear un protector, debe proporcionar una o más [cadenas de propósito](xref:security/data-protection/consumer-apis/purpose-strings).</span><span class="sxs-lookup"><span data-stu-id="4a1e8-110">When you create a protector you must provide one or more [Purpose Strings](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="4a1e8-111">Una cadena de propósito proporciona aislamiento entre los consumidores.</span><span class="sxs-lookup"><span data-stu-id="4a1e8-111">A purpose string provides isolation between consumers.</span></span> <span data-ttu-id="4a1e8-112">Por ejemplo, un protector creado con una cadena de propósito de "verde" no podría desproteger los datos proporcionados por un protector con un propósito de "púrpura".</span><span class="sxs-lookup"><span data-stu-id="4a1e8-112">For example, a protector created with a purpose string of "green" wouldn't be able to unprotect data provided by a protector with a purpose of "purple".</span></span>

>[!TIP]
> <span data-ttu-id="4a1e8-113">Las instancias de `IDataProtectionProvider` y `IDataProtector` son seguras para subprocesos para varios llamadores.</span><span class="sxs-lookup"><span data-stu-id="4a1e8-113">Instances of `IDataProtectionProvider` and `IDataProtector` are thread-safe for multiple callers.</span></span> <span data-ttu-id="4a1e8-114">Está pensado que una vez que un componente obtiene una referencia a un `IDataProtector` a través de una llamada a `CreateProtector`, usará esa referencia para varias llamadas a `Protect` y `Unprotect`.</span><span class="sxs-lookup"><span data-stu-id="4a1e8-114">It's intended that once a component gets a reference to an `IDataProtector` via a call to `CreateProtector`, it will use that reference for multiple calls to `Protect` and `Unprotect`.</span></span>
>
><span data-ttu-id="4a1e8-115">Una llamada a `Unprotect` producirá CryptographicException si la carga protegida no se puede comprobar o descifrar.</span><span class="sxs-lookup"><span data-stu-id="4a1e8-115">A call to `Unprotect` will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="4a1e8-116">Algunos componentes pueden querer omitir los errores durante las operaciones de desprotección; un componente que lee las cookies de autenticación podría controlar este error y tratar la solicitud como si no tuviera cookies en absoluto, en lugar de que se produzca un error en la solicitud.</span><span class="sxs-lookup"><span data-stu-id="4a1e8-116">Some components may wish to ignore errors during unprotect operations; a component which reads authentication cookies might handle this error and treat the request as if it had no cookie at all rather than fail the request outright.</span></span> <span data-ttu-id="4a1e8-117">Los componentes que quieren este comportamiento deben detectar específicamente CryptographicException en lugar de admitir todas las excepciones.</span><span class="sxs-lookup"><span data-stu-id="4a1e8-117">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>
