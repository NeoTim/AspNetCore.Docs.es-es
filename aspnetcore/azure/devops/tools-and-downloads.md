---
title: 'Herramientas y descargas: DevOps con ASP.NET Core y Azure'
author: CamSoper
description: Herramientas y descargas necesarias para DevOps con ASP.NET Core y Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 25ce311373b0aaddfa3bc2728c39e503acbca69d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647447"
---
# <a name="tools-and-downloads"></a><span data-ttu-id="4a7cc-103">Herramientas y descargas</span><span class="sxs-lookup"><span data-stu-id="4a7cc-103">Tools and downloads</span></span>

<span data-ttu-id="4a7cc-104">Azure tiene varias interfaces para aprovisionar y administrar recursos, como [Azure Portal](https://portal.azure.com), [CLI de Azure](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash) y Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4a7cc-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="4a7cc-105">Esta guía adopta un enfoque minimalista y usa Azure Cloud Shell siempre que sea posible para reducir los pasos necesarios.</span><span class="sxs-lookup"><span data-stu-id="4a7cc-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="4a7cc-106">Sin embargo, Azure Portal se puede usar en algunas partes.</span><span class="sxs-lookup"><span data-stu-id="4a7cc-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4a7cc-107">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="4a7cc-107">Prerequisites</span></span>

<span data-ttu-id="4a7cc-108">Se requieren las suscripciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="4a7cc-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="4a7cc-109">Azure: si no tiene una cuenta, [obtenga una prueba](https://azure.microsoft.com/free/).</span><span class="sxs-lookup"><span data-stu-id="4a7cc-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/).</span></span>
* <span data-ttu-id="4a7cc-110">Azure DevOps Services: la suscripción y organización de Azure DevOps se crea en el capítulo 4.</span><span class="sxs-lookup"><span data-stu-id="4a7cc-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="4a7cc-111">GitHub: si no tiene una cuenta, [regístrese gratis](https://github.com/join).</span><span class="sxs-lookup"><span data-stu-id="4a7cc-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="4a7cc-112">Se requieren las herramientas siguientes:</span><span class="sxs-lookup"><span data-stu-id="4a7cc-112">The following tools are required:</span></span>

* <span data-ttu-id="4a7cc-113">[Git](https://git-scm.com/downloads): se recomienda una comprensión fundamental de Git para esta guía.</span><span class="sxs-lookup"><span data-stu-id="4a7cc-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="4a7cc-114">Revise la [Documentación de Git](https://git-scm.com/doc), concretamente [Git remoto](https://git-scm.com/docs/git-remote) y [Git Push](https://git-scm.com/docs/git-push).</span><span class="sxs-lookup"><span data-stu-id="4a7cc-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="4a7cc-115">[SDK de .NET Core](https://www.microsoft.com/net/download/): se necesita la versión 2.1.300 o una posterior para compilar y ejecutar la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="4a7cc-115">[.NET Core SDK](https://www.microsoft.com/net/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="4a7cc-116">Si Visual Studio se instala con la carga de trabajo de **desarrollo multiplataforma de .NET Core**, el SDK de .NET Core ya está instalado.</span><span class="sxs-lookup"><span data-stu-id="4a7cc-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="4a7cc-117">Compruebe la instalación del SDK de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4a7cc-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="4a7cc-118">Abra un shell de comandos y ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="4a7cc-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="4a7cc-119">Herramientas recomendadas (solo Windows)</span><span class="sxs-lookup"><span data-stu-id="4a7cc-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="4a7cc-120">Las herramientas sólidas de Azure de [Visual Studio](https://visualstudio.microsoft.com) proporcionan una GUI para la mayoría de las capacidades descritas en esta guía.</span><span class="sxs-lookup"><span data-stu-id="4a7cc-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="4a7cc-121">Cualquier edición de Visual Studio funcionará, incluida la versión gratuita de Visual Studio Community Edition.</span><span class="sxs-lookup"><span data-stu-id="4a7cc-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="4a7cc-122">Los tutoriales se escriben para demostrar el desarrollo, la implementación y DevOps con y sin Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4a7cc-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="4a7cc-123">Confirme que Visual Studio tiene las [cargas de trabajo](/visualstudio/install/modify-visual-studio) siguientes instaladas:</span><span class="sxs-lookup"><span data-stu-id="4a7cc-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="4a7cc-124">Desarrollo web y ASP.NET</span><span class="sxs-lookup"><span data-stu-id="4a7cc-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="4a7cc-125">Desarrollo de Azure</span><span class="sxs-lookup"><span data-stu-id="4a7cc-125">Azure development</span></span>
  * <span data-ttu-id="4a7cc-126">Desarrollo multiplataforma de .NET Core</span><span class="sxs-lookup"><span data-stu-id="4a7cc-126">.NET Core cross-platform development</span></span>
