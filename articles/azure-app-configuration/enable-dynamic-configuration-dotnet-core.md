---
title: Samouczek dotyczący używania dynamicznej konfiguracji usługi Azure App Configuration w aplikacji .NET Core | Microsoft Docs
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
ms.openlocfilehash: e56aba81b2e6b8e66aeb2c3e5284843055713826
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316089"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Samouczek: Korzystanie z konfiguracji dynamicznej w aplikacji .NET Core

Konfiguracja aplikacji .NET Core Client Library obsługuje aktualizowanie zestawu ustawień konfiguracji na żądanie bez powodowania ponownego uruchomienia aplikacji. Można to wdrożyć, najpierw pobierając wystąpienie `IConfigurationRefresher` z opcji dla dostawcy konfiguracji, a następnie wywołując `Refresh` je w dowolnym miejscu w kodzie.

Aby zachować zaktualizowane ustawienia i uniknąć zbyt wielu wywołań w magazynie konfiguracji, dla każdego ustawienia zostanie użyta pamięć podręczna. Do momentu wygaśnięcia zbuforowanej wartości ustawienia operacja odświeżania nie aktualizuje wartości, nawet jeśli wartość została zmieniona w magazynie konfiguracji. Domyślny czas wygaśnięcia dla każdego żądania wynosi 30 sekund, ale w razie potrzeby można go zastąpić.

W tym samouczku pokazano, jak zaimplementować dynamiczne aktualizacje konfiguracji w swoim kodzie. Jest ona oparta na aplikacji wprowadzonej w przewodnikach Szybki Start. Przed kontynuowaniem należy najpierw [utworzyć aplikację platformy .NET Core z konfiguracją aplikacji](./quickstart-dotnet-core-app.md) .

Aby wykonać kroki opisane w tym samouczku, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) jest doskonałym rozwiązaniem dostępnym na platformach Windows, MacOS i Linux.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj swoją aplikację, aby zaktualizować jej konfigurację z magazynem konfiguracji aplikacji na żądanie.
> * Wstaw najnowszą konfigurację z kontrolerów aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten samouczek, zainstaluj [zestaw .NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Ponowne ładowanie danych z usługi App Configuration

Otwórz *program.cs* i zaktualizuj plik, aby dodać odwołanie do `System.Threading.Tasks` przestrzeni nazw, aby określić konfigurację `AddAzureAppConfiguration` odświeżania w metodzie `Refresh` i wyzwalać ręczne odświeżanie przy użyciu metody.

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

Ta `ConfigureRefresh` Metoda służy do określania ustawień służących do aktualizowania danych konfiguracji z magazynem konfiguracji aplikacji w przypadku wyzwolenia operacji odświeżania. Wystąpienie `IConfigurationRefresher` można pobrać przez wywołanie `GetRefresher` metody w opcjach dostarczonych `Refresh` do `AddAzureAppConfiguration` metody, a metoda w tym wystąpieniu może służyć do wyzwalania operacji odświeżania wszędzie w kodzie.
    
> [!NOTE]
> Domyślny czas wygaśnięcia pamięci podręcznej dla ustawienia konfiguracji wynosi 30 sekund, ale można go zastąpić, wywołując `SetCacheExpiration` metodę z inicjatora opcji przekazaną jako argument `ConfigureRefresh` do metody.

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString**i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie i ponownie uruchom wiersz polecenia, aby zezwolić na wprowadzenie zmiany:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Jeśli używasz macOS lub Linux, uruchom następujące polecenie:

        export ConnectionString='connection-string-of-your-app-configuration-store'

1. Uruchom następujące polecenie, aby skompilować aplikację konsolową:

        dotnet build

1. Po pomyślnym zakończeniu kompilacji Uruchom następujące polecenie, aby uruchomić aplikację lokalnie:

        dotnet run

    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/dotnet-core-app-run.png)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **wszystkie zasoby**, a następnie wybierz wystąpienie magazynu konfiguracji aplikacji utworzone w ramach przewodnika Szybki Start.

1. Wybierz pozycję **Eksplorator konfiguracji**i zaktualizuj wartości następujących kluczy:

    | Klucz | Value |
    |---|---|
    | TestApp:Settings:Message | Dane z konfiguracji aplikacji platformy Azure — zaktualizowane |

1. Naciśnij klawisz ENTER, aby wyzwolić odświeżanie i wydrukować zaktualizowaną wartość w wierszu polecenia lub w oknie programu PowerShell.

    ![Lokalne odświeżanie aplikacji z przewodnika Szybki start](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Ponieważ czas wygaśnięcia pamięci podręcznej został ustawiony na 10 sekund `SetCacheExpiration` przy użyciu metody podczas określania konfiguracji dla operacji odświeżania, wartość ustawienia konfiguracji będzie aktualizowana tylko wtedy, gdy od momentu ostatniego odświeżenia upłynie co najmniej 10 sekund. dla tego ustawienia.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku dodano tożsamość usługi zarządzanej przez platformę Azure, aby usprawnić dostęp do usługi App Configuration i poprawić zarządzanie poświadczeniami w Twojej aplikacji. Aby dowiedzieć się więcej na temat korzystania z konfiguracji aplikacji, przejdź do przykładów interfejsu wiersza polecenia platformy Azure.

> [!div class="nextstepaction"]
> [Przykłady interfejsu wiersza polecenia](./cli-samples.md)
