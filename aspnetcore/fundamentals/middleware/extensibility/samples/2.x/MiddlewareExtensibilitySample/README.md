# <a name="aspnet-core-middleware-extensibility-sample"></a>Ejemplo de extensibilidad de middleware de ASP.NET Core

En este ejemplo se muestran los escenarios descritos en [Activación de middleware basada en Factory en ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility).

La aplicación de ejemplo muestra middleware activado por:

* Convención. Para obtener más información sobre la activación de middleware convencional, consulte el tema [Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/).
* Una implementación de [IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware). La clase [IMiddlewareFactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) predeterminada activa el middleware.

Las implementaciones de middleware funcionan de forma idéntica y registran el valor proporcionado por un parámetro de cadena de consulta (`key`). El middleware usa un contexto de base de datos insertado (un servicio con ámbito) para registrar el valor de cadena de consulta en una base de datos en memoria.
