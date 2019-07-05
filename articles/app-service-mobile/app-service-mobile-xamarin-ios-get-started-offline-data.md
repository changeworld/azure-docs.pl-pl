---
title: Włączanie synchronizacji offline dla aplikacji mobilnej platformy Azure (Xamarin iOS)
description: Dowiedz się, jak używać aplikacji usługi Mobile App do pamięci podręcznej i synchronizacji danych w trybie offline w aplikacji platformy Xamarin dla systemu iOS
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
ms.openlocfilehash: b87a1d86370e3abdb200b691d5216b1262512b3e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440039"
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Włączanie synchronizacji w trybie offline dla aplikacji mobilnej platformy Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center jest inwestujemy w nowe i zintegrowane usługi decydujące znaczenie dla aplikacji mobilnych. Deweloperzy mogą używać **kompilacji**, **testu** i **dystrybucji** usług do konfigurowania potoku ciągłej integracji i ciągłego dostarczania. Gdy aplikacja jest wdrażana, deweloperzy mogą monitorować stan i użycie ich przy użyciu aplikacji **Analytics** i **diagnostyki** usług i angażuj użytkowników za pomocą **wypychania** Usługa. Deweloperzy mogą również wykorzystać **uwierzytelniania** do uwierzytelniania użytkowników i **danych** usługę, aby utrwalić i synchronizowanie danych aplikacji w chmurze. Zapoznaj się z [platformy App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-ios-get-started-offline-data) już dziś.
>

## <a name="overview"></a>Przegląd
Ten samouczek zawiera funkcję synchronizacji w trybie offline, które usługi Azure Mobile Apps dla platformy Xamarin.iOS. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną — wyświetlanie, dodawanie lub modyfikowanie danych — nawet w przypadku braku połączenia sieciowego. Zmiany są przechowywane w lokalnej bazie danych. Gdy urządzenie jest w trybie online, zmiany te są synchronizowane z usługi zdalnej.

W tym samouczku aktualizacji projektu aplikacji platformy Xamarin.iOS z [tworzenie aplikacji platformy Xamarin.IOS] do obsługi w trybie offline funkcji usługi Azure Mobile Apps. Jeśli nie używasz projektu serwera pobranego — szybki start, należy dodać pakiety rozszerzeń dostępu do danych do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzeń serwera, zobacz [pracy z zestawem SDK serwera zaplecza platformy .NET dla usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Aby dowiedzieć się więcej na temat funkcji synchronizacji w trybie offline, zobacz temat [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizuj aplikację kliencką do obsługi funkcji w trybie offline
Funkcje offline usług Azure Mobile App umożliwiają interakcję z lokalnej bazy danych, gdy znajdują się w ramach scenariusza w trybie offline. Korzystanie z tych funkcji w aplikacji należy zainicjować [SyncContext] do magazynu lokalnego. Odwoływać się do tabeli za pomocą interfejsu [IMobileServiceSyncTable]. Bazy danych SQLite jest używana jako magazynu lokalnego na urządzeniu.

1. Otwórz Menedżera pakietów NuGet w projekcie, który należy wykonać w [tworzenie aplikacji platformy Xamarin.IOS] samouczek, a następnie wyszukaj i zainstaluj **Microsoft.Azure.Mobile.Client.SQLiteStore** pakietu NuGet.
2. Otwórz plik QSTodoService.cs i usuń znaczniki komentarza `#define OFFLINE_SYNC_ENABLED` definicji.
3. Ponownie skompiluj i uruchom aplikację kliencką. Aplikacja działa tak samo jak przed włączeniem synchronizacji w trybie offline. Jednak lokalnej bazy danych jest teraz wypełniana z danymi, które mogą być używane w ramach scenariusza w trybie offline.

## <a name="update-sync"></a>Zaktualizować aplikację tak, aby zakończyć połączenie z wewnętrznej bazy danych
W tej sekcji możesz przerwać połączenie do zaplecza aplikacji mobilnej do symulacji taka sytuacja jest w trybie offline. Po dodaniu elementów danych, obsłudze wyjątków informujący o tym, że aplikacja jest w trybie offline. W tym stanie nowe elementy dodane w magazynie lokalnym i będą synchronizowane z zaplecza aplikacji mobilnej, po następnym uruchomieniu wypychania niepodłączony.

1. Edytuj QSToDoService.cs projektu udostępnionego. Zmiana **applicationURL** wskaż nieprawidłowy adres URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Można również pokazują działanie w trybie offline, wyłączając sieć Wi-Fi i sieci komórkowych na urządzeniu, lub za pomocą tryb samolotowy.
2. Skompiluj i uruchom aplikację. Zwróć uwagę, usługi synchronizacji nie powiodło się podczas odświeżania, gdy aplikacja jest uruchamiana.
3. Wprowadź nowe elementy i zwróć uwagę, że wypychania kończy się niepowodzeniem ze stanem [CancelledByNetworkError] każde kliknięcie **Zapisz**. Jednak nowe elementy zadań do wykonania istnieje w magazynie lokalnym do czasu może zostać przeniesiony do zaplecza aplikacji mobilnej.  W aplikacji produkcyjnej Jeśli pominąć te wyjątki aplikacji klienckiej zachowuje się tak, jakby nadal jest podłączony do zaplecza aplikacji mobilnej.
4. Zamknij aplikację i uruchom ponownie, aby sprawdzić, czy nowych elementów, który został utworzony są utrwalane w magazynie lokalnym.
5. (Opcjonalnie) Jeśli masz zainstalowany na komputerze program Visual Studio, otwórz **Eksploratora serwera**. Przejdź do bazy danych w **Azure**-> **baz danych SQL**. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Otwórz w Eksploratorze obiektów SQL Server**. Teraz możesz przejść do tabeli bazy danych SQL i jego zawartość. Upewnij się, że dane w wewnętrznej bazy danych nie została zmieniona.
6. (Opcjonalnie) Za pomocą narzędzia REST narzędzia Fiddler lub Postman do wykonywania zapytań w zapleczu mobilnym za pomocą zapytania GET w formie `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Zaktualizować aplikację tak, aby ponownie połączyć zaplecze aplikacji mobilnej
W tej sekcji ponowne łączenie aplikacji z zapleczem aplikacji mobilnej. Symuluje to aplikacji przejście ze stanu offline do trybu online, przy użyciu zaplecza aplikacji mobilnej.   Jeśli uszkodzenie sieci jest symulowane przez wyłączenie opcji łączności sieciowej, są wymagane żadne zmiany w kodzie.
Ponownie włącz sieci.  Przy pierwszym uruchomieniu aplikacji, `RefreshDataAsync` metoda jest wywoływana. To z kolei wywołuje `SyncAsync` do synchronizacji magazynu lokalnego przy użyciu wewnętrznej bazy danych.

1. Otwórz QSToDoService.cs w projekcie udostępnionym i przywrócić zmiana **applicationURL** właściwości.
2. Ponownie skompiluj i uruchom aplikację. Aplikacja synchronizuje zmiany lokalne przy użyciu zaplecza aplikacji mobilnej platformy Azure przy użyciu operacji wypychania i ściągania podczas `OnRefreshItemsSelected` metoda jest wykonywana.
3. (Opcjonalnie) Wyświetl zaktualizowane dane za pomocą Eksplorator obiektów SQL Server lub narzędzia REST, takiego jak Fiddler. Ogłoszenie dane zostały zsynchronizowane między bazy danych zaplecza aplikacji mobilnej platformy Azure i Magazyn lokalny.
4. W aplikacji kliknij pole wyboru obok kilka elementów, aby zakończyć je w magazynie lokalnym.

   `CompleteItemAsync` wywołania `SyncAsync` do synchronizacji każdego elementu przy użyciu zaplecza aplikacji mobilnej. `SyncAsync` wywołuje operacje wypychania i ściągania.
   **Po każdym wykonaniu ściągnięcia względem tabeli, który klient wprowadził zmiany do, powiadomienie wypychane na kontekst synchronizacji klienta jest zawsze wykonywane jako pierwsze automatycznie**. Niejawne wypychania gwarantuje, że wszystkie tabele w magazynie lokalnym oraz relacje pozostają spójne. Aby uzyskać więcej informacji na temat tego zachowania, zobacz [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

## <a name="review-the-client-sync-code"></a>Przejrzyj kod klienta synchronizacji
Projektu klienta Xamarin, który został pobrany, po ukończeniu tego samouczka [tworzenie aplikacji platformy Xamarin.IOS] już zawiera kod obsługi synchronizacji w trybie offline przy użyciu lokalnej bazy danych SQLite. Poniżej przedstawiono krótkie omówienie co to jest już uwzględniony w kodu z samouczka dotyczącego. Aby uzyskać omówienie pojęć dotyczących tej funkcji, zobacz [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

* Można było wykonać żadnych operacji tabeli, musi zostać zainicjowany Magazyn lokalny. Magazyn lokalny jest inicjowana po `QSTodoListViewController.ViewDidLoad()` wykonuje `QSTodoService.InitializeStoreAsync()`. Ta metoda tworzy nowy lokalnej bazy danych SQLite bazy danych przy użyciu `MobileServiceSQLiteStore` klasy udostępniane przez zestaw SDK klienta usługi Azure Mobile App.

    `DefineTable` Metoda tworzy tabelę w magazynie lokalnym, odpowiadającą polom w podany typ `ToDoItem` w tym przypadku. Typ nie musi zawierać wszystkie kolumny, które znajdują się w zdalnej bazy danych. Istnieje możliwość przechowywania tylko podzbiór kolumn.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* `todoTable` Członkiem `QSTodoService` jest `IMobileServiceSyncTable` wpisz zamiast `IMobileServiceTable`. IMobileServiceSyncTable kieruje wszystkie tworzenia, odczytu, aktualizacji i usuwania (CRUD) operacje tabeli w bazie danych magazynu lokalnego.

    Zdecyduj, kiedy te zmiany są przekazywane z zapleczem aplikacji mobilnej platformy Azure przez wywołanie metody `IMobileServiceSyncContext.PushAsync()`. Kontekst synchronizacji pomaga zachować relacje między tabelami, śledzenia i wypychaniu zmian można znaleźć we wszystkich tabelach aplikacji klienckiej został zmodyfikowany podczas `PushAsync` jest wywoływana.

    Podany kod wywołuje `QSTodoService.SyncAsync()` do synchronizacji przy każdym odświeżeniu listy todoitem lub dodano lub zakończona czynność do wykonania. Synchronizacje aplikacji po każdej zmianie lokalnego. Jeśli ściągnięcia jest wykonywana na tabeli, która ma oczekujące aktualizacje lokalnego śledzona przez kontekst, tej operacji ściągania automatycznie wyzwoli powiadomienie wypychane kontekstu najpierw.

    Podany kod, wszystkie rekordy w zdalnym `TodoItem` zapytania dotyczącego tabeli, ale jest również możliwe do filtrowania rekordów, przekazując identyfikator zapytania i Wyślij zapytanie w celu `PushAsync`. Aby uzyskać więcej informacji, zobacz sekcję *synchronizacja przyrostowa* w [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

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
* [Porada zestawu SDK .NET usługi Azure Mobile Apps][8]

<!-- Images -->

<!-- URLs. -->
[Tworzenie aplikacji platformy Xamarin.IOS]: app-service-mobile-xamarin-ios-get-started.md
[Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
