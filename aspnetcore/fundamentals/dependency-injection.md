---
title: Inserción de dependencias en ASP.NET Core
author: guardrex
description: Obtenga información sobre la manera en que ASP.NET Core implementa la inserción de dependencias y cómo se usa.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: fundamentals/dependency-injection
ms.openlocfilehash: a984bb766e6876db4f8ed4c850a1984ba87d627d
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022290"
---
# <a name="dependency-injection-in-aspnet-core"></a>Inserción de dependencias en ASP.NET Core

Por [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) y [Luke Latham](https://github.com/guardrex)

ASP.NET Core admite el patrón de diseño de software de inserción de dependencias (DI), que es una técnica para conseguir la [inversión de control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre clases y sus dependencias.

Para más información específica sobre la inserción de dependencias en los controladores MVC, vea <xref:mvc/controllers/dependency-injection>.

[Vea o descargue el código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Información general sobre la inserción de dependencias

Una *dependencia* es cualquier objeto requerido por otro objeto. Examine la siguiente clase `MyDependency` con un método `WriteMessage` del que dependen otras clases de una aplicación:

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

Se puede crear una instancia de la clase `MyDependency` para hacer que el método `WriteMessage` esté disponible para una clase. La clase `MyDependency` es una dependencia de la clase `IndexModel`:

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

La clase crea y depende directamente de la instancia `MyDependency`. Las dependencias de código (como en el ejemplo anterior) son problemáticas y deben evitarse por las siguientes razones:

* Para reemplazar `MyDependency` con una implementación diferente, se debe modificar la clase.
* Si `MyDependency` tiene dependencias, deben configurarse según la clase. En un proyecto grande con varias clases que dependen de `MyDependency`, el código de configuración se dispersa por la aplicación.
* Esta implementación es difícil para realizar pruebas unitarias. La aplicación debe usar una clase `MyDependency` como boceto o código auxiliar, que no es posible con este enfoque.

La inserción de dependencias aborda estos problemas mediante:

* Uso de una interfaz o clase base para abstraer la implementación de dependencias.
* Registro de la dependencia en un contenedor de servicios. ASP.NET Core proporciona un contenedor de servicios integrado, <xref:System.IServiceProvider>. Los servicios se registran en el método `Startup.ConfigureServices` de la aplicación.
* *Inserción* del servicio en el constructor de la clase en la que se usa. El marco de trabajo asume la responsabilidad de crear una instancia de la dependencia y de desecharla cuando ya no es necesaria.

En la [aplicación de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), la interfaz `IMyDependency` define un método que el servicio proporciona a la aplicación:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Esta interfaz se implementa mediante un tipo concreto, `MyDependency`:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency` solicita <xref:Microsoft.Extensions.Logging.ILogger`1> en su constructor. No es raro usar la inserción de dependencias de forma encadenada. Cada dependencia solicitada a su vez solicita sus propias dependencias. El contenedor resuelve las dependencias del gráfico y devuelve el servicio totalmente resuelto. El conjunto colectivo de dependencias que deben resolverse suele denominarse *árbol de dependencias*, *gráfico de dependencias* o *gráfico de objetos*.

`IMyDependency` y `ILogger<TCategoryName>` deben estar registrados en el contenedor de servicios. `IMyDependency` está registrado en `Startup.ConfigureServices`. `ILogger<TCategoryName>` está registrado en la infraestructura de abstracciones de registros, por lo que se trata de un [servicio proporcionado por el marco de trabajo](#framework-provided-services) registrado de forma predeterminada por el marco de trabajo.

El contenedor resuelve `ILogger<TCategoryName>` aprovechando las ventajas de los [tipos abiertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), lo que elimina la necesidad de registrar todos los [tipos construidos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):

```csharp
services.AddSingleton(typeof(ILogger<T>), typeof(Logger<T>));
```

En la aplicación de ejemplo, el servicio `IMyDependency` está registrado con el tipo concreto `MyDependency`. El registro abarca la duración del servicio como la duración de una única solicitud. Las [duraciones del servicio](#service-lifetimes) se describen más adelante en este tema.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Cada método de extensión `services.Add{SERVICE_NAME}` agrega servicios (y potencialmente los configura). Por ejemplo, `services.AddMvc()` agrega los servicios que Razor Pages y MVC requieren. Se recomienda que las aplicaciones sigan esta convención. Coloque los métodos de extensión en el espacio de nombres [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros del servicio.

Si el constructor del servicio requiere un [tipo integrado](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, se puede insertar mediante la [configuración](xref:fundamentals/configuration/index) o el [patrón de opciones](xref:fundamentals/configuration/options):

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

Se solicita una instancia del servicio mediante el constructor de una clase, en la que se usa el servicio y se asigna a un campo privado. El campo de utiliza para acceder al servicio, según sea necesario en la clase.

En la aplicación de ejemplo, la instancia `IMyDependency` se solicita y usa para llamar al método `WriteMessage` del servicio:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="framework-provided-services"></a>Servicios proporcionados por el marco de trabajo

El método `Startup.ConfigureServices` se encarga de definir los servicios que la aplicación usa, incluidas las características de plataforma como Entity Framework Core y ASP.NET Core MVC. Inicialmente, el valor `IServiceCollection` proporcionado a `ConfigureServices` tiene los siguientes servicios definidos (en función de [cómo se configurara el host](xref:fundamentals/index#host)):

| Tipo de servicio | Período de duración |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transitorio |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transitorio |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transitorio |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transitorio |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

Cuando un método de extensión de la colección de servicio está disponible para registrar un servicio (y sus servicios dependientes, si es necesario), la convención consiste en usar un solo método de extensión `Add{SERVICE_NAME}` para registrar todos los servicios requeridos por dicho servicio. El código siguiente es un ejemplo de cómo agregar servicios adicionales al contenedor mediante los métodos de extensión [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext), <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> y <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddMvc();
}
```

Para obtener más información, consulte la clase <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> en la documentación de la API.

## <a name="service-lifetimes"></a>Duraciones de servicios

Elija una duración adecuada para cada servicio registrado. Los servicios de ASP.NET Core pueden configurarse con las duraciones siguientes:

### <a name="transient"></a>Transitorio

Los servicios de duración transitoria (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se crean cada vez que el contenedor del servicio los solicita. Esta duración funciona mejor para servicios sin estado ligeros.

### <a name="scoped"></a>Con ámbito

Los servicios de duración con ámbito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) se crean una vez por solicitud del cliente (conexión).

> [!WARNING]
> Si usa un servicio con ámbito en un middleware, inserte el servicio en el método `Invoke` o `InvokeAsync`. No lo inserte a través de la inserción de constructores, porque ello hace que el servicio se comporte como un singleton. Para más información, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Singleton

Los servicios con duración Singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) se crean la primera vez que se solicitan, o bien al ejecutar `Startup.ConfigureServices` y especificar una instancia con el registro del servicio. Cada solicitud posterior usa la misma instancia. Si la aplicación requiere un comportamiento de singleton, se recomienda permitir que el contenedor de servicios administre la duración del servicio. No implemente el patrón de diseño de singleton y proporcione el código de usuario para administrar la duración del objeto en la clase.

> [!WARNING]
> Es peligroso resolver un servicio con ámbito desde un singleton. Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.

## <a name="service-registration-methods"></a>Métodos de registro del servicio

Cada método de extensión de registro del servicio ofrece sobrecargas útiles en escenarios específicos.

| Método | Automático<br>objeto<br>eliminación | Múltiple<br>implementaciones | Transferencia de argumentos |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Ejemplo:<br>`services.AddScoped<IMyDep, MyDep>();` | Sí | Sí | No |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Ejemplos:<br>`services.AddScoped<IMyDep>(sp => new MyDep());`<br>`services.AddScoped<IMyDep>(sp => new MyDep("A string!"));` | Sí | Sí | Sí |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Ejemplo:<br>`services.AddScoped<MyDep>();` | Sí | No | No |
| `Add{LIFETIME}<{SERVICE}>(new {IMPLEMENTATION})`<br>Ejemplos:<br>`services.AddScoped<IMyDep>(new MyDep());`<br>`services.AddScoped<IMyDep>(new MyDep("A string!"));` | No | Sí | Sí |
| `Add{LIFETIME}(new {IMPLEMENTATION})`<br>Ejemplos:<br>`services.AddScoped(new MyDep());`<br>`services.AddScoped(new MyDep("A string!"));` | No | No | Sí |

Para obtener más información sobre el tipo de eliminación, consulte la sección [Eliminación de servicios](#disposal-of-services). Un escenario común para varias implementaciones es [utilizar tipos de simulación para las pruebas](xref:test/integration-tests#inject-mock-services).

Los métodos `TryAdd{LIFETIME}` solo registran el servicio si no hay ya una implementación registrada.

En el ejemplo siguiente, la primera línea registra `MyDependency` para `IMyDependency`. La segunda línea no tiene ningún efecto porque `IMyDependency` ya tiene una implementación registrada:

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

Para obtener más información, consulte:

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

Los métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registran solo el servicio si no hay ya una implementación *del mismo tipo*. A través de `IEnumerable<{SERVICE}>` se resuelven varios servicios. Al registrar los servicios, el desarrollador solo quiere agregar una instancia si no se ha agregado ya una del mismo tipo. Por lo general, este método lo utilizan los creadores de bibliotecas para evitar registrar dos copias de una instancia en el contenedor.

En el ejemplo siguiente, la primera línea registra `MyDep` para `IMyDep1`. La segunda línea registra `MyDep` para `IMyDep2`. La tercera línea no tiene ningún efecto porque `IMyDep1` ya tiene una implementación registrada de `MyDep`:

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>Comportamiento de inserción de constructor

Los servicios se pueden resolver mediante dos mecanismos:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: permite la creación de objetos sin registrar el servicio en el contenedor de inserción de dependencias. `ActivatorUtilities` se utiliza con abstracciones orientadas al usuario, como asistentes de etiquetas, controladores MVC y enlazadores de modelos.

Los constructores pueden aceptar argumentos que no se proporcionan mediante la inserción de dependencias, pero los argumentos deben asignar valores predeterminados.

Cuando se resuelven los servicios mediante `IServiceProvider` o `ActivatorUtilities`, la inserción del constructor requiere un constructor *público*.

Cuando se resuelven los servicios mediante `ActivatorUtilities`, la inserción del constructor requiere que exista solo un constructor aplicable. Se admiten las sobrecargas de constructor, pero solo puede existir una sobrecarga cuyos argumentos pueda cumplir la inserción de dependencias.

## <a name="entity-framework-contexts"></a>Contextos de Entity Framework

Los contextos de Entity Framework normalmente se agregan al contenedor de servicios mediante la [duración con ámbito](#service-lifetimes) porque las operaciones de base de datos de aplicación web se suelen limitar a la solicitud de cliente. La duración predeterminada se limita si no se especifica una duración mediante una sobrecarga de [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) al registrar el contexto de base de datos. En los servicios de una duración determinada no se debe usar un contexto de base de datos con una duración más corta que el servicio.

## <a name="lifetime-and-registration-options"></a>Opciones de registro y duración

Para mostrar la diferencia entre la duración y las opciones de registro, considere las siguientes interfaces que representan tareas como una operación con un identificador único, `OperationId`. Según cómo esté configurada la duración de un servicio de operaciones para las interfaces siguientes, el contenedor proporciona la misma instancia del servicio u otra distinta cuando así lo solicita la clase:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Las interfaces se implementan en la clase `Operation`. El constructor `Operation` genera un GUID en caso de que no se proporcione uno:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

Se registra una instancia de `OperationService` que depende de cada uno de los demás tipos `Operation`. Cuando `OperationService` se solicita con la inserción de dependencias, recibe una instancia nueva de cada servicio o una instancia existente en función de la duración de los servicios dependientes.

* Si se crean servicios transitorios cuando se solicitan al contenedor, el elemento `OperationId` del servicio `IOperationTransient` es diferente del objeto `OperationId` de `OperationService`. `OperationService` recibe una instancia nueva de la clase `IOperationTransient`. La nueva instancia produce un objeto `OperationId` diferente.
* Si se crean servicios con ámbito por solicitud de cliente, el objeto `OperationId` del servicio `IOperationScoped` es el mismo que para `OperationService` dentro de la solicitud de cliente. Entre las solicitudes de cliente, ambos servicios comparten un valor `OperationId` diferente.
* Si se crean servicios singleton y servicios de instancia singleton una vez y se usan en todas las solicitudes de cliente y servicios, el objeto `OperationId` es constante en todas las solicitudes de servicio.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

En `Startup.ConfigureServices`, cada tipo se agrega al contenedor según su duración con nombre:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

El servicio `IOperationSingletonInstance` usa una instancia específica con un identificador conocido de `Guid.Empty`. Resulta evidente identificar cuándo este tipo está en uso (su GUID es todo ceros).

La aplicación de ejemplo muestra las duraciones de los objetos dentro y entre las solicitudes individuales. El objeto `IndexModel` de la aplicación de ejemplo solicita cada tipo de `IOperation` y `OperationService`. Después, la página muestra todos los valores `OperationId` del servicio y la clase del modelo de página mediante las asignaciones de propiedades:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

Las dos salidas siguientes muestran el resultado de dos solicitudes:

**Primera solicitud:**

Operaciones del controlador:

Transient: d233e165-f417-469b-a866-1cf1935d2518  
Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Operaciones `OperationService`:

Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

**Segunda solicitud:**

Operaciones del controlador:

Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Operaciones `OperationService`:

Transitorio: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Observe cuál de los valores `OperationId` varía dentro de una solicitud y entre solicitudes:

* Los objetos *Transient* siempre son diferentes. El valor `OperationId` transitorio de la primera y la segunda solicitud de cliente varía tanto para las operaciones `OperationService` como entre solicitudes de cliente. Se proporciona una nueva instancia para cada solicitud de servicio y solicitud de cliente.
* Los objetos *con ámbito* son iguales dentro de una solicitud de cliente, pero varían entre solicitudes de cliente.
* Los objetos *singleton* son iguales para todos los objetos y solicitudes, independientemente de si se proporciona una instancia `Operation` en `Startup.ConfigureServices`.

## <a name="call-services-from-main"></a>Llamada a servicios desde main

Cree un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver un servicio con ámbito dentro del ámbito de la aplicación. Este método resulta útil para tener acceso a un servicio con ámbito durante el inicio para realizar tareas de inicialización. En el siguiente ejemplo se indica cómo obtener un contexto para `MyScopedService` en `Program.Main`:

```csharp
public static void Main(string[] args)
{
    var host = CreateWebHostBuilder(args).Build();

    using (var serviceScope = host.Services.CreateScope())
    {
        var services = serviceScope.ServiceProvider;

        try
        {
            var serviceContext = services.GetRequiredService<MyScopedService>();
            // Use the context here
        }
        catch (Exception ex)
        {
            var logger = services.GetRequiredService<ILogger<Program>>();
            logger.LogError(ex, "An error occurred.");
        }
    }

    host.Run();
}
```

## <a name="scope-validation"></a>Validación del ámbito

Cuando la aplicación se ejecuta en el entorno de desarrollo, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:

* Los servicios con ámbito no se resuelven directa o indirectamente desde el proveedor de servicios raíz.
* Los servicios con ámbito no se insertan directa o indirectamente en singletons.

El proveedor de servicios raíz se crea cuando se llama a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>. La vigencia del proveedor de servicios raíz es la misma que la de la aplicación o el servidor cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.

De la eliminación de los servicios con ámbito se encarga el contenedor que los creó. Si un servicio con ámbito se crea en el contenedor raíz, su vigencia sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación o el servidor se cierran. Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.

Para más información, consulte <xref:fundamentals/host/web-host#scope-validation>.

## <a name="request-services"></a>Servicios de solicitud

Los servicios disponibles en una solicitud de ASP.NET Core desde `HttpContext` se exponen mediante la colección [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).

Los servicios de solicitud representan los servicios configurados y solicitados como parte de la aplicación. Cuando los objetos especifican dependencias, estas se cumplen mediante los tipos que se encuentran en `RequestServices`, no en `ApplicationServices`.

Por lo general, la aplicación no debe usar estas propiedades directamente. En su lugar, solicite los tipos que las clases requieren mediante el constructor de clases y permita que el marco de trabajo inserte las dependencias. Esto produce clases que son más fáciles de probar.

> [!NOTE]
> Se recomienda que solicite las dependencias como parámetros del constructor para obtener acceso a la colección `RequestServices`.

## <a name="design-services-for-dependency-injection"></a>Diseño de servicios para la inserción de dependencias

Los procedimientos recomendados son:

* Diseñar servicios para usar la inserción de dependencias a fin de obtener sus dependencias.
* Evite las llamadas de método estático y con estado.
* Evitar la creación directa de instancias de clases dependientes dentro de los servicios. La creación directa de instancias se acopla al código de una implementación particular.
* Cree clases de aplicación pequeñas, bien factorizadas y probadas con facilidad.

Si una clase parece tener demasiadas dependencias insertadas, suele indicar que la clase tiene demasiadas responsabilidades y que esto infringe el [principio de responsabilidad única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility). Trate de mover algunas de las responsabilidades de la clase a una nueva para intentar refactorizarla. Tenga en cuenta que las clases del modelo de página de Razor Pages y las clases de controlador MVC deben centrarse en aspectos de la interfaz de usuario. Los detalles de implementación de las reglas de negocio y del acceso a datos se deben mantener en las clases pertinentes para [cada uno de estos aspectos](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).

### <a name="disposal-of-services"></a>Eliminación de servicios

El contenedor llama a <xref:System.IDisposable.Dispose*> para los tipos <xref:System.IDisposable> que crea. Si se agrega una instancia al contenedor por código de usuario, no se elimina automáticamente.

```csharp
// Services that implement IDisposable:
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}
public class Service3 : IDisposable {}

public interface ISomeService {}
public class SomeServiceImplementation : ISomeService, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    // The container creates the following instances and disposes them automatically:
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<ISomeService>(sp => new SomeServiceImplementation());

    // The container doesn't create the following instances, so it doesn't dispose of
    // the instances automatically:
    services.AddSingleton<Service3>(new Service3());
    services.AddSingleton(new Service3());
}
```

## <a name="default-service-container-replacement"></a>Reemplazo del contenedor de servicios predeterminado

El contenedor de servicios integrado está pensado para atender las necesidades del marco de trabajo y de la mayoría de las aplicaciones de consumidor. Se recomienda usar el contenedor integrado a menos que se necesite una característica específica no admitida por el contenedor. Algunas de las características admitidas en contenedores de terceros no se incluyen en el contenedor integrado:

* Inserción de propiedades
* Inserción basada en nombres
* Contenedores secundarios
* Administración personalizada del ciclo de vida
* Compatibilidad con `Func<T>` para la inicialización diferida

Vea el [archivo readme.md de inserción de dependencias](https://github.com/aspnet/Extensions/tree/master/src/DependencyInjection) para obtener una lista de algunos de los contenedores que admiten adaptadores.

En el ejemplo siguiente se reemplaza el contenedor integrado por [Autofac](https://autofac.org/):

* Instale los paquetes de contenedor adecuados:

  * [Autofac](https://www.nuget.org/packages/Autofac/)
  * [Autofac.Extensions.DependencyInjection](https://www.nuget.org/packages/Autofac.Extensions.DependencyInjection/)

* Configure el contenedor en `Startup.ConfigureServices` y devuelva `IServiceProvider`:

    ```csharp
    public IServiceProvider ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        // Add other framework services

        // Add Autofac
        var containerBuilder = new ContainerBuilder();
        containerBuilder.RegisterModule<DefaultModule>();
        containerBuilder.Populate(services);
        var container = containerBuilder.Build();
        return new AutofacServiceProvider(container);
    }
    ```

    Para usar un contenedor de terceros, `Startup.ConfigureServices` debe devolver `IServiceProvider`.

* Configure Autofac en `DefaultModule`:

    ```csharp
    public class DefaultModule : Module
    {
        protected override void Load(ContainerBuilder builder)
        {
            builder.RegisterType<CharacterRepository>().As<ICharacterRepository>();
        }
    }
    ```

En tiempo de ejecución, se usa Autofac para resolver tipos e insertar dependencias. Para más información sobre el uso de Autofac con ASP.NET Core, vea la [documentación sobre Autofac](https://docs.autofac.org/en/latest/integration/aspnetcore.html).

### <a name="thread-safety"></a>Seguridad para subprocesos

Cree servicios de singleton seguros para subprocesos. Si un servicio de singleton tiene una dependencia en un servicio transitorio, es posible que este también deba ser seguro para subprocesos, según cómo lo use el singleton.

El patrón de diseño Factory Method de un servicio único, como el segundo argumento para [AddSingleton\<TService (IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), no necesita ser seguro para subprocesos. Al igual que un constructor de tipos (`static`), se garantiza que se le llame una vez mediante un único subproceso.

## <a name="recommendations"></a>Recomendaciones

* No se admite la resolución de servicio basada en `async/await` y `Task`. C# no admite los constructores asincrónicos, por lo que el patrón recomendado es usar métodos asincrónicos después de resolver el servicio de manera sincrónica.

* Evite almacenar datos y configuraciones directamente en el contenedor de servicios. Por ejemplo, el carro de la compra de un usuario no debería agregarse al contenedor de servicios. La configuración debe usar el [patrón de opciones](xref:fundamentals/configuration/options). Del mismo modo, evite los objetos de tipo "contenedor de datos" que solo existen para permitir el acceso a otro objeto. Es mejor solicitar el elemento real que se necesita mediante la inserción de dependencias.

* Evite el acceso estático a servicios (por ejemplo, escribiendo de forma estática [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usarlo en otro lugar).

* Evite el uso del *patrón del localizador de servicios*. Por ejemplo, no invoque a <xref:System.IServiceProvider.GetService*> para obtener una instancia de servicio si puede usar la inserción de dependencias en su lugar:

  **Incorrecto:**

  ```csharp
  public class MyClass()
  {
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

  **Correcto**:

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

* Otra variación del localizador de servicios que se debe evitar es insertar una fábrica que resuelva dependencias en tiempo de ejecución. Estas dos prácticas combinan estrategias de [Inversión de control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).

* Evite el acceso estático a `HttpContext` (por ejemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

Al igual que sucede con todas las recomendaciones, podría verse en una situación que le obligue a ignorar alguna de ellas. Las excepciones son poco frecuentes; principalmente en casos especiales dentro del propio marco de trabajo.

La inserción de dependencias es una *alternativa* a los patrones de acceso a objetos estáticos o globales. No podrá aprovechar las ventajas de la inserción de dependencias si la combina con el acceso a objetos estáticos.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Escritura de código limpio en ASP.NET Core con inserción de dependencias (MSDN)](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [Principio de dependencias explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Los contenedores de inversión de control y el patrón de inserción de dependencias (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)
* [Cómo registrar un servicio con varias interfaces de inserción de dependencias de ASP.NET Core](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)
