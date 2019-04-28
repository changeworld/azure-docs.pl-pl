---
title: Włączanie synchronizacji offline dla aplikacji mobilnej platformy Azure (xamarin) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać aplikacji usługi Mobile App do pamięci podręcznej i synchronizacji danych w trybie offline do aplikacji platformy Xamarin.Forms
documentationcenter: xamarin
author: conceptdev
manager: yochayk
editor: ''
services: app-service\mobile
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2016
ms.author: crdun
ms.openlocfilehash: 506c59ca24aeafbac59b1508bb78142051302765
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127883"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Włączanie synchronizacji w trybie offline dla aplikacji mobilnej platformy Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Omówienie
Ten samouczek zawiera funkcję synchronizacji w trybie offline, które usługi Azure Mobile Apps dla platformy Xamarin.Forms. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną — wyświetlanie, dodawanie lub modyfikowanie danych — nawet w przypadku braku połączenia sieciowego. Zmiany są przechowywane w lokalnej bazie danych. Gdy urządzenie jest w trybie online, zmiany te są synchronizowane z usługi zdalnej.

Ten samouczek opiera się na rozwiązanie Xamarin.Forms w przewodniku Szybki Start dla aplikacji mobilnych, utworzony po ukończeniu tego samouczka [tworzenie aplikacji platformy Xamarin.IOS]. Rozwiązania Xamarin.Forms w przewodniku Szybki Start zawiera kod w celu obsługi synchronizacji w trybie offline, który po prostu musi być włączona. W tym samouczku należy zaktualizować rozwiązanie szybkiego startu, aby włączyć funkcje trybu offline z usługą Azure Mobile Apps. Możemy też wyróżnić kodu specyficznego dla trybu offline w aplikacji. Jeśli nie używasz rozwiązania pobrany Szybki Start, należy dodać pakiety rozszerzeń dostępu do danych do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzeń serwera, zobacz [pracy z zestawem SDK serwera zaplecza platformy .NET dla usługi Azure Mobile Apps][1].

Aby dowiedzieć się więcej na temat funkcji synchronizacji w trybie offline, zobacz temat [synchronizacji danych w trybie Offline w usłudze Azure Mobile Apps][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Włączanie funkcji synchronizacji w trybie offline w rozwiązaniu Szybki Start
Kod synchronizacji w trybie offline jest dołączona do projektu przy użyciu C# dyrektywy preprocesora. Gdy **OFFLINE\_SYNCHRONIZACJI\_włączone** zdefiniowano symbol, tych ścieżek kodu jest uwzględniony w kompilacji. W przypadku aplikacji Windows należy również zainstalować platformy bazy danych SQLite.

1. W programie Visual Studio, kliknij prawym przyciskiem myszy rozwiązanie > **Zarządzaj pakietami NuGet dla rozwiązania...** , a następnie wyszukaj i zainstaluj **Microsoft.Azure.Mobile.Client.SQLiteStore** pakiet NuGet dla wszystkich projektów w rozwiązaniu.
2. W Eksploratorze rozwiązań Otwórz plik TodoItemManager.cs projektu z **przenośne** nazwę, która jest projekt przenośnej biblioteki klas, następnie usuń komentarz poniższej dyrektywy preprocesora:

        #define OFFLINE_SYNC_ENABLED
3. (Opcjonalnie) Do obsługi urządzeń Windows, należy zainstalować jedną z następujących pakietów dla środowiska uruchomieniowego bazy danych SQLite:

   * **Środowisko wykonawcze Windows 8.1:** Zainstaluj [SQLite dla Windows 8.1][3].
   * **Windows Phone 8.1:** Zainstaluj [SQLite dla Windows Phone 8.1][4].
   * **Platforma Universal Windows** zainstalować [SQLite for Universal Windows Universal][5].

     Mimo że tego przewodnika Szybki Start nie zawiera projektu Universal Windows, platforma Universal Windows jest obsługiwana przy użyciu narzędzia Xamarin Forms.
4. (Opcjonalnie) W każdym projekcie aplikacji Windows, kliknij prawym przyciskiem myszy **odwołania** > **Dodaj odwołanie...** , rozwiń węzeł **Windows** folder > **rozszerzenia**.
    Włącz odpowiednią **SQLite dla Windows** SDK wraz z **Visual C++ 2013 środowiska uruchomieniowego dla Windows** zestawu SDK.
    Nazwy zestawu SDK bazy danych SQLite różnią się nieco z każdej z platform Windows.

## <a name="review-the-client-sync-code"></a>Przejrzyj kod klienta synchronizacji
Poniżej przedstawiono krótkie omówienie co to jest już uwzględniony w samouczku kod wewnątrz `#if OFFLINE_SYNC_ENABLED` dyrektywy. Funkcja synchronizacji w trybie offline jest się w pliku projektu TodoItemManager.cs projektu biblioteki klas przenośnych. Aby uzyskać omówienie pojęć dotyczących tej funkcji, zobacz [w trybie Offline na synchronizowanie danych w usłudze Azure Mobile Apps][2].

* Można było wykonać żadnych operacji tabeli, musi zostać zainicjowany Magazyn lokalny. Magazyn lokalny jest inicjowana w **TodoItemManager** konstruktora klasy przy użyciu następującego kodu:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Ten kod tworzy nową lokalnej bazy danych SQLite bazy danych za pomocą **MobileServiceSQLiteStore** klasy.

    **DefineTable** metoda tworzy tabelę w magazynie lokalnym, odpowiadającą polom podanego typu.  Typ nie musi zawierać wszystkie kolumny, które znajdują się w zdalnej bazy danych. Istnieje możliwość przechowywania podzbiór kolumn.
* **TodoTable** pole **TodoItemManager** jest **IMobileServiceSyncTable** wpisz zamiast **IMobileServiceTable**. To klasa korzysta z lokalnej bazy danych w przypadku tworzenia, odczytu, aktualizacji i usuwania (CRUD) operacje tabeli. Zdecyduj, kiedy te zmiany są przekazywane do zaplecza aplikacji mobilnej przez wywołanie metody **PushAsync** na **IMobileServiceSyncContext**. Kontekst synchronizacji pomaga zachować relacje między tabelami, śledzenia i wypychaniu zmian można znaleźć we wszystkich tabelach aplikacji klienckiej został zmodyfikowany podczas **PushAsync** jest wywoływana.

    Następujące **SyncAsync** metoda jest wywoływana do synchronizacji z zapleczem aplikacji mobilnej:

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

    W tym przykładzie użyto prostych obsługi za pomocą synchronizacji domyślny program obsługi błędów. Rzeczywistej aplikacji będzie obsługiwać różne błędy, takich jak warunki w sieci i serwera konflikty za pomocą niestandardowego **IMobileServiceSyncHandler** implementacji.

## <a name="offline-sync-considerations"></a>Zagadnienia dotyczące synchronizacji w trybie offline
W tym przykładzie **SyncAsync** wywoływana jest metoda tylko rozruchu i po otrzymaniu żądania synchronizacji.  Aby zainicjować synchronizację w aplikacji systemu Android lub iOS, ściągnąć na liście elementów; Windows, można użyć **synchronizacji** przycisku. W przypadku aplikacji rzeczywistych można również wprowadzeniu wyzwalacza synchronizacji po zmianie stanu sieci.

Podczas ściągania jest wykonywane względem tabeli, która ma oczekujące lokalne aktualizacje śledzone przez kontekst, operacji ściągania automatycznie wyzwalaczy poprzedniego wypychania kontekstu. Podczas odświeżania, dodawania i kończenie elementów w tym przykładzie można pominąć jawną **PushAsync** wywołania.

Podany kod badane są wszystkie rekordy w tabeli TodoItem zdalnej, ale jest również możliwe do filtrowania rekordów, przekazując identyfikator zapytania i Wyślij zapytanie w celu **PushAsync**. Aby uzyskać więcej informacji, zobacz sekcję *synchronizacja przyrostowa* w [w trybie Offline na synchronizowanie danych w usłudze Azure Mobile Apps][2].

## <a name="run-the-client-app"></a>Uruchom aplikację kliencką
Synchronizacja w trybie offline teraz włączone Uruchom aplikację klienta co najmniej raz na każdej platformie, aby wypełnić bazę danych magazynu lokalnego. Później symulować scenariusz w trybie offline i modyfikować dane w magazynie lokalnym, gdy aplikacja działa w trybie offline.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Aktualizuj zachowanie synchronizacji aplikacji klienckiej
W tej sekcji należy zmodyfikować projekt klienta, aby symulować scenariusz w trybie offline przy użyciu nieprawidłowy adres URL aplikacji dla zaplecza. Alternatywnie można wyłączyć połączeń sieciowych, przenosząc urządzenia "Tryb samolotowy".  Podczas dodawania lub zmiany elementów danych, te zmiany są przechowywane w magazynie lokalnym, ale nie zostały zsynchronizowane z wewnętrznej bazy danych magazynu danych, dopóki nie zostanie ponownie nawiązane połączenie.

1. W Eksploratorze rozwiązań Otwórz plik projektu Constants.cs z **przenośne** projektu i zmień wartość właściwości `ApplicationURL` wskaż nieprawidłowy adres URL:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Otwórz plik TodoItemManager.cs z **przenośne** projektu, a następnie dodaj **catch** dla podstawy **wyjątek** klasy **try... catch**bloku **SyncAsync**. To **catch** bloku zapisuje komunikat o wyjątku do konsoli, w następujący sposób:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Skompiluj i uruchom aplikację kliencką.  Dodaj kilka nowych elementów. Należy zauważyć, że wyjątek jest rejestrowane w konsoli dla każdej próby synchronizacji z usługą zaplecza. Te nowe elementy istnieje tylko w magazynie lokalnym, dopóki nie może zostać przeniesiony do zaplecza aplikacji mobilnych. Aplikacja kliencka zachowuje się tak, jest ona dołączona do wewnętrznej bazy danych, obsługi utworzone przez wszystkie, Odczyt, aktualizacja, operacje usuwania (CRUD).
4. Zamknij aplikację i uruchom ponownie, aby sprawdzić, czy nowych elementów, który został utworzony są utrwalane w magazynie lokalnym.
5. (Opcjonalnie) Użyj programu Visual Studio, aby wyświetlić swoje tabeli Azure SQL Database, aby zobaczyć, że dane w wewnętrznej bazy danych nie została zmieniona.

    W programie Visual Studio, otwórz **Eksploratora serwera**. Przejdź do bazy danych w **Azure**->**baz danych SQL**. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Otwórz w Eksploratorze obiektów SQL Server**. Teraz możesz przejść do tabeli bazy danych SQL i jego zawartość.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Aktualizuj aplikację kliencką w celu ponownego nawiązania połączenia z zapleczem mobilnym
W tej sekcji ponownie nawiąż połączenie z aplikacji mobilnych zaplecza, co symuluje aplikacji powracające do trybu online. Podczas wykonywania gestu odświeżania danych jest synchronizowana z usługą w zapleczu mobilnym.

1. Ponownie otworzyć Constants.cs. Popraw `applicationURL` aby wskazywały poprawny adres URL.
2. Ponownie skompiluj i uruchom aplikację kliencką. Aplikacja próbuje synchronizować z zapleczem aplikacji mobilnej po uruchomieniu. Upewnij się, że żadne wyjątki są rejestrowane w konsoli debugowania.
3. (Opcjonalnie) Wyświetlanie zaktualizowanych danych za pomocą Eksplorator obiektów SQL Server lub narzędzia REST, takiego jak Fiddler lub [Postman][6]. Zwróć uwagę, że dane zostały synchronizowane między wewnętrznej bazy danych i magazynu lokalnego.

    Zwróć uwagę, dane zostały zsynchronizowane między bazy danych i magazynem lokalnym i zawiera elementy, które dodano odłączeniu Twojej aplikacji.

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Synchronizacja danych offline w usłudze Azure Mobile Apps][2]
* [Porada zestawu SDK .NET usługi Azure Mobile Apps][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: https://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: https://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: https://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: https://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
