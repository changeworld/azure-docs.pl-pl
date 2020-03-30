---
title: Włącz synchronizację w trybie offline (Xamarin iOS)
description: Dowiedz się, jak używać aplikacji mobilnej usługi App Service do buforowania i synchronizowania danych w trybie offline w aplikacji xamarin dla systemu iOS.
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 3a5128f6918b22be2ff1ef6adf3e453b1f373ea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461304"
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Włączanie synchronizacji w trybie offline dla aplikacji mobilnej Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Omówienie
W tym samouczku przedstawiono funkcję synchronizacji w trybie offline usługi Azure Mobile Apps for Xamarin.iOS. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną — wyświetlanie, dodawanie lub modyfikowanie danych — nawet wtedy, gdy nie ma połączenia sieciowego. Zmiany są przechowywane w lokalnej bazie danych. Po powrocie urządzenia do trybu online zmiany te są synchronizowane z usługą zdalną.

W tym samouczku zaktualizuj projekt aplikacji Xamarin.iOS z [tworzenia aplikacji platformy Xamarin na iOS] w celu obsługi funkcji aplikacji azure mobile w trybie offline. Jeśli nie używasz pobranego projektu serwera szybkiego startu, należy dodać pakiety rozszerzeń dostępu do danych do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzeń serwera, zobacz [Praca z zestawem SDK serwera wewnętrznej bazy danych .NET dla aplikacji azure mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Aby dowiedzieć się więcej o funkcji synchronizacji w trybie offline, zobacz temat [Synchronizacja danych w trybie offline w aplikacjach mobilnych platformy Azure].

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizowanie aplikacji klienckiej do obsługi funkcji w trybie offline
Funkcje usługi Azure Mobile App w trybie offline umożliwiają interakcję z lokalną bazą danych w przypadku, gdy jesteś w scenariuszu offline. Aby użyć tych funkcji w aplikacji, zaiwastuj [SyncContext] do sklepu lokalnego. Odwołaj się do tabeli za pomocą interfejsu [IMobileServiceSyncTable]. SQLite jest używany jako lokalny sklep na urządzeniu.

1. Otwórz Menedżera pakietów NuGet w projekcie, który został ukończony w samouczku [Tworzenie aplikacji systemu xamarin dla systemu iOS,] a następnie wyszukaj i zainstaluj pakiet **NuGet firmy Microsoft.Azure.Mobile.Client.SQLiteStore.**
2. Otwórz plik QSTodoService.cs i odkomentuj definicję. `#define OFFLINE_SYNC_ENABLED`
3. Odbuduj i uruchom aplikację kliencką. Aplikacja działa tak samo, jak przed włączeniem synchronizacji w trybie offline. Jednak lokalna baza danych jest teraz wypełniona danymi, które mogą być używane w scenariuszu offline.

## <a name="update-the-app-to-disconnect-from-the-backend"></a><a name="update-sync"></a>Aktualizowanie aplikacji w celu odłączenia się od wewnętrznej bazy danych
W tej sekcji można przerwać połączenie z zaplecza aplikacji mobilnej do symulacji sytuacji w trybie offline. Po dodaniu elementów danych, program obsługi wyjątków informuje, że aplikacja jest w trybie offline. W tym stanie nowe elementy dodane w magazynie lokalnym i zostaną zsynchronizowane z zaplecza aplikacji mobilnej, gdy wypychanie jest dalej uruchamiane w stanie połączonym.

1. Edytuj QSToDoService.cs w projekcie udostępnionym. Zmień **applicationURL,** aby wskazać nieprawidłowy adres URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Można również zademonstrować zachowanie w trybie offline, wyłączając sieci wi-fi i komórkowe na urządzeniu lub w trybie samolotowym.
2. Skompiluj i uruchom aplikację. Zwróć uwagę, że synchronizacja nie powiodła się podczas odświeżania po uruchomieniu aplikacji.
3. Wprowadź nowe elementy i zwróć uwagę, że wypychanie kończy się niepowodzeniem ze stanem [CancelledByNetworkError] przy każdym **kliknięciu przycisku Zapisz**. Jednak nowe elementy todo istnieją w lokalnym sklepie, dopóki nie mogą być wypychane do zaplecza aplikacji mobilnej.  W aplikacji produkcyjnej, jeśli pominąć te wyjątki aplikacja kliencka zachowuje się tak, jakby nadal jest podłączony do wewnętrznej bazy danych aplikacji mobilnej.
4. Zamknij aplikację i uruchom ją ponownie, aby sprawdzić, czy nowe utworzone elementy są utrwalone w magazynie lokalnym.
5. (Opcjonalnie) Jeśli na komputerze jest zainstalowany program Visual Studio, otwórz **Eksploratora serwera**. Przejdź do bazy danych w **usłudze Azure**-> **SQL Databases**. Kliknij prawym przyciskiem myszy bazę danych i wybierz polecenie **Otwórz w Eksploratorze obiektów programu SQL Server**. Teraz możesz przejść do tabeli bazy danych SQL i jej zawartości. Sprawdź, czy dane w bazie danych wewnętrznej bazy danych nie uległy zmianie.
6. (Opcjonalnie) Użyj narzędzia REST, takiego jak Fiddler lub Listonosz, aby zbadać `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`zaplecze telefonu komórkowego, używając zapytania GET w formularzu .

## <a name="update-the-app-to-reconnect-your-mobile-app-backend"></a><a name="update-online-app"></a>Aktualizowanie aplikacji w celu ponownego połączenia zaplecza aplikacji mobilnej
W tej sekcji ponownie połącz aplikację z zapleczem aplikacji mobilnej. Symuluje to przejście aplikacji ze stanu offline do stanu online z zapleczem aplikacji mobilnej.   Jeśli symulowano awarię sieci, wyłączając łączność sieciową, nie są potrzebne żadne zmiany kodu.
Włącz ponownie sieć.  Po pierwszym uruchomieniu aplikacji, `RefreshDataAsync` metoda jest wywoływana. To z `SyncAsync` kolei wywołuje, aby zsynchronizować magazyn lokalny z bazy danych wewnętrznej bazy danych.

1. Otwórz QSToDoService.cs w projekcie udostępnionym i przywróć zmianę właściwości **applicationURL.**
2. Odbuduj i uruchom aplikację. Aplikacja synchronizuje lokalne zmiany z zaplecza aplikacji azure mobile przy `OnRefreshItemsSelected` użyciu operacji wypychania i ściągania podczas wykonywania metody.
3. (Opcjonalnie) Wyświetlanie zaktualizowanych danych przy użyciu Eksploratora obiektów programu SQL Server lub narzędzia REST, takiego jak Fiddler. Zwróć uwagę, że dane zostały zsynchronizowane między bazą danych zaplecza aplikacji Azure Mobile i magazynem lokalnym.
4. W aplikacji kliknij pole wyboru obok kilku elementów, aby zakończyć je w lokalnym sklepie.

   `CompleteItemAsync`wywołania, `SyncAsync` aby zsynchronizować każdy ukończony element z zapleczem aplikacji mobilnej. `SyncAsync`połączenia zarówno push, jak i pull.
   **Za każdym razem, gdy wykonujesz ściąganie względem tabeli, do której klient dokonał zmian, naciśnięcie kontekstu synchronizacji klienta jest zawsze wykonywane najpierw automatycznie.** Niejawne wypychanie zapewnia, że wszystkie tabele w magazynie lokalnym wraz z relacjami pozostają spójne. Aby uzyskać więcej informacji na temat tego zachowania, zobacz [Synchronizacja danych w trybie offline w aplikacjach mobilnych platformy Azure].

## <a name="review-the-client-sync-code"></a>Przejrzyj kod synchronizacji klienta
Projekt klienta platformy Xamarin pobrany po zakończeniu [samouczka Tworzenie aplikacji platformy Xamarin dla systemu iOS] zawiera już kod obsługujący synchronizację w trybie offline przy użyciu lokalnej bazy danych SQLite. Oto krótki przegląd tego, co jest już zawarte w kodzie samouczka. Aby zapoznać się z koncepcyjnym omówieniem tej funkcji, zobacz [Synchronizacja danych w trybie offline w aplikacjach mobilnych platformy Azure].

* Przed wykonaniem operacji tabeli należy zainicjować magazyn lokalny. Podczas `QSTodoListViewController.ViewDidLoad()` wykonywania jest inicjowana `QSTodoService.InitializeStoreAsync()`baza danych magazynu lokalnego. Ta metoda tworzy nową lokalną bazę `MobileServiceSQLiteStore` danych SQLite przy użyciu klasy dostarczonej przez zestaw SDK klienta aplikacji mobilnej azure.

    Metoda `DefineTable` tworzy tabelę w magazynie lokalnym, która pasuje `ToDoItem` do pól w podanym typie, w tym przypadku. Typ nie musi zawierać wszystkich kolumn, które znajdują się w zdalnej bazie danych. Istnieje możliwość przechowywania tylko podzbioru kolumn.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* Element `todoTable` członkowski `QSTodoService` jest `IMobileServiceSyncTable` typu zamiast `IMobileServiceTable`. IMobileServiceSyncTable kieruje wszystkie operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) do lokalnej bazy danych magazynu.

    Ty decydujesz, kiedy te zmiany są wypychane `IMobileServiceSyncContext.PushAsync()`do wewnętrznej bazy danych aplikacji Azure Mobile przez wywołanie . Kontekst synchronizacji pomaga zachować relacje tabeli, śledząc i wypychając `PushAsync` zmiany we wszystkich tabelach, które aplikacja kliencka zmodyfikowała, gdy jest wywoływana.

    Podana wywołana kodem do `QSTodoService.SyncAsync()` synchronizacji za każdym razem, gdy lista todoitem jest odświeżana lub todoitem jest dodawany lub uzupełniany. Aplikacja synchronizuje się po każdej zmianie lokalnej. Jeśli ściąganie jest wykonywane względem tabeli, która oczekuje na aktualizacje lokalne śledzone przez kontekst, ta operacja ściągania automatycznie wyzwoli najpierw wypychanie kontekstu.

    W podanym kodzie wszystkie `TodoItem` rekordy w tabeli zdalnej są wyszukiwane, ale możliwe jest `PushAsync`również filtrowanie rekordów, przekazując identyfikator kwerendy i kwerendę do programu . Aby uzyskać więcej informacji, zobacz sekcję *Synchronizacja przyrostowa* w synchronizacji danych w [trybie offline w aplikacjach mobilnych platformy Azure].

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
* [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]
* [Azure Mobile Apps .NET SDK HOWTO][8]

<!-- Images -->

<!-- URLs. -->
[Tworzenie aplikacji platformy Xamarin na iOS]: app-service-mobile-xamarin-ios-get-started.md
[Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[SyncContext (SyncContext)]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
