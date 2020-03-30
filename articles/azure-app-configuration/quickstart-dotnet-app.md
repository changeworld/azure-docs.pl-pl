---
title: Przewodnik Szybki start dotyczący używania usługi Azure App Configuration z programem .NET Framework | Microsoft Docs
description: Przewodnik Szybki start dotyczący korzystania z usługi Azure App Configuration z aplikacjami .NET Framework
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: lcozzens
ms.openlocfilehash: 1c56088a2c51c50c7f9cf1ff1e790d580fdb08d8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245399"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Szybki start: tworzenie aplikacji .NET Framework za pomocą konfiguracji aplikacji platformy Azure

W tym przewodniku Szybki start można włączyć konfigurację aplikacji platformy Azure do aplikacji konsoli opartej na platformie .NET Framework, aby scentralizować przechowywanie i zarządzanie ustawieniami aplikacji oddzielnie od kodu.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Tworzenie sklepu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wybierz pozycję Tworzenie**Create** > **wartości klucza** w **Eksploratorze** > konfiguracji, aby dodać następujące pary klucz-wartość:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:Message | Dane z usługi Azure App Configuration |

    Pozostaw **etykietę** i **typ zawartości** puste na razie.

7. Wybierz przycisk **Zastosuj**.

## <a name="create-a-net-console-app"></a>Tworzenie aplikacji konsolowej platformy .NET

1. Uruchom program Visual Studio i wybierz pozycję **Plik** > **nowego** > **projektu**.

1. W **programie Tworzenie nowego projektu**filtruj typ projektu **konsoli** i kliknij aplikację Console **App (.NET Framework).** Wybierz **pozycję Dalej**.

1. W **obszarze Konfiguruj nowy projekt**wprowadź nazwę projektu. W obszarze **Framework**wybierz opcję **.NET Framework 4.7.1** lub nowszą. Wybierz **pozycję Utwórz**.

## <a name="connect-to-an-app-configuration-store"></a>Łączenie się ze sklepem konfiguracji aplikacji

1. Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami NuGet**. Na karcie **Przeglądaj** wyszukaj i dodaj do projektu następujące pakiety NuGet. Jeśli nie możesz ich znaleźć, zaznacz pole wyboru **Dołącz wydanie wstępne.**

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    System.Configuration.ConfigurationManager version 4.6.0 or later
    ```

1. Zaktualizuj plik *App.config* projektu w następujący sposób:

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

   Ciąg połączenia sklepu konfiguracji aplikacji jest odczytywany ze zmiennej `ConnectionString`środowiskowej . Dodaj `Environment` konstruktora `MyConfigStore` konfiguracji `configBuilders` przed właściwością `appSettings` sekcji.

1. Otwórz *Program.cs*i zaktualizuj `Main` metodę używania konfiguracji aplikacji przez wywołanie `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString** na ciąg połączenia magazynu konfiguracji aplikacji. Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```
1. Uruchom ponownie program Visual Studio, aby umożliwić zmianę, aby wejść w życie. Naciśnij klawisze Ctrl + F5, aby utworzyć i uruchomić aplikację konsoli.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono nowy magazyn konfiguracji aplikacji i użyto go z aplikacją konsoli .NET Framework. Wartość `AppSettings` nie `ConfigurationManager` zmieni się po uruchomieniu aplikacji. Biblioteka dostawcy konfiguracji .NET Standard konfiguracji można jednak również używać w aplikacji .NET Framework. Aby dowiedzieć się, jak włączyć aplikację .NET Framework dynamicznie odświeżać ustawienia konfiguracji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Włącz konfigurację dynamiczną](./enable-dynamic-configuration-dotnet.md)
