---
title: Przewodnik Szybki start dotyczący używania usługi Azure App Configuration z platformą .NET Core | Microsoft Docs
description: Przewodnik Szybki start dotyczący korzystania z usługi Azure App Configuration z aplikacjami platformy .NET Core
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: 420d9b48013f5f6debe588667fe1cc0390517e66
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245382"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Szybki start: tworzenie aplikacji .NET Core z konfiguracją aplikacji

W tym przewodniku Szybki start można włączyć konfigurację aplikacji platformy Azure do aplikacji konsoli .NET Core, aby scentralizować przechowywanie i zarządzanie ustawieniami aplikacji oddzielnie od kodu.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download) — dostępny również w usłudze [Azure Cloud Shell](https://shell.azure.com).

## <a name="create-an-app-configuration-store"></a>Tworzenie sklepu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wybierz pozycję Tworzenie**Create** > **wartości klucza** w **Eksploratorze** > konfiguracji, aby dodać następujące pary klucz-wartość:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:Message | Dane z usługi Azure App Configuration |

    Pozostaw **etykietę** i **typ zawartości** puste na razie.

7. Wybierz przycisk **Zastosuj**.

## <a name="create-a-net-core-console-app"></a>Tworzenie aplikacji konsolowej platformy .NET Core

Za pomocą [interfejsu wiersza polecenia .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) służy do tworzenia nowego projektu aplikacji konsoli .NET Core. Zaletą korzystania z interfejsu wiersza polecenia .NET Core w programie Visual Studio jest to, że jest on dostępny na platformach Windows, macOS i Linux.  Alternatywnie należy użyć preinstalowanych narzędzi dostępnych w usłudze [Azure Cloud Shell.](https://shell.azure.com)

1. Utwórz nowy folder dla projektu.

2. W nowym folderze uruchom następujące polecenie, aby utworzyć nowy projekt aplikacji konsoli ASP.NET Core:

    ```dotnetcli
    dotnet new console
    ```

## <a name="connect-to-an-app-configuration-store"></a>Łączenie się ze sklepem konfiguracji aplikacji

1. Dodaj odwołanie do `Microsoft.Extensions.Configuration.AzureAppConfiguration` pakietu NuGet, uruchamiając następujące polecenie:

    ```dotnetcli
    dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration
    ```

2. Uruchom następujące polecenie, aby przywrócić pakiety dla projektu:

    ```dotnetcli
    dotnet restore
    ```

3. Otwórz *Program.cs*i dodaj odwołanie do dostawcy konfiguracji aplikacji .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Zaktualizuj metodę, `Main` aby `builder.AddAzureAppConfiguration()` użyć konfiguracji aplikacji, wywołując metodę.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString**i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. W wierszu polecenia uruchom następujące polecenie:

    ```cmd
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

    ```azurepowershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz systemu macOS lub Linux, uruchom następujące polecenie:

        export ConnectionString='connection-string-of-your-app-configuration-store'

    Uruchom ponownie wiersz polecenia, aby umożliwić zmianę. Wydrukuj wartość zmiennej środowiskowej, aby sprawdzić, czy jest ona prawidłowo ustawiona.

2. Uruchom następujące polecenie, aby utworzyć aplikację konsoli:

    ```dotnetcli
    dotnet build
    ```

3. Po pomyślnym zakończeniu kompilacji uruchom następujące polecenie, aby uruchomić aplikację lokalnie:

    ```dotnetcli
    dotnet run
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono nowy sklep konfiguracji aplikacji i użyto go z aplikacją konsoli .NET Core za pośrednictwem [dostawcy konfiguracji aplikacji.](https://go.microsoft.com/fwlink/?linkid=2074664) Aby dowiedzieć się, jak skonfigurować aplikację .NET Core do dynamicznego odświeżania ustawień konfiguracji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Włącz konfigurację dynamiczną](./enable-dynamic-configuration-dotnet-core.md)
