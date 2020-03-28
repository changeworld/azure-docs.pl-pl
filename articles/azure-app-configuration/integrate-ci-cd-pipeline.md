---
title: Integracja konfiguracji aplikacji platformy Azure przy użyciu ciągłego potoku integracji i dostarczania
description: Dowiedz się, jak zaimplementować ciągłą integrację i dostarczanie przy użyciu konfiguracji aplikacji platformy Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: lcozzens
ms.openlocfilehash: c744557471a9b37bd620bb9195bdb709c24649ab
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77047287"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integracja z potokiem ciągłej integracji/ciągłego wdrażania

W tym artykule wyjaśniono, jak używać danych z usługi Azure App Configuration w systemie ciągłej integracji i ciągłego wdrażania.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Korzystanie z konfiguracji aplikacji w potoku programu Azure DevOps

Jeśli masz potok devops platformy Azure, można pobrać wartości klucza z konfiguracji aplikacji i ustawić je jako zmienne zadań. [Rozszerzenie DevOps konfiguracji aplikacji platformy Azure](https://go.microsoft.com/fwlink/?linkid=2091063) jest modułem dodatku, który udostępnia tę funkcję. Postępuj zgodnie z jego instrukcjami, aby użyć rozszerzenia w sekwencji zadań kompilacji lub wydania.

## <a name="deploy-app-configuration-data-with-your-application"></a>Wdrażanie danych konfiguracji aplikacji za pomocą aplikacji

Uruchomienie aplikacji może zakończyć się niepowodzeniem, jeśli zależy od konfiguracji aplikacji platformy Azure i nie można do niej dotrzeć. Zwiększ odporność aplikacji, pakując dane konfiguracyjne do pliku, który jest wdrażany z aplikacją i ładowany lokalnie podczas uruchamiania aplikacji. Takie podejście gwarantuje, że aplikacja ma domyślne wartości ustawień podczas uruchamiania. Te wartości są zastępowane przez wszystkie nowsze zmiany w sklepie konfiguracji aplikacji, gdy jest on dostępny.

Za pomocą funkcji [eksportu](./howto-import-export-data.md#export-data) konfiguracji aplikacji platformy Azure można zautomatyzować proces pobierania bieżących danych konfiguracji jako pojedynczy plik. Następnie można osadzić ten plik w kroku kompilacji lub wdrożenia w potoku ciągłej integracji i ciągłego wdrażania (CI/CD).

W poniższym przykładzie pokazano, jak uwzględnić dane konfiguracji aplikacji jako krok kompilacji dla aplikacji sieci web wprowadzone w przewodnikach Szybki start. Przed kontynuowaniem zakończ tworzenie aplikacji ASP.NET Core przy czym najpierw [zostanie utworzona konfiguracja aplikacji.](./quickstart-aspnet-core-app.md)

Aby wykonać czynności opisane w tym samouczku, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) to doskonała opcja dostępna na platformach Windows, macOS i Linux.

### <a name="prerequisites"></a>Wymagania wstępne

Jeśli tworzysz lokalnie, pobierz i zainstaluj [interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) jeśli jeszcze tego nie zrobiłeś.

Aby wykonać kompilację w chmurze, na przykład za pomocą usługi Azure DevOps, upewnij się, że [narzędzie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) jest zainstalowane w systemie kompilacji.

### <a name="export-an-app-configuration-store"></a>Eksportowanie sklepu konfiguracji aplikacji

1. Otwórz plik *csproj* i dodaj następujący skrypt:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```
1. Otwórz *Program.cs*i zaktualizuj `CreateWebHostBuilder` metodę używania eksportowanego pliku `config.AddJsonFile()` JSON, wywołując metodę.  Dodaj `System.Reflection` również obszar nazw.

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

### <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString**i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. 
    Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie i uruchom ponownie wiersz polecenia, aby umożliwić zastosowanie zmiany:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Jeśli używasz systemu macOS lub Linux, uruchom następujące polecenie:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Aby utworzyć aplikację przy użyciu interfejsu wiersza polecenia .NET Core, uruchom następujące polecenie w powłoce poleceń:

        dotnet build

3. Po pomyślnym zakończeniu kompilacji uruchom następujące polecenie, aby uruchomić aplikację sieci web lokalnie:

        dotnet run

4. Otwórz okno przeglądarki i `http://localhost:5000`przejdź do , który jest domyślnym adresem URL aplikacji internetowej hostowane lokalnie.

    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku wyeksportowano dane konfiguracji aplikacji platformy Azure do użycia w potoku wdrażania. Aby dowiedzieć się więcej na temat korzystania z konfiguracji aplikacji, przejdź do przykładów interfejsu wiersza polecenia platformy Azure.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./howto-integrate-azure-managed-service-identity.md)
