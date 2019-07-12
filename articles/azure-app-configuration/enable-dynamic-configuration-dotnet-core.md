---
title: Samouczek dotyczący przy użyciu konfiguracji dynamicznej konfiguracji aplikacji platformy Azure w aplikacji platformy .NET Core | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak dynamicznie aktualizować dane konfiguracji dla aplikacji platformy .NET Core
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
ms.openlocfilehash: 1649fefda5073761d616fc48c602cab84d293ed0
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799973"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Samouczek: Używanie dynamicznej konfiguracji w aplikacji platformy .NET Core

Biblioteka klienta aplikacji konfiguracji platformy .NET Core obsługuje aktualizowanie zestaw ustawień konfiguracji na żądanie bez powodowania ponowne uruchomienie aplikacji. Można to zaimplementować uzyskując pierwsze wystąpienie `IConfigurationRefresher` opcje dla dostawcy konfiguracji i następnie wywoływania `Refresh` w tym wystąpieniu w dowolnym miejscu w kodzie.

Aby zachować ustawienia zostały zaktualizowane i unikaj zbyt wielu wywołań do magazynu konfiguracji, pamięć podręczna jest używana dla każdego ustawienia. Dopóki wartość ustawienia w pamięci podręcznej wygasł, operacja odświeżania nie aktualizuje wartości, nawet wtedy, gdy wartość została zmieniona w magazynie konfiguracji. Domyślny czas wygaśnięcia dla każdego żądania jest 30 sekund, ale może zostać zastąpiona, jeśli jest to wymagane.

W tym samouczku pokazano, jak zaimplementować dynamiczne aktualizacje konfiguracji w swoim kodzie. Opiera się on na aplikację, wprowadzona w Szybki Start. Przed kontynuowaniem należy zakończyć [tworzenie aplikacji .NET Core z konfiguracji aplikacji](./quickstart-dotnet-core-app.md) pierwszy.

Wykonaj kroki w tym samouczku, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) doskonałym rozwiązaniem, która jest dostępna w Windows, macOS i platformy Linux.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie aplikacji w taki sposób, aby zaktualizować konfigurację przy użyciu magazynu konfiguracji aplikacji na żądanie.
> * Wstrzyknąć najnowszą konfigurację w kontrolerów danej aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skorzystać z tego samouczka, należy zainstalować [zestawu .NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Ponowne ładowanie danych z usługi App Configuration

Otwórz *Program.cs* i zaktualizuj plik, aby określić konfigurację odświeżania w `AddAzureAppConfiguration` metody i przy użyciu ręczne odświeżanie wyzwalacz `Refresh` metody.

```csharp
class Program
{
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;

    static void Main(string[] args)
    {
        IConfigurationRefresher refresher = null;

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
```

`ConfigureRefresh` Metoda jest używana do określenia ustawień, używane do aktualizowania danych konfiguracji z magazynu konfiguracji aplikacji, po wyzwoleniu operacji odświeżania. Wystąpienie `IConfigurationRefresher` może być pobierany przez wywołanie `GetRefresher` metody na opcje przekazane `AddAzureAppConfiguration` metody i `Refresh` metody dla tego wystąpienia może służyć do wyzwolenia operacji odświeżania w dowolnym miejscu w kodzie.
    
> [!NOTE]
> Domyślny czas wygaśnięcia pamięci podręcznej ustawienie konfiguracji to 30 sekund, ale może być zastąpiona przez wywołanie metody `SetCacheExpiration` metody w inicjatorze opcje przekazywany jako argument do `ConfigureRefresh` metody.

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString**i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Korzystając z wiersza polecenia Windows, uruchom następujące polecenie i ponownie uruchom wiersz polecenia, aby umożliwić zmiana zaczęła obowiązywać:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Jeśli korzystasz z systemem macOS lub Linux, uruchom następujące polecenie:

        export ConnectionString='connection-string-of-your-app-configuration-store'

1. Uruchom następujące polecenie, aby utworzyć aplikację konsoli:

        dotnet build

1. Po pomyślnym zakończeniu kompilacji, uruchom następujące polecenie, aby uruchomić aplikację lokalnie:

        dotnet run

    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/dotnet-core-app-run.png)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **wszystkie zasoby**i wybierz wystąpienie magazynu konfiguracji aplikacji, które utworzono w przewodniku Szybki Start.

1. Wybierz **Eksplorator konfiguracji**i zaktualizuj wartości następujących kluczy:

    | Klucz | Value |
    |---|---|
    | TestApp:Settings:Message | Dane z konfiguracji aplikacji platformy Azure — zaktualizowane |

1. Naciśnij klawisz Enter, aby wyzwalać odświeżanie i drukowanie zaktualizowane wartości w oknie wiersza polecenia lub programu PowerShell.

    ![Lokalne odświeżanie aplikacji z przewodnika Szybki start](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Ponieważ czas wygaśnięcia pamięci podręcznej został ustawiony na 10 sekund przy użyciu `SetCacheExpiration` metody podczas określania konfiguracji dla operacji odświeżania, wartość ustawienia konfiguracji będą aktualizowane po upływie co najmniej 10 sekund od czasu ostatniego odświeżenia tego ustawienia.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku dodano tożsamość usługi zarządzanej przez platformę Azure, aby usprawnić dostęp do usługi App Configuration i poprawić zarządzanie poświadczeniami w Twojej aplikacji. Aby dowiedzieć się więcej o tym, jak używać konfiguracji aplikacji, przejdź do przykładów interfejsu wiersza polecenia platformy Azure.

> [!div class="nextstepaction"]
> [Przykłady interfejsu wiersza polecenia](./cli-samples.md)
