---
title: Limitar la duración de las cargas protegidas en ASP.NET Core
author: rick-anderson
description: Obtenga información acerca de cómo limitar la duración de una carga protegida mediante las API de protección de datos de ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: bc1597f75d8c5f786d46e59ac027d01ffca077c0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768616"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a>Limitar la duración de las cargas protegidas en ASP.NET Core

Hay escenarios en los que el desarrollador de aplicaciones desea crear una carga protegida que expire después de un período de tiempo establecido. Por ejemplo, la carga protegida podría representar un token de restablecimiento de contraseña que solo debe ser válido durante una hora. Es posible que el desarrollador cree su propio formato de carga útil que contenga una fecha de expiración incrustada y que los desarrolladores avanzados quieran hacerlo de todos modos, pero para la mayoría de los desarrolladores que administran estas expiraciones pueden resultar tediosos.

Para facilitar esta tarea a los desarrolladores, el paquete [Microsoft. AspNetCore. deprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contiene las API de la utilidad para crear cargas que expiran automáticamente después de un período de tiempo establecido. Estas API no responden del `ITimeLimitedDataProtector` tipo.

## <a name="api-usage"></a>Uso de API

La `ITimeLimitedDataProtector` interfaz es la interfaz principal para proteger y desproteger las cargas de tiempo limitado o de expiración automática. Para crear una instancia de `ITimeLimitedDataProtector`, primero necesitará una instancia de un [IDataProtector](xref:security/data-protection/consumer-apis/overview) regular construido con un propósito específico. Una vez `IDataProtector` que la instancia esté disponible, `IDataProtector.ToTimeLimitedDataProtector` llame al método de extensión para obtener un protector con capacidades de expiración integradas.

`ITimeLimitedDataProtector`expone los siguientes métodos de extensión y superficie de API:

* CreateProtector (propósito de cadena): ITimeLimitedDataProtector: esta API es similar a la `IDataProtectionProvider.CreateProtector` existente, ya que se puede usar para crear [cadenas de propósito](xref:security/data-protection/consumer-apis/purpose-strings) a partir de un protector de hora raíz limitado.

* Proteger (Byte [] texto simple, expiración de DateTimeOffset): Byte []

* Proteger (Byte [] texto simple, duración de TimeSpan): Byte []

* Proteger (Byte [] texto simple): Byte []

* Protect (cadena sin formato, expiración de DateTimeOffset): cadena

* Protect (cadena sin formato, duración de TimeSpan): cadena

* Protect (cadena sin formato): cadena

Además de los métodos básicos `Protect` que toman solo el texto simple, hay nuevas sobrecargas que permiten especificar la fecha de expiración de la carga. La fecha de expiración se puede especificar como una fecha absoluta ( `DateTimeOffset`a través de) o como un tiempo relativo (de la hora del sistema `TimeSpan`actual, a través de). Si se llama a una sobrecarga que no toma una expiración, se supone que la carga no expira nunca.

* Unprotect (Byte [] protectedData, out DateTimeOffset de salida): Byte []

* Unprotect (Byte [] protectedData): Byte []

* Unprotect (cadena protectedData, out DateTimeOffset expire): cadena

* Unprotect (cadena protectedData): cadena

Los `Unprotect` métodos devuelven los datos no protegidos originales. Si la carga aún no ha expirado, la expiración absoluta se devuelve como un parámetro out opcional junto con los datos no protegidos originales. Si la carga ha expirado, todas las sobrecargas del método Unprotect producirán CryptographicException.

>[!WARNING]
> No se recomienda usar estas API para proteger las cargas que requieren una persistencia a largo plazo o indefinida. "¿Puedo permitirse que las cargas protegidas sean permanentemente irrecuperables después de un mes?" puede servir como una buena regla general; Si la respuesta es no, los desarrolladores deben considerar API alternativas.

En el ejemplo siguiente se usan las rutas de acceso de [código no di](xref:security/data-protection/configuration/non-di-scenarios) para crear instancias del sistema de protección de datos. Para ejecutar este ejemplo, asegúrese de que ha agregado primero una referencia al paquete Microsoft. AspNetCore. desproteccion. Extensions.

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
