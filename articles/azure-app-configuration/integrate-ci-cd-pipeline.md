---
title: 'Samouczek: integracja z potokiem ciągłej integracji i dostarczania'
titleSuffix: Azure App Configuration
description: W ramach tego samouczka nauczysz się generować plik konfiguracji przy użyciu danych w konfiguracji aplikacji platformy Azure podczas ciągłej integracji i dostarczania
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: f8adfee1e707181e78f4e845f61477b08af935e9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433633"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integracja z potokiem ciągłej integracji/ciągłego wdrażania

W tym artykule opisano różne sposoby używania danych z konfiguracji aplikacji platformy Azure w systemie ciągłej integracji i ciągłego wdrażania.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Korzystanie z konfiguracji aplikacji w potoku usługi Azure DevOps

Jeśli masz potok Azure DevOps, możesz pobrać wartości klucza z konfiguracji aplikacji i ustawić je jako zmienne zadań. [Rozszerzenie DevOps konfiguracji aplikacji platformy Azure](https://go.microsoft.com/fwlink/?linkid=2091063) to moduł dodatku, który udostępnia tę funkcję. Po prostu postępuj zgodnie z instrukcjami, aby użyć rozszerzenia w sekwencji zadań kompilacja lub wydanie.

## <a name="deploy-app-configuration-data-with-your-application"></a>Wdrażanie danych konfiguracji aplikacji za pomocą aplikacji

Uruchomienie aplikacji może się nie powieść, jeśli jest ona zależna od konfiguracji aplikacji platformy Azure i nie można się z nią skontaktować. Można zwiększyć odporność aplikacji na zaradzenie sobie z takimi zdarzeniami, jednak jest to mało prawdopodobne. W tym celu należy spakować bieżące dane konfiguracji do pliku, który jest wdrażany wraz z aplikacją i załadowany lokalnie podczas jego uruchamiania. To podejście gwarantuje, że aplikacja ma domyślne wartości ustawień co najmniej. Te wartości są zastępowane przez wszelkie nowsze zmiany w magazynie konfiguracji aplikacji, gdy jest on dostępny.

Za pomocą funkcji [eksportu](./howto-import-export-data.md#export-data) usługi Azure App Configuration można zautomatyzować proces pobierania bieżących danych konfiguracji w postaci pojedynczego pliku. Następnie osadź ten plik w kroku kompilacja lub wdrożenie w potoku ciągłej integracji i ciągłego wdrażania (CI/CD).

Poniższy przykład pokazuje, jak uwzględnić dane konfiguracji aplikacji jako krok kompilacji dla aplikacji sieci Web wprowadzonej w przewodnikach Szybki Start. Przed kontynuowaniem najpierw Zakończ [Tworzenie aplikacji ASP.NET Coreej z konfiguracją aplikacji](./quickstart-aspnet-core-app.md) .

Aby wykonać kroki opisane w tym samouczku, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) jest doskonałym rozwiązaniem dostępnym na platformach Windows, MacOS i Linux.

### <a name="prerequisites"></a>Wymagania wstępne

Jeśli kompilujesz lokalnie, Pobierz i zainstaluj [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , jeśli jeszcze tego nie zrobiono.

Aby przeprowadzić kompilację w chmurze, w usłudze Azure DevOps na przykład upewnij się, że [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) jest zainstalowany w systemie kompilacji.

### <a name="export-an-app-configuration-store"></a>Eksportowanie magazynu konfiguracji aplikacji

1. Otwórz plik *. csproj* i Dodaj następujący skrypt:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Dodaj *Parametry ConnectionString* skojarzone z magazynem konfiguracji aplikacji jako zmienną środowiskową.

2. Otwórz *program.cs*i zaktualizuj metodę `CreateWebHostBuilder`, aby użyć wyeksportowanego pliku JSON przez wywołanie metody `config.AddJsonFile()`.

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

1. Ustaw zmienną środowiskową o nazwie **ConnectionString**i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie i ponownie uruchom wiersz polecenia, aby zezwolić na wprowadzenie zmiany:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Jeśli używasz macOS lub Linux, uruchom następujące polecenie:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Aby skompilować aplikację przy użyciu interfejs wiersza polecenia platformy .NET Core, uruchom następujące polecenie w powłoce poleceń:

        dotnet build

3. Po pomyślnym zakończeniu kompilacji Uruchom następujące polecenie, aby uruchomić aplikację sieci Web lokalnie:

        dotnet run

4. Otwórz okno przeglądarki i przejdź do `http://localhost:5000`, który jest domyślnym adresem URL aplikacji sieci Web hostowanej lokalnie.

    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku wyeksportowano dane konfiguracji aplikacji platformy Azure, które będą używane w potoku wdrożenia. Aby dowiedzieć się więcej na temat korzystania z konfiguracji aplikacji, przejdź do przykładów interfejsu wiersza polecenia platformy Azure.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./howto-integrate-azure-managed-service-identity.md)
