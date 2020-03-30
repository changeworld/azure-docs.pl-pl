---
title: Przewodnik Szybki start dotyczący używania usługi Azure App Configuration z platformą ASP.NET Core | Microsoft Docs
description: Szybki start do korzystania z konfiguracji aplikacji platformy Azure z aplikacjami ASP.NET Core
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 537dabe09c41012b9e15998ce3af8198dcfb62d3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245778"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Szybki start: tworzenie aplikacji ASP.NET Core za pomocą konfiguracji aplikacji platformy Azure

W tym przewodniku Szybki start użyjesz usługi Azure App Configuration do scentralizowania magazynu i zarządzania ustawieniami aplikacji dla aplikacji ASP.NET Core. ASP.NET Core tworzy pojedynczy obiekt konfiguracji oparty na wartości klucza przy użyciu ustawień z jednego lub więcej źródeł danych określonych przez aplikację. Te źródła danych są znane jako *dostawcy konfiguracji*. Ponieważ klient .NET Core konfiguracji aplikacji jest implementowany jako dostawca konfiguracji, usługa wygląda jak inne źródło danych.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)
- [Zestaw SDK dla platformy .NET Core](https://dotnet.microsoft.com/download)

>[!TIP]
> Usługa Azure Cloud Shell to bezpłatna powłoka interaktywna, której można użyć do uruchomienia instrukcji wiersza polecenia w tym artykule.  Ma wstępnie zainstalowane typowe narzędzia platformy Azure, w tym zestaw SDK .NET Core. Jeśli jesteś zalogowany do subskrypcji platformy Azure, uruchom usługę [Azure Cloud Shell](https://shell.azure.com) z shell.azure.com.  Więcej informacji o usłudze Azure Cloud Shell można [znaleźć, czytając naszą dokumentację](../cloud-shell/overview.md)

## <a name="create-an-app-configuration-store"></a>Tworzenie sklepu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wybierz pozycję Tworzenie**Create** > **wartości klucza** w **Eksploratorze** > konfiguracji, aby dodać następujące pary klucz-wartość:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:BackgroundColor | Biały |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | Czarny |
    | TestApp:Settings:Message | Dane z usługi Azure App Configuration |

    Pozostaw **etykietę** i **typ zawartości** puste na razie. Wybierz przycisk **Zastosuj**.

## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

Użyj [interfejsu wiersza polecenia .NET Core (CLI),](https://docs.microsoft.com/dotnet/core/tools/) aby utworzyć nowy projekt aplikacji sieci Web ASP.NET Core MVC. [Usługa Azure Cloud Shell](https://shell.azure.com) udostępnia te narzędzia.  Są one również dostępne na platformach Windows, macOS i Linux.

1. Utwórz nowy folder dla projektu. Dla tego szybkiego startu, nazwij go *TestAppConfig*.

1. W nowym folderze uruchom następujące polecenie, aby utworzyć nowy projekt aplikacji sieci Web ASP.NET Core MVC:

```dotnetcli
dotnet new mvc --no-https
```

## <a name="add-secret-manager"></a>Dodawanie narzędzia Secret Manager

Aby użyć Menedżera programu `UserSecretsId` Tajnego, dodaj element do pliku *csproj.*

1. Otwórz plik *csproj.*

1.  Dodaj `UserSecretsId` element, jak pokazano tutaj. Można użyć tego samego identyfikatora GUID lub zastąpić tę wartość własną.

    > [!IMPORTANT]
    > `CreateHostBuilder`zastępuje `CreateWebHostBuilder` w .NET Core 3.0.  Wybierz poprawną składnię na podstawie środowiska.
    
    #### <a name="net-core-2x"></a>[.NET Rdzeń 2.x](#tab/core2x)
    
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
    
    #### <a name="net-core-3x"></a>[.NET Rdzeń 3.x](#tab/core3x)
    
    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
        
        <PropertyGroup>
            <TargetFramework>netcoreapp3.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
    </Project>
    ```
    ---

1. Zapisz plik *csproj.*

Narzędzie Secret manager przechowuje poufne dane potrzebne w pracy deweloperskiej poza Twoim drzewem projektu. Takie podejście zapobiega przypadkowemu ujawnieniu wpisów tajnych aplikacji w kodzie źródłowym.

> [!TIP]
> Aby dowiedzieć się więcej o Secret Manager, zobacz [Bezpieczne przechowywanie tajemnic aplikacji w rozwoju w ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets)

## <a name="connect-to-an-app-configuration-store"></a>Łączenie się ze sklepem konfiguracji aplikacji

1. Dodaj odwołanie do `Microsoft.Azure.AppConfiguration.AspNetCore` pakietu NuGet, uruchamiając następujące polecenie:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Uruchom następujące polecenie, aby przywrócić pakiety dla projektu:

    ```dotnetcli
    dotnet restore
    ```

1. Dodaj wpis tajny o nazwie *ConnectionStrings:AppConfig* do narzędzia Secret Manager.

    Ten klucz tajny zawiera parametry połączenia, aby uzyskać dostęp do magazynu konfiguracji aplikacji. Zastąp wartość w poniższym poleceniu ciągiem połączenia dla magazynu konfiguracji aplikacji. Ciąg połączenia można znaleźć w obszarze **Klucze dostępu** w witrynie Azure portal.

    To polecenie należy wykonać w tym samym katalogu, w którym znajduje się plik *csproj*.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > Niektóre powłoki obcinają ciąg połączenia, chyba że jest on ujęty w cudzysłowach. Upewnij się, że `dotnet user-secrets` dane wyjściowe polecenia pokazuje cały ciąg połączenia. Jeśli tak nie jest, uruchom ponownie polecenie, otaczając ciąg połączenia w cudzysłowie.

    Menedżer tajny jest używany tylko do testowania aplikacji sieci web lokalnie. Gdy aplikacja jest wdrażana w [usłudze Azure App Service](https://azure.microsoft.com/services/app-service/web), na przykład, należy użyć ustawienia aplikacji **Parametry połączenia** w usłudze App Service zamiast Menedżera tajnego do przechowywania ciągu połączenia.

    Dostęp do tego klucza tajnego przy użyciu interfejsu API konfiguracji. Dwukropek (:) działa w nazwie konfiguracji z interfejsem API konfiguracji na wszystkich obsługiwanych platformach. Zobacz [Konfiguracja według środowiska](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

1. Otwórz *Program.cs*i dodaj odwołanie do dostawcy konfiguracji aplikacji .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Zaktualizuj metodę, `CreateWebHostBuilder` aby `config.AddAzureAppConfiguration()` użyć konfiguracji aplikacji, wywołując metodę.

    > [!IMPORTANT]
    > `CreateHostBuilder`zastępuje `CreateWebHostBuilder` w .NET Core 3.0.  Wybierz poprawną składnię na podstawie środowiska.

    #### <a name="net-core-2x"></a>[.NET Rdzeń 2.x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Rdzeń 3.x](#tab/core3x)

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

    ---

1. Przejdź * <app root>do /Views/Home* i otwórz *index.cshtml*. Zastąp jego zawartość następującym kodem:

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

1. Przejdź * <app root>do /Views/Shared* i otwórz *_Layout.cshtml*. Zastąp jego zawartość następującym kodem:

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

1. Aby utworzyć aplikację przy użyciu interfejsu wiersza polecenia .NET Core, przejdź do katalogu głównego aplikacji i uruchom następujące polecenie w powłoce poleceń:

    ```dotnetcli
    dotnet build
    ```

1. Po pomyślnym zakończeniu kompilacji uruchom następujące polecenie, aby uruchomić aplikację sieci web lokalnie:

    ```dotnetcli
    dotnet run
    ```

1. Jeśli pracujesz na komputerze lokalnym, użyj przeglądarki, `http://localhost:5000`aby przejść do . Jest to domyślny adres URL aplikacji internetowej hostowane lokalnie.  

Jeśli pracujesz w usłudze Azure Cloud Shell, wybierz przycisk *Podgląd sieci Web,* po którym następuje *configure*.  

![Przycisk Znajdź podgląd sieci Web](./media/quickstarts/cloud-shell-web-preview.png)

Po wyświetleniu monitu o skonfigurowanie portu do podglądu wprowadź wartość "5000" i wybierz pozycję *Otwórz i przeglądaj*.  Na stronie internetowej będzie można przeczytać "Dane z konfiguracji aplikacji platformy Azure".

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono nowy sklep konfiguracji aplikacji i użyto go z aplikacją internetową ASP.NET Core za pośrednictwem [dostawcy konfiguracji aplikacji.](https://go.microsoft.com/fwlink/?linkid=2074664) Aby dowiedzieć się, jak skonfigurować aplikację ASP.NET Core do dynamicznego odświeżania ustawień konfiguracji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Włącz konfigurację dynamiczną](./enable-dynamic-configuration-aspnet-core.md)
