---
title: Włączanie synchronizacji w trybie offline (Xamarin Android)
description: Dowiedz się, jak używać App Service aplikacji mobilnej do buforowania i synchronizowania danych w trybie offline w aplikacji platformy Xamarin dla systemu Android.
ms.assetid: 91d59e4b-abaa-41f4-80cf-ee7933b32568
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 4faab572cd77e20bb61d147ca6e58b25cd88ad9f
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77458872"
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Włączanie synchronizacji w trybie offline dla aplikacji mobilnej platformy Xamarin. Android

[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Omówienie

W tym samouczku przedstawiono funkcję synchronizacji w trybie offline Mobile Apps platformy Xamarin. Android. Synchronizacja w trybie offline umożliwia użytkownikom końcowym korzystanie z aplikacji mobilnej — wyświetlanie, Dodawanie lub modyfikowanie danych — nawet w przypadku braku połączenia sieciowego. Zmiany są przechowywane w lokalnej bazie danych.
Gdy urządzenie przewróci do trybu online, te zmiany są synchronizowane z usługą zdalną.

W tym samouczku zostanie zaktualizowany projekt klienta z samouczka [Tworzenie aplikacji platformy Xamarin dla systemu Android] w celu obsługi funkcji w trybie offline platformy Azure Mobile Apps. Jeśli nie używasz pobranego projektu szybkiego startu serwera, musisz dodać pakiety rozszerzenia dostępu do danych do projektu. Aby uzyskać więcej informacji o pakietach rozszerzeń serwera, zobacz [Work with the .NET zaplecz Server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Aby dowiedzieć się więcej na temat funkcji synchronizacji w trybie offline, zobacz temat [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizowanie aplikacji klienckiej w celu obsługi funkcji w trybie offline

Funkcje offline aplikacji mobilnych platformy Azure umożliwiają współdziałanie z lokalną bazą danych w przypadku scenariusza w trybie offline. Aby korzystać z tych funkcji w aplikacji, należy zainicjować [SyncContext] do magazynu lokalnego. Następnie odwołują się do tabeli za pomocą interfejsu [IMobileServiceSyncTable](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable?view=azure-dotnet) . Oprogramowania SQLite jest używany jako magazyn lokalny na urządzeniu.

1. W programie Visual Studio Otwórz Menedżera pakietów NuGet w projekcie, który został ukończony w samouczku [Tworzenie aplikacji platformy Xamarin dla systemu Android] .  Wyszukaj i zainstaluj pakiet NuGet **Microsoft. Azure. Mobile. Client. SQLiteStore** .
2. Otwórz plik ToDoActivity.cs i Usuń oznaczenie `#define OFFLINE_SYNC_ENABLED` definicji.
3. W programie Visual Studio naciśnij klawisz **F5** , aby skompilować i uruchomić aplikację kliencką. Aplikacja działa tak samo, jak przed włączeniem synchronizacji w trybie offline. Jednak lokalna baza danych jest teraz wypełniana danymi, które mogą być używane w scenariuszu w trybie offline.

## <a name="update-sync"></a>Aktualizowanie aplikacji w celu rozłączenia z zapleczem

W tej sekcji można przerwać połączenie z zapleczem aplikacji mobilnej w celu symulowania sytuacji w trybie offline. Po dodaniu elementów danych program obsługi wyjątków informuje o tym, że aplikacja jest w trybie offline. W tym stanie nowe elementy dodane do magazynu lokalnego i są synchronizowane z zapleczem aplikacji mobilnej, gdy wypychanie jest wykonywane w stanie połączonym.

1. Edytuj ToDoActivity.cs w projekcie udostępnionym. Zmień **applicationURL** tak, aby wskazywał nieprawidłowy adres URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Zachowanie w trybie offline można także zademonstrować, wyłączając sieci Wi-Fi i komórkowe na urządzeniu lub korzystając z trybu samolotowego.
2. Naciśnij klawisz **F5** , aby skompilować i uruchomić aplikację. Zwróć uwagę, że synchronizacja nie powiodła się po uruchomieniu aplikacji.
3. Wprowadź nowe elementy i zwróć uwagę na to, że po każdym kliknięciu przycisku **Zapisz**wypchnięcie zakończy się niepowodzeniem z użyciem elementu [CancelledByNetworkError]. Nowe elementy do wykonania znajdują się jednak w magazynie lokalnym, dopóki nie będą mogły zostać wypchnięte do zaplecza aplikacji mobilnej.  W aplikacji produkcyjnej, jeśli pominięto te wyjątki, aplikacja kliencka działa tak, jakby nadal była połączona z zapleczem aplikacji mobilnej.
4. Zamknij aplikację i uruchom ją ponownie, aby sprawdzić, czy nowe utworzone elementy są utrwalane w magazynie lokalnym.
5. Obowiązkowe W programie Visual Studio Otwórz **Eksplorator serwera**. Przejdź do bazy danych w **usłudze Azure**->**bazy danych SQL**. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie **Otwórz w Eksplorator obiektów SQL Server**. Teraz możesz przejść do tabeli bazy danych SQL i jej zawartości. Sprawdź, czy dane w bazie danych zaplecza nie uległy zmianie.
6. Obowiązkowe Użyj narzędzia REST, takiego jak programu Fiddler lub Poster, aby wysłać zapytanie do zaplecza mobilnego, używając zapytania GET w formularzu `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Aktualizowanie aplikacji w celu ponownego połączenia zaplecza aplikacji mobilnej

W tej sekcji ponownie połącz aplikację z zapleczem aplikacji mobilnej. Po pierwszym uruchomieniu aplikacji program obsługi zdarzeń `OnCreate` wywołuje `OnRefreshItemsSelected`. Ta metoda wywołuje `SyncAsync` synchronizowanie magazynu lokalnego z bazą danych zaplecza.

1. Otwórz ToDoActivity.cs w projekcie udostępnionym i przywróć zmiany właściwości **applicationURL** .
2. Naciśnij klawisz **F5** , aby skompilować i uruchomić aplikację. Aplikacja synchronizuje lokalne zmiany z zapleczem aplikacji mobilnej platformy Azure przy użyciu operacji wypychania i ściągania, gdy zostanie wykonana Metoda `OnRefreshItemsSelected`.
3. Obowiązkowe Wyświetl zaktualizowane dane przy użyciu Eksplorator obiektów SQL Server lub narzędzia REST, takiego jak programu Fiddler. Zauważ, że dane zostały zsynchronizowane między bazą danych zaplecza aplikacji mobilnej Azure a magazynem lokalnym.
4. W aplikacji kliknij pole wyboru obok kilku elementów, aby zakończyć je w magazynie lokalnym.

   `CheckItem` wywołań `SyncAsync` do synchronizacji każdego ukończonego elementu z zapleczem aplikacji mobilnej. `SyncAsync` wywołuje zarówno wypychanie, jak i ściąganie. **Za każdym razem, gdy wykonujesz ściąganie względem tabeli, do której wprowadzono zmiany, wypchnięcie jest zawsze wykonywane automatycznie**. Dzięki temu wszystkie tabele w magazynie lokalnym wraz z relacjami pozostają spójne. Takie zachowanie może spowodować nieoczekiwane wypchnięcie. Aby uzyskać więcej informacji na temat tego zachowania, zobacz [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

## <a name="review-the-client-sync-code"></a>Przejrzyj kod synchronizacji klienta

Projekt programu Xamarin Client, który został pobrany po ukończeniu samouczka, [Tworzenie aplikacji platformy Xamarin dla systemu Android] już zawiera kod obsługujący synchronizację w trybie offline przy użyciu lokalnej bazy danych programu SQLite. Oto krótkie omówienie tego, co zostało już zawarte w kodzie samouczka. Aby zapoznać się z omówieniem koncepcyjnym tej funkcji, zobacz [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

* Przed wykonaniem jakiejkolwiek operacji na tabeli należy zainicjować magazyn lokalny. Baza danych lokalnego magazynu jest inicjowana, gdy `ToDoActivity.OnCreate()` wykonuje `ToDoActivity.InitLocalStoreAsync()`. Ta metoda tworzy lokalną bazę danych programu SQLite przy użyciu klasy `MobileServiceSQLiteStore` dostarczonej przez zestaw SDK klienta Mobile Apps platformy Azure.

    Metoda `DefineTable` tworzy tabelę w lokalnym magazynie, która pasuje do pól w danym typie, `ToDoItem` w tym przypadku. Typ nie musi zawierać wszystkich kolumn, które znajdują się w zdalnej bazie danych. Istnieje możliwość przechowywania tylko podzbioru kolumn.

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
* `toDoTable` składowej `ToDoActivity` jest typu `IMobileServiceSyncTable` zamiast `IMobileServiceTable`. IMobileServiceSyncTable kieruje wszystkie operacje tabeli Create, Read, Update i Delete (CRUD) do lokalnej bazy danych magazynu.

    Użytkownik decyduje o tym, kiedy zmiany są przekazywane do zaplecza aplikacji mobilnej Azure, wywołując `IMobileServiceSyncContext.PushAsync()`. Kontekst synchronizacji pomaga zachować relacje między tabelami przez śledzenie i wypychanie zmian we wszystkich tabelach, gdy aplikacja kliencka została zmodyfikowana po wywołaniu `PushAsync`.

    Podany kod wywołuje `ToDoActivity.SyncAsync()` do synchronizowania za każdym razem, gdy lista TodoItem jest odświeżana lub dodano lub zakończył TodoItem. Kod jest synchronizowany po każdej zmianie lokalnej.

    W podanym kodzie wszystkie rekordy w zdalnej tabeli `TodoItem` są badane, ale można również filtrować rekordy przez przekazanie identyfikatora zapytania i zapytania do `PushAsync`. Aby uzyskać więcej informacji, zobacz sekcję *Synchronizacja przyrostowa* w obszarze [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

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
* [Azure Mobile Apps .NET SDK porady][8]

<!-- URLs. -->
[Tworzenie aplikacji platformy Xamarin dla systemu Android]: ./app-service-mobile-xamarin-android-get-started.md
[Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]: ./app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Tworzenie aplikacji platformy Xamarin dla systemu Android]: app-service-mobile-xamarin-android-get-started.md
[Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: https://xamarin.com/download
[Xamarin extension]: https://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
