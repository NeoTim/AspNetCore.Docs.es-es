---
title: Confirmación de la cuenta y recuperación de la contraseña en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo crear una aplicación ASP.NET Core con confirmación por correo electrónico y restablecimiento de contraseña.
ms.author: riande
ms.date: 03/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: 8a515990be584aa1233fc3bf77811ae3784d9b1c
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081562"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a>Confirmación de la cuenta y recuperación de la contraseña en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)y [Joe Audette](https://twitter.com/joeaudette)

En este tutorial se muestra cómo crear una aplicación ASP.NET Core con confirmación de correo electrónico y restablecimiento de contraseña. Este tutorial **no** es un tema de inicio. Debe estar familiarizado con:

* [ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start)
* [Autenticación](xref:security/authentication/identity)
* [Entity Framework Core](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

Vea [este archivo PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) para obtener la versión ASP.net Core 1,1.

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a>Requisitos previos

[SDK de .NET Core 3,0 o posterior](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a>Crear y probar una aplicación web con autenticación

Ejecute los siguientes comandos para crear una aplicación web con autenticación.

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

Ejecute la aplicación, seleccione el vínculo **registrar** y registre un usuario. Una vez registrado, se le redirigirá `/Identity/Account/RegisterConfirmation` a la página a que contiene un vínculo para simular la confirmación de correo electrónico:

* Seleccione el `Click here to confirm your account` vínculo.
* Seleccione el vínculo de **Inicio de sesión** e inicie sesión con las mismas credenciales.
* Seleccione el `Hello YourEmail@provider.com!` vínculo, que le redirigirá a la `/Identity/Account/Manage/PersonalData` página.
* Seleccione la pestaña **datos personales** de la izquierda y, a continuación, seleccione **eliminar**.

### <a name="configure-an-email-provider"></a>Configuración de un proveedor de correo electrónico

En este tutorial, se usa [SendGrid](https://sendgrid.com) para enviar correo electrónico. Necesita una cuenta y una clave de SendGrid para enviar el correo electrónico. Puede usar otros proveedores de correo electrónico. Se recomienda usar SendGrid u otro servicio de correo electrónico para enviar correo electrónico. SMTP es difícil de proteger y configurar correctamente.

Cree una clase para capturar la clave de correo electrónico segura. Para este ejemplo, cree *Services/AuthMessageSenderOptions. CS*:

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>Configuración de secretos de usuario de SendGrid

`SendGridUser` Establezca y `SendGridKey` con la [herramienta de administrador de secretos](xref:security/app-secrets). Por ejemplo:

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

En Windows, el administrador de secretos almacena pares de clave/valor en un archivo *Secrets. JSON* en el `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directorio.

El contenido del archivo *Secrets. JSON* no está cifrado. El marcado siguiente muestra el archivo *Secrets. JSON* . Se `SendGridKey` ha quitado el valor.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

Para obtener más información, vea el [patrón de opciones](xref:fundamentals/configuration/options) y la [configuración](xref:fundamentals/configuration/index).

### <a name="install-sendgrid"></a>Instalación de SendGrid

En este tutorial se muestra cómo agregar notificaciones por correo electrónico a través de [SendGrid](https://sendgrid.com/), pero puede enviar correo electrónico mediante SMTP y otros mecanismos.

Instale el `SendGrid` paquete NuGet:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

En la consola del administrador de paquetes, escriba el siguiente comando:

```powershell
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

En la consola de, escriba el siguiente comando:

```dotnetcli
dotnet add package SendGrid
```

---

Consulte Introducción a [SendGrid gratis](https://sendgrid.com/free/) para registrarse para obtener una cuenta gratuita de SendGrid.

### <a name="implement-iemailsender"></a>Implementación de IEmailSender

Para implementar `IEmailSender`, cree *Services/EmailSender. CS* con código similar al siguiente:

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>Configurar el inicio para admitir correo electrónico

Agregue el código siguiente al `ConfigureServices` método en el archivo *Startup.CS* :

* Agregue `EmailSender` como un servicio transitorio.
* Registre la `AuthMessageSenderOptions` instancia de configuración.

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a>Registro, confirmación de correo electrónico y restablecimiento de contraseña

Ejecute la aplicación web y pruebe el flujo de recuperación de la contraseña y la confirmación de la cuenta.

* Ejecute la aplicación y registrar un nuevo usuario
* Compruebe su correo electrónico para el vínculo de confirmación de la cuenta. Consulte [depurar correo electrónico](#debug) si no recibe el correo electrónico.
* Haga clic en el vínculo para confirmar el correo electrónico.
* Inicie sesión con su correo electrónico y contraseña.
* Cierre la sesión.

### <a name="test-password-reset"></a>Restablecimiento de la contraseña de prueba

* Si ha iniciado sesión, seleccione **Logout**.
* Seleccione el vínculo **iniciar sesión** y seleccione el vínculo **¿olvidó su contraseña?** .
* Escriba el correo electrónico que usó para registrar la cuenta.
* Se envía un correo electrónico con un vínculo para restablecer la contraseña. Compruebe su correo electrónico y haga clic en el vínculo para restablecer la contraseña. Una vez que la contraseña se haya restablecido correctamente, puede iniciar sesión con el correo electrónico y la nueva contraseña.

## <a name="change-email-and-activity-timeout"></a>Cambiar el tiempo de espera del correo electrónico y la actividad

El tiempo de espera de inactividad predeterminado es de 14 días. El código siguiente establece el tiempo de espera de inactividad en 5 días:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>Cambiar todas las vigencias de los tokens de protección de datos

El siguiente código cambia el tiempo de espera de todos los tokens de protección de datos a 3 horas:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

Los tokens de usuario de identidad integrados (consulte [AspNetCore/src/Identity/Extensions. Core/src/TokenOptions. CS](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) tienen un [tiempo de espera de un día](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).

### <a name="change-the-email-token-lifespan"></a>Cambiar la duración del token de correo electrónico

La duración predeterminada del token de [los tokens de usuario de identidad](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) es [un día](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs). En esta sección se muestra cómo cambiar la duración del token de correo electrónico.

Agregue un [DataProtectorTokenProvider\<de TUser personalizado >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) y: <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

Agregue el proveedor personalizado al contenedor de servicio:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a>Confirmación de reenvío de correo electrónico

Consulte [este problema de GitHub](https://github.com/aspnet/AspNetCore/issues/5410).

<a name="debug"></a>

### <a name="debug-email"></a>Depurar correo electrónico

Si no consigue que el correo electrónico funcione:

* Establezca un punto de `EmailSender.Execute` interrupción en `SendGridClient.SendEmailAsync` para comprobar que se llama a.
* Cree una [aplicación de consola para enviar correo electrónico](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) mediante código `EmailSender.Execute`similar a.
* Revise la página [actividad de correo electrónico](https://sendgrid.com/docs/User_Guide/email_activity.html) .
* Compruebe la carpeta de correo no deseado.
* Pruebe otro alias de correo electrónico en un proveedor de correo electrónico diferente (Microsoft, Yahoo, gmail, etc.).
* Intente enviar a diferentes cuentas de correo electrónico.

**Un procedimiento** recomendado de seguridad es **no** usar secretos de producción en pruebas y desarrollo. Si publica la aplicación en Azure, establezca los secretos de SendGrid como configuración de la aplicación en el portal de aplicaciones Web de Azure. El sistema de configuración está configurado para leer las claves de las variables de entorno.

## <a name="combine-social-and-local-login-accounts"></a>Combinación de cuentas de inicio de sesión sociales y locales

Para completar esta sección, primero debe habilitar un proveedor de autenticación externo. Consulte la [autenticación de Facebook, Google y proveedores externos](xref:security/authentication/social/index).

Para combinar cuentas locales y sociales, haga clic en el vínculo de correo electrónico. En la siguiente secuencia, "RickAndMSFT@gmail.com" se crea primero como un inicio de sesión local; sin embargo, puede crear primero la cuenta como un inicio de sesión social y luego agregar un inicio de sesión local.

![Aplicación web: RickAndMSFT@gmail.com usuario autenticado](accconfirm/_static/rick.png)

Haga clic en el vínculo **administrar** . Observe los 0 externos (inicios de sesión sociales) asociados a esta cuenta.

![Administrar vista](accconfirm/_static/manage.png)

Haga clic en el vínculo a otro servicio de inicio de sesión y acepte las solicitudes de la aplicación. En la imagen siguiente, Facebook es el proveedor de autenticación externo:

![Administrar la vista de inicios de sesión externos enumerando Facebook](accconfirm/_static/fb.png)

Las dos cuentas se han combinado. Puede iniciar sesión con cualquiera de las dos cuentas. Es posible que desee que los usuarios agreguen cuentas locales en caso de que su servicio de autenticación de inicio de sesión social esté inactivo, o más probabilidades de que hayan perdido el acceso a su cuenta de redes sociales.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>Habilitar la confirmación de la cuenta después de que un sitio tenga usuarios

La habilitación de la confirmación de cuenta en un sitio con usuarios bloquea a todos los usuarios existentes. Los usuarios existentes están bloqueados porque sus cuentas no están confirmadas. Para solucionar el bloqueo de usuario existente, use uno de los métodos siguientes:

* Actualice la base de datos para marcar todos los usuarios existentes como confirmados.
* Confirme los usuarios existentes. Por ejemplo, enviar por lotes correos electrónicos con vínculos de confirmación.

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a>Requisitos previos

[SDK de .NET Core 2,2 o posterior](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a>Creación de una aplicación web y una identidad scaffolding

Ejecute los siguientes comandos para crear una aplicación web con autenticación.

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a>Prueba del nuevo registro de usuario

Ejecute la aplicación, seleccione el vínculo **registrar** y registre un usuario. En este momento, la única validación en el correo electrónico es con el atributo [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) . Después de enviar el registro, ha iniciado sesión en la aplicación. Más adelante en el tutorial, se actualiza el código para que los nuevos usuarios no puedan iniciar sesión hasta que se valide su correo electrónico.

[!INCLUDE[](~/includes/view-identity-db.md)]

Tenga en cuenta que `EmailConfirmed` el campo `False`de la tabla es.

Es posible que desee usar este correo electrónico de nuevo en el paso siguiente cuando la aplicación envíe un correo electrónico de confirmación. Haga clic con el botón derecho en la fila y seleccione **eliminar**. Al eliminar el alias de correo electrónico, se facilitan los pasos siguientes.

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a>Requerir confirmación de correo electrónico

Se recomienda confirmar el correo electrónico de un nuevo registro de usuario. La confirmación por correo electrónico ayuda a comprobar que no está suplantando a otra persona (es decir, que no se han registrado con el correo electrónico de otro usuario). Supongamos que tiene un foro de discusión y desea evitar queyli@example.com"" se registre como "nolivetto@contoso.com". Sin confirmación por correo electróniconolivetto@contoso.com, "" podría recibir correo electrónico no deseado de la aplicación. Supongamos que el usuario se registró accidentalmente como "ylo@example.com" y no detectó la falta de ortografía de "Yli". No podrán usar la recuperación de contraseña porque la aplicación no tiene el correo electrónico correcto. La confirmación por correo electrónico proporciona una protección limitada de bots. La confirmación por correo electrónico no proporciona protección contra usuarios malintencionados con muchas cuentas de correo electrónico.

Por lo general, querrá evitar que los nuevos usuarios publiquen datos en el sitio Web antes de que tengan un correo electrónico confirmado.

Actualización `Startup.ConfigureServices` para requerir un correo electrónico confirmado:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

`config.SignIn.RequireConfirmedEmail = true;`impide que los usuarios registrados inicien sesión hasta que se confirme su correo electrónico.

### <a name="configure-email-provider"></a>Configurar proveedor de correo electrónico

En este tutorial, se usa [SendGrid](https://sendgrid.com) para enviar correo electrónico. Necesita una cuenta y una clave de SendGrid para enviar el correo electrónico. Puede usar otros proveedores de correo electrónico. ASP.net Core 2. x incluye `System.Net.Mail`, que le permite enviar correo electrónico desde su aplicación. Se recomienda usar SendGrid u otro servicio de correo electrónico para enviar correo electrónico. SMTP es difícil de proteger y configurar correctamente.

Cree una clase para capturar la clave de correo electrónico segura. Para este ejemplo, cree *Services/AuthMessageSenderOptions. CS*:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>Configuración de secretos de usuario de SendGrid

`SendGridUser` Establezca y `SendGridKey` con la [herramienta de administrador de secretos](xref:security/app-secrets). Por ejemplo:

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

En Windows, el administrador de secretos almacena pares de clave/valor en un archivo *Secrets. JSON* en el `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directorio.

El contenido del archivo *Secrets. JSON* no está cifrado. El marcado siguiente muestra el archivo *Secrets. JSON* . Se `SendGridKey` ha quitado el valor.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

Para obtener más información, vea el [patrón de opciones](xref:fundamentals/configuration/options) y la [configuración](xref:fundamentals/configuration/index).

### <a name="install-sendgrid"></a>Instalación de SendGrid

En este tutorial se muestra cómo agregar notificaciones por correo electrónico a través de [SendGrid](https://sendgrid.com/), pero puede enviar correo electrónico mediante SMTP y otros mecanismos.

Instale el `SendGrid` paquete NuGet:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

En la consola del administrador de paquetes, escriba el siguiente comando:

```powershell
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

En la consola de, escriba el siguiente comando:

```dotnetcli
dotnet add package SendGrid
```

---

Consulte Introducción a [SendGrid gratis](https://sendgrid.com/free/) para registrarse para obtener una cuenta gratuita de SendGrid.

### <a name="implement-iemailsender"></a>Implementación de IEmailSender

Para implementar `IEmailSender`, cree *Services/EmailSender. CS* con código similar al siguiente:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>Configurar el inicio para admitir correo electrónico

Agregue el código siguiente al `ConfigureServices` método en el archivo *Startup.CS* :

* Agregue `EmailSender` como un servicio transitorio.
* Registre la `AuthMessageSenderOptions` instancia de configuración.

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a>Habilitar la confirmación de la cuenta y la recuperación de la contraseña

La plantilla tiene el código para la confirmación de la cuenta y la recuperación de la contraseña. Busque el `OnPostAsync` método en *areas/Identity/pages/Account/Register. cshtml. CS*.

Evite que los usuarios recién registrados inicien sesión automáticamente al comentar la siguiente línea:

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

El método complete se muestra con la línea resaltada:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a>Registro, confirmación de correo electrónico y restablecimiento de contraseña

Ejecute la aplicación web y pruebe el flujo de recuperación de la contraseña y la confirmación de la cuenta.

* Ejecute la aplicación y registrar un nuevo usuario
* Compruebe su correo electrónico para el vínculo de confirmación de la cuenta. Consulte [depurar correo electrónico](#debug) si no recibe el correo electrónico.
* Haga clic en el vínculo para confirmar el correo electrónico.
* Inicie sesión con su correo electrónico y contraseña.
* Cierre la sesión.

### <a name="view-the-manage-page"></a>Ver la página administrar

Seleccione el nombre de usuario en el explorador ![: ventana del explorador con el nombre de usuario](accconfirm/_static/un.png)

La página Administrar se muestra con la pestaña **perfil** seleccionada. El **correo electrónico** muestra una casilla que indica que se ha confirmado el correo electrónico.

### <a name="test-password-reset"></a>Restablecimiento de la contraseña de prueba

* Si ha iniciado sesión, seleccione **Logout**.
* Seleccione el vínculo **iniciar sesión** y seleccione el vínculo **¿olvidó su contraseña?** .
* Escriba el correo electrónico que usó para registrar la cuenta.
* Se envía un correo electrónico con un vínculo para restablecer la contraseña. Compruebe su correo electrónico y haga clic en el vínculo para restablecer la contraseña. Una vez que la contraseña se haya restablecido correctamente, puede iniciar sesión con el correo electrónico y la nueva contraseña.

## <a name="change-email-and-activity-timeout"></a>Cambiar el tiempo de espera del correo electrónico y la actividad

El tiempo de espera de inactividad predeterminado es de 14 días. El código siguiente establece el tiempo de espera de inactividad en 5 días:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>Cambiar todas las vigencias de los tokens de protección de datos

El siguiente código cambia el tiempo de espera de todos los tokens de protección de datos a 3 horas:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

Los tokens de usuario de identidad integrados (consulte [AspNetCore/src/Identity/Extensions. Core/src/TokenOptions. CS](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) tienen un [tiempo de espera de un día](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).

### <a name="change-the-email-token-lifespan"></a>Cambiar la duración del token de correo electrónico

La duración predeterminada del token de [los tokens de usuario de identidad](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) es [un día](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs). En esta sección se muestra cómo cambiar la duración del token de correo electrónico.

Agregue un [DataProtectorTokenProvider\<de TUser personalizado >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) y: <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

Agregue el proveedor personalizado al contenedor de servicio:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a>Confirmación de reenvío de correo electrónico

Consulte [este problema de GitHub](https://github.com/aspnet/AspNetCore/issues/5410).

<a name="debug"></a>

### <a name="debug-email"></a>Depurar correo electrónico

Si no consigue que el correo electrónico funcione:

* Establezca un punto de `EmailSender.Execute` interrupción en `SendGridClient.SendEmailAsync` para comprobar que se llama a.
* Cree una [aplicación de consola para enviar correo electrónico](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) mediante código `EmailSender.Execute`similar a.
* Revise la página [actividad de correo electrónico](https://sendgrid.com/docs/User_Guide/email_activity.html) .
* Compruebe la carpeta de correo no deseado.
* Pruebe otro alias de correo electrónico en un proveedor de correo electrónico diferente (Microsoft, Yahoo, gmail, etc.).
* Intente enviar a diferentes cuentas de correo electrónico.

**Un procedimiento** recomendado de seguridad es **no** usar secretos de producción en pruebas y desarrollo. Si publica la aplicación en Azure, puede establecer los secretos de SendGrid como configuración de la aplicación en el portal de aplicaciones Web de Azure. El sistema de configuración está configurado para leer las claves de las variables de entorno.

## <a name="combine-social-and-local-login-accounts"></a>Combinación de cuentas de inicio de sesión sociales y locales

Para completar esta sección, primero debe habilitar un proveedor de autenticación externo. Consulte la [autenticación de Facebook, Google y proveedores externos](xref:security/authentication/social/index).

Para combinar cuentas locales y sociales, haga clic en el vínculo de correo electrónico. En la siguiente secuencia, "RickAndMSFT@gmail.com" se crea primero como un inicio de sesión local; sin embargo, puede crear primero la cuenta como un inicio de sesión social y luego agregar un inicio de sesión local.

![Aplicación web: RickAndMSFT@gmail.com usuario autenticado](accconfirm/_static/rick.png)

Haga clic en el vínculo **administrar** . Observe los 0 externos (inicios de sesión sociales) asociados a esta cuenta.

![Administrar vista](accconfirm/_static/manage.png)

Haga clic en el vínculo a otro servicio de inicio de sesión y acepte las solicitudes de la aplicación. En la imagen siguiente, Facebook es el proveedor de autenticación externo:

![Administrar la vista de inicios de sesión externos enumerando Facebook](accconfirm/_static/fb.png)

Las dos cuentas se han combinado. Puede iniciar sesión con cualquiera de las dos cuentas. Es posible que desee que los usuarios agreguen cuentas locales en caso de que su servicio de autenticación de inicio de sesión social esté inactivo, o más probabilidades de que hayan perdido el acceso a su cuenta de redes sociales.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>Habilitar la confirmación de la cuenta después de que un sitio tenga usuarios

La habilitación de la confirmación de cuenta en un sitio con usuarios bloquea a todos los usuarios existentes. Los usuarios existentes están bloqueados porque sus cuentas no están confirmadas. Para solucionar el bloqueo de usuario existente, use uno de los métodos siguientes:

* Actualice la base de datos para marcar todos los usuarios existentes como confirmados.
* Confirme los usuarios existentes. Por ejemplo, enviar por lotes correos electrónicos con vínculos de confirmación.

::: moniker-end
