---
title: Włączanie synchronizacji w trybie offline dla aplikacji mobilnej Azure (platformy Xamarin.Forms) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać aplikacji usługi Mobile App do pamięci podręcznej i synchronizacji danych w trybie offline w aplikacji platformy Xamarin.Forms
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
ms.openlocfilehash: f88e6a4037bcca54982359742cdc6021f020882d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2018
ms.locfileid: "27594721"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Włączanie synchronizacji w trybie offline dla aplikacji mobilnej platformy Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Przegląd
Ten samouczek przedstawia funkcję synchronizacji w trybie offline w usłudze Azure Mobile Apps dla platformy Xamarin.Forms. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacji mobilnej — przeglądanie, dodawanie lub modyfikowanie danych — nawet wtedy, gdy istnieje połączenie sieciowe. Zmiany są przechowywane w lokalnej bazie danych. Gdy urządzenie jest w trybie online, te zmiany są synchronizowane z usługi zdalnej.

W tym samouczku opiera się na rozwiązanie szybkiego startu platformy Xamarin.Forms Mobile Apps, utworzony po ukończeniu samouczka [tworzenie aplikacji platformy Xamarin.IOS]. Rozwiązanie szybkiego startu dla platformy Xamarin.Forms zawiera kod służący do obsługi synchronizacji w trybie offline, po prostu musi być włączona. W tym samouczku należy zaktualizować rozwiązania Szybki Start o włączenie funkcji w trybie offline z usługą Azure Mobile Apps. Możemy również zaznacz kodu w trybie offline określonych w aplikacji. Jeśli nie używasz szybkiego startu pobranego rozwiązania, należy dodać pakietów rozszerzenia dostępu do danych do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzenia serwera, zobacz [pracować z serwera wewnętrznej bazy danych .NET SDK usługi Azure Mobile Apps][1].

Aby dowiedzieć się więcej na temat funkcji synchronizacji w trybie offline, zobacz temat [synchronizacji danych w trybie Offline w usłudze Azure Mobile Apps][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Włączanie funkcji synchronizacji w trybie offline w rozwiązaniu Szybki Start
Kod synchronizacji w trybie offline jest dołączony do projektu przy użyciu dyrektywy preprocesora C#. Gdy **OFFLINE\_SYNCHRONIZACJI\_włączone** symbol jest zdefiniowany, tych ścieżek kodu zostaną uwzględnione w kompilacji. Dla aplikacji systemu Windows musisz zainstalować platformę SQLite.

1. W programie Visual Studio, kliknij prawym przyciskiem myszy rozwiązanie > **Zarządzaj pakietami NuGet rozwiązania...** , następnie wyszukaj i zainstaluj **Microsoft.Azure.Mobile.Client.SQLiteStore** pakietu NuGet dla wszystkich projektów w rozwiązaniu.
2. W Eksploratorze rozwiązań Otwórz plik TodoItemManager.cs z projektu z **przenośnych** w nazwie, czyli projektu biblioteki przenośnej klasy, następnie usuń komentarz poniższej dyrektywy preprocesora:

        #define OFFLINE_SYNC_ENABLED
3. (Opcjonalnie) Do obsługi urządzeń z systemem Windows, należy zainstalować jedną z następujących pakietów środowiska uruchomieniowego SQLite:

   * **Środowisko uruchomieniowe Windows 8.1:** zainstalować [SQLite dla Windows 8.1][3].
   * **Windows Phone 8.1:** zainstalować [SQLite dla Windows Phone 8.1][4].
   * **Platforma uniwersalna systemu Windows** zainstalować [SQLite dla uniwersalnych systemu Windows Universal][5].

     Mimo że szybkiego startu zawiera projektów uniwersalnych systemu Windows, platforma uniwersalna systemu Windows jest obsługiwana w programie Xamarin Forms.
4. (Opcjonalnie) W każdym projekcie aplikacji systemu Windows, kliknij prawym przyciskiem myszy **odwołania** > **Dodawanie odwołania...** , rozwiń węzeł **Windows** folder > **rozszerzenia**.
    Włącz odpowiednie **SQLite dla systemu Windows** SDK wraz z **Visual C++ 2013 środowiska wykonawczego systemu Windows** zestawu SDK.
    Nazwy zestawu SDK SQLite się nieco różnić z każdej z platform Windows.

## <a name="review-the-client-sync-code"></a>Przegląd kodu klienta synchronizacji
Oto krótkie omówienie co to jest już uwzględniony w samouczku kodu wewnątrz `#if OFFLINE_SYNC_ENABLED` dyrektywy. Funkcja synchronizacji w trybie offline jest w pliku projektu TodoItemManager.cs projektu biblioteki klas przenośnych. Omówienie koncepcji funkcji, zobacz [w trybie Offline synchronizacji danych w usłudze Azure Mobile Apps][2].

* Aby można było wykonać wszystkie operacje tabeli, muszą zostać zainicjowane magazynu lokalnego. Magazyn lokalny jest inicjowana w **TodoItemManager** konstruktora klasy przy użyciu następującego kodu:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Ten kod tworzy nowego lokalnego SQLite bazy danych przy użyciu **MobileServiceSQLiteStore** klasy.

    **DefineTable** metoda tworzy tabelę w lokalnym magazynie odpowiadającego pola podanego typu.  Typ nie ma zawierać wszystkie kolumny, które znajdują się w zdalnej bazy danych. Istnieje możliwość przechowywania podzbiór kolumn.
* **TodoTable** w **TodoItemManager** jest **IMobileServiceSyncTable** wpisz zamiast **IMobileServiceTable**. To klasa korzysta z lokalnej bazy danych dla wszystkich tworzenia, odczytu, aktualizacji i usuwania (CRUD) operacje tabeli. Zdecyduj, kiedy te zmiany są przenoszone do zaplecza aplikacji mobilnej, wywołując **PushAsync** na **IMobileServiceSyncContext**. Kontekst synchronizacji pomaga zachować relacje między tabelami, śledzenia i wypychanie zmiany we wszystkich tabelach aplikacji klienckiej został zmodyfikowany podczas obliczania **PushAsync** jest wywoływana.

    Następujące **SyncAsync** metoda jest wywoływana w celu synchronizacji z zaplecza aplikacji mobilnej:

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

    W przykładzie użyto prostych obsługi z synchronizacji domyślny program obsługi błędów. Rzeczywistej aplikacji będzie obsługiwać różne błędy, takich jak warunki sieciowe i konflikty serwera przy użyciu niestandardowego **IMobileServiceSyncHandler** implementacji.

## <a name="offline-sync-considerations"></a>Zagadnienia dotyczące synchronizacji w trybie offline
W przykładzie **SyncAsync** wywoływana jest metoda tylko rozruchu i kiedy zażądano synchronizacji.  Aby zainicjować synchronizację w aplikacji systemu Android i iOS, rozwiń na liście elementów; w systemie Windows, należy użyć **synchronizacji** przycisku. W przypadku aplikacji rzeczywistych można również upewnij wyzwalacza synchronizacji po zmianie stanu sieci.

Podczas wykonywania ściąganie względem tabeli, która ma oczekujące aktualizacje lokalnego śledzone przez kontekstu, który automatycznie pobierać operacji wyzwalaczy poprzedniego wypychania kontekstu. Podczas odświeżania, dodawanie i wykonując elementów w tym przykładzie można pominąć jawnych **PushAsync** wywołania.

Podany kod odpytywane będą wszystkie rekordy w tabeli TodoItem zdalnego, ale jest również możliwe filtrowanie rekordów przez przekazanie identyfikator zapytania i kwerendę **PushAsync**. Aby uzyskać więcej informacji, zobacz sekcję *synchronizacja przyrostowa* w [w trybie Offline synchronizacji danych w usłudze Azure Mobile Apps][2].

## <a name="run-the-client-app"></a>Uruchamianie aplikacji klienta
Teraz włączony synchronizacja w trybie offline uruchamianie aplikacji klienckiej co najmniej raz na każdej z platform do wypełniania bazy danych magazynu lokalnego. Później symulować scenariusz w trybie offline i modyfikować danych w magazynie lokalnym, gdy aplikacja jest w trybie offline.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Aktualizacja zachowanie synchronizacji aplikacji klienta
W tej sekcji modyfikacja projektu klienta, aby symulować scenariusz w trybie offline za pomocą nieprawidłowy adres URL aplikacji w danym zapleczu. Alternatywnie można wyłączyć połączeń sieciowych przez przeniesienie urządzenia do "Tryb samolotowy".  Podczas dodawania lub zmienić elementów danych, te zmiany są przechowywane w lokalnym magazynie, ale nie zostały zsynchronizowane z wewnętrznej bazy danych magazynu danych, dopóki nie zostanie ponownie nawiązać połączenie.

1. W Eksploratorze rozwiązań Otwórz plik projektu Constants.cs **przenośne** projektu i zmień wartość `ApplicationURL` wskaż nieprawidłowy adres URL:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Otwórz plik TodoItemManager.cs z **przenośne** projektu, a następnie dodaj **catch** dla podstawy **wyjątek** klasy do **try... catch**blok w **SyncAsync**. To **catch** bloku zapisuje komunikat o wyjątku do konsoli, w następujący sposób:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Skompiluj i uruchom aplikację klienta.  Dodać niektóre nowe elementy. Zwróć uwagę, że wyjątek jest rejestrowane w konsoli dla każdej próby synchronizacji z wewnętrznej bazy danych. Te nowe elementy istnieje tylko w lokalnym magazynie, dopóki nie może zostać przeniesiony do zaplecza aplikacji mobilnych. Aplikacja kliencka zachowuje się tak, jakby nie jest dołączona do wewnętrznej bazy danych, obsługa wszystkich tworzyć, odczytywać, update, operacji usuwania (CRUD).
4. Zamknij aplikację i uruchom ponownie, aby sprawdzić, czy nowe elementy utworzone zostały utrwalone w magazynie lokalnym.
5. (Opcjonalnie) Aby wyświetlić tabelę bazy danych SQL Azure, aby zobaczyć, czy dane w bazie danych zaplecza nie uległy zmianie, należy użyć programu Visual Studio.

    W programie Visual Studio Otwórz **Eksploratora serwera**. Przejdź do bazy danych w **Azure**->**baz danych SQL**. Kliknij prawym przyciskiem myszy bazę danych i wybierz **Otwórz w Eksploratorze obiektów SQL Server**. Możesz teraz przejść do tabeli bazy danych SQL i jego zawartość.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Aktualizuj aplikację klienta, aby ponownie połączyć się z zaplecza aplikacji mobilnych
W tej sekcji ponowne łączenie aplikacji zaplecza mobilnego, która symuluje aplikacji powracające do trybu online. Podczas wykonywania gestu odświeżania danych jest synchronizowany z przenośnymi wewnętrzną bazą danych.

1. Otwórz ponownie Constants.cs. Popraw `applicationURL` aby wskazywały poprawny adres URL.
2. Ponownie skompilować i uruchomić aplikację klienta. Aplikacja próbuje synchronizować z zaplecza aplikacji mobilnej po uruchomieniu. Upewnij się, że żadne wyjątki są rejestrowane w konsoli debugowania.
3. (Opcjonalnie) Wyświetlanie zaktualizowanych danych przy użyciu Eksplorator obiektów SQL Server lub narzędzia REST, takiego jak Fiddler lub [Postman][6]. Należy zauważyć, że zsynchronizowano dane między wewnętrzna baza danych i magazynu lokalnego.

    Zwróć uwagę, dane zostały zsynchronizowane między bazy danych i Magazyn lokalny i zawiera elementy, które dodano odłączeniu aplikacji.

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Synchronizacja danych w trybie offline w usługi Azure Mobile Apps][2]
* [Porada zestawu SDK .NET usługi Azure Mobile Apps][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: http://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: http://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: http://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: http://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
