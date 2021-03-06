---
title: Virtualización de componentes de ASP.NET Core Blazor
author: guardrex
description: Información sobre cómo usar la virtualización de componentes en aplicaciones de ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/virtualization
ms.openlocfilehash: eafad420d72a974cc64ebfd6abb3eff2d73a115d
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805562"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a>Virtualización de componentes de ASP.NET Core Blazor

Por [Daniel Roth](https://github.com/danroth27)

Mejore el rendimiento percibido de la representación de componentes usando la compatibilidad de virtualización integrada del marco Blazor. La virtualización es una técnica para limitar la representación de la interfaz de usuario a únicamente las partes visibles actualmente. Por ejemplo, la virtualización es útil cuando la aplicación debe representar una lista larga de elementos y solo es necesario que haya un subconjunto de elementos visible en un momento dado. Blazor proporciona el componente `Virtualize`, que se puede usar para agregar virtualización a los componentes de una aplicación.

Sin la virtualización, una lista típica podría usar un bucle [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) de C# para representar cada elemento de la lista:

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

Si la lista contiene miles de elementos, la representación de la lista podría tardar mucho tiempo, con el consiguiente retraso de la interfaz de usuario que puede experimentarse.

En lugar de representar todos y cada uno de los elementos de la lista a la vez, reemplace el bucle [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) por el componente `Virtualize` y especifique un origen de elemento fijo con `Items`. Solo se representarán los elementos visibles actualmente:

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

Si no especifica un contexto en el componente con `Context`, use el valor de `context` (`@context.{PROPERTY}`) en la plantilla de contenido del elemento:

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

El componente `Virtualize` calcula el número de elementos que se van a representar en función del alto del contenedor y del tamaño de los elementos representados.

El contenido del elemento para el componente `Virtualize` puede incluir lo siguiente:

* HTML sin formato y código Razor, tal como se muestra en el ejemplo anterior.
* Uno o más componentes Razor.
* Una combinación de componentes HTML/Razor y Razor.

## <a name="item-provider-delegate"></a>Delegado de proveedor de elementos

Si no se quieren cargar todos los elementos en la memoria, se puede especificar un método de delegado de proveedor de elementos en el parámetro `ItemsProvider` del componente, que recupera los elementos solicitados a petición de manera asincrónica:

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

El proveedor de elementos recibe un objeto `ItemsProviderRequest`, que especifica el número necesario de elementos empezando por un índice de inicio específico. Luego, el proveedor de elementos recupera los elementos solicitados de una base de datos u otro servicio y los devuelve como un objeto `ItemsProviderResult<TItem>` junto con un recuento total de elementos. El proveedor de elementos puede elegir entre recuperar los elementos con cada solicitud o almacenarlos en la memoria caché para que estén disponibles fácilmente. No intente usar un proveedor de elementos y asignar una colección a `Items` con el mismo componente `Virtualize`.

En el siguiente ejemplo se cargan empleados desde `EmployeeService`:

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

## <a name="placeholder"></a>Marcador de posición

Dado que la solicitud de elementos de un origen de datos remoto puede tardar tiempo, existe la posibilidad de representar un marcador de posición (`<Placeholder>...</Placeholder>`) hasta que los datos del elemento estén disponibles:

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a>Tamaño del elemento

El tamaño de cada elemento en píxeles se puede establecer con `ItemSize` (valor predeterminado: 50 px):

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a>Recuento de sobrebarridos

`OverscanCount` determina el número de elementos adicionales que se representan antes y después del área visible. Esta configuración ayuda a reducir la frecuencia de representación durante el desplazamiento. Pese a esto, unos valores superiores hacen que se representen más elementos en la página (valor predeterminado: 3):

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a>Cambios de estado

Al realizar cambios en los elementos representados por el componente `Virtualize`, llame a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> para forzar que el componente vuelva a evaluarse y representarse.
