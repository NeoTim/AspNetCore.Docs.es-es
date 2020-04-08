---
title: Componentes con plantilla de Blazor en ASP.NET Core
author: guardrex
description: Descubra cómo los componentes con plantilla pueden aceptar una o varias plantillas de interfaz de usuario como parámetros, que se pueden usar como parte de la lógica de representación del componente.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: b57e3fe186402723607e90b1628062f602c77632
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2020
ms.locfileid: "79989493"
---
# <a name="aspnet-core-opno-locblazor-templated-components"></a>Componentes con plantilla de Blazor en ASP.NET Core

Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)

Los componentes con plantilla son componentes que aceptan una o varias plantillas de interfaz de usuario como parámetros, que se pueden usar como parte de la lógica de representación del componente. Los componentes con plantilla permiten crear componentes de nivel superior que son más reutilizables que los componentes normales. Estos son un par de ejemplos:

* Un componente de tabla que permite a un usuario especificar plantillas para el encabezado, las filas y el pie de página de la tabla.
* Un componente de lista que permite a un usuario especificar una plantilla para representar elementos en una lista.

## <a name="template-parameters"></a>Parámetros de plantilla

Un componente con plantilla se define especificando uno o más parámetros de componente de tipo `RenderFragment` o `RenderFragment<T>`. Un fragmento de representación representa un segmento de interfaz de usuario que se va a representar. `RenderFragment<T>` toma un parámetro de tipo que se puede especificar cuando se invoca el fragmento de representación.

Componente `TableTemplate`:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

Al usar un componente con plantilla, los parámetros de plantilla se pueden especificar utilizando los elementos secundarios que coinciden con los nombres de los parámetros (`TableHeader` y `RowTemplate` en el ejemplo siguiente):

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

> [!NOTE]
> La restricciones de tipo genérico se admitirán en una versión futura. Para obtener más información, vea [Permitir restricciones de tipo genérico (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).

## <a name="template-context-parameters"></a>Parámetros de contexto de la plantilla

Los argumentos de los componentes de tipo `RenderFragment<T>` pasados como elementos tienen un parámetro implícito denominado `context` (por ejemplo, en el ejemplo de código anterior, `@context.PetId`), pero puede cambiar el nombre del parámetro mediante el atributo `Context` en el elemento secundario. En el ejemplo siguiente, el atributo `RowTemplate` del elemento `Context` especifica el parámetro `pet`:

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

También puede especificar el atributo `Context` en el elemento de componente. El atributo `Context` especificado se aplica a todos los parámetros de plantilla especificados. Esto puede ser útil si desea especificar el nombre del parámetro de contenido para el contenido secundario implícito (sin ningún elemento secundario de ajuste). En el ejemplo siguiente, el atributo `Context` aparece en el elemento `TableTemplate` y se aplica a todos los parámetros de plantilla:

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

## <a name="generic-typed-components"></a>Componentes de tipo genérico

Los componentes con plantilla suelen tener tipos genéricos. Por ejemplo, se puede usar un componente `ListViewTemplate` genérico para representar valores `IEnumerable<T>`. Para definir un componente genérico, utilice la directiva [`@typeparam`](xref:mvc/views/razor#typeparam) para especificar parámetros de tipo:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

Al usar componentes de tipo genérico, el parámetro de tipo se infiere si es posible:

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

De lo contrario, el parámetro de tipo se debe especificar explícitamente mediante un atributo que coincida con el nombre del parámetro de tipo. En el ejemplo siguiente, `TItem="Pet"` especifica el tipo:

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
