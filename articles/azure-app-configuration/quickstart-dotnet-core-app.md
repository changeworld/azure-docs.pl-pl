---
title: Przewodnik Szybki start dotyczący używania usługi Azure App Configuration z platformą .NET Core | Microsoft Docs
description: Przewodnik Szybki start dotyczący korzystania z usługi Azure App Configuration z aplikacjami platformy .NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 986bda4ab95985820e61c93bbe82431fb50c983b
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185028"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Szybki Start: Tworzenie aplikacji platformy .NET Core przy użyciu konfiguracji aplikacji

W tym przewodniku szybki start dołączysz konfigurację aplikacji platformy Azure do aplikacji konsolowej .NET Core w celu scentralizowanego przechowywania i zarządzania ustawieniami aplikacji oddzielonymi od kodu.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- [Zestaw SDK dla platformy .NET Core](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wybierz pozycję **Eksplorator konfiguracji** >  **+ Utwórz** , aby dodać następujące pary klucz-wartość:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:Message | Dane z usługi Azure App Configuration |

    Dla tej pory pozostaw pustą **etykietę** i **Typ zawartości** .

## <a name="create-a-net-core-console-app"></a>Tworzenie aplikacji konsolowej platformy .NET Core

Za pomocą [interfejsu wiersza polecenia platformy .NET Core](https://docs.microsoft.com/dotnet/core/tools/) można utworzyć nowy projekt aplikacji konsoli .NET Core. Zaletą korzystania z interfejs wiersza polecenia platformy .NET Core przez program Visual Studio jest to, że jest on dostępny na platformach Windows, macOS i Linux.

1. Utwórz nowy folder dla projektu.

2. W nowym folderze Uruchom następujące polecenie, aby utworzyć nowy projekt aplikacji konsoli ASP.NET Core:

        dotnet new console

## <a name="connect-to-an-app-configuration-store"></a>Nawiązywanie połączenia z magazynem konfiguracji aplikacji

1. Dodaj odwołanie do pakietu NuGet `Microsoft.Extensions.Configuration.AzureAppConfiguration`, uruchamiając następujące polecenie:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 2.0.0-preview-009470001-1371

2. Uruchom następujące polecenie, aby przywrócić pakiety dla projektu:

        dotnet restore

3. Otwórz *program.cs*i Dodaj odwołanie do dostawcy konfiguracji aplikacji .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Zaktualizuj metodę `Main`, aby użyć konfiguracji aplikacji przez wywołanie metody `builder.AddAzureAppConfiguration()`.

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

1. Ustaw zmienną środowiskową o nazwie **ConnectionString**i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie i ponownie uruchom wiersz polecenia, aby zezwolić na wprowadzenie zmiany:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Jeśli używasz macOS lub Linux, uruchom następujące polecenie:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Uruchom następujące polecenie, aby skompilować aplikację konsolową:

        dotnet build

3. Po pomyślnym zakończeniu kompilacji Uruchom następujące polecenie, aby uruchomić aplikację lokalnie:

        dotnet run

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono nowy magazyn konfiguracji aplikacji i używał go z aplikacją konsolową platformy .NET Core za pośrednictwem [dostawcy konfiguracji aplikacji](https://go.microsoft.com/fwlink/?linkid=2074664). Aby dowiedzieć się, jak skonfigurować aplikację .NET Core do dynamicznego odświeżania ustawień konfiguracji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Włącz konfigurację dynamiczną](./enable-dynamic-configuration-dotnet-core.md)
