---
title: Włączanie synchronizacji w trybie offline dla aplikacji mobilnej Azure (Xamarin Android)
description: Dowiedz się, jak używać aplikacji usługi Mobile App do pamięci podręcznej i synchronizacji danych w trybie offline w aplikacji platformy Xamarin Android
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
ms.openlocfilehash: 5c6ff5ac909e2dc6918f85d39beb781952ee6dd0
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2018
ms.locfileid: "27593100"
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Włączanie synchronizacji w trybie offline dla aplikacji mobilnej platformy Xamarin.Android
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Przegląd
Ten samouczek przedstawia funkcję synchronizacji w trybie offline w usłudze Azure Mobile Apps dla platformy Xamarin.Android. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacji mobilnej — przeglądanie, dodawanie lub modyfikowanie danych — nawet wtedy, gdy istnieje połączenie sieciowe. Zmiany są przechowywane w lokalnej bazie danych.
Gdy urządzenie jest w trybie online, te zmiany są synchronizowane z usługi zdalnej.

W tym samouczku, aktualizacja projektu klienta z samouczka [tworzenie aplikacji dla systemu Xamarin Android] do obsługi funkcji w trybie offline z usługą Azure Mobile Apps. Jeśli nie używasz szybki start pobrany Projekt serwera, należy dodać pakietów rozszerzenia dostępu do danych do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzenia serwera, zobacz [pracować z serwera wewnętrznej bazy danych .NET SDK usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Aby dowiedzieć się więcej na temat funkcji synchronizacji w trybie offline, zobacz temat [synchronizacji danych w trybie Offline w usłudze Azure Mobile Apps].

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizowanie aplikacji klienta do obsługi funkcji w trybie offline
Funkcje platformy Azure w trybie offline aplikacji mobilnej pozwala na interakcję z lokalnej bazy danych podczas pracy w trybie offline scenariuszu. Aby korzystać z tych funkcji w aplikacji, należy zainicjować [SyncContext] do lokalnego magazynu. Następnie odwoływać się do tabeli za pomocą interfejsu [IMobileServiceSyncTable] [IMobileServiceSyncTable]. SQLite jest używany w magazynie lokalnym urządzenia.

1. W programie Visual Studio, otwórz Menedżera pakietów NuGet w projekcie, który zakończył w [tworzenie aplikacji dla systemu Xamarin Android] samouczka.  Wyszukiwanie i instalowanie **Microsoft.Azure.Mobile.Client.SQLiteStore** pakietu NuGet.
2. Otwórz plik o nazwie ToDoActivity.cs i Usuń komentarz `#define OFFLINE_SYNC_ENABLED` definicji.
3. W programie Visual Studio, naciśnij klawisz **F5** klawisz, aby ponownie skompilować i uruchomić aplikację klienta. Aplikacja działa tak samo jak przed włączeniem synchronizacji w trybie offline. Jednak lokalnej bazy danych jest teraz wypełniona dane, które mogą być używane w scenariuszu w trybie offline.

## <a name="update-sync"></a>Aktualizowanie aplikacji, aby zakończyć połączenie z wewnętrznej bazy danych
W tej sekcji możesz przerwać połączenie do zaplecza aplikacji mobilnej do symulowania sytuacji w trybie offline. Po dodaniu elementów danych z programu obsługi wyjątków informuje, że dana aplikacja jest w trybie offline. W tym stanie nowe elementy dodane w lokalnym magazynie i są synchronizowane do zaplecza aplikacji mobilnej, gdy wypychania jest wykonywana jest niepodłączony.

1. Edytuj o nazwie ToDoActivity.cs w projekcie udostępnionym. Zmień **applicationURL** wskaż nieprawidłowy adres URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Zachowanie w trybie offline można także pokazują, wyłączając sieci Wi-Fi i sieci komórkowych na urządzeniu lub w trybie samolotowy.
2. Naciśnij klawisz **F5** Aby skompilować i uruchomić aplikację. Zwróć uwagę, synchronizacji nie powiodło się podczas odświeżania, gdy aplikacja jest uruchamiana.
3. Wprowadź nowe elementy i zwróć uwagę, że wypychania nie powiedzie się ze stanem [CancelledByNetworkError] każdym kliknięciu **zapisać**. Jednak nowych elementów todo istnieje w lokalnym magazynie dopóki nie może zostać przeniesiony do zaplecza aplikacji mobilnej.  W aplikacji produkcyjnej Jeśli pominąć te wyjątki aplikacji klienckiej zachowuje się tak, jakby nadal jest podłączony do zaplecza aplikacji mobilnej.
4. Zamknij aplikację i uruchom ponownie, aby sprawdzić, czy nowe elementy utworzone zostały utrwalone w magazynie lokalnym.
5. (Opcjonalnie) W programie Visual Studio Otwórz **Eksploratora serwera**. Przejdź do bazy danych w **Azure**->**baz danych SQL**. Kliknij prawym przyciskiem myszy bazę danych i wybierz **Otwórz w Eksploratorze obiektów SQL Server**. Możesz teraz przejść do tabeli bazy danych SQL i jego zawartość. Sprawdź, czy dane w bazie danych zaplecza nie uległy zmianie.
6. (Opcjonalnie) Zapytania z zaplecze aplikacji mobilnej, za pomocą zapytania GET w formularzu za pomocą narzędzia REST, takie jak Fiddler lub Postman `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Aktualizowanie aplikacji ponownie połączyć zaplecza aplikacji mobilnej
W tej sekcji ponowne łączenie aplikacji z zapleczem aplikacji mobilnej. Przy pierwszym uruchomieniu aplikacji, `OnCreate` wywołań obsługi zdarzeń `OnRefreshItemsSelected`. Ta metoda wywołuje `SyncAsync` do synchronizacji z bazy danych zaplecza magazynu lokalnego.

1. Otwórz o nazwie ToDoActivity.cs w projekcie udostępnionym, a następnie przywrócić zmiana **applicationURL** właściwości.
2. Naciśnij klawisz **F5** klawisz, aby ponownie skompilować i uruchomić aplikację. Aplikacja przeprowadza synchronizację zmian z zaplecza aplikacji mobilnej Azure przy użyciu operacji wypychania i ściągania podczas `OnRefreshItemsSelected` metoda jest wykonywana.
3. (Opcjonalnie) Wyświetlanie zaktualizowanych danych przy użyciu Eksplorator obiektów SQL Server lub narzędzia REST, takiego jak Fiddler. Powiadomienie danych został zsynchronizowany między bazą danych zaplecza aplikacji mobilnej Azure i lokalnego magazynu.
4. W aplikacji kliknij pole wyboru obok kilka elementów, aby zakończyć je w lokalnym magazynie.

   `CheckItem`wywołania `SyncAsync` do elementu synchronizacja każdego z zaplecza aplikacji mobilnej. `SyncAsync`wywołuje wypychania i ściągania. **Przy każdym wykonaniu ściągania przed tabelę, w której klient wprowadził zmiany do wypychania jest zawsze wykonywana automatycznie**. Dzięki temu wszystkie tabele w lokalnym magazynie wraz z relacji zachować spójność. To zachowanie może spowodować nieoczekiwane wypychania. Aby uzyskać więcej informacji dotyczących tego zachowania, zobacz [synchronizacji danych w trybie Offline w usłudze Azure Mobile Apps].

## <a name="review-the-client-sync-code"></a>Przegląd kodu klienta synchronizacji
Pobrany po ukończeniu samouczka projekt klienta Xamarin [tworzenie aplikacji dla systemu Xamarin Android] już zawiera kod obsługujący synchronizacji w trybie offline przy użyciu lokalnej bazy danych SQLite. Oto krótkie omówienie co to jest już uwzględniony w kodzie samouczka. Omówienie koncepcji funkcji, zobacz [synchronizacji danych w trybie Offline w usłudze Azure Mobile Apps].

* Aby można było wykonać wszystkie operacje tabeli, muszą zostać zainicjowane magazynu lokalnego. Magazyn lokalny jest inicjowana po `ToDoActivity.OnCreate()` wykonuje `ToDoActivity.InitLocalStoreAsync()`. Ta metoda tworzy lokalnej bazy danych SQLite, przy użyciu `MobileServiceSQLiteStore` klasy dostarczonych przez klienta usługi Azure Mobile Apps SDK.

    `DefineTable` Metoda tworzy tabelę w lokalnym magazynie odpowiadającego pola udostępnionego typu `ToDoItem` w takim przypadku. Typ nie ma zawierać wszystkie kolumny, które znajdują się w zdalnej bazy danych. Istnieje możliwość przechowywania tylko ich podzbiór kolumn.

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
            // For more details, see http://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }
* `toDoTable` Członkiem `ToDoActivity` jest `IMobileServiceSyncTable` wpisz zamiast `IMobileServiceTable`. IMobileServiceSyncTable kieruje wszystkie tworzenia, odczytu, aktualizacji i usuwania (CRUD) operacje tabeli w bazie danych magazynu lokalnego.

    Zdecyduj, kiedy zmiany są przenoszone do zaplecza aplikacji mobilnej Azure przez wywołanie metody `IMobileServiceSyncContext.PushAsync()`. Kontekst synchronizacji pomaga zachować relacje między tabelami, śledzenia i wypychanie zmiany we wszystkich tabelach aplikacji klienckiej został zmodyfikowany podczas obliczania `PushAsync` jest wywoływana.

    Podany kod wywołuje `ToDoActivity.SyncAsync()` do synchronizacji przy każdym odświeżeniu listy todoitem lub dodano lub wykonać zadania do wykonania. Synchronizacje kodu, po każdej zmianie lokalnego.

    Podany kod wszystkie rekordy w obiekcie zdalnym `TodoItem` badane są tabele tabeli, ale jest również możliwe filtrowanie rekordów przez przekazanie identyfikator zapytania i kwerendę `PushAsync`. Aby uzyskać więcej informacji, zobacz sekcję *synchronizacja przyrostowa* w [synchronizacji danych w trybie Offline w usłudze Azure Mobile Apps].

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
* [synchronizacji danych w trybie Offline w usłudze Azure Mobile Apps]
* [Porada zestawu SDK .NET usługi Azure Mobile Apps][8]

<!-- URLs. -->
[tworzenie aplikacji dla systemu Xamarin Android]: ../app-service-mobile-xamarin-android-get-started.md
[synchronizacji danych w trybie Offline w usłudze Azure Mobile Apps]: ../app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Tworzenie aplikacji dla systemu Xamarin Android]: app-service-mobile-xamarin-android-get-started.md
[Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: http://xamarin.com/download
[Xamarin extension]: http://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
