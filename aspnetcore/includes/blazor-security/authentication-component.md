La página generada por `Authentication` el componente (*pages/Authentication. Razor*) define las rutas necesarias para controlar diferentes fases de autenticación.

El `RemoteAuthenticatorView` componente:

* Lo proporciona el paquete [Microsoft. AspNetCore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .
* Administra la realización de las acciones adecuadas en cada fase de autenticación.

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
