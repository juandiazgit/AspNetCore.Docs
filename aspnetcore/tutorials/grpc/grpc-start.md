---
title: Create a .NET Core gRPC client and server in ASP.NET Core
author: juntaoluo
description: This tutorial shows how to create a gRPC Service and gRPC client on ASP.NET Core. Learn how to create a gRPC Service project, edit a proto file, and add a duplex streaming call.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 5/30/2019
uid: tutorials/grpc/grpc-start
---
# Tutorial: Create a gRPC client and server in ASP.NET Core

By [John Luo](https://github.com/juntaoluo)

This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.

At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.

[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).

In this tutorial, you:

> [!div class="checklist"]
> * Create a gRPC Server.
> * Create a gRPC client.
> * Test the gRPC client service with the gRPC Greeter service.

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## Create a gRPC service

# [Visual Studio](#tab/visual-studio)

* From the Visual Studio **File** menu, select **New** > **Project**.
* In the **Create a new project** dialog, select **ASP.NET Core Web Application**.
* Select **Next**
* Name the project **GrpcGreeter**. It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.
* Select **Create**
* In the **Create a new ASP.NET Core Web Application** dialog:
  * Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus. 
  * Select the **gRPC Service** template.
  * Select **Create**

# [Visual Studio Code](#tab/visual-studio-code)

* Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Change directories (`cd`) to a folder which will contain the project.
* Run the following commands:

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.
  * The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.

  A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**
* Select **Yes**

# [Visual Studio for Mac](#tab/visual-studio-mac)

From a terminal, run the following commands:

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.

### Open the project

From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.

<!-- End of VS tabs -->

---

### Run the service

# [Visual Studio](#tab/visual-studio)

* Press Ctrl+F5 to run the gRPC service without the debugger.

  Visual Studio runs the service in a command prompt.

# [Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* Run the gRPC Greeter project GrpcGreeter from the command line using `dotnet run`.

<!-- End of combined VS/Mac tabs -->

---

The logs show the service listening on `http://localhost:50051`.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

### Examine the project files

GrpcGreeter files:

* *greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets. For more information, see [Introduction to gRPC](xref:grpc/index).
* *Services* folder: Contains the implementation of the `Greeter` service.
* *appSettings.json*: Contains configuration data, such as protocol used by Kestrel. For more information, see <xref:fundamentals/configuration/index>.
* *Program.cs*: Contains the entry point for the gRPC service. For more information, see <xref:fundamentals/host/web-host>.
* *Startup.cs*: Contains code that configures app behavior. For more information, see [App startup](xref:fundamentals/startup).

## Create the gRPC client in a .NET console app

## [Visual Studio](#tab/visual-studio)

* Select **File** > **New** > **Project** from the menu bar.
* In the **Create a new project** dialog, select **Console App (.NET Core)**.
* Select **Next**
* In the **Name** text box, enter "GrpcGreeterClient".
* Select **Create**.

# [Visual Studio Code](#tab/visual-studio-code)

* Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Change directories (`cd`) to a folder which will contain the project.
* Run the following commands:

```console
dotnet new console -o GrpcGreeterClient
code -r GrpcGreeterClient
```

# [Visual Studio for Mac](#tab/visual-studio-mac)

Follow the instructions [here](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.

<!-- End of VS tabs -->

---

### Add required packages

Add the following packages to the gRPC client project:

* [Grpc.Core](https://www.nuget.org/packages/Grpc.Core), which contains the C# API for the C-core client.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files. The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.

### [Visual Studio](#tab/visual-studio)

Install the packages using either the Package Manager Console (PMC) or Manage NuGet Package

#### PMC option to install packages

* From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**
* From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.
* Run the following commands:

 ```powershell
Install-Package Grpc.Core
Install-Package Grpc.Protobuf
Install-Package Grpc.Tools
```

#### Manage NuGet Packages option to install packages

* Right-click the project in **Solution Explorer** > **Manage NuGet Packages**
* Select the **Browse** tab.
* Enter **Grpc.Core** in the search box.
* Select the **Grpc.Core** package from the **Browse** tab and select **Install**.
* Repeat for `Google.Protobuf` and `Grpc.Tools`.

### [Visual Studio Code](#tab/visual-studio-code)

Run the following commands from the **Integrated Terminal**:

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Core
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

### [Visual Studio for Mac](#tab/visual-studio-mac)

* Right-click the **Packages** folder in **Solution Pad** > **Add Packages**
* Enter **Grpc.Core** in the search box.
* Select the **Grpc.Core** package from the results pane and select **Add Package**
* Repeat for `Google.Protobuf` and `Grpc.Tools`.

---

### Add greet.proto

* Create a **Protos** folder in the gRPC client project.
* Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.
* Edit the *GrpcGreeterClient.csproj* project file:

  # [Visual Studio](#tab/visual-studio) 

  Right-click the project and select the **Edit GrpcGreeterClient.csproj**.

  # [Visual Studio Code](#tab/visual-studio-code) 

  Select the *GrpcGreeterClient.csproj* file.

  # [Visual Studio for Mac](#tab/visual-studio-mac)

  Right click the project and select **Tools > Edit File**.

  ---

* Add the **greet.proto** file to the `<Protobuf>` item group of the GrpcGreeterClient project file:

  ```XML
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

Build the client project to trigger the generation of the C# client assets.

### Create the greater client

Build the project to create the types in the **Greeter** namespace. The `Greeter` types are generated automatically by the build process.

Update the gRPC client *Program.cs* file with the following code:

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

*Program.cs* contains the entry point and logic for the gRPC client.

The greater client is created by:

* Instantiating a `Channel` containing the information for creating the connection to the gRPC service.
* Using the `Channel` to construct the greater client:

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=4-6)]

The greater client calls the asynchronous `SayHello` method. The result of the `SayHello` call is displayed:

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-9)]

Shut down the `Channel` used by the client when operations have finished to release all resources.

## Test the gRPC client with the gRPC Greeter service

### [Visual Studio](#tab/visual-studio)

* In the Greeter service, press Ctrl+F5 to start the server without the debugger.
* In the GrpcGreeterClient project, press Ctrl+F5 to start the server without the debugger.

### [Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* Start the Greeter service.
* Start the client.

<!-- End of combined VS/Mac tabs -->

---

The client sends a greeting to the service with a message containing its name "GreeterClient". The service sends the message "Hello GreeterClient" as a response. The "Hello GreeterClient" response is displayed in the command prompt:

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

The gRPC service records the details of the successful call in the logs written to the command prompt.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST http://localhost:50051/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

### Next steps

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
