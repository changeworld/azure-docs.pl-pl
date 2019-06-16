---
title: Włączanie synchronizacji offline dla aplikacji mobilnej platformy Azure (Xamarin Android)
description: Dowiedz się, jak używać aplikacji usługi Mobile App do pamięci podręcznej i synchronizacji danych w trybie offline do aplikacji platformy Xamarin Android
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 91d59e4b-abaa-41f4-80cf-ee7933b32568
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 7e951b9f2c2fda3c63f154b5b144addcbf65aacf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127968"
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Włączanie synchronizacji offline dla aplikacji mobilnej platformy Xamarin.Android

[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Omówienie

Ten samouczek zawiera funkcję synchronizacji w trybie offline, które usługi Azure Mobile Apps dla platformy Xamarin.Android. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną — wyświetlanie, dodawanie lub modyfikowanie danych — nawet w przypadku braku połączenia sieciowego. Zmiany są przechowywane w lokalnej bazie danych.
Gdy urządzenie jest w trybie online, zmiany te są synchronizowane z usługi zdalnej.

W tym samouczku aktualizujesz projekt klienta z tego samouczka [tworzenie aplikacji platformy Xamarin Android] do obsługi w trybie offline funkcji usługi Azure Mobile Apps. Jeśli nie używasz projektu serwera pobranego — szybki start, należy dodać pakiety rozszerzeń dostępu do danych do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzeń serwera, zobacz [pracy z zestawem SDK serwera zaplecza platformy .NET dla usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Aby dowiedzieć się więcej na temat funkcji synchronizacji w trybie offline, zobacz temat [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizuj aplikację kliencką do obsługi funkcji w trybie offline

Funkcje offline usług Azure Mobile App umożliwiają interakcję z lokalnej bazy danych, gdy znajdują się w ramach scenariusza w trybie offline. Korzystanie z tych funkcji w aplikacji należy zainicjować [SyncContext] do magazynu lokalnego. Następnie odwoływać się do tabeli za pomocą [IMobileServiceSyncTable](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable?view=azure-dotnet) interfejsu. Bazy danych SQLite jest używana jako magazynu lokalnego na urządzeniu.

1. W programie Visual Studio, otwórz Menedżera pakietów NuGet w projekcie, który należy wykonać w [tworzenie aplikacji platformy Xamarin Android] samouczka.  Wyszukiwanie i instalowanie **Microsoft.Azure.Mobile.Client.SQLiteStore** pakietu NuGet.
2. Otwórz plik o nazwie ToDoActivity.cs i usuń znaczniki komentarza `#define OFFLINE_SYNC_ENABLED` definicji.
3. W programie Visual Studio, naciśnij klawisz **F5** klawisz, aby ponownie skompilować i uruchomić aplikację kliencką. Aplikacja działa tak samo jak przed włączeniem synchronizacji w trybie offline. Jednak lokalnej bazy danych jest teraz wypełniana z danymi, które mogą być używane w ramach scenariusza w trybie offline.

## <a name="update-sync"></a>Zaktualizować aplikację tak, aby zakończyć połączenie z wewnętrznej bazy danych

W tej sekcji możesz przerwać połączenie do zaplecza aplikacji mobilnej do symulacji taka sytuacja jest w trybie offline. Po dodaniu elementów danych, obsłudze wyjątków informujący o tym, że aplikacja jest w trybie offline. W tym stanie nowe elementy dodane w magazynie lokalnym i są synchronizowane do zaplecza aplikacji mobilnej, gdy powiadomienie wypychane jest wykonywana w stanie połączonym.

1. Edytowanie projektu udostępnionego o nazwie ToDoActivity.cs. Zmiana **applicationURL** wskaż nieprawidłowy adres URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Można również pokazują działanie w trybie offline, wyłączając sieć Wi-Fi i sieci komórkowych na urządzeniu, lub za pomocą tryb samolotowy.
2. Naciśnij klawisz **F5** Aby skompilować i uruchomić aplikację. Zwróć uwagę, usługi synchronizacji nie powiodło się podczas odświeżania, gdy aplikacja jest uruchamiana.
3. Wprowadź nowe elementy i zwróć uwagę, że wypychania kończy się niepowodzeniem ze stanem [CancelledByNetworkError] każde kliknięcie **Zapisz**. Jednak nowe elementy zadań do wykonania istnieje w magazynie lokalnym do czasu może zostać przeniesiony do zaplecza aplikacji mobilnej.  W aplikacji produkcyjnej Jeśli pominąć te wyjątki aplikacji klienckiej zachowuje się tak, jakby nadal jest podłączony do zaplecza aplikacji mobilnej.
4. Zamknij aplikację i uruchom ponownie, aby sprawdzić, czy nowych elementów, który został utworzony są utrwalane w magazynie lokalnym.
5. (Opcjonalnie) W programie Visual Studio, otwórz **Eksploratora serwera**. Przejdź do bazy danych w **Azure**->**baz danych SQL**. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Otwórz w Eksploratorze obiektów SQL Server**. Teraz możesz przejść do tabeli bazy danych SQL i jego zawartość. Upewnij się, że dane w wewnętrznej bazy danych nie została zmieniona.
6. (Opcjonalnie) Za pomocą narzędzia REST narzędzia Fiddler lub Postman do wykonywania zapytań w zapleczu mobilnym za pomocą zapytania GET w formie `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Zaktualizować aplikację tak, aby ponownie połączyć zaplecze aplikacji mobilnej

W tej sekcji ponowne łączenie aplikacji z zapleczem aplikacji mobilnej. Przy pierwszym uruchomieniu aplikacji, `OnCreate` wywołań obsługi zdarzeń `OnRefreshItemsSelected`. Ta metoda wywołuje `SyncAsync` do synchronizacji magazynu lokalnego przy użyciu wewnętrznej bazy danych.

1. Otwórz o nazwie ToDoActivity.cs w projekcie udostępnionym i przywrócić zmiana **applicationURL** właściwości.
2. Naciśnij klawisz **F5** klawisz, aby ponownie skompilować i uruchomić aplikację. Aplikacja synchronizuje zmiany lokalne przy użyciu zaplecza aplikacji mobilnej platformy Azure przy użyciu operacji wypychania i ściągania podczas `OnRefreshItemsSelected` metoda jest wykonywana.
3. (Opcjonalnie) Wyświetl zaktualizowane dane za pomocą Eksplorator obiektów SQL Server lub narzędzia REST, takiego jak Fiddler. Ogłoszenie dane zostały zsynchronizowane między bazy danych zaplecza aplikacji mobilnej platformy Azure i Magazyn lokalny.
4. W aplikacji kliknij pole wyboru obok kilka elementów, aby zakończyć je w magazynie lokalnym.

   `CheckItem` wywołania `SyncAsync` do synchronizacji każdego elementu przy użyciu zaplecza aplikacji mobilnej. `SyncAsync` wywołuje operacje wypychania i ściągania. **Po każdym wykonaniu ściągnięcia względem tabeli, który klient wprowadził zmiany do, powiadomienie wypychane jest zawsze wykonywana automatycznie**. Gwarantuje to, że wszystkie tabele w magazynie lokalnym oraz relacje pozostają spójne. To zachowanie może spowodować nieoczekiwane wypychania. Aby uzyskać więcej informacji na temat tego zachowania, zobacz [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

## <a name="review-the-client-sync-code"></a>Przejrzyj kod klienta synchronizacji

Projektu klienta Xamarin, który został pobrany, po ukończeniu tego samouczka [tworzenie aplikacji platformy Xamarin Android] już zawiera kod obsługi synchronizacji w trybie offline przy użyciu lokalnej bazy danych SQLite. Poniżej przedstawiono krótkie omówienie co to jest już uwzględniony w kodu z samouczka dotyczącego. Aby uzyskać omówienie pojęć dotyczących tej funkcji, zobacz [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

* Można było wykonać żadnych operacji tabeli, musi zostać zainicjowany Magazyn lokalny. Magazyn lokalny jest inicjowana po `ToDoActivity.OnCreate()` wykonuje `ToDoActivity.InitLocalStoreAsync()`. Ta metoda tworzy lokalnej bazy danych SQLite, za pomocą `MobileServiceSQLiteStore` klasy udostępniane przez zestaw SDK klienta usługi Azure Mobile Apps.

    `DefineTable` Metoda tworzy tabelę w magazynie lokalnym, odpowiadającą polom w podany typ `ToDoItem` w tym przypadku. Typ nie musi zawierać wszystkie kolumny, które znajdują się w zdalnej bazy danych. Istnieje możliwość przechowywania tylko podzbiór kolumn.

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
* `toDoTable` Członkiem `ToDoActivity` jest `IMobileServiceSyncTable` wpisz zamiast `IMobileServiceTable`. IMobileServiceSyncTable kieruje wszystkie tworzenia, odczytu, aktualizacji i usuwania (CRUD) operacje tabeli w bazie danych magazynu lokalnego.

    Zdecyduj, kiedy wypychania zmian do zaplecza aplikacji mobilnej platformy Azure, przez wywołanie metody `IMobileServiceSyncContext.PushAsync()`. Kontekst synchronizacji pomaga zachować relacje między tabelami, śledzenia i wypychaniu zmian można znaleźć we wszystkich tabelach aplikacji klienckiej został zmodyfikowany podczas `PushAsync` jest wywoływana.

    Podany kod wywołuje `ToDoActivity.SyncAsync()` do synchronizacji przy każdym odświeżeniu listy todoitem lub dodano lub zakończona czynność do wykonania. Synchronizacje kodu po każdej zmianie lokalnego.

    Podany kod, wszystkie rekordy w zdalnym `TodoItem` zapytania dotyczącego tabeli, ale jest również możliwe do filtrowania rekordów, przekazując identyfikator zapytania i Wyślij zapytanie w celu `PushAsync`. Aby uzyskać więcej informacji, zobacz sekcję *synchronizacja przyrostowa* w [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

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
* [Porada zestawu SDK .NET usługi Azure Mobile Apps][8]

<!-- URLs. -->
[Tworzenie aplikacji platformy Xamarin Android]: ./app-service-mobile-xamarin-android-get-started.md
[Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]: ./app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Tworzenie aplikacji platformy Xamarin Android]: app-service-mobile-xamarin-android-get-started.md
[Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: https://xamarin.com/download
[Xamarin extension]: https://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
