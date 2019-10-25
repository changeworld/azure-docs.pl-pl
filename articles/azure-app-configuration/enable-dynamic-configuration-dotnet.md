---
title: Samouczek dotyczący używania dynamicznej konfiguracji usługi Azure App Configuration w aplikacji .NET Framework | Microsoft Docs
description: W tym samouczku dowiesz się, jak dynamicznie aktualizować dane konfiguracji dla aplikacji .NET Framework
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 7e28cdacce8eac4774683013ae1c30ca34ebfaad
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821711"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Samouczek: używanie konfiguracji dynamicznej w aplikacji .NET Framework

Biblioteka klienta platformy .NET konfiguracji aplikacji obsługuje aktualizowanie zestawu ustawień konfiguracji na żądanie bez powodowania ponownego uruchomienia aplikacji. Można to wdrożyć, najpierw pobierając wystąpienie `IConfigurationRefresher` z opcji dla dostawcy konfiguracji, a następnie wywołując `Refresh` na tym wystąpieniu w dowolnym miejscu w kodzie.

Aby zachować zaktualizowane ustawienia i uniknąć zbyt wielu wywołań w magazynie konfiguracji, dla każdego ustawienia zostanie użyta pamięć podręczna. Do momentu wygaśnięcia zbuforowanej wartości ustawienia operacja odświeżania nie aktualizuje wartości, nawet jeśli wartość została zmieniona w magazynie konfiguracji. Domyślny czas wygaśnięcia dla każdego żądania wynosi 30 sekund, ale w razie potrzeby można go zastąpić.

W tym samouczku pokazano, jak zaimplementować dynamiczne aktualizacje konfiguracji w swoim kodzie. Jest ona oparta na aplikacji wprowadzonej w przewodnikach Szybki Start. Przed kontynuowaniem najpierw Zakończ [Tworzenie aplikacji .NET Frameworkej z konfiguracją aplikacji](./quickstart-dotnet-app.md) .

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj swoją aplikację, aby zaktualizować jej konfigurację z magazynem konfiguracji aplikacji na żądanie.
> * Wstaw najnowszą konfigurację z kontrolerów aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- [Program Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 lub nowszy](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wybierz pozycję **Eksplorator konfiguracji** >  **+ Utwórz** , aby dodać następujące pary klucz-wartość:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:Message | Dane z usługi Azure App Configuration |

    Dla tej pory pozostaw pustą **etykietę** i **Typ zawartości** .

## <a name="create-a-net-console-app"></a>Tworzenie aplikacji konsolowej platformy .NET

1. Uruchom program Visual Studio i wybierz pozycję **plik** > **Nowy** > **projektu**.

1. W obszarze **Utwórz nowy projekt**odfiltruj typ projektu **konsoli** i kliknij pozycję **Aplikacja konsolowa (.NET Framework)** . Kliknij przycisk **Dalej**.

1. W obszarze **Konfigurowanie nowego projektu**wprowadź nazwę projektu. W obszarze **Struktura**wybierz pozycję **.NET Framework 4.7.1** lub wyższy. Kliknij przycisk **Utwórz**.

## <a name="reload-data-from-app-configuration"></a>Ponowne ładowanie danych z usługi App Configuration
1. Kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**. Na karcie **Przeglądaj** Wyszukaj i Dodaj pakiet NuGet *Microsoft. Extensions. Configuration. AzureAppConfiguration* do projektu. Jeśli nie możesz go znaleźć, zaznacz pole wyboru **Uwzględnij wersję wstępną** .

1. Otwórz *program.cs*i Dodaj odwołanie do dostawcy konfiguracji aplikacji .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Dodaj dwie zmienne do przechowywania obiektów związanych z konfiguracją.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Zaktualizuj metodę `Main`, aby połączyć się z konfiguracją aplikacji przy użyciu określonych opcji odświeżania.

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
    Metoda `ConfigureRefresh` służy do określania ustawień używanych do aktualizowania danych konfiguracji z magazynem konfiguracji aplikacji w przypadku wyzwolenia operacji odświeżania. Wystąpienie `IConfigurationRefresher` można pobrać przez wywołanie metody `GetRefresher` w opcjach dostarczonych do metody `AddAzureAppConfiguration`, a metoda `Refresh` w tym wystąpieniu może służyć do wyzwalania operacji odświeżania wszędzie w kodzie.

    > [!NOTE]
    > Domyślny czas wygaśnięcia pamięci podręcznej dla ustawienia konfiguracji wynosi 30 sekund, ale można go zastąpić, wywołując metodę `SetCacheExpiration` w inicjatorze opcji przekazaną jako argument do metody `ConfigureRefresh`.

1. Dodaj metodę o nazwie `PrintMessage()`, która wyzwala ręczne odświeżanie danych konfiguracyjnych z konfiguracji aplikacji.

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

1. Ustaw zmienną środowiskową o nazwie **ConnectionString**i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie i ponownie uruchom wiersz polecenia, aby zezwolić na wprowadzenie zmiany:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Uruchom ponownie program Visual Studio, aby zmiany zaczęły obowiązywać. 

1. Naciśnij kombinację klawiszy CTRL + F5, aby skompilować i uruchomić aplikację konsolową.

    ![Lokalne uruchamianie aplikacji](./media/dotnet-app-run.png)

1. Zaloguj się do [portalu Azure](https://portal.azure.com). Wybierz pozycję **wszystkie zasoby**, a następnie wybierz wystąpienie magazynu konfiguracji aplikacji utworzone w ramach przewodnika Szybki Start.

1. Wybierz pozycję **Eksplorator konfiguracji**i zaktualizuj wartości następujących kluczy:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:Message | Dane z konfiguracji aplikacji platformy Azure — zaktualizowane |

1. Wróć do uruchomionej aplikacji, naciśnij klawisz ENTER, aby wyzwolić odświeżanie i wydrukować zaktualizowaną wartość w wierszu polecenia lub w oknie programu PowerShell.

    ![Lokalne odświeżanie aplikacji](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Ponieważ czas wygaśnięcia pamięci podręcznej został ustawiony na 10 sekund przy użyciu metody `SetCacheExpiration` podczas określania konfiguracji dla operacji odświeżania, wartość ustawienia konfiguracji będzie aktualizowana tylko wtedy, gdy od momentu ostatniego odświeżenia dla to ustawienie.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku dodano tożsamość usługi zarządzanej przez platformę Azure, aby usprawnić dostęp do usługi App Configuration i poprawić zarządzanie poświadczeniami w Twojej aplikacji. Aby dowiedzieć się, jak dodać tożsamość usługi zarządzanej przez platformę Azure, która usprawnia dostęp do konfiguracji aplikacji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./howto-integrate-azure-managed-service-identity.md)
