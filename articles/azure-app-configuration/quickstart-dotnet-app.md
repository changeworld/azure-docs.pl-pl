---
title: Przewodnik Szybki start dotyczący używania usługi Azure App Configuration z programem .NET Framework | Microsoft Docs
description: Przewodnik Szybki start dotyczący korzystania z usługi Azure App Configuration z aplikacjami .NET Framework
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: b5e41b1f9ee982b8ff8c86232f715d5dab705cd6
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2019
ms.locfileid: "56962166"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Szybki start: tworzenie aplikacji .NET Framework przy użyciu usługi Azure App Configuration

Usługa Azure App Configuration to zarządzana usługa konfiguracji na platformie Azure. Dzięki niej możesz łatwo przechowywać wszystkie ustawienia aplikacji w jednym miejscu oddzielonym od kodu i zarządzać tymi ustawieniami. W tym przewodniku Szybki start pokazano, jak zintegrować usługę z aplikacją konsolową systemu Windows opartą na programie NET Framework.

![Przewodnik Szybki start ukończony, wersja lokalna](./media/quickstarts/dotnet-fx-app-run.png)

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, zainstaluj program [Visual Studio 2017](https://visualstudio.microsoft.com/vs) i program [.NET Framework 4.7.1](https://dotnet.microsoft.com/download) lub nowszy, jeśli jeszcze tego nie zrobiono.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-console-app"></a>Tworzenie aplikacji konsolowej platformy .NET

1. Uruchom program Visual Studio, a następnie wybierz pozycję **Plik** > **Nowy** > **Projekt**.

2. W oknie dialogowym **Nowy projekt** wybierz pozycję **Zainstalowane**, rozwiń pozycję **Visual C#** > **Aplikacja klasyczna systemu Windows**, wybierz pozycję**Aplikacja konsolowa (.NET Framework)**, wpisz **nazwę** projektu, wybierz program **.NET Framework 4.7.1** lub nowszy i kliknij przycisk **OK**.

## <a name="connect-to-app-configuration-store"></a>Łączenie z magazynem konfiguracji aplikacji

1. Kliknij projekt prawym przyciskiem myszy i wybierz pozycję **Zarządzaj pakietami NuGet**. Na karcie **Przeglądaj** wyszukaj i dodaj do projektu następujące pakiety NuGet (zaznacz pole **Uwzględnij wersję wstępną**, jeśli nie możesz ich znaleźć).
    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    ```

2. Zaktualizuj plik *App.config* w projekcie w następujący sposób:

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   Uwaga! Ponieważ będziemy odczytywać parametry połączenia Twojego magazynu konfiguracji aplikacji ze zmiennej środowiskowej `ConnectionString`, pamiętaj o dodaniu konstruktora konfiguracji `Environment` przed elementem `MyConfigStore` we właściwości `configBuilders` sekcji `appSettings`.

3. Otwórz plik *Program.cs* i zaktualizuj metodę `Main`, aby używała usługi App Configuration przez wywołanie elementu `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString** na parametry połączenia magazynu konfiguracji aplikacji. Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

2. Uruchom ponownie program Visual Studio, aby zastosować zmiany, a następnie naciśnij klawisze **Ctrl + F5** na klawiaturze, aby skompilować i uruchomić aplikację konsolową.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start został utworzony nowy magazyn konfiguracji aplikacji i użyto go z aplikacją konsolową .NET Framework. Aby dowiedzieć się więcej o korzystaniu z usługi App Configuration, przejdź do następnego samouczka, w którym zaprezentowano uwierzytelnianie.

> [!div class="nextstepaction"]
> [Tożsamości zarządzane na potrzeby integracji zasobów platformy Azure](./integrate-azure-managed-service-identity.md)
