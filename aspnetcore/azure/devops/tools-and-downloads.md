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
# <a name="tools-and-downloads"></a>Herramientas y descargas

Azure tiene varias interfaces para aprovisionar y administrar recursos, como [Azure Portal](https://portal.azure.com), [CLI de Azure](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash) y Visual Studio. Esta guía adopta un enfoque minimalista y usa Azure Cloud Shell siempre que sea posible para reducir los pasos necesarios. Sin embargo, Azure Portal se puede usar en algunas partes.

## <a name="prerequisites"></a>Requisitos previos

Se requieren las suscripciones siguientes:

* Azure: si no tiene una cuenta, [obtenga una prueba](https://azure.microsoft.com/free/).
* Azure DevOps Services: la suscripción y organización de Azure DevOps se crea en el capítulo 4.
* GitHub: si no tiene una cuenta, [regístrese gratis](https://github.com/join).

Se requieren las herramientas siguientes:

* [Git](https://git-scm.com/downloads): se recomienda una comprensión fundamental de Git para esta guía. Revise la [Documentación de Git](https://git-scm.com/doc), concretamente [Git remoto](https://git-scm.com/docs/git-remote) y [Git Push](https://git-scm.com/docs/git-push).
* [SDK de .NET Core](https://www.microsoft.com/net/download/): se necesita la versión 2.1.300 o una posterior para compilar y ejecutar la aplicación de ejemplo. Si Visual Studio se instala con la carga de trabajo de **desarrollo multiplataforma de .NET Core**, el SDK de .NET Core ya está instalado.

    Compruebe la instalación del SDK de .NET Core. Abra un shell de comandos y ejecute el comando siguiente:

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Herramientas recomendadas (solo Windows)

* Las herramientas sólidas de Azure de [Visual Studio](https://visualstudio.microsoft.com) proporcionan una GUI para la mayoría de las capacidades descritas en esta guía. Cualquier edición de Visual Studio funcionará, incluida la versión gratuita de Visual Studio Community Edition. Los tutoriales se escriben para demostrar el desarrollo, la implementación y DevOps con y sin Visual Studio.

  Confirme que Visual Studio tiene las [cargas de trabajo](/visualstudio/install/modify-visual-studio) siguientes instaladas:

  * Desarrollo web y ASP.NET
  * Desarrollo de Azure
  * Desarrollo multiplataforma de .NET Core
