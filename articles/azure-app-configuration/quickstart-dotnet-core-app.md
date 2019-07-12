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
ms.openlocfilehash: c42066b3ac02264ee357bac7ed3fdd360e9d5d4a
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798414"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Szybki start: Utwórz .NET Core aplikacji za pomocą konfiguracji aplikacji

Usługa Azure App Configuration to zarządzana usługa konfiguracji na platformie Azure. Umożliwia on łatwe przechowywać i zarządzać wszystkie ustawienia aplikacji w jednym miejscu, który jest oddzielony od kodu. W tym przewodniku Szybki start pokazano, jak zintegrować usługę z aplikacją konsolową platformy .NET Core.

Wykonaj kroki w tym przewodniku Szybki Start, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) jest doskonałą opcją dostępne w Windows, macOS i platformy Linux.

![Uruchomienia aplikacji przewodnika Szybki start](./media/quickstarts/dotnet-core-app-run.png)

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start, instaluje [zestawu .NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wybierz **Eksplorator konfiguracji** >  **+ Utwórz** można dodać następujące pary klucz wartość:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:Message | Dane z usługi Azure App Configuration |

    Pozostaw **etykiety** i **typu zawartości** puste, aby teraz.

## <a name="create-a-net-core-console-app"></a>Tworzenie aplikacji konsolowej platformy .NET Core

Możesz użyć [platformy .NET Core interfejsu wiersza polecenia (CLI)](https://docs.microsoft.com/dotnet/core/tools/) Aby utworzyć nowy projekt aplikacji konsoli .NET Core. Zaletą używania interfejsu wiersza polecenia platformy .NET Core w programie Visual Studio jest, że jest ona dostępna w Windows, macOS i Linux platformy.

1. Utwórz nowy folder dla projektu.

2. W nowym folderze uruchom następujące polecenie, aby utworzyć nowy projekt aplikacji konsoli programu ASP.NET Core:

        dotnet new console

## <a name="connect-to-an-app-configuration-store"></a>Łączenie do sklepu z aplikacjami konfiguracji

1. Dodaj odwołanie do `Microsoft.Azure.AppConfiguration.AspNetCore` pakietu NuGet, uruchamiając następujące polecenie:

        dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-009200001-7

2. Uruchom następujące polecenie, aby przywrócić pakiety dla projektu:

        dotnet restore

3. Otwórz *Program.cs*i Dodaj odwołanie do dostawcy konfiguracji aplikacji programu .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Aktualizacja `Main` metodę, aby można było używać konfiguracji aplikacji, wywołując `builder.AddAzureAppConfiguration()` metody.

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

1. Ustaw zmienną środowiskową o nazwie **ConnectionString**i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Korzystając z wiersza polecenia Windows, uruchom następujące polecenie i ponownie uruchom wiersz polecenia, aby umożliwić zmiana zaczęła obowiązywać:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Jeśli korzystasz z systemem macOS lub Linux, uruchom następujące polecenie:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Uruchom następujące polecenie, aby utworzyć aplikację konsoli:

        dotnet build

3. Po pomyślnym zakończeniu kompilacji, uruchom następujące polecenie, aby uruchomić aplikację lokalnie:

        dotnet run

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki Start został utworzony nowy magazyn konfiguracji aplikacji i jej za pomocą aplikacji konsoli .NET Core za pomocą [dostawcę konfiguracji aplikacji](https://go.microsoft.com/fwlink/?linkid=2074664). Aby dowiedzieć się więcej o tym, jak używać konfiguracji aplikacji, przejdź do następnego samouczka, który demonstruje uwierzytelniania.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanych](./howto-integrate-azure-managed-service-identity.md)
