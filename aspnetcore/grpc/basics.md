---
<span data-ttu-id="a7613-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a7613-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="a7613-102">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="a7613-102">'Blazor'</span></span>
- <span data-ttu-id="a7613-103">"Identity"</span><span class="sxs-lookup"><span data-stu-id="a7613-103">'Identity'</span></span>
- <span data-ttu-id="a7613-104">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="a7613-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="a7613-105">"Razor"</span><span class="sxs-lookup"><span data-stu-id="a7613-105">'Razor'</span></span>
- <span data-ttu-id="a7613-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a7613-106">'SignalR' uid:</span></span> 

---
# <a name="grpc-services-with-c"></a><span data-ttu-id="a7613-107">Servicios gRPC con C\#</span><span class="sxs-lookup"><span data-stu-id="a7613-107">gRPC services with C\#</span></span>

<span data-ttu-id="a7613-108">En este documento se describen los conceptos necesarios para escribir de aplicaciones [gRPC](https://grpc.io/docs/guides/) en C#.</span><span class="sxs-lookup"><span data-stu-id="a7613-108">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="a7613-109">Los temas aquí tratados son válidos para aplicaciones de gRPC basadas tanto en ASP.NET Core como en [C-core](https://grpc.io/blog/grpc-stacks).</span><span class="sxs-lookup"><span data-stu-id="a7613-109">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="proto-file"></a><span data-ttu-id="a7613-110">Archivo .proto</span><span class="sxs-lookup"><span data-stu-id="a7613-110">proto file</span></span>

<span data-ttu-id="a7613-111">gRPC usa un enfoque de contrato primero para el desarrollo de API.</span><span class="sxs-lookup"><span data-stu-id="a7613-111">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="a7613-112">Los búferes de protocolo ("protobuf") se usan de forma predeterminada como el lenguaje de diseño de interfaz (IDL).</span><span class="sxs-lookup"><span data-stu-id="a7613-112">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="a7613-113">El archivo *\*.proto* contiene lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a7613-113">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="a7613-114">La definición del servicio gRPC</span><span class="sxs-lookup"><span data-stu-id="a7613-114">The definition of the gRPC service.</span></span>
* <span data-ttu-id="a7613-115">Los mensajes enviados entre clientes y servidores</span><span class="sxs-lookup"><span data-stu-id="a7613-115">The messages sent between clients and servers.</span></span>

<span data-ttu-id="a7613-116">Para más información sobre la sintaxis de los archivos de protobuf, vea la [documentación oficial (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="a7613-116">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="a7613-117">Vamos a considerar, por ejemplo, el archivo *greeter.proto* que se usa en [Introducción a un servicio gRPC](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="a7613-117">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="a7613-118">Define un servicio `Greeter`.</span><span class="sxs-lookup"><span data-stu-id="a7613-118">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="a7613-119">El servicio `Greeter` define una llamada a `SayHello`.</span><span class="sxs-lookup"><span data-stu-id="a7613-119">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="a7613-120">`SayHello` envía un mensaje `HelloRequest` y recibe un mensaje `HelloReply`:</span><span class="sxs-lookup"><span data-stu-id="a7613-120">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="a7613-121">Adición de un archivo .proto a una aplicación de C\#</span><span class="sxs-lookup"><span data-stu-id="a7613-121">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="a7613-122">Para incluir el archivo *\*.proto* en un proyecto, hay que agregarlo al grupo de elementos `<Protobuf>`:</span><span class="sxs-lookup"><span data-stu-id="a7613-122">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="a7613-123">Compatibilidad de herramientas de C# con archivos .proto</span><span class="sxs-lookup"><span data-stu-id="a7613-123">C# Tooling support for .proto files</span></span>

<span data-ttu-id="a7613-124">El paquete de herramientas [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) es necesario para generar los activos de C# a partir de los *archivos \*.proto*.</span><span class="sxs-lookup"><span data-stu-id="a7613-124">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="a7613-125">Los activos (archivos) generados:</span><span class="sxs-lookup"><span data-stu-id="a7613-125">The generated assets (files):</span></span>

* <span data-ttu-id="a7613-126">Se generan según sea necesario cada vez que el proyecto se compila.</span><span class="sxs-lookup"><span data-stu-id="a7613-126">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="a7613-127">No se agregan al proyecto ni se protegen en el control de código fuente.</span><span class="sxs-lookup"><span data-stu-id="a7613-127">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="a7613-128">Son un artefacto de compilación contenido en el directorio *obj*.</span><span class="sxs-lookup"><span data-stu-id="a7613-128">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="a7613-129">Este paquete es necesario en los proyectos tanto de servidor como de cliente.</span><span class="sxs-lookup"><span data-stu-id="a7613-129">This package is required by both the server and client projects.</span></span> <span data-ttu-id="a7613-130">El metapaquete `Grpc.AspNetCore` incluye una referencia a `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="a7613-130">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="a7613-131">Los proyectos de servidor pueden agregar `Grpc.AspNetCore` por medio del administrador de paquetes de Visual Studio, o bien agregando un elemento `<PackageReference>` al archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="a7613-131">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="a7613-132">Los proyectos de cliente, por su parte, deben hacer referencia directamente a `Grpc.Tools` junto con los demás paquetes necesarios para usar el cliente de gRPC.</span><span class="sxs-lookup"><span data-stu-id="a7613-132">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="a7613-133">El paquete de herramientas no es necesario en tiempo de ejecución, de modo que la dependencia se marca con `PrivateAssets="All"`:</span><span class="sxs-lookup"><span data-stu-id="a7613-133">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="a7613-134">Activos de C# generados</span><span class="sxs-lookup"><span data-stu-id="a7613-134">Generated C# assets</span></span>

<span data-ttu-id="a7613-135">El paquete de herramientas genera los tipos de C# que representan los mensajes definidos en los archivos *\*.proto* incluidos.</span><span class="sxs-lookup"><span data-stu-id="a7613-135">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="a7613-136">En el caso de los activos del lado servidor, se genera un tipo base de servicio abstracto.</span><span class="sxs-lookup"><span data-stu-id="a7613-136">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="a7613-137">El tipo base contiene las definiciones de todas las llamadas a gRPC contenidas en el archivo *.proto*.</span><span class="sxs-lookup"><span data-stu-id="a7613-137">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="a7613-138">Cree una implementación de servicio concreta que se derive de este tipo base e implemente la lógica de las llamadas a gRPC.</span><span class="sxs-lookup"><span data-stu-id="a7613-138">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="a7613-139">Respecto a `greet.proto`, el ejemplo descrito anteriormente, se genera un tipo de elemento `GreeterBase` abstracto que contiene un método `SayHello` virtual.</span><span class="sxs-lookup"><span data-stu-id="a7613-139">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="a7613-140">Una elemento `GreeterService` de una implementación concreta invalida el método e implementa la lógica que controla la llamada de gRPC.</span><span class="sxs-lookup"><span data-stu-id="a7613-140">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="a7613-141">En cuanto a los activos del lado cliente, se genera un tipo de cliente concreto.</span><span class="sxs-lookup"><span data-stu-id="a7613-141">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="a7613-142">Las llamadas de gRPC en el archivo *.proto* se traducen en métodos en el tipo concreto a los que se puede llamar.</span><span class="sxs-lookup"><span data-stu-id="a7613-142">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="a7613-143">Respecto a `greet.proto`, el ejemplo descrito anteriormente, se genera un tipo de elemento `GreeterClient` concreto.</span><span class="sxs-lookup"><span data-stu-id="a7613-143">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="a7613-144">Llame a `GreeterClient.SayHelloAsync` para iniciar una llamada de gRPC al servidor.</span><span class="sxs-lookup"><span data-stu-id="a7613-144">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="a7613-145">Se generarán activos de servidor y de cliente de forma predeterminada por cada archivo *\*.proto* incluido en el grupo de elementos `<Protobuf>`.</span><span class="sxs-lookup"><span data-stu-id="a7613-145">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="a7613-146">Para garantizar que solo se generan activos de servidor en un proyecto de servidor, el atributo `GrpcServices` se establece en `Server`.</span><span class="sxs-lookup"><span data-stu-id="a7613-146">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="a7613-147">Y lo mismo sucede en los proyectos de cliente, donde el atributo se establece en `Client`.</span><span class="sxs-lookup"><span data-stu-id="a7613-147">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7613-148">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="a7613-148">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
