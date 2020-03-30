---
title: Włącz synchronizację w trybie offline (Xamarin.Forms) | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać aplikacji mobilnej usługi App Service do buforowania i synchronizowania danych w trybie offline w aplikacji Xamarin.Forms.
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 57776073ef7e2760b308df22280faf1d65b8d104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458855"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Włączanie synchronizacji w trybie offline dla aplikacji mobilnej Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Omówienie
W tym samouczku przedstawiono funkcję synchronizacji w trybie offline usługi Azure Mobile Apps for Xamarin.Forms. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną — wyświetlanie, dodawanie lub modyfikowanie danych — nawet wtedy, gdy nie ma połączenia sieciowego. Zmiany są przechowywane w lokalnej bazie danych. Po powrocie urządzenia do trybu online zmiany te są synchronizowane z usługą zdalną.

Ten samouczek jest oparty na rozwiązaniu szybki start platformy Xamarin.Forms dla aplikacji mobilnych, które można utworzyć po zakończeniu samouczka [Tworzenie aplikacji xamarin systemu iOS]. Rozwiązanie szybki start dla platformy Xamarin.Forms zawiera kod do obsługi synchronizacji w trybie offline, który po prostu musi być włączony. W tym samouczku zaktualizuj rozwiązanie szybkiego startu, aby włączyć funkcje usługi Azure Mobile Apps w trybie offline. Wyróżnimy również kod specyficzny dla trybu offline w aplikacji. Jeśli nie używasz pobranego rozwiązania szybki start, należy dodać pakiety rozszerzenia dostępu do danych do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzeń serwera, zobacz [Praca z zestawem SDK serwera wewnętrznej bazy danych .NET dla aplikacji azure mobile apps][1].

Aby dowiedzieć się więcej o funkcji synchronizacji w trybie offline, zobacz temat [Synchronizacja danych w trybie offline w aplikacjach mobilnych platformy Azure][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Włączanie funkcji synchronizacji w trybie offline w rozwiązaniu szybki start
Kod synchronizacji w trybie offline jest uwzględniony w projekcie przy użyciu dyrektyw preprocesora języka C#. Po **\_zdefiniowaniu\_** symbolu OFFLINE SYNC ENABLED te ścieżki kodu są uwzględniane w kompilacji. W przypadku aplikacji systemu Windows należy również zainstalować platformę SQLite.

1. W programie Visual Studio kliknij prawym przyciskiem myszy rozwiązanie > **Zarządzaj pakietami NuGet dla rozwiązania...**, a następnie wyszukaj i zainstaluj pakiet NuGet **firmy Microsoft.Azure.Mobile.Client.SQLiteStore** dla wszystkich projektów w rozwiązaniu.
2. W Eksploratorze rozwiązań otwórz plik TodoItemManager.cs z projektu z **portable** w nazwie, który jest portable class library projektu, a następnie odkomentować następujące dyrektywy preprocesora:

        #define OFFLINE_SYNC_ENABLED
3. (Opcjonalnie) Aby obsługiwać urządzenia z systemem Windows, zainstaluj jeden z następujących pakietów środowiska wykonawczego SQLite:

   * **Środowisko wykonawcze systemu Windows 8.1:** Zainstaluj [SQLite dla Windows 8.1][3].
   * **Windows Phone 8.1:** Zainstaluj [SQLite dla Windows Phone 8.1][4].
   * **Uniwersalna platforma systemu Windows** Zainstaluj [SQLite dla universalu systemu Windows Universal][5].

     Mimo że szybki start nie zawiera projektu systemu Uniwersalnego systemu Windows, platforma uniwersalnego systemu Windows jest obsługiwana za pomocą formularzy platformy Xamarin.
4. (Opcjonalnie) W każdym projekcie aplikacji systemu Windows kliknij prawym przyciskiem myszy **pozycję Odwołania** > **Dodaj odwołanie...**, rozwiń folder **systemu Windows** > **rozszerzenia**.
    Włącz odpowiedni **plik SQLite dla systemu Windows** SDK wraz ze **środowiskiem wizualnym C++ 2013 Runtime dla systemu Windows** SDK.
    Nazwy SQLite SDK różnią się nieznacznie w zależności od platformy systemu Windows.

## <a name="review-the-client-sync-code"></a>Przejrzyj kod synchronizacji klienta
Oto krótki przegląd tego, co jest już zawarte `#if OFFLINE_SYNC_ENABLED` w kodzie samouczka wewnątrz dyrektyw. Funkcja synchronizacji w trybie offline znajduje się w pliku projektu TodoItemManager.cs w projekcie Biblioteka klas przenośnych. Aby zapoznać się z koncepcyjnym omówieniem tej funkcji, zobacz [Synchronizacja danych w trybie offline w aplikacjach mobilnych platformy Azure][2].

* Przed wykonaniem operacji tabeli należy zainicjować magazyn lokalny. Baza danych magazynu lokalnego jest inicjowana w konstruktorze klasy **TodoItemManager** przy użyciu następującego kodu:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Ten kod tworzy nową lokalną bazę danych SQLite przy użyciu **mobileservicesqlitestore** klasy.

    **DefineTable** Metoda tworzy tabelę w magazynie lokalnym, który pasuje do pól w podanym typie.  Typ nie musi zawierać wszystkich kolumn, które znajdują się w zdalnej bazie danych. Istnieje możliwość przechowywania podzbioru kolumn.
* Pole **todoTable** w **TodoItemManager** jest typem **IMobileServiceSyncTable** zamiast **IMobileServiceTable**. Ta klasa używa lokalnej bazy danych dla wszystkich operacji tworzenia, odczytu, aktualizacji i usuwania (CRUD). Użytkownik decyduje, kiedy te zmiany zostaną wypchnięte do zaplecza aplikacji mobilnej, wywołując **pushasync** na **IMobileServiceSyncContext**. Kontekst synchronizacji pomaga zachować relacje tabeli przez śledzenie i wypychanie zmian we wszystkich tabelach aplikacja kliencka została zmodyfikowana, gdy **pushasync** jest wywoływana.

    Następująca metoda **SyncAsync** jest wywoływana do synchronizacji z zapleczem aplikacji mobilnej:

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    W tym przykładzie użyto prostej obsługi błędów z domyślnym programem obsługi synchronizacji. Rzeczywista aplikacja będzie obsługiwać różne błędy, takie jak warunki sieciowe i konflikty serwera przy użyciu niestandardowej implementacji **IMobileServiceSyncHandler.**

## <a name="offline-sync-considerations"></a>Zagadnienia dotyczące synchronizacji w trybie offline
W przykładzie **SyncAsync** metoda jest wywoływana tylko podczas uruchamiania i gdy wymagane jest synchronizacja.  Aby zainicjować synchronizację w aplikacji na Androida lub iOS, pociągnij w dół na liście elementów; w systemie Windows użyj przycisku **Synchronizuj.** W rzeczywistych aplikacjach można również wprowadzić wyzwalacz synchronizacji po zmianie stanu sieci.

Gdy ściąganie jest wykonywane względem tabeli, która oczekuje na aktualizacje lokalne śledzone przez kontekst, ta operacja ściągania automatycznie wyzwala poprzedzającego wypychania kontekstu. Podczas odświeżania, dodawania i uzupełniania elementów w tym przykładzie, można pominąć jawne **wywołanie PushAsync.**

W podanym kodzie wszystkie rekordy w zdalnej tabeli TodoItem są wyszukiwane, ale możliwe jest również filtrowanie rekordów, przekazując identyfikator zapytania i kwerendę do **Pliku PushAsync**. Aby uzyskać więcej informacji, zobacz sekcję *Synchronizacja przyrostowa* w synchronizacji danych w [trybie offline w aplikacjach mobilnych platformy Azure][2].

## <a name="run-the-client-app"></a>Uruchamianie aplikacji klienckiej
Po włączeniu synchronizacji w trybie offline uruchom aplikację kliencką co najmniej raz na każdej platformie, aby wypełnić lokalną bazę danych magazynu. Później symuluj scenariusz w trybie offline i zmodyfikuj dane w magazynie lokalnym, gdy aplikacja jest w trybie offline.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Aktualizowanie zachowania synchronizacji aplikacji klienckiej
W tej sekcji zmodyfikuj projekt klienta, aby symulować scenariusz w trybie offline przy użyciu nieprawidłowego adresu URL aplikacji dla wewnętrznej bazy danych. Alternatywnie można wyłączyć połączenia sieciowe, przenosząc urządzenie do trybu samolotowego.  Po dodaniu lub zmianie elementów danych te zmiany są przechowywane w magazynie lokalnym, ale nie są synchronizowane z magazynem danych wewnętrznej bazy danych, dopóki połączenie nie zostanie ponownie nawiązane.

1. W Eksploratorze rozwiązań otwórz plik projektu Constants.cs z projektu `ApplicationURL` **przenośnego** i zmień wartość wskażenia na nieprawidłowy adres URL:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Otwórz plik TodoItemManager.cs z projektu **Przenośny,** a następnie dodaj **catch** dla podstawowej klasy **Wyjątk do** **try... złapać** blok w **SyncAsync**. Ten blok **catch** zapisuje komunikat o wyjątku do konsoli, w następujący sposób:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Tworzenie i uruchamianie aplikacji klienckiej.  Dodaj kilka nowych elementów. Należy zauważyć, że wyjątek jest rejestrowany w konsoli dla każdej próby synchronizacji z wewnętrznej bazy danych. Te nowe elementy istnieją tylko w lokalnym magazynie, dopóki nie mogą być wypychane do zaplecza mobilnego. Aplikacja kliencka zachowuje się tak, jakby była połączona z zapleczem, obsługując wszystkie operacje tworzenia, odczytu, aktualizacji, usuwania (CRUD).
4. Zamknij aplikację i uruchom ją ponownie, aby sprawdzić, czy nowe utworzone elementy są utrwalone w magazynie lokalnym.
5. (Opcjonalnie) Użyj programu Visual Studio, aby wyświetlić tabelę bazy danych SQL azure, aby zobaczyć, że dane w bazie danych wewnętrznej bazy danych nie uległy zmianie.

    W programie Visual Studio otwórz **Eksploratora serwera**. Przejdź do bazy danych w **usłudze Azure**->**SQL Databases**. Kliknij prawym przyciskiem myszy bazę danych i wybierz polecenie **Otwórz w Eksploratorze obiektów programu SQL Server**. Teraz możesz przejść do tabeli bazy danych SQL i jej zawartości.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Aktualizowanie aplikacji klienckiej w celu ponownego połączenia zaplecza sieci komórkowej
W tej sekcji ponownie połącz aplikację z zapleczem mobilnym, który symuluje powrót aplikacji do stanu online. Podczas wykonywania gestu odświeżania dane są synchronizowane z zapleczem telefonu komórkowego.

1. Otwórz ponownie Constants.cs. Popraw, `applicationURL` aby wskazać poprawny adres URL.
2. Odbuduj i uruchom aplikację kliencką. Aplikacja próbuje zsynchronizować z zapleczem aplikacji mobilnej po uruchomieniu. Sprawdź, czy żadne wyjątki nie są rejestrowane w konsoli debugowania.
3. (Opcjonalnie) Wyświetlanie zaktualizowanych danych przy użyciu Eksploratora obiektów programu SQL Server lub narzędzia REST, takiego jak Fiddler lub [Listonosz.][6] Zwróć uwagę, że dane zostały zsynchronizowane między bazą danych wewnętrznej bazy danych a magazynem lokalnym.

    Zwróć uwagę, że dane zostały zsynchronizowane między bazą danych a magazynem lokalnym i zawiera elementy dodane podczas rozłączenia aplikacji.

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps][2]
* [Azure Mobile Apps .NET SDK HOWTO][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: https://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: https://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: https://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: https://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
