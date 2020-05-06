---
title: Publicación de una SignalR aplicación ASP.NET Core en Azure App Service
author: bradygaster
description: Obtenga información sobre cómo publicar una SignalR aplicación ASP.NET Core en Azure App Service.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: a5d19c1519c69351605e8da1d8fa70bff784efd4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777194"
---
# <a name="publish-an-aspnet-core-signalr-app-to-azure-app-service"></a>Publicación de una aplicación ASP.NET Core Signalr en Azure App Service

Por el [Brady](https://twitter.com/bradygaster)

[Azure App Service](/azure/app-service/app-service-web-overview) es un servicio de plataforma de [informática en la nube de Microsoft](https://azure.microsoft.com/) para hospedar aplicaciones Web, incluidas ASP.net Core.

> [!NOTE]
> En este artículo se hace referencia a la publicación de una aplicación de Signalr ASP.NET Core desde Visual Studio. Para más información, consulte [signalr Service para Azure](https://azure.microsoft.com/services/signalr-service).

## <a name="publish-the-app"></a>Publicación de la aplicación

En este artículo se describe la publicación con las herramientas de Visual Studio. Visual Studio Code los usuarios pueden usar [CLI de Azure](/cli/azure) comandos para publicar aplicaciones en Azure. Para obtener más información, consulte [publicación de una aplicación ASP.net Core en Azure con herramientas de línea de comandos](/azure/app-service/app-service-web-get-started-dotnet).

1. Desde el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Publicar**.

1. Confirme que **App Service** y **crear nuevos** están seleccionados en el cuadro de diálogo **seleccionar un destino de publicación** .

1. Seleccione **crear perfil** en la lista desplegable del botón **publicar** .

   Escriba la información que se describe en la tabla siguiente en el cuadro de diálogo **crear App Service** y seleccione **crear**.

   | Elemento               | Descripción |
   | ------------------ | ----------- |
   | **Nombre**           | Nombre único de la aplicación. |
   | **Suscripción**   | Suscripción de Azure que usa la aplicación. |
   | **Grupo de recursos** | Grupo de recursos relacionados a los que pertenece la aplicación. |
   | **Plan de hospedaje**   | Plan de precios de la aplicación Web. |

1. Seleccione el **servicio SignalR de Azure** en la lista desplegable **dependencias** > **Agregar** :

   ![Área de dependencias que muestra la selección SignalR del servicio de Azure en la lista desplegable agregar](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. En el cuadro de diálogo **servicio de SignalR Azure** , seleccione **crear SignalR una nueva instancia de servicio de Azure**.

1. Proporcione un **nombre**, un **grupo de recursos**y una **Ubicación**. Vuelva al cuadro de diálogo **servicio de Azure SignalR ** y seleccione **Agregar**.

Visual Studio completa las siguientes tareas:

* Crea un perfil de publicación que contiene la configuración de publicación.
* Crea una *aplicación Web de Azure* con los detalles proporcionados.
* Publica la aplicación.
* Inicia un explorador, que carga la aplicación Web.

El formato de la dirección URL de la `{APP SERVICE NAME}.azurewebsites.net`aplicación es. Por ejemplo, una aplicación denominada `SignalRChatApp` tiene una dirección URL `https://signalrchatapp.azurewebsites.net`de.

Si se produce un error *de puerta de enlace HTTP 502,2-Bad* al implementar una aplicación que tiene como destino una versión preliminar de .net Core, consulte implementación de la [versión preliminar Azure App Service de ASP.net Core para](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) resolverlo.

## <a name="configure-the-app-in-azure-app-service"></a>Configurar la aplicación en Azure App Service

> [!NOTE]
> *Esta sección solo se aplica a las aplicaciones que no SignalR usan el servicio de Azure.*
>
> Si la aplicación usa el servicio SignalR de Azure, la App Service no requiere la configuración de la afinidad de enrutamiento de solicitud de aplicaciones (arr) y los sockets Web descritos en esta sección. Los clientes conectan sus Sockets web al SignalR servicio de Azure, no directamente a la aplicación.

En el caso de las aplicaciones SignalR hospedadas sin el servicio de Azure, habilite:

* [Afinidad de ARR](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) para enrutar las solicitudes de un usuario a la misma instancia de App Service. La configuración predeterminada es **on**.
* [Sockets web](xref:fundamentals/websockets) para permitir que el transporte de sockets web funcione. El valor predeterminado es **OFF**.

1. En el Azure Portal, vaya a la aplicación web en **App Services**.
1.  > Abra **configuración****General**.
1. Establezca **Sockets web** en **activado**.
1. Compruebe que la **afinidad ARR** está establecida en **on**.

## <a name="app-service-plan-limits"></a>Límites del plan de App Service

Los sockets web y otros transportes se limitan en función del plan de App Service seleccionado. Para obtener más información, consulte los límites de *azure Cloud Services* y los límites de *App Service* de las secciones de los límites [, cuotas y restricciones de suscripción y servicios de Azure](/azure/azure-subscription-service-limits#app-service-limits) .

## <a name="additional-resources"></a>Recursos adicionales

* [¿Qué es SignalR el servicio de Azure?](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Publicar una aplicación ASP.NET Core en Azure con las herramientas de línea de comandos](/azure/app-service/app-service-web-get-started-dotnet)
* [Hospedar e implementar ASP.NET Core aplicaciones de vista previa en Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
