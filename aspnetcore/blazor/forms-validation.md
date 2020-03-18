---
title: Formularios y validación de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo usar los escenarios de formularios y validación de campos en Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 2758bcbbc76c8a59716fe224dd2deb4ca8c06929
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648455"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="36b9c-103">Formularios y validación de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36b9c-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="36b9c-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="36b9c-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="36b9c-105">Blazor admite los formularios y la validación a través de [anotaciones de datos](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="36b9c-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="36b9c-106">El siguiente tipo `ExampleModel` define la lógica de validación a través de anotaciones de datos:</span><span class="sxs-lookup"><span data-stu-id="36b9c-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="36b9c-107">Un formulario se define mediante el componente `EditForm`.</span><span class="sxs-lookup"><span data-stu-id="36b9c-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="36b9c-108">En el siguiente formulario se reflejan los elementos, componentes y código de Razor típicos:</span><span class="sxs-lookup"><span data-stu-id="36b9c-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@_exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="_exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel _exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="36b9c-109">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="36b9c-109">In the preceding example:</span></span>

* <span data-ttu-id="36b9c-110">El formulario valida la entrada del usuario en el campo `name` por medio de la validación definida en el tipo `ExampleModel`.</span><span class="sxs-lookup"><span data-stu-id="36b9c-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="36b9c-111">El modelo se crea en el bloque `@code` del componente y se conserva en un campo privado (`_exampleModel`).</span><span class="sxs-lookup"><span data-stu-id="36b9c-111">The model is created in the component's `@code` block and held in a private field (`_exampleModel`).</span></span> <span data-ttu-id="36b9c-112">El campo se asigna al atributo `Model` del elemento `<EditForm>`.</span><span class="sxs-lookup"><span data-stu-id="36b9c-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="36b9c-113">El elemento `@bind-Value` del componente `InputText` enlaza:</span><span class="sxs-lookup"><span data-stu-id="36b9c-113">The `InputText` component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="36b9c-114">La propiedad del modelo (`_exampleModel.Name`) a la propiedad `Value` del componente `InputText`.</span><span class="sxs-lookup"><span data-stu-id="36b9c-114">The model property (`_exampleModel.Name`) to the `InputText` component's `Value` property.</span></span>
  * <span data-ttu-id="36b9c-115">Un delegado de evento de cambio a la propiedad `ValueChanged` del componente `InputText`.</span><span class="sxs-lookup"><span data-stu-id="36b9c-115">A change event delegate to the `InputText` component's `ValueChanged` property.</span></span>
* <span data-ttu-id="36b9c-116">El componente `DataAnnotationsValidator` adjunta la compatibilidad con la validación mediante anotaciones de datos.</span><span class="sxs-lookup"><span data-stu-id="36b9c-116">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="36b9c-117">El componente `ValidationSummary` resume los mensajes de validación.</span><span class="sxs-lookup"><span data-stu-id="36b9c-117">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="36b9c-118">`HandleValidSubmit` se desencadena cuando el formulario se envía correctamente (supera la validación).</span><span class="sxs-lookup"><span data-stu-id="36b9c-118">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="36b9c-119">Hay disponible un conjunto de componentes de entrada integrados para recibir y validar las entradas de usuario.</span><span class="sxs-lookup"><span data-stu-id="36b9c-119">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="36b9c-120">Las entradas se validan cuando se cambian y cuando un formulario se envía.</span><span class="sxs-lookup"><span data-stu-id="36b9c-120">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="36b9c-121">Los componentes de entrada disponibles se muestran en la siguiente tabla.</span><span class="sxs-lookup"><span data-stu-id="36b9c-121">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="36b9c-122">Componente de entrada</span><span class="sxs-lookup"><span data-stu-id="36b9c-122">Input component</span></span> | <span data-ttu-id="36b9c-123">Se representa como&hellip;</span><span class="sxs-lookup"><span data-stu-id="36b9c-123">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="36b9c-124">Todos los componentes de entrada, incluido `EditForm`, admiten atributos arbitrarios.</span><span class="sxs-lookup"><span data-stu-id="36b9c-124">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="36b9c-125">Cualquier atributo que no coincida con un parámetro de componente se agrega al elemento HTML representado.</span><span class="sxs-lookup"><span data-stu-id="36b9c-125">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="36b9c-126">Los componentes de entrada proporcionan un comportamiento predeterminado de validación al editarse y cambiar su clase CSS para reflejar el estado del campo.</span><span class="sxs-lookup"><span data-stu-id="36b9c-126">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="36b9c-127">Algunos componentes incluyen lógica de análisis de utilidad.</span><span class="sxs-lookup"><span data-stu-id="36b9c-127">Some components include useful parsing logic.</span></span> <span data-ttu-id="36b9c-128">Así, por ejemplo, `InputDate` e `InputNumber` tratan los valores no analizables sin problemas registrándolos como errores de validación.</span><span class="sxs-lookup"><span data-stu-id="36b9c-128">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="36b9c-129">Los tipos con capacidad para aceptar valores nulos también admiten la nulabilidad del campo de destino (por ejemplo, `int?`).</span><span class="sxs-lookup"><span data-stu-id="36b9c-129">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="36b9c-130">El siguiente tipo `Starship` define una lógica de validación mediante un conjunto de propiedades y de anotaciones de datos mayor que el del tipo `ExampleModel` anterior:</span><span class="sxs-lookup"><span data-stu-id="36b9c-130">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="36b9c-131">En el ejemplo anterior, `Description` es opcional porque no hay ninguna anotación de datos presente.</span><span class="sxs-lookup"><span data-stu-id="36b9c-131">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="36b9c-132">El siguiente formulario valida la entrada de usuario por medio de la validación definida en el modelo `Starship`:</span><span class="sxs-lookup"><span data-stu-id="36b9c-132">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@_starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="_starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="_starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="_starship.Classification">
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
            <InputNumber @bind-Value="_starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="_starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="_starship.ProductionDate" />
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
    private Starship _starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="36b9c-133">`EditForm` crea un elemento `EditContext` como un [valor en cascada](xref:blazor/components#cascading-values-and-parameters) que lleva un seguimiento de los metadatos del proceso de edición, incluidos los campos que se han modificado y los mensajes de validación actuales.</span><span class="sxs-lookup"><span data-stu-id="36b9c-133">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="36b9c-134">`EditForm` también proporciona eventos prácticos de envíos válidos y no válidos (`OnValidSubmit`, `OnInvalidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="36b9c-134">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="36b9c-135">También se puede usar `OnSubmit` para desencadenar la validación y comprobar los valores de campo con código de validación personalizado.</span><span class="sxs-lookup"><span data-stu-id="36b9c-135">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="36b9c-136">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="36b9c-136">In the following example:</span></span>

* <span data-ttu-id="36b9c-137">El método `HandleSubmit` se ejecuta cuando se selecciona el botón **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="36b9c-137">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="36b9c-138">El formulario se valida con el elemento `EditContext` de dicho formulario.</span><span class="sxs-lookup"><span data-stu-id="36b9c-138">The form is validated using the form's `EditContext`.</span></span>
* <span data-ttu-id="36b9c-139">El formulario se sigue validando pasando `EditContext` al método `ServerValidate` que llama a un punto de conexión de API web en el servidor (*no se muestra*).</span><span class="sxs-lookup"><span data-stu-id="36b9c-139">The form is further validated by passing the `EditContext` to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="36b9c-140">Se ejecutará más código en función del resultado de la validación del lado cliente y del lado servidor, mediante la comprobación de `isValid`.</span><span class="sxs-lookup"><span data-stu-id="36b9c-140">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

```razor
<EditForm EditContext="@_editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = _editContext.Validate() && 
            await ServerValidate(_editContext);

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="36b9c-141">InputText basado en el evento de entrada</span><span class="sxs-lookup"><span data-stu-id="36b9c-141">InputText based on the input event</span></span>

<span data-ttu-id="36b9c-142">Use el componente `InputText` para crear un componente personalizado que usa el evento `input` en lugar del evento `change`.</span><span class="sxs-lookup"><span data-stu-id="36b9c-142">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="36b9c-143">Cree un componente con el siguiente marcado y use dicho componente igual que `InputText`:</span><span class="sxs-lookup"><span data-stu-id="36b9c-143">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="36b9c-144">Trabajo con botones de radio</span><span class="sxs-lookup"><span data-stu-id="36b9c-144">Work with radio buttons</span></span>

<span data-ttu-id="36b9c-145">Al trabajar con botones de radio en un formulario, el enlace de datos se controla de manera diferente que otros elementos, ya que los botones de radio se evalúan como un grupo.</span><span class="sxs-lookup"><span data-stu-id="36b9c-145">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="36b9c-146">El valor de cada botón de radio es fijo, pero el valor del grupo de botones de radio es el valor del botón de radio seleccionado.</span><span class="sxs-lookup"><span data-stu-id="36b9c-146">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="36b9c-147">El ejemplo siguiente muestra cómo:</span><span class="sxs-lookup"><span data-stu-id="36b9c-147">The following example shows how to:</span></span>

* <span data-ttu-id="36b9c-148">Controlar el enlace de datos en un grupo de botones de radio</span><span class="sxs-lookup"><span data-stu-id="36b9c-148">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="36b9c-149">Admitir la validación usando un componente `InputRadio` personalizado</span><span class="sxs-lookup"><span data-stu-id="36b9c-149">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="36b9c-150">El siguiente elemento `EditForm` usa el componente `InputRadio` anterior para obtener y validar una clasificación del usuario:</span><span class="sxs-lookup"><span data-stu-id="36b9c-150">The following `EditForm` uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="_model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="_model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @_model.Rating</p>

@code {
    private Model _model = new Model();

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

## <a name="validation-support"></a><span data-ttu-id="36b9c-151">Compatibilidad con la validación</span><span class="sxs-lookup"><span data-stu-id="36b9c-151">Validation support</span></span>

<span data-ttu-id="36b9c-152">El componente `DataAnnotationsValidator` adjunta la compatibilidad con la validación mediante anotaciones de datos al elemento `EditContext` en cascada.</span><span class="sxs-lookup"><span data-stu-id="36b9c-152">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="36b9c-153">Este gesto explícito es necesario para habilitar la compatibilidad con la validación mediante anotaciones.</span><span class="sxs-lookup"><span data-stu-id="36b9c-153">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="36b9c-154">Para usar un sistema de validación distinto al de las anotaciones de datos, reemplace `DataAnnotationsValidator` por una implementación personalizada.</span><span class="sxs-lookup"><span data-stu-id="36b9c-154">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="36b9c-155">La implementación de ASP.NET Core está disponible para su inspección en el origen de referencia: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="36b9c-155">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="36b9c-156"> realiza dos tipos de validación:</span><span class="sxs-lookup"><span data-stu-id="36b9c-156"> performs two types of validation:</span></span>

* <span data-ttu-id="36b9c-157">La *validación del campo* se realiza cuando el usuario sale de un campo usando la tecla TAB.</span><span class="sxs-lookup"><span data-stu-id="36b9c-157">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="36b9c-158">Durante una validación del campo, el componente `DataAnnotationsValidator` asocia al campo todos los resultados de validación notificados.</span><span class="sxs-lookup"><span data-stu-id="36b9c-158">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="36b9c-159">La *validación del modelo* se realiza cuando el usuario envía el formulario.</span><span class="sxs-lookup"><span data-stu-id="36b9c-159">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="36b9c-160">Durante una validación del modelo, el componente `DataAnnotationsValidator` intenta determinar el campo en función del nombre del miembro que se indica en el resultado de la validación.</span><span class="sxs-lookup"><span data-stu-id="36b9c-160">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="36b9c-161">Los resultados de validación que no están asociados a un miembro individual se asocian al modelo en lugar de a un campo.</span><span class="sxs-lookup"><span data-stu-id="36b9c-161">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="36b9c-162">Resumen de validación y componentes de los mensajes de validación</span><span class="sxs-lookup"><span data-stu-id="36b9c-162">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="36b9c-163">El componente `ValidationSummary`, que resume todos los mensajes de validación, es similar a la [aplicación auxiliar de etiquetas ValidationSummary](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="36b9c-163">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="36b9c-164">Mensajes de validación de salida de un modelo específico con el parámetro `Model`:</span><span class="sxs-lookup"><span data-stu-id="36b9c-164">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@_starship" />
```

<span data-ttu-id="36b9c-165">El componente `ValidationMessage`, que muestra los mensajes de validación de un campo específico, es similar a la [aplicación auxiliar de etiquetas Validation Message](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="36b9c-165">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="36b9c-166">Especifique el campo de validación con el atributo `For` y una expresión lambda donde se indique la propiedad del modelo:</span><span class="sxs-lookup"><span data-stu-id="36b9c-166">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

<span data-ttu-id="36b9c-167">Los componentes `ValidationMessage` y `ValidationSummary` admiten atributos arbitrarios.</span><span class="sxs-lookup"><span data-stu-id="36b9c-167">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="36b9c-168">Cualquier atributo que no coincida con un parámetro de componente se agrega a los elementos `<div>` o `<ul>` generados.</span><span class="sxs-lookup"><span data-stu-id="36b9c-168">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="36b9c-169">Atributos de validación personalizados</span><span class="sxs-lookup"><span data-stu-id="36b9c-169">Custom validation attributes</span></span>

<span data-ttu-id="36b9c-170">Para asegurarnos de que un resultado de validación está correctamente asociado a un campo cuando se usa un [atributo de validación personalizado](xref:mvc/models/validation#custom-attributes), pase el elemento <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> del contexto de validación al crear el elemento <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span><span class="sxs-lookup"><span data-stu-id="36b9c-170">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="opno-locblazor-data-annotations-validation-package"></a><span data-ttu-id="36b9c-171">Paquete de validación de anotaciones de datos de Blazor</span><span class="sxs-lookup"><span data-stu-id="36b9c-171">Blazor data annotations validation package</span></span>

<span data-ttu-id="36b9c-172">[Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) es un paquete que llena los huecos de la experiencia de validación mediante el componente `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="36b9c-172">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="36b9c-173">Actualmente, el paquete está en fase *experimental*.</span><span class="sxs-lookup"><span data-stu-id="36b9c-173">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="36b9c-174">Atributo [CompareProperty]</span><span class="sxs-lookup"><span data-stu-id="36b9c-174">[CompareProperty] attribute</span></span>

<span data-ttu-id="36b9c-175"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> no funciona bien con el componente `DataAnnotationsValidator` porque no asocia el resultado de la validación a un miembro específico.</span><span class="sxs-lookup"><span data-stu-id="36b9c-175">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="36b9c-176">Esto puede generar un comportamiento incoherente entre la validación de nivel de campo y el momento en que el modelo completo se valida al enviarse.</span><span class="sxs-lookup"><span data-stu-id="36b9c-176">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="36b9c-177">El paquete *experimental* [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) incluye un atributo de validación más, `ComparePropertyAttribute`, que pone fin a estas limitaciones.</span><span class="sxs-lookup"><span data-stu-id="36b9c-177">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="36b9c-178">En una aplicación Blazor, `[CompareProperty]` es un reemplazo directo del atributo `[Compare]`.</span><span class="sxs-lookup"><span data-stu-id="36b9c-178">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="36b9c-179">Tipos de colección, tipos complejos y modelos anidados</span><span class="sxs-lookup"><span data-stu-id="36b9c-179">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="36b9c-180"> proporciona compatibilidad para validar la entrada del formulario mediante anotaciones de datos con el elemento integrado `DataAnnotationsValidator`,</span><span class="sxs-lookup"><span data-stu-id="36b9c-180"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="36b9c-181">pero `DataAnnotationsValidator` solo valida las propiedades de nivel superior del modelo enlazadas al formulario que no son propiedades de tipos complejos o de colección.</span><span class="sxs-lookup"><span data-stu-id="36b9c-181">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="36b9c-182">Para validar el gráfico de objetos completo del modelo enlazado, incluidas las propiedades de tipos complejos o de colección, use el elemento `ObjectGraphDataAnnotationsValidator` proporcionado por el paquete *experimental* [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation):</span><span class="sxs-lookup"><span data-stu-id="36b9c-182">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="36b9c-183">Anote las propiedades del modelo con `[ValidateComplexType]`.</span><span class="sxs-lookup"><span data-stu-id="36b9c-183">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="36b9c-184">En las siguientes clases de modelo, la clase `ShipDescription` contiene más anotaciones de datos para validar cuando el modelo está enlazado al formulario:</span><span class="sxs-lookup"><span data-stu-id="36b9c-184">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="36b9c-185">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="36b9c-185">*Starship.cs*:</span></span>

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

<span data-ttu-id="36b9c-186">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="36b9c-186">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="36b9c-187">Habilitar el botón Enviar según la validación del formulario</span><span class="sxs-lookup"><span data-stu-id="36b9c-187">Enable the submit button based on form validation</span></span>

<span data-ttu-id="36b9c-188">Para habilitar el botón Enviar según la validación del formulario:</span><span class="sxs-lookup"><span data-stu-id="36b9c-188">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="36b9c-189">Use el elemento `EditContext` del formulario para asignar el modelo cuando el componente se inicialice.</span><span class="sxs-lookup"><span data-stu-id="36b9c-189">Use the form's `EditContext` to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="36b9c-190">Valide el formulario en la devolución de llamada `OnFieldChanged` del contexto para habilitar y deshabilitar el botón Enviar.</span><span class="sxs-lookup"><span data-stu-id="36b9c-190">Validate the form in the context's `OnFieldChanged` callback to enable and disable the submit button.</span></span>

```razor
<EditForm EditContext="@_editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private bool _formInvalid = true;
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);

        _editContext.OnFieldChanged += (_, __) =>
        {
            _formInvalid = !_editContext.Validate();
            StateHasChanged();
        };
    }
}
```

<span data-ttu-id="36b9c-191">En el ejemplo anterior, establezca `_formInvalid` en `false` en los siguientes casos:</span><span class="sxs-lookup"><span data-stu-id="36b9c-191">In the preceding example, set `_formInvalid` to `false` if:</span></span>

* <span data-ttu-id="36b9c-192">El formulario se carga previamente con valores predeterminados válidos.</span><span class="sxs-lookup"><span data-stu-id="36b9c-192">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="36b9c-193">Quiere habilitar el botón Enviar cuando el formulario se cargue.</span><span class="sxs-lookup"><span data-stu-id="36b9c-193">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="36b9c-194">Un efecto secundario del método anterior es que un componente `ValidationSummary` se rellena con campos no válidos después de que el usuario interactúe con algún campo.</span><span class="sxs-lookup"><span data-stu-id="36b9c-194">A side effect of the preceding approach is that a `ValidationSummary` component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="36b9c-195">Este escenario se puede abordar de cualquiera de las siguientes maneras:</span><span class="sxs-lookup"><span data-stu-id="36b9c-195">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="36b9c-196">No use un componente `ValidationSummary` en el formulario.</span><span class="sxs-lookup"><span data-stu-id="36b9c-196">Don't use a `ValidationSummary` component on the form.</span></span>
* <span data-ttu-id="36b9c-197">Haga que el componente `ValidationSummary` esté visible cuando se seleccione el botón Enviar (por ejemplo, en un método `HandleValidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="36b9c-197">Make the `ValidationSummary` component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@_editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@_displaySummary" />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private string _displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        _displaySummary = "display:block";
    }
}
```
