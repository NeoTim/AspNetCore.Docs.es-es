---
title: Imágenes de Docker para ASP.NET Core
author: tdykstra
description: Obtenga información sobre cómo usar las imágenes de Docker de .NET Core publicadas desde el registro de Docker. Extraiga imágenes y cree las suyas propias.
ms.author: tdykstra
ms.custom: mvc
ms.date: 04/09/2019
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 48fc53a4c2139960c0f696af5732ff68fc6c4b8a
ms.sourcegitcommit: a3926eae3f687013027a2828830c12a89add701f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2019
ms.locfileid: "65451014"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="634e3-104">Imágenes de Docker para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="634e3-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="634e3-105">En este tutorial se muestra cómo ejecutar una aplicación ASP.NET Core en contenedores de Docker.</span><span class="sxs-lookup"><span data-stu-id="634e3-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="634e3-106">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="634e3-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="634e3-107">Obtener información sobre las imágenes de Microsoft .NET Core Docker</span><span class="sxs-lookup"><span data-stu-id="634e3-107">Learn about Microsoft .NET Core Docker images</span></span> 
> * <span data-ttu-id="634e3-108">Descargará una aplicación de ejemplo de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="634e3-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="634e3-109">Ejecutará la aplicación de ejemplo localmente</span><span class="sxs-lookup"><span data-stu-id="634e3-109">Run the sample app locally</span></span>
> * <span data-ttu-id="634e3-110">Ejecutará la aplicación de ejemplo en contenedores de Linux</span><span class="sxs-lookup"><span data-stu-id="634e3-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="634e3-111">Ejecutará la aplicación de ejemplo en contenedores de Windows</span><span class="sxs-lookup"><span data-stu-id="634e3-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="634e3-112">Realizará compilaciones e implementaciones manualmente</span><span class="sxs-lookup"><span data-stu-id="634e3-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="634e3-113">Imágenes de Docker para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="634e3-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="634e3-114">En este tutorial, descargará una aplicación de ejemplo de ASP.NET Core y la ejecutará en contenedores de Docker.</span><span class="sxs-lookup"><span data-stu-id="634e3-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="634e3-115">El ejemplo funciona con contenedores de Linux y Windows.</span><span class="sxs-lookup"><span data-stu-id="634e3-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="634e3-116">El Dockerfile de ejemplo usa la [característica de compilación en varias fases de Docker](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) para la compilación y ejecución en distintos contenedores.</span><span class="sxs-lookup"><span data-stu-id="634e3-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="634e3-117">Los contenedores de compilación y ejecución se crean a partir de imágenes que proporciona Microsoft en Docker Hub:</span><span class="sxs-lookup"><span data-stu-id="634e3-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

* `dotnet/core/sdk`

  <span data-ttu-id="634e3-118">En el ejemplo se usa esta imagen para compilar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="634e3-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="634e3-119">La imagen contiene el SDK de .NET Core, que incluye las herramientas de línea de comandos (CLI).</span><span class="sxs-lookup"><span data-stu-id="634e3-119">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="634e3-120">Esta imagen está optimizada para el desarrollo local, la depuración y las pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="634e3-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="634e3-121">Las herramientas instaladas para el desarrollo y la compilación hacen que esta sea una imagen relativamente grande.</span><span class="sxs-lookup"><span data-stu-id="634e3-121">The tools installed for development and compilation make this a relatively large image.</span></span> 

* `dotnet/core/aspnet` 

   <span data-ttu-id="634e3-122">En el ejemplo se usa esta imagen para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="634e3-122">The sample uses this image for running the app.</span></span> <span data-ttu-id="634e3-123">La imagen contiene el entorno de ejecución y las bibliotecas de ASP.NET Core y está optimizada para la ejecución de aplicaciones en producción.</span><span class="sxs-lookup"><span data-stu-id="634e3-123">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="634e3-124">Diseñada para acelerar la implementación y el inicio de las aplicaciones, la imagen es relativamente pequeña, de forma que se optimiza el rendimiento de la red desde Docker Registry hasta el host de Docker.</span><span class="sxs-lookup"><span data-stu-id="634e3-124">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="634e3-125">Solo se copian en el contenedor los archivos binarios y el contenido necesario para ejecutar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="634e3-125">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="634e3-126">El contenido está listo para ejecutarse, lo que permite el tiempo más rápido desde `Docker run` hasta el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="634e3-126">The contents are ready to run, enabling the fastest time from `Docker run` to app startup.</span></span> <span data-ttu-id="634e3-127">En el modelo de Docker no se necesita compilación de código dinámico.</span><span class="sxs-lookup"><span data-stu-id="634e3-127">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="634e3-128">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="634e3-128">Prerequisites</span></span>

* [<span data-ttu-id="634e3-129">SDK de .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="634e3-129">.NET Core 2.2 SDK</span></span>](https://www.microsoft.com/net/core)

* <span data-ttu-id="634e3-130">Cliente de Docker 18.03 o posterior</span><span class="sxs-lookup"><span data-stu-id="634e3-130">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="634e3-131">Distribuciones de Linux</span><span class="sxs-lookup"><span data-stu-id="634e3-131">Linux distributions</span></span>
    * [<span data-ttu-id="634e3-132">CentOS</span><span class="sxs-lookup"><span data-stu-id="634e3-132">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="634e3-133">Debian</span><span class="sxs-lookup"><span data-stu-id="634e3-133">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="634e3-134">Fedora</span><span class="sxs-lookup"><span data-stu-id="634e3-134">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="634e3-135">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="634e3-135">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="634e3-136">macOS</span><span class="sxs-lookup"><span data-stu-id="634e3-136">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="634e3-137">Windows</span><span class="sxs-lookup"><span data-stu-id="634e3-137">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="634e3-138">Git</span><span class="sxs-lookup"><span data-stu-id="634e3-138">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="634e3-139">Descarga de la aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="634e3-139">Download the sample app</span></span>

* <span data-ttu-id="634e3-140">Para descargar el ejemplo, clone el [repositorio de Docker de .NET Core](https://github.com/dotnet/dotnet-docker):</span><span class="sxs-lookup"><span data-stu-id="634e3-140">Download the sample by cloning the [.NET Core Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="634e3-141">Probar la aplicación localmente</span><span class="sxs-lookup"><span data-stu-id="634e3-141">Run the app locally</span></span>

* <span data-ttu-id="634e3-142">Vaya a la carpeta de proyecto en *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="634e3-142">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="634e3-143">Ejecute el siguiente comando para compilar y ejecutar localmente la aplicación:</span><span class="sxs-lookup"><span data-stu-id="634e3-143">Run the following command to build and run the app locally:</span></span>

  ```console
  dotnet run
  ```

* <span data-ttu-id="634e3-144">Vaya a `http://localhost:5000` en un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="634e3-144">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="634e3-145">Presione Ctrl+C en el símbolo del sistema para detener la aplicación.</span><span class="sxs-lookup"><span data-stu-id="634e3-145">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="634e3-146">Ejecución en un contenedor de Linux</span><span class="sxs-lookup"><span data-stu-id="634e3-146">Run in a Linux container</span></span>

* <span data-ttu-id="634e3-147">En el cliente de Docker, cambie a contenedores de Linux.</span><span class="sxs-lookup"><span data-stu-id="634e3-147">In the Docker client, switch to Linux containers.</span></span>

* <span data-ttu-id="634e3-148">Vaya a la carpeta de Dockerfile en *dotnet-docker/samples/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="634e3-148">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="634e3-149">Ejecute los siguientes comandos para compilar y ejecutar el ejemplo en Docker:</span><span class="sxs-lookup"><span data-stu-id="634e3-149">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="634e3-150">Los argumentos del comando `build`:</span><span class="sxs-lookup"><span data-stu-id="634e3-150">The `build` command arguments:</span></span>
  * <span data-ttu-id="634e3-151">Asigne a la imagen el nombre aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="634e3-151">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="634e3-152">Busque el archivo Dockerfile en la carpeta actual (el punto al final).</span><span class="sxs-lookup"><span data-stu-id="634e3-152">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="634e3-153">Los argumentos del comando de ejecución:</span><span class="sxs-lookup"><span data-stu-id="634e3-153">The run command arguments:</span></span>
  * <span data-ttu-id="634e3-154">Asigne un pseudo-TTY y manténgalo abierto aunque no esté asociado.</span><span class="sxs-lookup"><span data-stu-id="634e3-154">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="634e3-155">(El mismo efecto que `--interactive --tty`).</span><span class="sxs-lookup"><span data-stu-id="634e3-155">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="634e3-156">Quite automáticamente el contenedor cuando se cierre.</span><span class="sxs-lookup"><span data-stu-id="634e3-156">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="634e3-157">Asigne al puerto 5000 de la máquina local el puerto 80 en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="634e3-157">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="634e3-158">Asigne al contenedor el nombre aspnetcore_sample.</span><span class="sxs-lookup"><span data-stu-id="634e3-158">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="634e3-159">Especifique la imagen aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="634e3-159">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="634e3-160">Vaya a `http://localhost:5000` en un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="634e3-160">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="634e3-161">Ejecución en un contenedor de Windows</span><span class="sxs-lookup"><span data-stu-id="634e3-161">Run in a Windows container</span></span>

* <span data-ttu-id="634e3-162">En el cliente de Docker, cambie a los contenedores de Windows.</span><span class="sxs-lookup"><span data-stu-id="634e3-162">In the Docker client, switch to Windows containers.</span></span>

<span data-ttu-id="634e3-163">Vaya a la carpeta de archivos de Docker en `dotnet-docker/samples/aspnetapp`.</span><span class="sxs-lookup"><span data-stu-id="634e3-163">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="634e3-164">Ejecute los siguientes comandos para compilar y ejecutar el ejemplo en Docker:</span><span class="sxs-lookup"><span data-stu-id="634e3-164">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="634e3-165">Para contenedores de Windows, necesita la dirección IP del contenedor (no sirve ir a `http://localhost:5000`):</span><span class="sxs-lookup"><span data-stu-id="634e3-165">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="634e3-166">Abra otro símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="634e3-166">Open up another command prompt.</span></span>
  * <span data-ttu-id="634e3-167">Ejecute `docker ps` para ver los contenedores en ejecución.</span><span class="sxs-lookup"><span data-stu-id="634e3-167">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="634e3-168">Compruebe que aparece el contenedor "aspnetcore_sample".</span><span class="sxs-lookup"><span data-stu-id="634e3-168">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="634e3-169">Ejecute `docker exec aspnetcore_sample ipconfig` para mostrar la dirección IP del contenedor.</span><span class="sxs-lookup"><span data-stu-id="634e3-169">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="634e3-170">La salida del comando es similar a este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="634e3-170">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="634e3-171">Copie la dirección IPv4 (por ejemplo, 172.29.245.43) del contenedor y péguela en la barra de direcciones del explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="634e3-171">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="634e3-172">Compilaciones e implementaciones manuales</span><span class="sxs-lookup"><span data-stu-id="634e3-172">Build and deploy manually</span></span>

<span data-ttu-id="634e3-173">En algunos escenarios, puede que quiera implementar una aplicación en un contenedor mediante la copia de los archivos de aplicación que son necesarios en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="634e3-173">In some scenarios, you might want to deploy an app to a container by copying to it the application files that are needed at run time.</span></span> <span data-ttu-id="634e3-174">En esta sección se muestra cómo realizar una implementación manual.</span><span class="sxs-lookup"><span data-stu-id="634e3-174">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="634e3-175">Vaya a la carpeta de proyecto en *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="634e3-175">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="634e3-176">Ejecute el comando [dotnet publish](https://docs.microsoft.com/dotnet/core/tools/dotnet-publish.md):</span><span class="sxs-lookup"><span data-stu-id="634e3-176">Run the [dotnet publish](https://docs.microsoft.com/dotnet/core/tools/dotnet-publish.md) command:</span></span>

  ```console
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="634e3-177">Los argumentos del comando:</span><span class="sxs-lookup"><span data-stu-id="634e3-177">The command arguments:</span></span>
  * <span data-ttu-id="634e3-178">Compile la aplicación en modo de versión (el valor predeterminado es modo de depuración).</span><span class="sxs-lookup"><span data-stu-id="634e3-178">Build the application in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="634e3-179">Cree los archivos en la carpeta *publicada*.</span><span class="sxs-lookup"><span data-stu-id="634e3-179">Create the files in the *published* folder.</span></span>

* <span data-ttu-id="634e3-180">Ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="634e3-180">Run the application.</span></span>

  * <span data-ttu-id="634e3-181">Windows:</span><span class="sxs-lookup"><span data-stu-id="634e3-181">Windows:</span></span>

    ```console
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="634e3-182">Linux:</span><span class="sxs-lookup"><span data-stu-id="634e3-182">Linux:</span></span>

    ```bash
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="634e3-183">Vaya a `http://localhost:5000` para ver la página principal.</span><span class="sxs-lookup"><span data-stu-id="634e3-183">Browse to `http://localhost:5000` to see the home page.</span></span>

### <a name="the-dockerfile"></a><span data-ttu-id="634e3-184">El archivo Dockerfile</span><span class="sxs-lookup"><span data-stu-id="634e3-184">The Dockerfile</span></span>

<span data-ttu-id="634e3-185">Aquí el archivo Dockerfile se usa con el comando `docker build` que ejecutó anteriormente.</span><span class="sxs-lookup"><span data-stu-id="634e3-185">Here's the Dockerfile used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="634e3-186">Se usa `dotnet publish` de la misma manera que en esta sección para realizar compilaciones e implementaciones.</span><span class="sxs-lookup"><span data-stu-id="634e3-186">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```console
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out


FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

## <a name="additional-resources"></a><span data-ttu-id="634e3-187">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="634e3-187">Additional resources</span></span>

* [<span data-ttu-id="634e3-188">Comando de compilación de Docker</span><span class="sxs-lookup"><span data-stu-id="634e3-188">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="634e3-189">Comando de ejecución de Docker</span><span class="sxs-lookup"><span data-stu-id="634e3-189">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="634e3-190">[Ejemplo de Docker de ASP.NET Core](https://github.com/dotnet/dotnet-docker) (el usado en este tutorial).</span><span class="sxs-lookup"><span data-stu-id="634e3-190">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="634e3-191">Configurar ASP.NET Core para trabajar con servidores proxy y equilibradores de carga</span><span class="sxs-lookup"><span data-stu-id="634e3-191">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](/aspnet/core/host-and-deploy/proxy-load-balancer)
* <span data-ttu-id="634e3-192">[Working with Visual Studio Docker Tools](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker) (Trabajo con Visual Studio Docker Tools)</span><span class="sxs-lookup"><span data-stu-id="634e3-192">[Working with Visual Studio Docker Tools](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker)</span></span>
* [<span data-ttu-id="634e3-193">Depuración con Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="634e3-193">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers) 

## <a name="next-steps"></a><span data-ttu-id="634e3-194">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="634e3-194">Next steps</span></span>

<span data-ttu-id="634e3-195">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="634e3-195">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="634e3-196">Obtener información sobre las imágenes de Microsoft .NET Core Docker</span><span class="sxs-lookup"><span data-stu-id="634e3-196">Learned about Microsoft .NET Core Docker images</span></span> 
> * <span data-ttu-id="634e3-197">Descargado una aplicación de ejemplo de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="634e3-197">Downloaded an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="634e3-198">Ejecutado la aplicación de ejemplo localmente</span><span class="sxs-lookup"><span data-stu-id="634e3-198">Run the sample app locally</span></span>
> * <span data-ttu-id="634e3-199">Ejecutado la aplicación de ejemplo en contenedores de Linux</span><span class="sxs-lookup"><span data-stu-id="634e3-199">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="634e3-200">Ejecutado el ejemplo con contenedores de Windows</span><span class="sxs-lookup"><span data-stu-id="634e3-200">Run the sample with in Windows containers</span></span>
> * <span data-ttu-id="634e3-201">Realizado compilaciones e implementaciones manualmente</span><span class="sxs-lookup"><span data-stu-id="634e3-201">Built and deployed manually</span></span>

<span data-ttu-id="634e3-202">El repositorio de Git que contiene la aplicación de ejemplo también incluye documentación.</span><span class="sxs-lookup"><span data-stu-id="634e3-202">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="634e3-203">Para información general de los recursos disponibles en el repositorio, consulte [el archivo Léame](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span><span class="sxs-lookup"><span data-stu-id="634e3-203">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="634e3-204">En concreto, vea cómo implementar HTTPS:</span><span class="sxs-lookup"><span data-stu-id="634e3-204">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="634e3-205">[Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md) (Desarrollo de aplicaciones ASP.NET Core con Docker a través de HTTPS)</span><span class="sxs-lookup"><span data-stu-id="634e3-205">[Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md)</span></span>
