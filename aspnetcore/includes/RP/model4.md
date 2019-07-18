<a name="codegenerator"></a> En la tabla siguiente se incluyen los detalles de los parámetros del generador de código de ASP.NET Core:

| Parámetro               | DESCRIPCIÓN|
| ----------------- | ------------ |
| -m  | Nombre del modelo |
| -dc  | La clase `DbContext` que se va a usar. |
| -udl | Usa el diseño predeterminado. |
| -outDir | Ruta de acceso relativa de la carpeta de salida para crear las vistas |
| --referenceScriptLibraries | Agrega `_ValidationScriptsPartial` a las páginas Editar y Crear. |

Active o desactive `h` para obtener ayuda con el comando `aspnet-codegenerator razorpage`:

```console
dotnet aspnet-codegenerator razorpage -h
```
