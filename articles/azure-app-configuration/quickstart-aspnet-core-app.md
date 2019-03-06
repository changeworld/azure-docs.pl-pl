---
title: Przewodnik Szybki start dotyczący używania usługi Azure App Configuration z platformą ASP.NET Core | Microsoft Docs
description: Przewodnik Szybki start dotyczący korzystania z usługi Azure App Configuration z aplikacjami platformy ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: ce19041b29d567f061dde59fbe041adf61f889a0
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961486"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Szybki start: Tworzenie aplikacji platformy ASP.NET Core używającej usługi Azure App Configuration

Usługa Azure App Configuration to zarządzana usługa konfiguracji na platformie Azure. Dzięki niej możesz łatwo przechowywać wszystkie ustawienia aplikacji w jednym miejscu oddzielonym od kodu i zarządzać tymi ustawieniami. W tym przewodniku Szybki start pokazano, jak zintegrować usługę z aplikacją internetową platformy ASP.NET Core. Platforma ASP.NET Core tworzy pojedynczy obiekt konfiguracji na podstawie pary klucz-wartość przy użyciu ustawień z jednego lub większej liczby określonych przez aplikację źródeł danych nazywanych *dostawcami konfiguracji*. Ponieważ klient .NET Core usługi App Configuration jest implementowany jako taki właśnie dostawca, usługa jest widoczna po prostu jako jeszcze jedno źródło danych.

Do wykonania kroków tego przewodnika Szybki start możesz użyć dowolnego edytora kodu. Doskonałym wyborem jest program [Visual Studio Code](https://code.visualstudio.com/), dostępny na platformach Windows, macOS i Linux.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, zainstaluj zestaw [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

Za pomocą [interfejsu wiersza polecenia platformy .NET Core ](https://docs.microsoft.com/dotnet/core/tools/) utworzysz nowy projekt Aplikacja internetowa platformy ASP.NET Core MVC. Przewagą używania interfejsu wiersza polecenia platformy .NET Core zamiast programu Visual Studio jest to, że jest dostępny na platformach Windows, macOS i Linux.

1. Utwórz nowy folder dla projektu. Na potrzeby tego przewodnika Szybki start nadamy mu nazwę *TestAppConfig*.

2. W nowym folderze wykonaj następujące polecenie, aby utworzyć nowy projekt Aplikacja internetowa platformy ASP.NET Core MVC:

        dotnet new mvc

## <a name="add-secret-manager"></a>Dodawanie narzędzia Secret Manager

Do swojego projektu dodasz narzędzie [Secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets). Narzędzie Secret manager przechowuje poufne dane potrzebne w pracy deweloperskiej poza Twoim drzewem projektu. Takie podejście zapobiega przypadkowemu ujawnieniu wpisów tajnych aplikacji w kodzie źródłowym.

- Otwórz plik *csproj*. Dodaj element `UserSecretsId`, jak pokazano poniżej, i zastąp jego wartość swoją własną, którą zazwyczaj jest identyfikator GUID. Zapisz plik.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.App" />
        <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
    </ItemGroup>

    </Project>
    ```

## <a name="connect-to-app-configuration-store"></a>Łączenie z magazynem konfiguracji aplikacji

1. Dodaj odwołanie do pakietu NuGet `Microsoft.Extensions.Configuration.AzureAppConfiguration`, wykonując następujące polecenie:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration

2. Uruchom następujące polecenie, aby przywrócić pakiety dla swojego projektu.

        dotnet restore

3. Dodaj wpis tajny o nazwie *ConnectionStrings:AppConfig* do narzędzia Secret Manager.

    Ten wpis tajny będzie zawierać parametry połączenia umożliwiające dostęp do magazynu konfiguracji aplikacji. Zastąp wartość w poleceniu poniżej parametrami połączenia dla Twojego magazynu konfiguracji aplikacji.

    To polecenie należy wykonać w tym samym katalogu, w którym znajduje się plik *csproj*.

        dotnet user-secrets set ConnectionStrings:AppConfig "Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>"

    Narzędzie Secret Manager będzie służyć tylko do testowania hostowanej lokalnie aplikacji internetowej. Po wdrożeniu aplikacji (na przykład w usłudze [Azure App Service](https://azure.microsoft.com/services/app-service/web)), użyjesz ustawienia aplikacji (na przykład **parametrów połączenia** w usłudze App Service) zamiast przechowywać parametry połączenia w narzędziu Secret Manager.

    Dostęp do tego wpisu tajnego można uzyskać za pomocą interfejsu API konfiguracji. Dwukropek (:) działa w nazwie konfiguracji z interfejsem API konfiguracji na wszystkich obsługiwanych platformach. Zobacz [Konfiguracja przez środowisko](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0#configuration-by-environment).

4. Otwórz plik *Program.cs* i zaktualizuj metodę `CreateWebHostBuilder`, tak aby używała usługi App Configuration przez wywołanie metody `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

5. Otwórz plik *Index.cshtml* w katalogu *Views* > *Home* i zastąp jego zawartość następującym kodem:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@Configuration["TestApp:Settings:Message"]</h1>
    </body>
    </html>
    ```

6. Otwórz plik *_Layout.cshtml* w katalogu *Views* > *Shared* i zastąp jego zawartość następującym kodem:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>@ViewData["Title"] - hello_world</title>

        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </head>
    <body>
        <div class="container body-content">
            @RenderBody()
        </div>

        <script src="~/lib/jquery/dist/jquery.js"></script>
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>

        @RenderSection("Scripts", required: false)
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Aby skompilować aplikację przy użyciu interfejsu wiersza polecenia platformy .NET Core, wykonaj następujące polecenie w powłoce poleceń:

        dotnet build

2. Po pomyślnym zakończeniu kompilacji wykonaj następujące polecenie w celu lokalnego uruchomienia aplikacji internetowej:

        dotnet run

3. Uruchom okno przeglądarki i przejdź pod adres `http://localhost:5000`, który jest domyślnym adresem URL aplikacji internetowej hostowanej lokalnie.

    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start został utworzony nowy magazyn konfiguracji aplikacji i użyto go z aplikacją internetową platformy ASP.NET Core. Aby dowiedzieć się więcej o korzystaniu z usługi App Configuration, przejdź do następnego samouczka, w którym zaprezentowano uwierzytelnianie.

> [!div class="nextstepaction"]
> [Tożsamości zarządzane na potrzeby integracji zasobów platformy Azure](./integrate-azure-managed-service-identity.md)
