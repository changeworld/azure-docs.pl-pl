---
title: Włączanie synchronizacji w trybie offline dla aplikacji mobilnej platformy Azure (Xamarin iOS)
description: Dowiedz się, jak używać App Service aplikacji mobilnej do buforowania i synchronizowania danych w trybie offline w aplikacji platformy Xamarin iOS
documentationcenter: xamarin
author: elamalani
manager: cfowler
editor: ''
services: app-service\mobile
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 654144c79023cc5ed113e7ac4b80b5e137284388
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72024905"
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Włączanie synchronizacji w trybie offline dla aplikacji mobilnej platformy Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi centralne do tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług **kompilowania**, **testowania** i **dystrybucji** , aby skonfigurować ciągłą integrację i potok dostarczania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie swojej aplikacji przy użyciu usług **analizy** i **diagnostyki** oraz angażować się z użytkownikami za pomocą usługi **wypychania** . Deweloperzy mogą również korzystać z **uwierzytelniania** w celu uwierzytelniania użytkowników i usługi **danych** w celu utrwalania i synchronizowania danych aplikacji w chmurze.
> Jeśli chcesz zintegrować usługi w chmurze w swojej aplikacji mobilnej, zarejestruj się w usłudze App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

## <a name="overview"></a>Przegląd
W tym samouczku przedstawiono funkcję synchronizacji offline usługi Azure Mobile Apps dla platformy Xamarin. iOS. Synchronizacja w trybie offline umożliwia użytkownikom końcowym korzystanie z aplikacji mobilnej — wyświetlanie, Dodawanie lub modyfikowanie danych — nawet w przypadku braku połączenia sieciowego. Zmiany są przechowywane w lokalnej bazie danych. Gdy urządzenie przewróci do trybu online, te zmiany są synchronizowane z usługą zdalną.

W tym samouczku zaktualizuj projekt aplikacji Xamarin. iOS z [Tworzenie aplikacji platformy Xamarin dla systemu iOS] w celu obsługi funkcji w trybie offline w usłudze Azure Mobile Apps. Jeśli nie używasz pobranego projektu szybkiego startu serwera, musisz dodać pakiety rozszerzenia dostępu do danych do projektu. Aby uzyskać więcej informacji o pakietach rozszerzeń serwera, zobacz [Work with the .NET zaplecz Server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Aby dowiedzieć się więcej na temat funkcji synchronizacji w trybie offline, zobacz temat [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizowanie aplikacji klienckiej w celu obsługi funkcji w trybie offline
Funkcje offline aplikacji mobilnych platformy Azure umożliwiają współdziałanie z lokalną bazą danych w przypadku scenariusza w trybie offline. Aby korzystać z tych funkcji w aplikacji, zainicjuj [SyncContext] do magazynu lokalnego. Odwołuje się do tabeli za pomocą interfejsu [IMobileServiceSyncTable]. Oprogramowania SQLite jest używany jako magazyn lokalny na urządzeniu.

1. Otwórz Menedżera pakietów NuGet w projekcie, który został ukończony w samouczku [Tworzenie aplikacji platformy Xamarin dla systemu iOS] , a następnie wyszukaj i zainstaluj pakiet NuGet **Microsoft. Azure. Mobile. Client. SQLiteStore** .
2. Otwórz plik QSTodoService.cs i Usuń komentarz z definicji `#define OFFLINE_SYNC_ENABLED`.
3. Skompiluj ponownie i uruchom aplikację kliencką. Aplikacja działa tak samo, jak przed włączeniem synchronizacji w trybie offline. Jednak lokalna baza danych jest teraz wypełniana danymi, które mogą być używane w scenariuszu w trybie offline.

## <a name="update-sync"></a>Aktualizowanie aplikacji w celu rozłączenia z zapleczem
W tej sekcji można przerwać połączenie z zapleczem aplikacji mobilnej w celu symulowania sytuacji w trybie offline. Po dodaniu elementów danych program obsługi wyjątków informuje o tym, że aplikacja jest w trybie offline. W tym stanie nowe elementy dodane w magazynie lokalnym są synchronizowane z zapleczem aplikacji mobilnej po następnym uruchomieniu wypychania w stanie połączonym.

1. Edytuj QSToDoService.cs w projekcie udostępnionym. Zmień **applicationURL** tak, aby wskazywał nieprawidłowy adres URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Zachowanie w trybie offline można także zademonstrować, wyłączając sieci Wi-Fi i komórkowe na urządzeniu lub korzystając z trybu samolotowego.
2. Skompiluj i uruchom aplikację. Zwróć uwagę, że synchronizacja nie powiodła się po uruchomieniu aplikacji.
3. Wprowadź nowe elementy i zwróć uwagę na to, że po każdym kliknięciu przycisku **Zapisz**wypchnięcie zakończy się niepowodzeniem z użyciem elementu [CancelledByNetworkError]. Nowe elementy do wykonania znajdują się jednak w magazynie lokalnym, dopóki nie będą mogły zostać wypchnięte do zaplecza aplikacji mobilnej.  W aplikacji produkcyjnej, jeśli pominięto te wyjątki, aplikacja kliencka działa tak, jakby nadal była połączona z zapleczem aplikacji mobilnej.
4. Zamknij aplikację i uruchom ją ponownie, aby sprawdzić, czy nowe utworzone elementy są utrwalane w magazynie lokalnym.
5. Obowiązkowe Jeśli na komputerze jest zainstalowany program Visual Studio, Otwórz **Eksplorator serwera**. Przejdź do bazy danych w **usłudze Azure**@no__t — 1**bazy danych SQL**. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie **Otwórz w Eksplorator obiektów SQL Server**. Teraz możesz przejść do tabeli bazy danych SQL i jej zawartości. Sprawdź, czy dane w bazie danych zaplecza nie uległy zmianie.
6. Obowiązkowe Użyj narzędzia REST, takiego jak programu Fiddler lub Poster, aby wysłać zapytanie do zaplecza mobilnego, używając zapytania GET w formularzu `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Aktualizowanie aplikacji w celu ponownego połączenia zaplecza aplikacji mobilnej
W tej sekcji ponownie połącz aplikację z zapleczem aplikacji mobilnej. To symuluje przechodzenie aplikacji ze stanu offline do stanu online przy użyciu zaplecza aplikacji mobilnej.   W przypadku symulowania zerwania sieci przez wyłączenie łączności sieciowej nie są potrzebne żadne zmiany w kodzie.
Włącz ponownie sieć.  Po pierwszym uruchomieniu aplikacji wywoływana jest metoda `RefreshDataAsync`. To z kolei wywołuje `SyncAsync` w celu zsynchronizowania magazynu lokalnego z bazą danych zaplecza.

1. Otwórz QSToDoService.cs w projekcie udostępnionym i przywróć zmiany właściwości **applicationURL** .
2. Skompiluj ponownie i uruchom aplikację. Aplikacja synchronizuje lokalne zmiany z zapleczem aplikacji mobilnej platformy Azure przy użyciu operacji wypychania i ściągania, gdy zostanie wykonana Metoda `OnRefreshItemsSelected`.
3. Obowiązkowe Wyświetl zaktualizowane dane przy użyciu Eksplorator obiektów SQL Server lub narzędzia REST, takiego jak programu Fiddler. Zauważ, że dane zostały zsynchronizowane między bazą danych zaplecza aplikacji mobilnej Azure a magazynem lokalnym.
4. W aplikacji kliknij pole wyboru obok kilku elementów, aby zakończyć je w magazynie lokalnym.

   `CompleteItemAsync` wywołań `SyncAsync` w celu zsynchronizowania każdego ukończonego elementu z zapleczem aplikacji mobilnej. `SyncAsync` wywołuje zarówno wypychanie, jak i ściąganie.
   **Za każdym razem, gdy wykonujesz ściąganie względem tabeli, do której wprowadzono zmiany, zostanie automatycznie wykonane wypychanie kontekstu synchronizacji klienta**. Niejawne wypychanie gwarantuje, że wszystkie tabele w magazynie lokalnym wraz z relacjami pozostają spójne. Aby uzyskać więcej informacji na temat tego zachowania, zobacz [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

## <a name="review-the-client-sync-code"></a>Przejrzyj kod synchronizacji klienta
Projekt programu Xamarin Client pobrany po ukończeniu samouczka [Tworzenie aplikacji platformy Xamarin dla systemu iOS] już zawiera kod obsługujący synchronizację w trybie offline przy użyciu lokalnej bazy danych programu SQLite. Oto krótkie omówienie tego, co zostało już zawarte w kodzie samouczka. Aby zapoznać się z omówieniem koncepcyjnym tej funkcji, zobacz [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

* Przed wykonaniem jakiejkolwiek operacji na tabeli należy zainicjować magazyn lokalny. Baza danych lokalnego magazynu jest inicjowana, gdy `QSTodoListViewController.ViewDidLoad()` wykonuje `QSTodoService.InitializeStoreAsync()`. Ta metoda tworzy nową lokalną bazę danych programu SQLite przy użyciu klasy `MobileServiceSQLiteStore` dostarczonej przez zestaw SDK klienta aplikacji mobilnych platformy Azure.

    Metoda `DefineTable` tworzy tabelę w lokalnym magazynie, która pasuje do pól w danym typie, `ToDoItem` w tym przypadku. Typ nie musi zawierać wszystkich kolumn, które znajdują się w zdalnej bazie danych. Istnieje możliwość przechowywania tylko podzbioru kolumn.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* Członek `todoTable` elementu `QSTodoService` jest typu `IMobileServiceSyncTable` zamiast `IMobileServiceTable`. IMobileServiceSyncTable kieruje wszystkie operacje tabeli Create, Read, Update i Delete (CRUD) do lokalnej bazy danych magazynu.

    Użytkownik decyduje o tym, kiedy te zmiany są przekazywane do zaplecza aplikacji mobilnej Azure, wywołując `IMobileServiceSyncContext.PushAsync()`. Kontekst synchronizacji pomaga zachować relacje między tabelami przez śledzenie i wypychanie zmian we wszystkich tabelach, gdy aplikacja kliencka została zmodyfikowana po wywołaniu `PushAsync`.

    Podany kod wywołuje `QSTodoService.SyncAsync()` do synchronizowania za każdym razem, gdy lista TodoItem jest odświeżana lub dodano lub zakończył TodoItem. Aplikacja jest synchronizowana po każdej zmianie lokalnej. Jeśli ściąganie jest wykonywane względem tabeli, która ma oczekujące aktualizacje lokalne śledzone przez kontekst, ta operacja ściągania automatycznie wyzwala najpierw wypychanie kontekstu.

    W podanym kodzie wszystkie rekordy w tabeli zdalnej `TodoItem` są badane, ale można również filtrować rekordy, przekazując identyfikator zapytania i zapytanie do `PushAsync`. Aby uzyskać więcej informacji, zobacz sekcję *Synchronizacja przyrostowa* w obszarze [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

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

## <a name="additional-resources"></a>Zasoby dodatkowe
* [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]
* [Azure Mobile Apps .NET SDK porady][8]

<!-- Images -->

<!-- URLs. -->
[Tworzenie aplikacji platformy Xamarin dla systemu iOS]: app-service-mobile-xamarin-ios-get-started.md
[Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
