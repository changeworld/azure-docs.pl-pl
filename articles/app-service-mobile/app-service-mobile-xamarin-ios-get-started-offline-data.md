---
title: Włączanie synchronizacji w trybie offline dla aplikacji mobilnej Azure (Xamarin iOS)
description: Dowiedz się, jak używać aplikacji usługi Mobile App do pamięci podręcznej i synchronizacji danych w trybie offline w aplikacji platformy Xamarin iOS
documentationcenter: xamarin
author: conceptdev
manager: cfowler
editor: ''
services: app-service\mobile
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 287977d55656a4b2bc42d90730d16f522fae9b9e
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2018
ms.locfileid: "27594670"
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Włączanie synchronizacji w trybie offline dla aplikacji mobilnej platformy Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Przegląd
Ten samouczek przedstawia funkcję synchronizacji w trybie offline w usłudze Azure Mobile Apps dla platformy Xamarin.iOS. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacji mobilnej — przeglądanie, dodawanie lub modyfikowanie danych — nawet wtedy, gdy istnieje połączenie sieciowe. Zmiany są przechowywane w lokalnej bazie danych. Gdy urządzenie jest w trybie online, te zmiany są synchronizowane z usługi zdalnej.

W tym samouczku, zaktualizuj projekt aplikacji platformy Xamarin.iOS z [tworzenie aplikacji platformy Xamarin.IOS] do obsługi funkcji w trybie offline z usługą Azure Mobile Apps. Jeśli nie używasz szybki start pobrany Projekt serwera, należy dodać pakietów rozszerzenia dostępu do danych do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzenia serwera, zobacz [pracować z serwera wewnętrznej bazy danych .NET SDK usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Aby dowiedzieć się więcej na temat funkcji synchronizacji w trybie offline, zobacz temat [synchronizacji danych w trybie Offline w usłudze Azure Mobile Apps].

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizowanie aplikacji klienta do obsługi funkcji w trybie offline
Funkcje platformy Azure w trybie offline aplikacji mobilnej pozwala na interakcję z lokalnej bazy danych podczas pracy w trybie offline scenariuszu. Aby korzystać z tych funkcji w aplikacji, należy zainicjować [SyncContext] do lokalnego magazynu. Odwoływać się do tabeli za pomocą interfejsu [IMobileServiceSyncTable]. SQLite jest używany w magazynie lokalnym urządzenia.

1. Otwórz Menedżera pakietów NuGet w projekcie, który zakończył w [tworzenie aplikacji platformy Xamarin.IOS] samouczek, a następnie wyszukaj i zainstaluj **Microsoft.Azure.Mobile.Client.SQLiteStore** pakietu NuGet.
2. Otwórz plik QSTodoService.cs i Usuń komentarz `#define OFFLINE_SYNC_ENABLED` definicji.
3. Ponownie skompilować i uruchomić aplikację klienta. Aplikacja działa tak samo jak przed włączeniem synchronizacji w trybie offline. Jednak lokalnej bazy danych jest teraz wypełniona dane, które mogą być używane w scenariuszu w trybie offline.

## <a name="update-sync"></a>Aktualizowanie aplikacji, aby zakończyć połączenie z wewnętrznej bazy danych
W tej sekcji możesz przerwać połączenie do zaplecza aplikacji mobilnej do symulowania sytuacji w trybie offline. Po dodaniu elementów danych z programu obsługi wyjątków informuje, że dana aplikacja jest w trybie offline. W tym stanie nowe elementy dodane w lokalnym magazynie i będą synchronizowane z zaplecza aplikacji mobilnej przy następnym uruchomieniu wypychania jest niepodłączony.

1. Edytuj QSToDoService.cs w projekcie udostępnionym. Zmień **applicationURL** wskaż nieprawidłowy adres URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Zachowanie w trybie offline można także pokazują, wyłączając sieci Wi-Fi i sieci komórkowych na urządzeniu lub w trybie samolotowy.
2. Skompiluj i uruchom aplikację. Zwróć uwagę, synchronizacji nie powiodło się podczas odświeżania, gdy aplikacja jest uruchamiana.
3. Wprowadź nowe elementy i zwróć uwagę, że wypychania nie powiedzie się ze stanem [CancelledByNetworkError] każdym kliknięciu **zapisać**. Jednak nowych elementów todo istnieje w lokalnym magazynie dopóki nie może zostać przeniesiony do zaplecza aplikacji mobilnej.  W aplikacji produkcyjnej Jeśli pominąć te wyjątki aplikacji klienckiej zachowuje się tak, jakby nadal jest podłączony do zaplecza aplikacji mobilnej.
4. Zamknij aplikację i uruchom ponownie, aby sprawdzić, czy nowe elementy utworzone zostały utrwalone w magazynie lokalnym.
5. (Opcjonalnie) Jeśli masz zainstalowanego na komputerze programu Visual Studio Otwórz **Eksploratora serwera**. Przejdź do bazy danych w **Azure**-> **baz danych SQL**. Kliknij prawym przyciskiem myszy bazę danych i wybierz **Otwórz w Eksploratorze obiektów SQL Server**. Możesz teraz przejść do tabeli bazy danych SQL i jego zawartość. Sprawdź, czy dane w bazie danych zaplecza nie uległy zmianie.
6. (Opcjonalnie) Zapytania z zaplecze aplikacji mobilnej, za pomocą zapytania GET w formularzu za pomocą narzędzia REST, takie jak Fiddler lub Postman `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Aktualizowanie aplikacji ponownie połączyć zaplecza aplikacji mobilnej
W tej sekcji ponowne łączenie aplikacji z zapleczem aplikacji mobilnej. Symuluje to aplikacji przejście z trybu offline w tryb online z zaplecza aplikacji mobilnej.   Jeśli uszkodzenie sieci jest symulowane przez wyłączenie opcji łączności sieciowej, nie są konieczne żadne zmiany kodu.
Ponownie włącz sieci.  Przy pierwszym uruchomieniu aplikacji, `RefreshDataAsync` metoda jest wywoływana. To z kolei wywołuje `SyncAsync` do synchronizacji z bazy danych zaplecza magazynu lokalnego.

1. Otwórz QSToDoService.cs w projekcie udostępnionym, a następnie przywrócić zmiana **applicationURL** właściwości.
2. Ponownie skompilować i uruchomić aplikację. Aplikacja przeprowadza synchronizację zmian z zaplecza aplikacji mobilnej Azure przy użyciu operacji wypychania i ściągania podczas `OnRefreshItemsSelected` metoda jest wykonywana.
3. (Opcjonalnie) Wyświetlanie zaktualizowanych danych przy użyciu Eksplorator obiektów SQL Server lub narzędzia REST, takiego jak Fiddler. Powiadomienie danych został zsynchronizowany między bazą danych zaplecza aplikacji mobilnej Azure i lokalnego magazynu.
4. W aplikacji kliknij pole wyboru obok kilka elementów, aby zakończyć je w lokalnym magazynie.

   `CompleteItemAsync`wywołania `SyncAsync` do elementu synchronizacja każdego z zaplecza aplikacji mobilnej. `SyncAsync`wywołuje wypychania i ściągania.
   **Przy każdym wykonaniu ściągania przed tabelę, w której klient wprowadził zmiany do wypychania klienta kontekstu synchronizacji jest zawsze najpierw automatycznie wykonywać**. Niejawne wypychania gwarantuje, że wszystkie tabele w lokalnym magazynie wraz z relacji zachować spójność. Aby uzyskać więcej informacji dotyczących tego zachowania, zobacz [synchronizacji danych w trybie Offline w usłudze Azure Mobile Apps].

## <a name="review-the-client-sync-code"></a>Przegląd kodu klienta synchronizacji
Pobrany po ukończeniu samouczka projekt klienta Xamarin [tworzenie aplikacji platformy Xamarin.IOS] już zawiera kod obsługujący synchronizacji w trybie offline przy użyciu lokalnej bazy danych SQLite. Oto krótkie omówienie co to jest już uwzględniony w kodzie samouczka. Omówienie koncepcji funkcji, zobacz [synchronizacji danych w trybie Offline w usłudze Azure Mobile Apps].

* Aby można było wykonać wszystkie operacje tabeli, muszą zostać zainicjowane magazynu lokalnego. Magazyn lokalny jest inicjowana po `QSTodoListViewController.ViewDidLoad()` wykonuje `QSTodoService.InitializeStoreAsync()`. Ta metoda tworzy nowego lokalnego SQLite bazy danych przy użyciu `MobileServiceSQLiteStore` klasy dostarczonych przez klienta aplikacji mobilnej Azure SDK.

    `DefineTable` Metoda tworzy tabelę w lokalnym magazynie odpowiadającego pola udostępnionego typu `ToDoItem` w takim przypadku. Typ nie ma zawierać wszystkie kolumny, które znajdują się w zdalnej bazy danych. Istnieje możliwość przechowywania tylko ich podzbiór kolumn.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* `todoTable` Członkiem `QSTodoService` jest `IMobileServiceSyncTable` wpisz zamiast `IMobileServiceTable`. IMobileServiceSyncTable kieruje wszystkie tworzenia, odczytu, aktualizacji i usuwania (CRUD) operacje tabeli w bazie danych magazynu lokalnego.

    Zdecyduj, kiedy te zmiany są przenoszone do zaplecza aplikacji mobilnej Azure przez wywołanie metody `IMobileServiceSyncContext.PushAsync()`. Kontekst synchronizacji pomaga zachować relacje między tabelami, śledzenia i wypychanie zmiany we wszystkich tabelach aplikacji klienckiej został zmodyfikowany podczas obliczania `PushAsync` jest wywoływana.

    Podany kod wywołuje `QSTodoService.SyncAsync()` do synchronizacji przy każdym odświeżeniu listy todoitem lub dodano lub wykonać zadania do wykonania. Synchronizacje aplikacji, po każdej zmianie lokalnego. Ściąganie jest wykonywane względem tabeli, która ma oczekujące aktualizacje lokalnego śledzone przez kontekst, tej operacji replikacji ściąganej automatycznie wyzwoli wypychania kontekstu najpierw.

    Podany kod wszystkie rekordy w obiekcie zdalnym `TodoItem` badane są tabele tabeli, ale jest również możliwe filtrowanie rekordów przez przekazanie identyfikator zapytania i kwerendę `PushAsync`. Aby uzyskać więcej informacji, zobacz sekcję *synchronizacja przyrostowa* w [synchronizacji danych w trybie Offline w usłudze Azure Mobile Apps].

        // QSTodoService.cs
        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

## <a name="additional-resources"></a>Dodatkowe zasoby
* [synchronizacji danych w trybie Offline w usłudze Azure Mobile Apps]
* [Porada zestawu SDK .NET usługi Azure Mobile Apps][8]

<!-- Images -->

<!-- URLs. -->
[tworzenie aplikacji platformy Xamarin.IOS]: app-service-mobile-xamarin-ios-get-started.md
[synchronizacji danych w trybie Offline w usłudze Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
