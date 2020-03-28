---
title: 'Samouczek: Używanie konfiguracji dynamicznej w aplikacji .NET Core'
titleSuffix: Azure App Configuration
description: W tym samouczku dowiesz się, jak dynamicznie aktualizować dane konfiguracyjne dla aplikacji .NET Core
services: azure-app-configuration
documentationcenter: ''
author: abarora
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: abarora
ms.openlocfilehash: afecc84748ae8ce85c07e3b482bd9b596bdca251
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75433674"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Samouczek: Używanie konfiguracji dynamicznej w aplikacji .NET Core

Biblioteka klienta .NET Core konfiguracji aplikacji obsługuje aktualizowanie zestawu ustawień konfiguracji na żądanie bez powodowania ponownego uruchomienia aplikacji. Można to zaimplementować, `IConfigurationRefresher` najpierw uzyskując wystąpienie z `Refresh` opcji dla dostawcy konfiguracji, a następnie wywołując to wystąpienie w dowolnym miejscu w kodzie.

Aby aktualizować ustawienia i uniknąć zbyt wielu wywołań do magazynu konfiguracji, dla każdego ustawienia jest używana pamięć podręczna. Dopóki wartość buforowana ustawienia nie wygaśnie, operacja odświeżania nie aktualizuje wartości, nawet jeśli wartość została zmieniona w magazynie konfiguracji. Domyślny czas wygaśnięcia dla każdego żądania wynosi 30 sekund, ale w razie potrzeby można go zastąpić.

W tym samouczku pokazano, jak zaimplementować dynamiczne aktualizacje konfiguracji w swoim kodzie. Opiera się na aplikacji wprowadzonej w przewodnikach Szybki start. Przed kontynuowaniem należy najpierw zakończyć [tworzenie aplikacji .NET Core przy obliczu konfiguracji aplikacji.](./quickstart-dotnet-core-app.md)

Aby wykonać czynności opisane w tym samouczku, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) to doskonała opcja dostępna na platformach Windows, macOS i Linux.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj aplikację .NET Core, aby zaktualizować jej konfigurację w odpowiedzi na zmiany w sklepie konfiguracji aplikacji.
> * Korzystaj z najnowszej konfiguracji w aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten samouczek, zainstaluj [pakiet .NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Ponowne ładowanie danych z usługi App Configuration

Otwórz *Program.cs* i zaktualizuj plik, `System.Threading.Tasks` aby dodać odwołanie do `AddAzureAppConfiguration` obszaru nazw, określić konfigurację odświeżania w metodzie i wyzwolić ręczne odświeżanie przy użyciu `Refresh` tej metody.

```csharp
using System;
using System.Threading.Tasks;

namespace TestConsole
{
class Program
{
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;

    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                               .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });
                    
                    _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }

    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.Refresh();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
}
}
```

Metoda `ConfigureRefresh` służy do określania ustawień używanych do aktualizowania danych konfiguracyjnych w magazynie konfiguracji aplikacji po wyzwoleniu operacji odświeżania. Wystąpienie `IConfigurationRefresher` można pobrać przez `GetRefresher` wywołanie metody na `AddAzureAppConfiguration` opcje dostarczone `Refresh` do metody i metoda w tym wystąpieniu może służyć do wyzwalania operacji odświeżania w dowolnym miejscu w kodzie.
    
> [!NOTE]
> Domyślny czas wygaśnięcia pamięci podręcznej dla ustawienia konfiguracji wynosi 30 `SetCacheExpiration` sekund, ale można go zastąpić, `ConfigureRefresh` wywołując metodę na inicjatora opcji przekazany jako argument do metody.

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString**i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie i uruchom ponownie wiersz polecenia, aby umożliwić zastosowanie zmiany:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Jeśli używasz systemu macOS lub Linux, uruchom następujące polecenie:

        export ConnectionString='connection-string-of-your-app-configuration-store'

1. Uruchom następujące polecenie, aby utworzyć aplikację konsoli:

        dotnet build

1. Po pomyślnym zakończeniu kompilacji uruchom następujące polecenie, aby uruchomić aplikację lokalnie:

        dotnet run

    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/dotnet-core-app-run.png)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz **pozycję Wszystkie zasoby**i wybierz wystąpienie sklepu konfiguracja aplikacji utworzone w przewodniku Szybki start.

1. Wybierz **pozycję Eksplorator konfiguracji**i zaktualizuj wartości następujących klawiszy:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:Message | Dane z konfiguracji aplikacji platformy Azure — zaktualizowane |

1. Naciśnij klawisz Enter, aby wyzwolić odświeżenie i wydrukować zaktualizowaną wartość w wierszu polecenia lub oknie programu PowerShell.

    ![Lokalne odświeżanie aplikacji z przewodnika Szybki start](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Ponieważ czas wygaśnięcia pamięci podręcznej został ustawiony `SetCacheExpiration` na 10 sekund przy użyciu metody podczas określania konfiguracji dla operacji odświeżania, wartość ustawienia konfiguracji zostanie zaktualizowana tylko wtedy, gdy upłynęło co najmniej 10 sekund od ostatniego odświeżenia dla tego ustawienia.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono aplikację .NET Core, aby dynamicznie odświeżać ustawienia konfiguracji z konfiguracji aplikacji. Aby dowiedzieć się, jak użyć tożsamości zarządzanej platformy Azure, aby usprawnić dostęp do konfiguracji aplikacji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./howto-integrate-azure-managed-service-identity.md)
