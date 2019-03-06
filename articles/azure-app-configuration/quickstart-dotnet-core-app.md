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
ms.openlocfilehash: cd4115aaeec15d14d48dcb71cbdc75212c6dc2db
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960681"
---
# <a name="quickstart-create-an-net-core-app-with-app-configuration"></a>Szybki start: Tworzenie aplikacji platformy .NET Core używającej usługi App Configuration

Usługa Azure App Configuration to zarządzana usługa konfiguracji na platformie Azure. Dzięki niej możesz łatwo przechowywać wszystkie ustawienia aplikacji w jednym miejscu oddzielonym od kodu i zarządzać tymi ustawieniami. W tym przewodniku Szybki start pokazano, jak zintegrować usługę z aplikacją konsolową platformy .NET Core.

Do wykonania kroków tego przewodnika Szybki start możesz użyć dowolnego edytora kodu. Doskonałym wyborem jest program [Visual Studio Code](https://code.visualstudio.com/), dostępny na platformach Windows, macOS i Linux.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, zainstaluj zestaw [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-core-console-app"></a>Tworzenie aplikacji konsolowej platformy .NET Core

Do utworzenia nowego projektu aplikacji konsolowej platformy .NET Core użyjesz [interfejsu wiersza polecenia platformy .NET Core](https://docs.microsoft.com/dotnet/core/tools/). Przewagą używania interfejsu wiersza polecenia platformy .NET Core zamiast programu Visual Studio jest to, że jest dostępny na platformach Windows, macOS i Linux.

1. Utwórz nowy folder dla projektu.

2. W nowym folderze wykonaj następujące polecenie, aby utworzyć nowy projekt Aplikacja internetowa platformy ASP.NET Core MVC:

        dotnet new console

## <a name="connect-to-app-configuration-store"></a>Łączenie z magazynem konfiguracji aplikacji

1. Dodaj odwołanie do pakietu NuGet `Microsoft.Extensions.Configuration.AzureAppConfiguration`, wykonując następujące polecenie:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration

2. Uruchom następujące polecenie, aby przywrócić pakiety dla swojego projektu.

        dotnet restore

3. Otwórz plik *Program.cs* i zaktualizuj metodę `Main`, tak aby używała usługi App Configuration przez wywołanie metody `builder.AddAzureAppConfiguration()`.

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

1. Ustaw zmienną środowiskową o nazwie **ConnectionString** i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Jeśli używasz wiersza polecenia systemu Windows, wykonaj następujące polecenie i ponownie uruchom wiersz polecenia, aby umożliwić zastosowanie zmiany:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Jeśli używasz systemu macOS lub Linux, wykonaj następujące polecenie:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Wykonaj następujące polecenie, aby skompilować aplikację konsoli:

        dotnet build

3. Po pomyślnym zakończeniu kompilacji wykonaj następujące polecenie w celu lokalnego uruchomienia aplikacji:

        dotnet run

    ![Uruchomienia aplikacji przewodnika Szybki start](./media/quickstarts/dotnet-core-app-run.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start został utworzony nowy magazyn konfiguracji aplikacji i użyto go z aplikacją konsolową platformy .NET Core. Aby dowiedzieć się więcej o korzystaniu z usługi App Configuration, przejdź do następnego samouczka, w którym zaprezentowano uwierzytelnianie.

> [!div class="nextstepaction"]
> [Tożsamości zarządzane na potrzeby integracji zasobów platformy Azure](./integrate-azure-managed-service-identity.md)
