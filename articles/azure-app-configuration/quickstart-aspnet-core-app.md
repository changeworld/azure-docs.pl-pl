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
ms.openlocfilehash: 29cea7e72d6bd7f64f6cf2a68b7620090ea4eef3
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995937"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Szybki start: Tworzenie aplikacji platformy ASP.NET Core używającej usługi Azure App Configuration

Usługa Azure App Configuration to zarządzana usługa konfiguracji na platformie Azure. Umożliwia on łatwe przechowywać i zarządzać wszystkie ustawienia aplikacji w jednym miejscu, który jest oddzielony od kodu. W tym przewodniku Szybki start pokazano, jak zintegrować usługę z aplikacją internetową platformy ASP.NET Core. 

Platforma ASP.NET Core tworzy obiekt jednej konfiguracji na podstawie wartości klucza przy użyciu ustawienia ze źródeł danych, które są określone przez aplikację. Te źródła danych są znane jako *dostawcy konfiguracji*. Ponieważ konfiguracja aplikacji .NET Core, klient jest implementowany jako takie dostawcy, usługa wygląda innego źródła danych.

Wykonaj kroki w tym przewodniku Szybki Start, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) jest doskonałą opcją dostępne w Windows, macOS i platformy Linux.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start, instaluje [zestawu .NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wybierz **Explorer klucz/wartość** > **+ Utwórz** można dodać następujące pary klucz wartość:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:BackgroundColor | Biały |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | Czarny |
    | TestApp:Settings:Message | Dane z usługi Azure App Configuration |

    Pozostaw **etykiety** i **typu zawartości** puste, aby teraz.

## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

Możesz użyć [platformy .NET Core interfejsu wiersza polecenia (CLI)](https://docs.microsoft.com/dotnet/core/tools/) do utworzenia nowego projektu aplikacji sieci web platformy ASP.NET Core MVC. Zaletą używania interfejsu wiersza polecenia platformy .NET Core w programie Visual Studio jest, że jest ona dostępna w Windows, macOS i Linux platformy.

1. Utwórz nowy folder dla projektu. W tym przewodniku Szybki Start, nadaj jej nazwę *TestAppConfig*.

2. W nowym folderze uruchom następujące polecenie, aby utworzyć nowy projekt aplikacji sieci web platformy ASP.NET Core MVC:

        dotnet new mvc

## <a name="add-secret-manager"></a>Dodawanie narzędzia Secret Manager

Dodaj [narzędzie Menedżer klucz tajny](https://docs.microsoft.com/aspnet/core/security/app-secrets) do projektu. Narzędzie Secret manager przechowuje poufne dane potrzebne w pracy deweloperskiej poza Twoim drzewem projektu. Takie podejście zapobiega przypadkowemu ujawnieniu wpisów tajnych aplikacji w kodzie źródłowym.

- Otwórz plik *csproj*. Dodaj `UserSecretsId` elementu, jak pokazano poniżej i zastąp jego wartość swoją własną, który zazwyczaj jest identyfikatorem GUID. Zapisz plik.

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

## <a name="connect-to-an-app-configuration-store"></a>Łączenie do sklepu z aplikacjami konfiguracji

1. Dodaj odwołanie do `Microsoft.Extensions.Configuration.AzureAppConfiguration` pakietu NuGet, uruchamiając następujące polecenie:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 1.0.0-preview-007830001

2. Uruchom następujące polecenie, aby przywrócić pakiety dla projektu:

        dotnet restore

3. Dodaj wpis tajny o nazwie *ConnectionStrings:AppConfig* do narzędzia Secret Manager.

    Ten wpis tajny zawiera parametry połączenia, aby dostęp do magazynu konfiguracji aplikacji. Zastąp wartość w poniższym poleceniu ciąg połączenia dla magazynu konfiguracji aplikacji.

    To polecenie należy wykonać w tym samym katalogu, w którym znajduje się plik *csproj*.

        dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>

    Menedżer klucz tajny jest używana tylko w celu testowania aplikacji sieci web w środowisku lokalnym. Gdy aplikacja jest wdrożona, na przykład do [usługi Azure App Service](https://azure.microsoft.com/services/app-service/web), za pomocą aplikacji ustawienia, na przykład **parametry połączenia** w usłudze App Service. Użyj tego ustawienia, zamiast przechowywać parametry połączenia z menedżerem klucz tajny.

    Ten klucz tajny jest dostępny za pomocą konfiguracji interfejsu API. Dwukropek (:) działanie jest nazwa konfiguracji za pomocą interfejsu API konfiguracji na wszystkich obsługiwanych platformach. Zobacz [konfiguracji przez środowisko](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

4. Otwórz *Program.cs*i Dodaj odwołanie do dostawcy usługi konfiguracji aplikacji konfiguracji platformy .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

5. Aktualizacja `CreateWebHostBuilder` metodę, aby można było używać konfiguracji aplikacji, wywołując `config.AddAzureAppConfiguration()` metody.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .SetOfflineCache(new OfflineFileCache());
                });
            })
            .UseStartup<Startup>();
    ```

6. Otwórz Index.cshtml w widokach > Strona główna katalogu i zastąp jego zawartość następującym kodem:

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

7. Otwórz _Layout.cshtml w widokach > udostępniony katalog i zastąp jego zawartość następującym kodem:

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

1. Aby skompilować aplikację przy użyciu interfejsu wiersza polecenia platformy .NET Core, uruchom następujące polecenie w powłoce poleceń:

        dotnet build

2. Po pomyślnym zakończeniu kompilacji, uruchom następujące polecenie, aby lokalnie uruchomić aplikację sieci web:

        dotnet run

3. Otwórz okno przeglądarki i przejdź do `http://localhost:5000`, która jest domyślny adres URL aplikacji sieci web hostowanych lokalnie.

    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start został utworzony nowy magazyn konfiguracji aplikacji i go z aplikacją sieci web platformy ASP.NET Core za pomocą [dostawcę konfiguracji aplikacji](https://go.microsoft.com/fwlink/?linkid=2074664). Aby dowiedzieć się więcej o tym, jak używać konfiguracji aplikacji, przejdź do następnego samouczka, który demonstruje uwierzytelniania.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanych](./howto-integrate-azure-managed-service-identity.md)
