---
title: 'Samouczek .NET Framework: konfiguracja dynamiczna w konfiguracji aplikacji platformy Azure'
description: W tym samouczku dowiesz się, jak dynamicznie aktualizować dane konfiguracyjne dla aplikacji .NET Framework przy użyciu konfiguracji aplikacji platformy Azure.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 7780bdbc92868f62e8d066d171b2a04fe06a981d
ms.sourcegitcommit: 940e16ff194d5163f277f98d038833b1055a1a3e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80245807"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Samouczek: Używanie konfiguracji dynamicznej w aplikacji .NET Framework

Biblioteka klienta Konfiguracja aplikacji .NET obsługuje aktualizowanie zestawu ustawień konfiguracji na żądanie bez powodowania ponownego uruchomienia aplikacji. Można to zaimplementować, `IConfigurationRefresher` najpierw uzyskując wystąpienie z `Refresh` opcji dla dostawcy konfiguracji, a następnie wywołując to wystąpienie w dowolnym miejscu w kodzie.

Aby aktualizować ustawienia i uniknąć zbyt wielu wywołań do magazynu konfiguracji, dla każdego ustawienia jest używana pamięć podręczna. Dopóki wartość buforowana ustawienia nie wygaśnie, operacja odświeżania nie aktualizuje wartości, nawet jeśli wartość została zmieniona w magazynie konfiguracji. Domyślny czas wygaśnięcia dla każdego żądania wynosi 30 sekund, ale w razie potrzeby można go zastąpić.

W tym samouczku pokazano, jak zaimplementować dynamiczne aktualizacje konfiguracji w swoim kodzie. Opiera się na aplikacji wprowadzonej w przewodnikach Szybki start. Przed kontynuowaniem należy najpierw zakończyć [tworzenie aplikacji .NET Framework przy obliczu konfiguracji aplikacji.](./quickstart-dotnet-app.md)

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj aplikację .NET Framework, aby zaktualizować jej konfigurację w odpowiedzi na zmiany w sklepie konfiguracji aplikacji.
> * Wstrzyknąć najnowszą konfigurację w aplikacji.
## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 lub nowsze](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Tworzenie sklepu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wybierz **Eksplorator** > konfiguracji **+ Utwórz** > **wartość klucza,** aby dodać następujące pary klucz-wartość:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:Message | Dane z usługi Azure App Configuration |

    Pozostaw **etykietę** i **typ zawartości** puste na razie.

7. Wybierz przycisk **Zastosuj**.

## <a name="create-a-net-framework-console-app"></a>Tworzenie aplikacji konsoli platformy .NET Framework

1. Uruchom program Visual Studio i wybierz pozycję **Plik** > **nowego** > **projektu**.

1. W **programie Tworzenie nowego projektu**filtruj typ projektu **konsoli** i kliknij aplikację Console **App (.NET Framework).** Kliknij przycisk **alej**.

1. W **obszarze Konfiguruj nowy projekt**wprowadź nazwę projektu. W obszarze **Framework**wybierz opcję **.NET Framework 4.7.1** lub nowszą. Kliknij przycisk **Utwórz**.

## <a name="reload-data-from-app-configuration"></a>Ponowne ładowanie danych z usługi App Configuration
1. Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami NuGet**. Na karcie **Przeglądaj** wyszukaj i dodaj pakiet *Microsoft.Extensions.Configuration.AzureAppConfiguration* NuGet do projektu. Jeśli nie możesz go znaleźć, zaznacz pole wyboru **Dołącz wydanie wstępne.**

1. Otwórz *Program.cs*i dodaj odwołanie do dostawcy konfiguracji aplikacji .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Dodaj dwie zmienne do przechowywania obiektów związanych z konfiguracją.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Zaktualizuj metodę, `Main` aby połączyć się z konfiguracją aplikacji za pomocą określonych opcji odświeżania.

    ```csharp
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
    ```
    Metoda `ConfigureRefresh` służy do określania ustawień używanych do aktualizowania danych konfiguracyjnych w magazynie konfiguracji aplikacji po wyzwoleniu operacji odświeżania. Wystąpienie `IConfigurationRefresher` można pobrać przez `GetRefresher` wywołanie metody na `AddAzureAppConfiguration` opcje dostarczone `Refresh` do metody i metoda w tym wystąpieniu może służyć do wyzwalania operacji odświeżania w dowolnym miejscu w kodzie.

    > [!NOTE]
    > Domyślny czas wygaśnięcia pamięci podręcznej dla ustawienia konfiguracji wynosi 30 `SetCacheExpiration` sekund, ale można go zastąpić, `ConfigureRefresh` wywołując metodę na inicjatora opcji przekazany jako argument do metody.

1. Dodaj metodę `PrintMessage()` o nazwie, która wyzwala ręczne odświeżanie danych konfiguracyjnych z konfiguracji aplikacji.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.Refresh();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString**i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie i uruchom ponownie wiersz polecenia, aby umożliwić zastosowanie zmiany:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Uruchom ponownie program Visual Studio, aby umożliwić zmianę, aby wejść w życie. 

1. Naciśnij klawisze Ctrl + F5, aby utworzyć i uruchomić aplikację konsoli.

    ![Uruchamianie aplikacji lokalne](./media/dotnet-app-run.png)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz **pozycję Wszystkie zasoby**i wybierz wystąpienie sklepu konfiguracja aplikacji utworzone w przewodniku Szybki start.

1. Wybierz **pozycję Eksplorator konfiguracji**i zaktualizuj wartości następujących klawiszy:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:Message | Dane z konfiguracji aplikacji platformy Azure — zaktualizowane |

1. W uruchomionej aplikacji naciśnij klawisz Enter, aby wyzwolić odświeżenie i wydrukować zaktualizowaną wartość w wierszu polecenia lub oknie programu PowerShell.

    ![Odświeżanie aplikacji lokalne](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Ponieważ czas wygaśnięcia pamięci podręcznej został ustawiony `SetCacheExpiration` na 10 sekund przy użyciu metody podczas określania konfiguracji dla operacji odświeżania, wartość ustawienia konfiguracji zostanie zaktualizowana tylko wtedy, gdy upłynęło co najmniej 10 sekund od ostatniego odświeżenia dla tego ustawienia.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono aplikację .NET Framework, aby dynamicznie odświeżać ustawienia konfiguracji z konfiguracji aplikacji. Aby dowiedzieć się, jak użyć tożsamości zarządzanej platformy Azure, aby usprawnić dostęp do konfiguracji aplikacji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./howto-integrate-azure-managed-service-identity.md)
