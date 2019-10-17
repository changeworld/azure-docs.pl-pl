---
title: Włączanie synchronizacji w trybie offline dla aplikacji mobilnej platformy Azure (Xamarin. Forms) | Microsoft Docs
description: Dowiedz się, jak używać aplikacji mobilnej App Service do buforowania i synchronizowania danych w trybie offline w aplikacji platformy Xamarin. Forms
documentationcenter: xamarin
author: elamalani
manager: yochayk
editor: ''
services: app-service\mobile
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 65bd619a95b102c4ad4be57f1661a89fbae43e22
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388429"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Włączanie synchronizacji w trybie offline dla aplikacji mobilnej platformy Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Usługa Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi mające kluczowe znaczenie podczas tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług do **tworzenia**, **testowania** i **dystrybuowania** w celu konfigurowania potoku ciągłej integracji i ciągłego wdrażania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie aplikacji za pomocą usług do **analizy** i **diagnostyki**, a także współpracować z użytkownikami za pomocą usługi do **wypychania**. Deweloperzy mogą również korzystać z usługi **uwierzytelniania** do uwierzytelniania użytkowników oraz usługi **danych** do utrwalania i synchronizowania danych aplikacji w chmurze.
>
> Jeśli chcesz zintegrować usługi w chmurze w swojej aplikacji mobilnej, zarejestruj się w usłudze [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

## <a name="overview"></a>Przegląd
W tym samouczku przedstawiono funkcję synchronizacji w trybie offline w usłudze Azure Mobile Apps dla platformy Xamarin. Forms. Synchronizacja w trybie offline umożliwia użytkownikom końcowym korzystanie z aplikacji mobilnej — wyświetlanie, Dodawanie lub modyfikowanie danych — nawet w przypadku braku połączenia sieciowego. Zmiany są przechowywane w lokalnej bazie danych. Gdy urządzenie przewróci do trybu online, te zmiany są synchronizowane z usługą zdalną.

Ten samouczek jest oparty na rozwiązaniu Xamarin. Forms szybkiego startu dla Mobile Apps tworzonego po zakończeniu samouczka [Tworzenie aplikacji platformy Xamarin dla systemu iOS]. Rozwiązanie szybkiego startu dla platformy Xamarin. Forms zawiera kod obsługujący synchronizację w trybie offline, który należy tylko włączyć. W tym samouczku należy zaktualizować rozwiązanie szybkiego startu, aby włączyć funkcje w trybie offline platformy Azure Mobile Apps. W aplikacji zostanie również wyróżniony kod specyficzny dla trybu offline. Jeśli nie korzystasz z pobranego rozwiązania szybkiego startu, musisz dodać pakiety rozszerzenia dostępu do danych do projektu. Aby uzyskać więcej informacji o pakietach rozszerzeń serwera, zobacz [Work with the .NET zaplecz Server SDK for Azure Mobile Apps][1].

Aby dowiedzieć się więcej na temat funkcji synchronizacji w trybie offline, zobacz temat [Synchronizacja danych w trybie offline w usłudze Azure Mobile Apps][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Włączanie funkcji synchronizacji w trybie offline w rozwiązaniu szybkiego startu
Kod synchronizacji w trybie offline jest dołączany do projektu C# za pomocą dyrektyw preprocesora. Gdy jest zdefiniowany symbol **offline @ no__t-1SYNC @ no__t-2ENABLED** , te ścieżki kodu są zawarte w kompilacji. W przypadku aplikacji systemu Windows należy również zainstalować platformę SQLite.

1. W programie Visual Studio kliknij prawym przyciskiem myszy rozwiązanie > **Zarządzanie pakietami NuGet dla rozwiązania...** , a następnie wyszukaj i zainstaluj pakiet NuGet **Microsoft. Azure. Mobile. Client. SQLiteStore** dla wszystkich projektów w rozwiązaniu.
2. W Eksplorator rozwiązań otwórz plik TodoItemManager.cs z projektu z **przenośną** nazwą, która jest projektem biblioteki klas przenośnych, a następnie Usuń komentarz z następującej dyrektywy preprocesora:

        #define OFFLINE_SYNC_ENABLED
3. Obowiązkowe Aby zapewnić obsługę urządzeń z systemem Windows, zainstaluj jeden z następujących pakietów środowiska uruchomieniowego oprogramowania SQLite:

   * **Windows 8.1 środowiska uruchomieniowego:** Zainstaluj program [SQLite dla Windows 8.1][3].
   * **Windows Phone 8,1:** Zainstaluj program [SQLite dla Windows Phone 8,1][4].
   * **Platforma uniwersalna systemu Windows** Zainstaluj program [SQLite dla uniwersalnego uniwersalnego systemu Windows][5].

     Mimo że Przewodnik Szybki Start nie zawiera uniwersalnego projektu systemu Windows, platforma uniwersalna systemu Windows jest obsługiwana przy użyciu formularzy Xamarin.
4. Obowiązkowe W każdym projekcie aplikacji systemu Windows kliknij prawym przyciskiem myszy pozycję **odwołania** > **Dodaj odwołanie...** , rozwiń węzeł **rozszerzenia**> folderu **systemu Windows** .
    Włącz odpowiedni program **SQLite dla zestawu Windows** SDK oraz **środowisko uruchomieniowe Visual C++ 2013 dla zestawu Windows** SDK.
    Nazwy zestawów SDK oprogramowania SQLite różnią się nieco w zależności od platformy systemu Windows.

## <a name="review-the-client-sync-code"></a>Przejrzyj kod synchronizacji klienta
Poniżej przedstawiono krótkie omówienie tego, co jest już zawarte w kodzie samouczka wewnątrz dyrektyw `#if OFFLINE_SYNC_ENABLED`. Funkcja synchronizacji w trybie offline znajduje się w pliku projektu TodoItemManager.cs w projekcie biblioteki klas przenośnych. Aby zapoznać się z omówieniem koncepcyjnym tej funkcji, zobacz [Synchronizacja danych offline na platformie Azure Mobile Apps][2].

* Przed wykonaniem jakiejkolwiek operacji na tabeli należy zainicjować magazyn lokalny. Lokalna baza danych magazynu jest inicjowana w konstruktorze klasy **TodoItemManager** przy użyciu następującego kodu:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Ten kod tworzy nową lokalną bazę danych programu SQLite przy użyciu klasy **MobileServiceSQLiteStore** .

    Metoda **define** tworzy tabelę w lokalnym magazynie, która pasuje do pól w podanym typie.  Typ nie musi zawierać wszystkich kolumn, które znajdują się w zdalnej bazie danych. Istnieje możliwość przechowywania podzestawu kolumn.
* Pole do **zrobienia** w **TodoItemManager** jest typem **IMobileServiceSyncTable** zamiast **IMobileServiceTable**. Ta klasa używa lokalnej bazy danych dla wszystkich operacji tworzenia, odczytu, aktualizacji i usuwania (CRUD) tabeli. Użytkownik decyduje o tym, kiedy te zmiany są wypychane do zaplecza aplikacji mobilnej przez wywołanie **PushAsync** w **IMobileServiceSyncContext**. Kontekst synchronizacji pomaga zachować relacje między tabelami przez śledzenie i wypychanie zmian we wszystkich tabelach aplikacja kliencka została zmodyfikowana po wywołaniu **PushAsync** .

    Następująca metoda **SyncAsync** jest wywoływana w celu synchronizacji z zapleczem aplikacji mobilnej:

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

    Ten przykład używa prostej obsługi błędów z domyślnym programem obsługi synchronizacji. Rzeczywista aplikacja będzie obsługiwać różne błędy, takie jak warunki sieci i konflikty serwera przy użyciu niestandardowej implementacji **IMobileServiceSyncHandler** .

## <a name="offline-sync-considerations"></a>Zagadnienia dotyczące synchronizacji w trybie offline
W przykładzie metoda **SyncAsync** jest wywoływana tylko w momencie uruchamiania i gdy wymagana jest synchronizacja.  Aby zainicjować synchronizację w aplikacji dla systemu Android lub iOS, Pobierz listę elementów w dół. w przypadku systemu Windows użyj przycisku **Synchronizuj** . W aplikacji rzeczywistej można również utworzyć wyzwalacz synchronizacji po zmianie stanu sieci.

Gdy ściąganie jest wykonywane względem tabeli, która ma oczekujące aktualizacje lokalne śledzone przez kontekst, ta operacja ściągania automatycznie wyzwala poprzedni kontekst wypychania. Podczas odświeżania, dodawania i kończenia elementów w tym przykładzie można pominąć jawne wywołanie **PushAsync** .

W podanym kodzie wszystkie rekordy w zdalnej tabeli TodoItem są badane, ale można również filtrować rekordy, przekazując identyfikator zapytania i zapytanie do **PushAsync**. Aby uzyskać więcej informacji, zobacz sekcję *Synchronizacja przyrostowa* w obszarze [Synchronizacja danych w trybie offline w usłudze Azure Mobile Apps][2].

## <a name="run-the-client-app"></a>Uruchom aplikację kliencką
Po włączeniu synchronizacji w trybie offline należy uruchomić aplikację kliencką co najmniej raz na każdej platformie, aby wypełnić lokalną bazę danych magazynu. Później można symulować scenariusz w trybie offline i modyfikować dane w magazynie lokalnym, gdy aplikacja jest w trybie offline.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Aktualizowanie zachowania synchronizacji aplikacji klienckiej
W tej sekcji zmodyfikuj projekt klienta, aby symulować scenariusz w trybie offline przy użyciu nieprawidłowego adresu URL aplikacji dla zaplecza. Alternatywnie można wyłączyć połączenia sieciowe, przenosząc urządzenie do trybu samolotowego.  W przypadku dodawania lub zmieniania elementów danych te zmiany są przechowywane w magazynie lokalnym, ale nie są synchronizowane z magazynem danych zaplecza, dopóki połączenie nie zostanie ponownie nawiązane.

1. W Eksplorator rozwiązań otwórz plik projektu Constants.cs z **przenośnego** projektu i zmień wartość `ApplicationURL` w celu wskazywania nieprawidłowego adresu URL:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Otwórz plik TodoItemManager.cs z **przenośnego** projektu, a następnie Dodaj **przechwycenie** dla bazowej klasy **wyjątków** do instrukcji **try... Catch** Block in **SyncAsync**. Ten blok **catch** zapisuje komunikat o wyjątku w konsoli programu w następujący sposób:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Kompiluj i uruchom aplikację kliencką.  Dodaj nowe elementy. Należy zauważyć, że wyjątek jest rejestrowany w konsoli programu dla każdej próby synchronizacji z zapleczem. Te nowe elementy znajdują się tylko w magazynie lokalnym, dopóki nie będą mogły zostać wypchnięte do zaplecza mobilnego. Aplikacja kliencka działa tak, jakby była podłączona do zaplecza, obsługująca wszystkie operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD).
4. Zamknij aplikację i uruchom ją ponownie, aby sprawdzić, czy nowe utworzone elementy są utrwalane w magazynie lokalnym.
5. Obowiązkowe Użyj programu Visual Studio, aby wyświetlić tabelę Azure SQL Database, aby zobaczyć, że dane w bazie danych zaplecza nie uległy zmianie.

    W programie Visual Studio Otwórz **Eksplorator serwera**. Przejdź do bazy danych w **usłudze Azure**@no__t — 1**bazy danych SQL**. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie **Otwórz w Eksplorator obiektów SQL Server**. Teraz możesz przejść do tabeli bazy danych SQL i jej zawartości.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Aktualizowanie aplikacji klienckiej w celu ponownego nawiązania połączenia z zapleczem mobilnym
W tej sekcji ponownie podłącz aplikację do zaplecza mobilnego, która symuluje aplikację z powrotem do stanu online. Po wykonaniu gestu odświeżania dane są synchronizowane z zapleczem mobilnym.

1. Otwórz ponownie Constants.cs. Popraw wartość `applicationURL`, aby wskazywała prawidłowy adres URL.
2. Skompiluj ponownie i uruchom aplikację kliencką. Aplikacja próbuje przeprowadzić synchronizację z zapleczem aplikacji mobilnej po uruchomieniu. Sprawdź, czy w konsoli debugowania nie zarejestrowano żadnych wyjątków.
3. Obowiązkowe Wyświetl zaktualizowane dane przy użyciu Eksplorator obiektów SQL Server lub narzędzia REST, takiego jak programu Fiddler lub [Poster][6]. Zauważ, że dane zostały zsynchronizowane między bazą danych zaplecza a magazynem lokalnym.

    Zwróć uwagę, że dane zostały zsynchronizowane między bazą danych programu i magazynem lokalnym i zawierają elementy dodane w czasie, gdy aplikacja została odłączona.

## <a name="additional-resources"></a>Zasoby dodatkowe
* [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps][2]
* [Azure Mobile Apps .NET SDK porady][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: https://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: https://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: https://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: https://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
