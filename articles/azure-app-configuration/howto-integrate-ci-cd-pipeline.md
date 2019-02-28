---
title: Integracja z potoku ciągłej integracji i dostarczania za pomocą konfiguracji aplikacji platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można wygenerować pliku konfiguracji, korzystanie z danych w konfiguracji aplikacji platformy Azure podczas ciągłej integracji i ciągłego dostarczania
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 7b2e919bc46810e8478956675ffeb1cb0542da85
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957372"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integracja z potokiem ciągłej integracji/ciągłego wdrażania

Aby zwiększyć odporność aplikacji możliwość zdalnego nie będą mogli nawiązać połączenie konfiguracji aplikacji platformy Azure, należy spakować bieżące dane konfiguracji w pliku, który jest wdrożony z aplikacją i załadować lokalnie podczas jego uruchamiania . Ta metoda gwarantuje, czy aplikacja będzie co najmniej mają przypisane wartości domyślne ustawienie. Te wartości zostaną zastąpione przez wszystkie nowsze zmiany wprowadzone w konfiguracji sklepu z aplikacjami, gdy będzie ona dostępna.

Za pomocą [ **wyeksportować** ](./howto-import-export-data.md#export-data) funkcję konfiguracji aplikacji platformy Azure, możesz zautomatyzować proces pobierania bieżące dane konfiguracji jako pojedynczy plik. Ten plik można następnie osadzić w kroku kompilacji lub wdrożenia ciągłej integracji i ciągłego wdrażania potoku.

Poniższy przykład pokazuje, jak konfiguracja aplikacji obejmują dane jako kompilację krok dla aplikacji sieci web, wprowadzona w Szybki Start. Pełne [tworzenie aplikacji platformy ASP.NET Core z konfiguracji aplikacji](./quickstart-aspnet-core-app.md) pierwszy przed kontynuowaniem.

Do wykonania kroków tego przewodnika Szybki start możesz użyć dowolnego edytora kodu. Doskonałym wyborem jest program [Visual Studio Code](https://code.visualstudio.com/), dostępny na platformach Windows, macOS i Linux.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli tworzysz lokalnie, Pobierz i zainstaluj [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Jeśli jeszcze go.

Jeśli chcesz wykonać kompilację w chmurze za pomocą usługi Azure DevOps na przykład, upewnij się, [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) jest zainstalowany w systemie kompilacji.

## <a name="export-app-configuration-store"></a>Eksportowanie konfiguracji sklepu

1. Otwórz swoje *.csproj* pliku i Dodaj następujący kod:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    *ConnectionString* skojarzony z konfiguracją aplikacji magazynu powinny zostać dodane jako zmienną środowiskową.

2. Otwórz *Program.cs* i zaktualizuj `CreateWebHostBuilder` metodę, aby użyć pliku json wyeksportowanego przez wywołanie metody `config.AddJsonFile()` metody.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var directory = System.IO.Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);
                var settings = config.Build();

                config.AddJsonFile(Path.Combine(directory, "azureappconfig.json"));
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

## <a name="build-and-run-the-app-locally"></a>Kompilowanie i uruchamianie aplikacji lokalnie

1. Ustaw zmienną środowiskową o nazwie **ConnectionString** i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Korzystając z wiersza polecenia Windows, wykonaj następujące polecenie i ponownie uruchom wiersz polecenia, aby umożliwić zmiana zaczęła obowiązywać:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Jeśli korzystasz z systemem macOS lub Linux, wykonaj następujące polecenie:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Aby skompilować aplikację przy użyciu interfejsu wiersza polecenia platformy .NET Core, wykonaj następujące polecenie w powłoce poleceń:

        dotnet build

3. Po pomyślnym zakończeniu kompilacji wykonaj następujące polecenie w celu lokalnego uruchomienia aplikacji internetowej:

        dotnet run

4. Uruchom okno przeglądarki i przejdź do `http://localhost:5000`, która jest domyślny adres URL aplikacji sieci web hostowanych lokalnie.

    ![Lokalne uruchamianie aplikacji Szybki Start](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Kolejne kroki

* [Zarządzanych tożsamości dla integracji zasobów platformy Azure](./integrate-azure-managed-service-identity.md)
