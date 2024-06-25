# Microsoft eShopOnWeb ASP.NET Core Reference Application

This Microsoft reference application is now being maintained by [NimblePros](https://nimblepros.com/).

If you're looking for the .NET Aspire eShop sample it's [here](https://github.com/dotnet/eShop). Microsoft also recommends the [Reliable Web App](https://learn.microsoft.com/azure/architecture/web-apps/guides/reliable-web-app/overview) patterns guidance for building web apps with enterprise app patterns.

This is a sample ASP.NET Core reference application, powered by Microsoft, demonstrating a single-process (monolithic) application architecture and deployment model. If you're new to .NET development, read the [Getting Started for Beginners](https://github.com/dotnet-architecture/eShopOnWeb/wiki/Getting-Started-for-Beginners) guide.

A list of Frequently Asked Questions about this repository can be found [here](https://github.com/dotnet-architecture/eShopOnWeb/wiki/Frequently-Asked-Questions).

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
## Table of Contents

- [Overview Video](#overview-video)
- [eBook](#ebook)
- [Topics (eBook TOC)](#topics-ebook-toc)
- [Running the sample using Azd template](#running-the-sample-using-azd-template)
  - [Windows](#windows)
  - [Linux/MacOS](#linuxmacos)
- [Running the sample locally](#running-the-sample-locally)
  - [Running the sample via the command line](#running-the-sample-via-the-command-line)
  - [Running the sample via multiple startup projects](#running-the-sample-via-multiple-startup-projects)
  - [Configuring the sample to use a data store](#configuring-the-sample-to-use-a-data-store)
- [Running the sample in the dev container](#running-the-sample-in-the-dev-container)
- [Running the sample using Docker](#running-the-sample-using-docker)
- [Community Extensions](#community-extensions)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Overview Video

[Steve "ardalis" Smith](https://twitter.com/ardalis) recorded [a live stream providing an overview of the eShopOnWeb reference app](https://www.youtube.com/watch?v=vRZ8ucGac8M&ab_channel=Ardalis) in October 2020. 

## eBook

This reference application is meant to support the free .PDF download ebook: [Architecting Modern Web Applications with ASP.NET Core and Azure](https://aka.ms/webappebook), updated to **ASP.NET Core 8.0**. [Also available in ePub/mobi formats](https://dotnet.microsoft.com/learn/web/aspnet-architecture).

You can also read the book in online pages at the .NET docs here: 
https://docs.microsoft.com/dotnet/architecture/modern-web-apps-azure/

[<img src="https://dotnet.microsoft.com/blob-assets/images/e-books/aspnet.png" height="300" />](https://dotnet.microsoft.com/learn/web/aspnet-architecture)

The **eShopOnWeb** sample is related to the [eShopOnContainers](https://github.com/dotnet/eShopOnContainers) sample application which, in that case, focuses on a microservices/containers-based application architecture. However, **eShopOnWeb** is much simpler in regards to its current functionality and focuses on traditional Web Application Development with a single deployment.

The goal for this sample is to demonstrate some of the principles and patterns described in the [eBook](https://aka.ms/webappebook). It is not meant to be an eCommerce reference application, and as such it does not implement many features that would be obvious and/or essential to a real eCommerce application.

> ### VERSIONS
> #### The `main` branch is currently running ASP.NET Core 8.0.
> #### Older versions are tagged.

## Topics (eBook TOC)

- Introduction
- Characteristics of Modern Web Applications
- Choosing Between Traditional Web Apps and SPAs
- Architectural Principles
- Common Web Application Architectures
- Common Client Side Technologies
- Developing ASP.NET Core MVC Apps
- Working with Data in ASP.NET Core Apps
- Testing ASP.NET Core MVC Apps
- Development Process for Azure-Hosted ASP.NET Core Apps
- Azure Hosting Recommendations for ASP.NET Core Web Apps

## Running the sample using Azd template

The store's home page should look like this:

![eShopOnWeb home page screenshot](https://user-images.githubusercontent.com/782127/88414268-92d83a00-cdaa-11ea-9b4c-db67d95be039.png)

The Azure Developer CLI (`azd`) is a developer-centric command-line interface (CLI) tool for creating Azure applications.

You need to install it before running and deploying with Azure Developer CLI.

### Windows

```powershell
powershell -ex AllSigned -c "Invoke-RestMethod 'https://aka.ms/install-azd.ps1' | Invoke-Expression"
```

### Linux/MacOS

```
curl -fsSL https://aka.ms/install-azd.sh | bash
```

And you can also install with package managers, like winget, choco, and brew. For more details, you can follow the documentation: https://aka.ms/azure-dev/install.

After logging in with the following command, you will be able to use the azd cli to quickly provision and deploy the application.

```
azd auth login
```

Then, execute the `azd init` command to initialize the environment.
```
azd init -t dotnet-architecture/eShopOnWeb 
```

Run `azd up` to provision all the resources to Azure and deploy the code to those resources.
```
azd up 
```

According to the prompt, enter an `env name`, and select `subscription` and `location`, these are the necessary parameters when you create resources. Wait a moment for the resource deployment to complete, click the web endpoint and you will see the home page.

**Notes:**
1. Considering security, we store its related data (id, password) in the **Azure Key Vault** when we create the database, and obtain it from the Key Vault when we use it. This is different from directly deploying applications locally.
2. The resource group name created in azure portal will be **rg-{env name}**.

You can also run the sample directly locally (See below).

## Running the sample locally

There are 2 sites that run as part of eShopOnWeb:

- **PublicApi** - The API for querying the catalog
- **Web** - The eCommerce site for shopping merchandise

Most of the solution's functionality works with just the web application running.

However, the site's Admin page relies on Blazor WebAssembly running in the browser, and it must communicate with the server using the solution's `PublicApi` web application.

You can run the sample via the command line. You can also configure Visual Studio to start multiple projects. After cloning or downloading the sample, you must setup your database. 

To use the sample with a persistent database, you will need to run its Entity Framework Core migrations before you will be able to run the app.

You can also run the samples in Docker (see below).

### Running the sample via the command line

1. Go to the `PublicApi` folder in a terminal window and run `dotnet run` from there. 
2. After that, from the `Web` folder, you should run `dotnet run --launch-profile Web`. Now you should be able to browse to `https://localhost:5001/`. The admin part in Blazor is accessible to `https://localhost:5001/admin`  

Note that if you use this approach, you'll need to stop the application manually in order to build the solution (otherwise you'll get file locking errors).

### Running the sample via multiple startup projects

In Visual Studio, you can run with multiple startup projects.

1. Open `eShopOnWeb.sln` in Visual Studio.
2. Select **Project** > **Configure Startup Projects...**.
3. Select **Multiple startup projects**.
4. Set the following projects' Action option to `Start`:   
   - PublicApi
   - Web
5. Select **Apply**.
6. Select **OK**.
7. Select **Debug** > **Start Without Debugging**.

(This part isn't working yet.)

### Configuring the sample to use a data store

1. By default, the project uses a real database. If you want an in memory database, you can add in the `appsettings.json` file in the Web folder

   ```json
   {
       "UseOnlyInMemoryDatabase": true
   }
   ```

> **Note**: If you choose to use the in-memory implementation, be aware of the [In-memory Database Provider Limitations](https://learn.microsoft.com/en-us/ef/core/providers/in-memory/?tabs=dotnet-core-cli).

1. Ensure your connection strings in `appsettings.json` for both `PublicApi` and `Web` projects point to a local SQL Server instance. As this uses a SQL Server-based implementation for Entity Framework Core, [LocalDB](https://learn.microsoft.com/en-us/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-ver16) or other SQL Server databases are supported.
1. Ensure the tool EF was already installed. You can find some help [here](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet)

    ```
    dotnet tool update --global dotnet-ef
    ```

1. Open a command prompt in the `src\Web` folder and execute the following commands:

    ```
    dotnet restore
    dotnet tool restore
    dotnet ef database update -c catalogcontext -p ../Infrastructure/Infrastructure.csproj -s Web.csproj
    dotnet ef database update -c appidentitydbcontext -p ../Infrastructure/Infrastructure.csproj -s Web.csproj
    ```

    These commands will create two separate databases, one for the store's catalog data and shopping cart information, and one for the app's user credentials and identity data.

1. Run the application.

    The first time you run the application, it will seed both databases with data such that you should see products in the store, and you should be able to log in using the **demouser@microsoft.com** account.

    Note: If you need to create migrations, you can use these commands:

    ```
    -- create migration (from Web folder CLI)
    dotnet ef migrations add InitialModel --context catalogcontext -p ../Infrastructure/Infrastructure.csproj -s Web.csproj -o Data/Migrations

    dotnet ef migrations add InitialIdentityModel --context appidentitydbcontext -p ../Infrastructure/Infrastructure.csproj -s Web.csproj -o Identity/Migrations
    ```

## Running the sample in the dev container

This project includes a `.devcontainer` folder with a [dev container configuration](https://containers.dev/), which lets you use a container as a full-featured dev environment.

You can use the dev container to build and run the app without needing to install any of its tools locally! You can work in GitHub Codespaces or the VS Code Dev Containers extension.

Learn more about using the dev container in its [readme](/.devcontainer/devcontainerreadme.md).

## Running the sample using Docker

You can run the Web sample by running these commands from the root folder (where the .sln file is located):

```
docker-compose build
docker-compose up
```

You should be able to make requests to localhost:5106 for the Web project, and localhost:5200 for the Public API project once these commands complete. If you have any problems, especially with login, try from a new guest or incognito browser instance.

You can also run the applications by using the instructions located in their `Dockerfile` file in the root of each project. Again, run these commands from the root of the solution (where the .sln file is located).

## Community Extensions

We have some great contributions from the community, and while these aren't maintained by Microsoft we still want to highlight them.

[eShopOnWeb VB.NET](https://github.com/VBAndCs/eShopOnWeb_VB.NET) by Mohammad Hamdy Ghanem

[FShopOnWeb](https://github.com/NitroDevs/FShopOnWeb) An F# take on eShopOnWeb by Sean G. Wright and Kyle McMaster
