---
title: Formularios y validación de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo usar los escenarios de formularios y validación de campos en Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: ec2bc2867acdd1c9be42f77cb38be36abb8c8108
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967485"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a>Formularios y validación de Blazor de ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)

Blazor admite los formularios y la validación a través de [anotaciones de datos](xref:mvc/models/validation).

El siguiente tipo `ExampleModel` define la lógica de validación a través de anotaciones de datos:

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Un formulario se define mediante el componente `EditForm`. En el siguiente formulario se reflejan los elementos, componentes y código de Razor típicos:

```razor
<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

En el ejemplo anterior:

* El formulario valida la entrada del usuario en el campo `name` por medio de la validación definida en el tipo `ExampleModel`. El modelo se crea en el bloque `@code` del componente y se conserva en un campo privado (`exampleModel`). El campo se asigna al atributo `Model` del elemento `<EditForm>`.
* El elemento `@bind-Value` del componente `InputText` enlaza:
  * La propiedad del modelo (`exampleModel.Name`) a la propiedad `Value` del componente `InputText`.
  * Un delegado de evento de cambio a la propiedad `ValueChanged` del componente `InputText`.
* El componente `DataAnnotationsValidator` adjunta la compatibilidad con la validación mediante anotaciones de datos.
* El componente `ValidationSummary` resume los mensajes de validación.
* `HandleValidSubmit` se desencadena cuando el formulario se envía correctamente (supera la validación).

Hay disponible un conjunto de componentes de entrada integrados para recibir y validar las entradas de usuario. Las entradas se validan cuando se cambian y cuando un formulario se envía. Los componentes de entrada disponibles se muestran en la siguiente tabla.

| Componente de entrada | Se representa como&hellip;       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

Todos los componentes de entrada, incluido `EditForm`, admiten atributos arbitrarios. Cualquier atributo que no coincida con un parámetro de componente se agrega al elemento HTML representado.

Los componentes de entrada proporcionan un comportamiento predeterminado de validación al editarse y cambiar su clase CSS para reflejar el estado del campo. Algunos componentes incluyen lógica de análisis de utilidad. Así, por ejemplo, `InputDate` e `InputNumber` tratan los valores no analizables sin problemas registrándolos como errores de validación. Los tipos con capacidad para aceptar valores nulos también admiten la nulabilidad del campo de destino (por ejemplo, `int?`).

El siguiente tipo `Starship` define una lógica de validación mediante un conjunto de propiedades y de anotaciones de datos mayor que el del tipo `ExampleModel` anterior:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

En el ejemplo anterior, `Description` es opcional porque no hay ninguna anotación de datos presente.

El siguiente formulario valida la entrada de usuario por medio de la validación definida en el modelo `Starship`:

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

`EditForm` crea un elemento `EditContext` como un [valor en cascada](xref:blazor/components#cascading-values-and-parameters) que lleva un seguimiento de los metadatos del proceso de edición, incluidos los campos que se han modificado y los mensajes de validación actuales. `EditForm` también proporciona eventos prácticos de envíos válidos y no válidos (`OnValidSubmit`, `OnInvalidSubmit`). También se puede usar `OnSubmit` para desencadenar la validación y comprobar los valores de campo con código de validación personalizado.

En el ejemplo siguiente:

* El método `HandleSubmit` se ejecuta cuando se selecciona el botón **Enviar**.
* El formulario se valida con el elemento `EditContext` de dicho formulario.
* El formulario se sigue validando pasando `EditContext` al método `ServerValidate` que llama a un punto de conexión de API web en el servidor (*no se muestra*).
* Se ejecutará más código en función del resultado de la validación del lado cliente y del lado servidor, mediante la comprobación de `isValid`.

```razor
<EditForm EditContext="@editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate() && 
            await ServerValidate(editContext);

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }

    private async Task<bool> ServerValidate(EditContext editContext)
    {
        var serverChecksValid = ...

        return serverChecksValid;
    }
}
```

## <a name="inputtext-based-on-the-input-event"></a>InputText basado en el evento de entrada

Use el componente `InputText` para crear un componente personalizado que usa el evento `input` en lugar del evento `change`.

Cree un componente con el siguiente marcado y use dicho componente igual que `InputText`:

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a>Trabajo con botones de radio

Al trabajar con botones de radio en un formulario, el enlace de datos se controla de manera diferente que otros elementos, ya que los botones de radio se evalúan como un grupo. El valor de cada botón de radio es fijo, pero el valor del grupo de botones de radio es el valor del botón de radio seleccionado. El ejemplo siguiente muestra cómo:

* Controlar el enlace de datos en un grupo de botones de radio
* Admitir la validación usando un componente `InputRadio` personalizado

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

El siguiente elemento `EditForm` usa el componente `InputRadio` anterior para obtener y validar una clasificación del usuario:

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="validation-support"></a>Compatibilidad con la validación

El componente `DataAnnotationsValidator` adjunta la compatibilidad con la validación mediante anotaciones de datos al elemento `EditContext` en cascada. Este gesto explícito es necesario para habilitar la compatibilidad con la validación mediante anotaciones. Para usar un sistema de validación distinto al de las anotaciones de datos, reemplace `DataAnnotationsValidator` por una implementación personalizada. La implementación de ASP.NET Core está disponible para su inspección en el origen de referencia: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

Blazor realiza dos tipos de validación:

* La *validación del campo* se realiza cuando el usuario sale de un campo usando la tecla TAB. Durante una validación del campo, el componente `DataAnnotationsValidator` asocia al campo todos los resultados de validación notificados.
* La *validación del modelo* se realiza cuando el usuario envía el formulario. Durante una validación del modelo, el componente `DataAnnotationsValidator` intenta determinar el campo en función del nombre del miembro que se indica en el resultado de la validación. Los resultados de validación que no están asociados a un miembro individual se asocian al modelo en lugar de a un campo.

### <a name="validation-summary-and-validation-message-components"></a>Resumen de validación y componentes de los mensajes de validación

El componente `ValidationSummary`, que resume todos los mensajes de validación, es similar a la [aplicación auxiliar de etiquetas ValidationSummary](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):

```razor
<ValidationSummary />
```

Mensajes de validación de salida de un modelo específico con el parámetro `Model`:
  
```razor
<ValidationSummary Model="@starship" />
```

El componente `ValidationMessage`, que muestra los mensajes de validación de un campo específico, es similar a la [aplicación auxiliar de etiquetas Validation Message](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Especifique el campo de validación con el atributo `For` y una expresión lambda donde se indique la propiedad del modelo:

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

Los componentes `ValidationMessage` y `ValidationSummary` admiten atributos arbitrarios. Cualquier atributo que no coincida con un parámetro de componente se agrega a los elementos `<div>` o `<ul>` generados.

### <a name="custom-validation-attributes"></a>Atributos de validación personalizados

Para asegurarnos de que un resultado de validación está correctamente asociado a un campo cuando se usa un [atributo de validación personalizado](xref:mvc/models/validation#custom-attributes), pase el elemento <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> del contexto de validación al crear el elemento <xref:System.ComponentModel.DataAnnotations.ValidationResult>:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class MyCustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

### <a name="blazor-data-annotations-validation-package"></a>Paquete de validación de anotaciones de datos de Blazor

[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) es un paquete que llena los huecos de la experiencia de validación mediante el componente `DataAnnotationsValidator`. Actualmente, el paquete está en fase *experimental*.

### <a name="compareproperty-attribute"></a>Atributo [CompareProperty]

<xref:System.ComponentModel.DataAnnotations.CompareAttribute> no funciona bien con el componente `DataAnnotationsValidator` porque no asocia el resultado de la validación a un miembro específico. Esto puede generar un comportamiento incoherente entre la validación de nivel de campo y el momento en que el modelo completo se valida al enviarse. El paquete *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) incluye un atributo de validación más, `ComparePropertyAttribute`, que pone fin a estas limitaciones. En una aplicación Blazor, `[CompareProperty]` es un reemplazo directo del atributo `[Compare]`.

### <a name="nested-models-collection-types-and-complex-types"></a>Tipos de colección, tipos complejos y modelos anidados

Blazor proporciona compatibilidad para validar la entrada del formulario mediante anotaciones de datos con el elemento integrado `DataAnnotationsValidator`, pero `DataAnnotationsValidator` solo valida las propiedades de nivel superior del modelo enlazadas al formulario que no son propiedades de tipos complejos o de colección.

Para validar el gráfico de objetos completo del modelo enlazado, incluidas las propiedades de tipos complejos o de colección, use el elemento `ObjectGraphDataAnnotationsValidator` proporcionado por el paquete *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation):

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

Anote las propiedades del modelo con `[ValidateComplexType]`. En las siguientes clases de modelo, la clase `ShipDescription` contiene más anotaciones de datos para validar cuando el modelo está enlazado al formulario:

*Starship.cs*:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

*ShipDescription.cs*:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a>Habilitar el botón Enviar según la validación del formulario

Para habilitar el botón Enviar según la validación del formulario:

* Use el elemento `EditContext` del formulario para asignar el modelo cuando el componente se inicialice.
* Valide el formulario en la devolución de llamada `OnFieldChanged` del contexto para habilitar y deshabilitar el botón Enviar.
* Desenlace el controlador de eventos en el método `Dispose`. Para obtener más información, vea <xref:blazor/lifecycle#component-disposal-with-idisposable>.

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

En el ejemplo anterior, establezca `formInvalid` en `false` en los siguientes casos:

* El formulario se carga previamente con valores predeterminados válidos.
* Quiere habilitar el botón Enviar cuando el formulario se cargue.

Un efecto secundario del método anterior es que un componente `ValidationSummary` se rellena con campos no válidos después de que el usuario interactúe con algún campo. Este escenario se puede abordar de cualquiera de las siguientes maneras:

* No use un componente `ValidationSummary` en el formulario.
* Haga que el componente `ValidationSummary` esté visible cuando se seleccione el botón Enviar (por ejemplo, en un método `HandleValidSubmit`).

```razor
<EditForm EditContext="@editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```
