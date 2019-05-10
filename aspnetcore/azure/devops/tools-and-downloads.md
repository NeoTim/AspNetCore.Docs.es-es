---
title: Herramientas y descargas - DevOps con ASP.NET Core y Azure
author: CamSoper
description: Herramientas y descargas necesarias para DevOps con ASP.NET Core y Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: ae4827f735274405021e5ee539d1029b7ddb9553
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/27/2019
ms.locfileid: "64891992"
---
# <a name="tools-and-downloads"></a>Herramientas y descargas

Azure tiene varias interfaces para aprovisionar y administrar los recursos, como el [portal Azure](https://portal.azure.com), [CLI de Azure](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [en la nube de Azure Shell](https://shell.azure.com/bash)y Visual Studio. Esta guía adopta un enfoque minimalista y usa Azure Cloud Shell siempre que sea posible para reducir los pasos necesarios. Sin embargo, se debe usar el portal de Azure para algunas partes.

## <a name="prerequisites"></a>Requisitos previos

Se requieren las siguientes suscripciones:

* Azure &mdash; si no tienes una cuenta, [obtener una evaluación gratuita](https://azure.microsoft.com/free/).
* Servicios de Azure DevOps &mdash; su suscripción de Azure DevOps y la organización se crea en el capítulo 4.
* GitHub &mdash; si no tienes una cuenta, [Regístrese gratuitamente](https://github.com/join).

Se requieren las siguientes herramientas:

* [GIT](https://git-scm.com/downloads) &mdash; se recomienda un entendimiento básico de Git en esta guía. Revise el [documentación de Git](https://git-scm.com/doc), concretamente [git remoto](https://git-scm.com/docs/git-remote) y [git push](https://git-scm.com/docs/git-push).
* [SDK de .NET core](https://www.microsoft.com/net/download/) &mdash; versión 2.1.300 o posterior, es necesario para compilar y ejecutar la aplicación de ejemplo. Si está instalado Visual Studio con el **desarrollo multiplataforma de .NET Core** ya está instalada la carga de trabajo, el SDK de .NET Core.

    Compruebe la instalación del SDK de .NET Core. Abra un shell de comandos y ejecute el siguiente comando:

    ```console
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Herramientas recomendadas (solo Windows)

* [Visual Studio](https://visualstudio.microsoft.com)del sólidas herramientas de Azure proporcionan una interfaz gráfica de usuario para la mayoría de la funcionalidad descrita en esta guía. Funcionará cualquier edición de Visual Studio, incluida la edición gratuita de Visual Studio Community. Los tutoriales se escriben en demuestran el desarrollo, implementación y DevOps con y sin Visual Studio.

  Confirme que Visual Studio tiene las siguientes [cargas de trabajo](/visualstudio/install/modify-visual-studio) instalado:

  * Desarrollo web y ASP.NET
  * Desarrollo de Azure
  * Desarrollo multiplataforma de .NET Core
