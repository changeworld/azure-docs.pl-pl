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
ms.openlocfilehash: b4cb571653cbe69939a1cbdc92338663e4e7125f
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576076"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Szybki start: tworzenie aplikacji .NET Framework przy użyciu usługi Azure App Configuration

Usługa Azure App Configuration to zarządzana usługa konfiguracji na platformie Azure. Umożliwia on łatwe przechowywać i zarządzać wszystkie ustawienia aplikacji w jednym miejscu, który jest oddzielony od kodu. W tym przewodniku Szybki start pokazano, jak zintegrować usługę z aplikacją konsolową systemu Windows opartą na programie NET Framework.

![Lokalny kompletny przewodnik Szybki Start](./media/quickstarts/dotnet-fx-app-run.png)

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start, instaluje [programu Visual Studio 2017](https://visualstudio.microsoft.com/vs) i [platformy .NET Framework 4.7.1](https://dotnet.microsoft.com/download) lub nowszy, jeśli jeszcze go.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-console-app"></a>Tworzenie aplikacji konsolowej platformy .NET

1. Uruchom program Visual Studio, a następnie wybierz pozycję **pliku** > **New** > **projektu**.

2. W **nowy projekt**, wybierz opcję **zainstalowane** > **Visual C#**   >  **pulpitu Windows**. Wybierz **Aplikacja konsoli (.NET Framework)**, a następnie wprowadź nazwę dla projektu. Wybierz **platformy .NET Framework 4.7.1** lub się i wybierz **OK**.

## <a name="connect-to-an-app-configuration-store"></a>Łączenie do sklepu z aplikacjami konfiguracji

1. Kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**. Na **Przeglądaj** kartę, wyszukiwanie i dodaj następujące pakiety NuGet do projektu. Jeśli nie można go znaleźć, wybrać **Uwzględnij wersję wstępną** pole wyboru.

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    ```

2. Aktualizacja *App.config* plik projektu w następujący sposób:

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

   Parametry połączenia magazynu konfiguracji aplikacji jest do odczytu w zmiennej środowiskowej `ConnectionString`. Dodaj `Environment` konstruktora konfiguracji przed `MyConfigStore` w `configBuilders` właściwość `appSettings` sekcji.

3. Otwórz *Program.cs*i zaktualizuj `Main` metodę, aby można było używać konfiguracji aplikacji, wywołując `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString** na parametry połączenia magazynu konfiguracji aplikacji. Korzystając z wiersza polecenia Windows, uruchom następujące polecenie:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

2. Uruchom program Visual Studio, aby umożliwić zmiana zaczęła obowiązywać. Naciśnij klawisze Ctrl + F5, aby skompilować i uruchomić aplikację konsoli.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start utworzyliśmy nowym magazynem konfiguracji aplikacji i używać go z aplikacji konsoli .NET Framework. Aby dowiedzieć się więcej o tym, jak używać konfiguracji aplikacji, przejdź do następnego samouczka, który demonstruje uwierzytelniania.

> [!div class="nextstepaction"]
> [Zarządzanych tożsamości dla integracji zasobów platformy Azure](./integrate-azure-managed-service-identity.md)
