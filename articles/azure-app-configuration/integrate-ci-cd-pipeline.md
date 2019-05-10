---
title: Samouczek w celu integracji z potoku ciągłej integracji i dostarczania za pomocą konfiguracji aplikacji platformy Azure | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak wygenerować plik konfiguracji przy użyciu danych w konfiguracji aplikacji platformy Azure podczas ciągłej integracji i ciągłego dostarczania
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
ms.openlocfilehash: 26bd49af7245d6e6dde3162a2e1d95c54f13e35b
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415947"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integracja z potokiem ciągłej integracji/ciągłego wdrażania

W tym artykule opisano różne sposoby przy użyciu danych z konfiguracji aplikacji platformy Azure w ciągłej integracji i ciągłego wdrażania systemu.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Używać konfiguracji aplikacji w potoku metodyki DevOps platformy Azure

Jeśli masz potokiem DevOps platformy Azure, można pobrać wartości kluczy z konfiguracji aplikacji i ustaw je jako zmienne zadań. [Rozszerzenia DevOps konfiguracji aplikacji platformy Azure](https://go.microsoft.com/fwlink/?linkid=2091063) jest modułem dodatkowym, które zapewnia tę funkcjonalność. Po prostu wykonaj jego instrukcje, aby użyć rozszerzenia w kompilacji lub wydania sekwencji zadań.

## <a name="deploy-app-configuration-data-with-your-application"></a>Wdróż dane konfiguracji aplikacji z aplikacją

Aplikacja może przestać działać do uruchomienia, jeśli zależy od konfiguracji aplikacji platformy Azure i nie można przejść do niego. Możesz zwiększyć odporność aplikacji radzenia sobie z takich przypadkach jednak prawdopodobnie nie jest w stanie. Aby to zrobić, Utwórz pakiet bieżące dane konfiguracji do pliku, który jest wdrożony z aplikacją i ładowanie lokalnie podczas jego uruchamiania. Ta metoda gwarantuje, że Twoja aplikacja ma domyślne ustawienie wartości co najmniej. Te wartości są zastępowane przez wszystkie nowsze zmiany wprowadzone w konfiguracji sklepu z aplikacjami, gdy będzie ona dostępna.

Za pomocą [wyeksportować](./howto-import-export-data.md#export-data) funkcję konfiguracji aplikacji platformy Azure, możesz zautomatyzować proces pobierania bieżące dane konfiguracji jako pojedynczy plik. Następnie osadź ten plik w kroku kompilacji lub wdrożenia ciągłej integracji i ciągłego wdrażania (CI/CD) potoku.

Poniższy przykład pokazuje, jak konfiguracja aplikacji obejmują dane jako kompilację krok dla aplikacji sieci web, wprowadzona w Szybki Start. Przed kontynuowaniem należy zakończyć [tworzenie aplikacji platformy ASP.NET Core z konfiguracji aplikacji](./quickstart-aspnet-core-app.md) pierwszy.

Wykonaj kroki w tym samouczku, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) jest doskonałą opcją dostępne w Windows, macOS i platformy Linux.

### <a name="prerequisites"></a>Wymagania wstępne

Jeśli tworzysz lokalnie, Pobierz i zainstaluj [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Jeśli jeszcze go.

Aby wykonać kompilację w chmurze, za pomocą usługi Azure DevOps na przykład, upewnij się, [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) jest zainstalowany w systemie kompilacji.

### <a name="export-an-app-configuration-store"></a>Eksportowanie konfiguracji sklepu z aplikacjami

1. Otwórz swoje *.csproj* pliku i Dodaj następujący skrypt:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Dodaj *ConnectionString* skojarzonych z Twoim sklepie z aplikacjami konfiguracji jako zmienną środowiskową.

2. Otwórz *Program.cs*i zaktualizuj `CreateWebHostBuilder` metodę wyeksportowany plik JSON, wywołując `config.AddJsonFile()` metody.

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

1. Ustaw zmienną środowiskową o nazwie **ConnectionString**i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Korzystając z wiersza polecenia Windows, uruchom następujące polecenie i ponownie uruchom wiersz polecenia, aby umożliwić zmiana zaczęła obowiązywać:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Jeśli korzystasz z systemem macOS lub Linux, uruchom następujące polecenie:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Aby skompilować aplikację przy użyciu interfejsu wiersza polecenia platformy .NET Core, uruchom następujące polecenie w powłoce poleceń:

        dotnet build

3. Po pomyślnym zakończeniu kompilacji, uruchom następujące polecenie, aby lokalnie uruchomić aplikację sieci web:

        dotnet run

4. Otwórz okno przeglądarki i przejdź do `http://localhost:5000`, która jest domyślny adres URL aplikacji sieci web hostowanych lokalnie.

    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku został wyeksportowany dane konfiguracji aplikacji platformy Azure do użycia w potoku wdrożenia. Aby dowiedzieć się więcej o tym, jak używać konfiguracji aplikacji, przejdź do przykładów interfejsu wiersza polecenia platformy Azure.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanych](./howto-integrate-azure-managed-service-identity.md)
