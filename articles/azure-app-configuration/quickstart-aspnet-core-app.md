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
ms.date: 10/11/2019
ms.author: yegu
ms.openlocfilehash: 91712b3f730317e65cda7b48c8f5636b2fb9ab2c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185088"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Szybki Start: Tworzenie aplikacji ASP.NET Core przy użyciu konfiguracji aplikacji platformy Azure

W tym przewodniku szybki start dołączysz konfigurację aplikacji platformy Azure do aplikacji ASP.NET Core w celu scentralizowanego przechowywania i zarządzania ustawieniami aplikacji oddzielonymi od kodu. ASP.NET Core kompiluje pojedynczy obiekt konfiguracji oparty na kluczu wartości przy użyciu ustawień z co najmniej jednego źródła danych, które są określone przez aplikację. Te źródła danych są nazywane *dostawcami konfiguracji*. Ponieważ klient .NET Core konfiguracji aplikacji jest zaimplementowany jako ten dostawca, usługa będzie wyświetlana jak inne źródło danych.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- [Zestaw SDK dla platformy .NET Core](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wybierz pozycję **Eksplorator konfiguracji** >  **+ Utwórz** , aby dodać następujące pary klucz-wartość:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:BackgroundColor | Biały |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | Czarny |
    | TestApp:Settings:Message | Dane z usługi Azure App Configuration |

    Dla tej pory pozostaw pustą **etykietę** i **Typ zawartości** .

## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

Aby utworzyć nowy projekt aplikacji sieci Web MVC ASP.NET Core, należy użyć [interfejsu wiersza polecenia platformy .NET Core](https://docs.microsoft.com/dotnet/core/tools/) . Zaletą korzystania z interfejs wiersza polecenia platformy .NET Core przez program Visual Studio jest to, że jest on dostępny na platformach Windows, macOS i Linux.

1. Utwórz nowy folder dla projektu. W tym przewodniku szybki start nadaj mu nazwę *TestAppConfig*.

2. W nowym folderze Uruchom następujące polecenie, aby utworzyć nowy projekt aplikacji sieci Web ASP.NET Core MVC:

    ```CLI
        dotnet new mvc --no-https
    ```

## <a name="add-secret-manager"></a>Dodawanie narzędzia Secret Manager

Aby użyć Menedżera wpisów tajnych, Dodaj element `UserSecretsId` do pliku *. csproj* .

- Otwórz plik *. csproj* . Dodaj element `UserSecretsId`, jak pokazano poniżej. Możesz użyć tego samego identyfikatora GUID lub można zastąpić tę wartość własną. Zapisz plik.

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

Narzędzie Secret manager przechowuje poufne dane potrzebne w pracy deweloperskiej poza Twoim drzewem projektu. Takie podejście zapobiega przypadkowemu ujawnieniu wpisów tajnych aplikacji w kodzie źródłowym. Aby uzyskać więcej informacji na temat Menedżera wpisów tajnych, zobacz [bezpieczne przechowywanie wpisów tajnych aplikacji w programie Development w ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets)

## <a name="connect-to-an-app-configuration-store"></a>Nawiązywanie połączenia z magazynem konfiguracji aplikacji

1. Dodaj odwołanie do pakietu NuGet `Microsoft.Azure.AppConfiguration.AspNetCore`, uruchamiając następujące polecenie:

    ```CLI
        dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-010060003-1250
    ```
2. Uruchom następujące polecenie, aby przywrócić pakiety dla projektu:

    ```CLI
        dotnet restore
    ```
3. Dodaj wpis tajny o nazwie *ConnectionStrings:AppConfig* do narzędzia Secret Manager.

    Ten wpis tajny zawiera parametry połączenia w celu uzyskania dostępu do magazynu konfiguracji aplikacji. Zastąp wartość w poniższym poleceniu parametrami połączenia dla magazynu konfiguracji aplikacji.

    To polecenie należy wykonać w tym samym katalogu, w którym znajduje się plik *csproj*.

    ```CLI
        dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > Niektóre powłoki poobcinają parametry połączenia, chyba że są ujęte w cudzysłów. Upewnij się, że dane wyjściowe polecenia `dotnet user-secrets` pokazują wszystkie parametry połączenia. Jeśli tak nie jest, należy ponownie uruchomić polecenie, umieszczając parametry połączenia w cudzysłowie.

    Menedżer wpisów tajnych służy tylko do lokalnego testowania aplikacji sieci Web. Gdy aplikacja jest wdrażana w [Azure App Service](https://azure.microsoft.com/services/app-service/web), na przykład w celu przechowywania parametrów połączenia należy użyć **parametrów połączenia** w App Service, a nie za pomocą Menedżera Secret.

    Ten klucz tajny jest dostępny z interfejsem API konfiguracji. Dwukropek (:) działa w nazwie konfiguracji z interfejsem API konfiguracji na wszystkich obsługiwanych platformach. Zobacz [Konfiguracja według środowiska](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

4. Otwórz *program.cs*i Dodaj odwołanie do dostawcy konfiguracji aplikacji .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

5. Zaktualizuj metodę `CreateWebHostBuilder`, aby użyć konfiguracji aplikacji przez wywołanie metody `config.AddAzureAppConfiguration()`.
    
    > [!IMPORTANT]
    > `CreateHostBuilder` zastępuje `CreateWebHostBuilder` na platformie .NET Core 3,0.  Wybierz poprawną składnię opartą na Twoim środowisku.

    ### <a name="update-createwebhostbuilder-for-net-core-2x"></a>Aktualizacja `CreateWebHostBuilder` dla programu .NET Core 2. x

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

    ### <a name="update-createhostbuilder-for-net-core-3x"></a>Aktualizacja `CreateHostBuilder` dla programu .NET Core 3. x

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
        })
        .UseStartup<Startup>());
    ```

6. Otwórz *index. cshtml* w widokach > katalog macierzysty i Zastąp jego zawartość następującym kodem:

    ```HTML
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]</h1>
    ```

7. Otwórz *_Layout. cshtml* w widokach > udostępniony katalog i Zastąp jego zawartość następującym kodem:

    ```HTML
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

1. Aby skompilować aplikację przy użyciu interfejs wiersza polecenia platformy .NET Core, uruchom następujące polecenie w powłoce poleceń:

    ```CLI
       dotnet build
    ```

2. Po pomyślnym zakończeniu kompilacji Uruchom następujące polecenie, aby uruchomić aplikację sieci Web lokalnie:

    ```CLI
        dotnet run
    ```

3. Otwórz okno przeglądarki i przejdź do `http://localhost:5000`, który jest domyślnym adresem URL aplikacji sieci Web hostowanej lokalnie.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku szybki start utworzono nowy magazyn konfiguracji aplikacji i użył go za pomocą aplikacji internetowej ASP.NET Core za pośrednictwem [dostawcy konfiguracji aplikacji](https://go.microsoft.com/fwlink/?linkid=2074664). Aby dowiedzieć się, jak skonfigurować aplikację ASP.NET Core do dynamicznego odświeżania ustawień konfiguracji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Włącz konfigurację dynamiczną](./enable-dynamic-configuration-aspnet-core.md)
