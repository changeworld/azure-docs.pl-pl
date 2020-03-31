---
title: Włącz synchronizację w trybie offline (Xamarin Android)
description: Dowiedz się, jak używać aplikacji mobilnej usługi App Service do buforowania i synchronizowania danych offline w aplikacji platformy Xamarin na Androida.
ms.assetid: 91d59e4b-abaa-41f4-80cf-ee7933b32568
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 4faab572cd77e20bb61d147ca6e58b25cd88ad9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458872"
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Włącz synchronizację offline dla aplikacji mobilnej Xamarin.Android

[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Omówienie

W tym samouczku przedstawiono funkcję synchronizacji w trybie offline usługi Azure Mobile Apps for Xamarin.Android. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną — wyświetlanie, dodawanie lub modyfikowanie danych — nawet wtedy, gdy nie ma połączenia sieciowego. Zmiany są przechowywane w lokalnej bazie danych.
Po powrocie urządzenia do trybu online zmiany te są synchronizowane z usługą zdalną.

W tym samouczku zaktualizujesz projekt klienta z [samouczka Tworzenie aplikacji platformy Xamarin dla systemu Android] w celu obsługi funkcji w trybie offline aplikacji mobilnych platformy Azure. Jeśli nie używasz pobranego projektu serwera szybkiego startu, należy dodać pakiety rozszerzeń dostępu do danych do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzeń serwera, zobacz [Praca z zestawem SDK serwera wewnętrznej bazy danych .NET dla aplikacji azure mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Aby dowiedzieć się więcej o funkcji synchronizacji w trybie offline, zobacz temat [Synchronizacja danych w trybie offline w aplikacjach mobilnych platformy Azure].

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizowanie aplikacji klienckiej do obsługi funkcji w trybie offline

Funkcje usługi Azure Mobile App w trybie offline umożliwiają interakcję z lokalną bazą danych w przypadku, gdy jesteś w scenariuszu offline. Aby użyć tych funkcji w aplikacji, należy zainicjować [SyncContext] do sklepu lokalnego. Następnie odwołaj się do tabeli za pośrednictwem interfejsu [IMobileServiceSyncTable.](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable?view=azure-dotnet) SQLite jest używany jako lokalny sklep na urządzeniu.

1. W programie Visual Studio otwórz Menedżera pakietów NuGet w projekcie, który został ukończony w [samouczku Tworzenie aplikacji systemu Android platformy Xamarin.]  Wyszukaj i zainstaluj pakiet **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet.
2. Otwórz plik ToDoActivity.cs i odkomentuj definicję. `#define OFFLINE_SYNC_ENABLED`
3. W programie Visual Studio naciśnij klawisz **F5,** aby odbudować i uruchomić aplikację kliencką. Aplikacja działa tak samo, jak przed włączeniem synchronizacji w trybie offline. Jednak lokalna baza danych jest teraz wypełniona danymi, które mogą być używane w scenariuszu offline.

## <a name="update-the-app-to-disconnect-from-the-backend"></a><a name="update-sync"></a>Aktualizowanie aplikacji w celu odłączenia się od wewnętrznej bazy danych

W tej sekcji można przerwać połączenie z zaplecza aplikacji mobilnej do symulacji sytuacji w trybie offline. Po dodaniu elementów danych, program obsługi wyjątków informuje, że aplikacja jest w trybie offline. W tym stanie nowe elementy dodane w magazynie lokalnym i są synchronizowane z zaplecza aplikacji mobilnej, gdy wypychanie jest wykonywane w stanie połączonym.

1. Edytuj ToDoActivity.cs w projekcie udostępnionym. Zmień **applicationURL,** aby wskazać nieprawidłowy adres URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Można również zademonstrować zachowanie w trybie offline, wyłączając sieci wi-fi i komórkowe na urządzeniu lub w trybie samolotowym.
2. Naciśnij **klawisz F5,** aby utworzyć i uruchomić aplikację. Zwróć uwagę, że synchronizacja nie powiodła się podczas odświeżania po uruchomieniu aplikacji.
3. Wprowadź nowe elementy i zwróć uwagę, że wypychanie kończy się niepowodzeniem ze stanem [CancelledByNetworkError] przy każdym **kliknięciu przycisku Zapisz**. Jednak nowe elementy todo istnieją w lokalnym sklepie, dopóki nie mogą być wypychane do zaplecza aplikacji mobilnej.  W aplikacji produkcyjnej, jeśli pominąć te wyjątki aplikacja kliencka zachowuje się tak, jakby nadal jest podłączony do wewnętrznej bazy danych aplikacji mobilnej.
4. Zamknij aplikację i uruchom ją ponownie, aby sprawdzić, czy nowe utworzone elementy są utrwalone w magazynie lokalnym.
5. (Opcjonalnie) W programie Visual Studio otwórz **Eksploratora serwera**. Przejdź do bazy danych w **usłudze Azure**->**SQL Databases**. Kliknij prawym przyciskiem myszy bazę danych i wybierz polecenie **Otwórz w Eksploratorze obiektów programu SQL Server**. Teraz możesz przejść do tabeli bazy danych SQL i jej zawartości. Sprawdź, czy dane w bazie danych wewnętrznej bazy danych nie uległy zmianie.
6. (Opcjonalnie) Użyj narzędzia REST, takiego jak Fiddler lub Listonosz, aby zbadać `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`zaplecze telefonu komórkowego, używając zapytania GET w formularzu .

## <a name="update-the-app-to-reconnect-your-mobile-app-backend"></a><a name="update-online-app"></a>Aktualizowanie aplikacji w celu ponownego połączenia zaplecza aplikacji mobilnej

W tej sekcji ponownie połącz aplikację z zapleczem aplikacji mobilnej. Po pierwszym uruchomieniu aplikacji `OnCreate` program obsługi `OnRefreshItemsSelected`zdarzeń wywołuje . Ta metoda `SyncAsync` wywołuje, aby zsynchronizować magazyn lokalny z bazy danych wewnętrznej bazy danych.

1. Otwórz ToDoActivity.cs w projekcie udostępnionym i przywróć zmianę właściwości **applicationURL.**
2. Naciśnij klawisz **F5,** aby odbudować i uruchomić aplikację. Aplikacja synchronizuje lokalne zmiany z zaplecza aplikacji azure mobile przy `OnRefreshItemsSelected` użyciu operacji wypychania i ściągania podczas wykonywania metody.
3. (Opcjonalnie) Wyświetlanie zaktualizowanych danych przy użyciu Eksploratora obiektów programu SQL Server lub narzędzia REST, takiego jak Fiddler. Zwróć uwagę, że dane zostały zsynchronizowane między bazą danych zaplecza aplikacji Azure Mobile i magazynem lokalnym.
4. W aplikacji kliknij pole wyboru obok kilku elementów, aby zakończyć je w lokalnym sklepie.

   `CheckItem`wywołania, `SyncAsync` aby zsynchronizować każdy ukończony element z zapleczem aplikacji mobilnej. `SyncAsync`połączenia zarówno push, jak i pull. **Za każdym razem, gdy wykonujesz pociągnięcie względem tabeli, do której klient dokonał zmian, wypychanie jest zawsze wykonywane automatycznie.** Dzięki temu wszystkie tabele w magazynie lokalnym wraz z relacjami pozostają spójne. To zachowanie może spowodować nieoczekiwane wypychanie. Aby uzyskać więcej informacji na temat tego zachowania, zobacz [Synchronizacja danych w trybie offline w aplikacjach mobilnych platformy Azure].

## <a name="review-the-client-sync-code"></a>Przejrzyj kod synchronizacji klienta

Projekt klienta platformy Xamarin pobrany po zakończeniu [samouczka Tworzenie aplikacji systemu Android platformy Xamarin] zawiera już kod obsługujący synchronizację w trybie offline przy użyciu lokalnej bazy danych SQLite. Oto krótki przegląd tego, co jest już zawarte w kodzie samouczka. Aby zapoznać się z koncepcyjnym omówieniem tej funkcji, zobacz [Synchronizacja danych w trybie offline w aplikacjach mobilnych platformy Azure].

* Przed wykonaniem operacji tabeli należy zainicjować magazyn lokalny. Podczas `ToDoActivity.OnCreate()` wykonywania jest inicjowana `ToDoActivity.InitLocalStoreAsync()`baza danych magazynu lokalnego. Ta metoda tworzy lokalną bazę `MobileServiceSQLiteStore` danych SQLite przy użyciu klasy dostarczonej przez zestaw SDK klienta usługi Azure Mobile Apps.

    Metoda `DefineTable` tworzy tabelę w magazynie lokalnym, która pasuje `ToDoItem` do pól w podanym typie, w tym przypadku. Typ nie musi zawierać wszystkich kolumn, które znajdują się w zdalnej bazie danych. Istnieje możliwość przechowywania tylko podzbioru kolumn.

        // ToDoActivity.cs
        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment
                .GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            // To use a different conflict handler, pass a parameter to InitializeAsync.
            // For more details, see https://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }
* Element `toDoTable` członkowski `ToDoActivity` jest `IMobileServiceSyncTable` typu zamiast `IMobileServiceTable`. IMobileServiceSyncTable kieruje wszystkie operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) do lokalnej bazy danych magazynu.

    Ty decydujesz, kiedy zmiany są wypychane `IMobileServiceSyncContext.PushAsync()`do wewnętrznej bazy danych aplikacji Azure Mobile przez wywołanie . Kontekst synchronizacji pomaga zachować relacje tabeli, śledząc i wypychając `PushAsync` zmiany we wszystkich tabelach, które aplikacja kliencka zmodyfikowała, gdy jest wywoływana.

    Podana wywołana kodem do `ToDoActivity.SyncAsync()` synchronizacji za każdym razem, gdy lista todoitem jest odświeżana lub todoitem jest dodawany lub uzupełniany. Kod synchronizuje się po każdej zmianie lokalnej.

    W podanym kodzie wszystkie `TodoItem` rekordy w tabeli zdalnej są wyszukiwane, ale możliwe jest `PushAsync`również filtrowanie rekordów, przekazując identyfikator kwerendy i kwerendę do programu . Aby uzyskać więcej informacji, zobacz sekcję *Synchronizacja przyrostowa* w synchronizacji danych w [trybie offline w aplikacjach mobilnych platformy Azure].

        // ToDoActivity.cs
        private async Task SyncAsync()
        {
            try {
                await client.SyncContext.PushAsync();
                await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
            } catch (Java.Net.MalformedURLException) {
                CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
            } catch (Exception e) {
                CreateAndShowDialog (e, "Error");
            }
        }

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]
* [Azure Mobile Apps .NET SDK HOWTO][8]

<!-- URLs. -->
[Tworzenie aplikacji platformy Xamarin na Androida]: ./app-service-mobile-xamarin-android-get-started.md
[Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]: ./app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Tworzenie aplikacji platformy Xamarin na Androida]: app-service-mobile-xamarin-android-get-started.md
[Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: https://xamarin.com/download
[Xamarin extension]: https://xamarin.com/visual-studio
[SyncContext (SyncContext)]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
