En la tabla siguiente se incluyen los detalles de los parámetros del generador de código de ASP.NET Core:

| Parámetro               | DESCRIPCIÓN|
| ----------------- | ------------ |
| -m  | Nombre del modelo |
| -dc  | Contexto de datos |
| -udl | Usa el diseño predeterminado. |
| --relativeFolderPath | Ruta de acceso relativa de la carpeta de salida para crear los archivos. |
| --useDefaultLayout | Se debe usar el diseño predeterminado para las vistas. |
| --referenceScriptLibraries | Agrega `_ValidationScriptsPartial` a las páginas Editar y Crear. |

Active o desactive `h` para obtener ayuda con el comando `aspnet-codegenerator controller`:

```console
dotnet aspnet-codegenerator controller -h
```

Para más información, consulte [aspnet-codegenerator de dotnet](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
