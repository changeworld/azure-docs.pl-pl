---
title: Włączanie synchronizacji offline dla aplikacji uniwersalnych platformy Windows (UWP) przy użyciu funkcji Mobile Apps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure Mobile App do pamięci podręcznej i synchronizacji danych w trybie offline w aplikacji platformy uniwersalnej Windows (UWP).
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 69ee9e7101a2b7337e1e42ff5ae09954fbfd50b2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128053"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Włączanie synchronizacji w trybie offline dla aplikacji systemu Windows
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Omówienie
W tym samouczku dowiesz się, jak dodać obsługę trybu offline do aplikacji platformy uniwersalnej Windows (UWP), przy użyciu zaplecza aplikacji mobilnej platformy Azure. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną — wyświetlanie, dodawanie lub modyfikowanie danych — nawet w przypadku braku połączenia sieciowego. Zmiany są przechowywane w lokalnej bazie danych. Gdy urządzenie jest w trybie online, zmiany te są synchronizowane za pomocą zdalnego wewnętrznej bazy danych.

W tym samouczku aktualizujesz projekt aplikacji platformy uniwersalnej systemu Windows z tego samouczka [Tworzenie aplikacji systemu windows] do obsługi w trybie offline funkcji usługi Azure Mobile Apps. Jeśli nie używasz projektu serwera pobranego — szybki start, należy dodać pakiety rozszerzeń dostępu do danych do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzeń serwera, zobacz [pracy z zestawem SDK serwera zaplecza platformy .NET dla usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Aby dowiedzieć się więcej na temat funkcji synchronizacji w trybie offline, zobacz temat [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

## <a name="requirements"></a>Wymagania  
Ten samouczek wymaga następujących wymagań wstępnych:

* Program Visual Studio 2013 uruchomiony na Windows 8.1 lub nowszym.
* Ukończenie [tworzenie aplikacji Windows][tworzenie aplikacji systemu windows].
* [Azure Mobile Services SQLite Store][sqlite store nuget]
* [Bazy danych SQLite dla programowania na platformę uniwersalną Windows](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizuj aplikację kliencką do obsługi funkcji w trybie offline
Funkcje offline usług Azure Mobile App umożliwiają interakcję z lokalnej bazy danych, gdy znajdują się w ramach scenariusza w trybie offline. Korzystanie z tych funkcji w aplikacji należy zainicjować [SyncContext] [ synccontext] do magazynu lokalnego. Następnie odwoływać się do tabeli za pomocą [IMobileServiceSyncTable][IMobileServiceSyncTable] interfejsu. Bazy danych SQLite jest używana jako magazynu lokalnego na urządzeniu.

1. Zainstaluj [runtime SQLite dla platformy uniwersalnej Windows](https://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. W programie Visual Studio, otwórz Menedżera pakietów NuGet dla projektu aplikacji platformy uniwersalnej systemu Windows, który należy wykonać w [Tworzenie aplikacji systemu windows] samouczka.
    Wyszukiwanie i instalowanie **Microsoft.Azure.Mobile.Client.SQLiteStore** pakietu NuGet.
3. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **odwołania** > **Dodaj odwołanie...** > **Windows Universal** > **rozszerzenia**, następnie włączyć zarówno **SQLite dla platformy uniwersalnej Windows** i **2015 środowiska uruchomieniowego Visual C++ dla Windows Universal Aplikacje dla platform**.

    ![Dodaj odwołanie do bazy danych SQLite platformy uniwersalnej systemu Windows][1]
4. Otwórz plik MainPage.xaml.cs i usuń znaczniki komentarza `#define OFFLINE_SYNC_ENABLED` definicji.
5. W programie Visual Studio, naciśnij klawisz **F5** klawisz, aby ponownie skompilować i uruchomić aplikację kliencką. Aplikacja działa tak samo jak przed włączeniem synchronizacji w trybie offline. Jednak lokalnej bazy danych jest teraz wypełniana z danymi, które mogą być używane w ramach scenariusza w trybie offline.

## <a name="update-sync"></a>Zaktualizować aplikację tak, aby zakończyć połączenie z wewnętrznej bazy danych
W tej sekcji możesz przerwać połączenie do zaplecza aplikacji mobilnej do symulacji taka sytuacja jest w trybie offline. Po dodaniu elementów danych, obsłudze wyjątków informujący o tym, że aplikacja jest w trybie offline. W tym stanie nowe elementy dodane w magazynie lokalnym i będą synchronizowane z zaplecza aplikacji mobilnej, po następnym uruchomieniu wypychania niepodłączony.

1. Edytowanie pliku App.xaml.cs projektu udostępnionego. Komentarz inicjowanie **MobileServiceClient** i Dodaj następujący wiersz, który korzysta z adresu URL aplikacji mobilnej nieprawidłowy:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Możesz również prezentacja działania w trybie offline, wyłączając sieć Wi-Fi i sieci komórkowych na urządzeniu lub użyć tryb samolotowy.
2. Naciśnij klawisz **F5** Aby skompilować i uruchomić aplikację. Zwróć uwagę, usługi synchronizacji nie powiodło się podczas odświeżania, gdy aplikacja jest uruchamiana.
3. Wprowadź nowe elementy i zwróć uwagę, że wypychania kończy się niepowodzeniem z [CancelledByNetworkError] stanu każdorazowo po kliknięciu **Zapisz**. Jednak nowe elementy zadań do wykonania istnieje w magazynie lokalnym do czasu może zostać przeniesiony do zaplecza aplikacji mobilnej.  W aplikacji produkcyjnej Jeśli pominąć te wyjątki aplikacji klienckiej zachowuje się tak, jakby nadal jest podłączony do zaplecza aplikacji mobilnej.
4. Zamknij aplikację i uruchom ponownie, aby sprawdzić, czy nowych elementów, który został utworzony są utrwalane w magazynie lokalnym.
5. (Opcjonalnie) W programie Visual Studio, otwórz **Eksploratora serwera**. Przejdź do bazy danych w **Azure**->**baz danych SQL**. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Otwórz w Eksploratorze obiektów SQL Server**. Teraz możesz przejść do tabeli bazy danych SQL i jego zawartość. Upewnij się, że dane w wewnętrznej bazy danych nie została zmieniona.
6. (Opcjonalnie) Za pomocą narzędzia REST narzędzia Fiddler lub Postman do wykonywania zapytań w zapleczu mobilnym za pomocą zapytania GET w formie `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Zaktualizować aplikację tak, aby ponownie połączyć zaplecze aplikacji mobilnej
W tej sekcji ponowne łączenie aplikacji z zapleczem aplikacji mobilnej. Te zmiany zasymulować ponowne połączenie sieciowe w ramach aplikacji.

Przy pierwszym uruchomieniu aplikacji, `OnNavigatedTo` wywołań obsługi zdarzeń `InitLocalStoreAsync`. Ta metoda z kolei wywołuje `SyncAsync` do synchronizacji magazynu lokalnego przy użyciu wewnętrznej bazy danych. Aplikacja próbuje synchronizować przy uruchamianiu.

1. Otwórz plik App.xaml.cs w projekcie udostępnionym, i usuń znaczniki komentarza Twoje poprzednie inicjowanie `MobileServiceClient` służące poprawny adres URL aplikacji mobilnej.
2. Naciśnij klawisz **F5** klawisz, aby ponownie skompilować i uruchomić aplikację. Aplikacja synchronizuje zmiany lokalne przy użyciu zaplecza aplikacji mobilnej platformy Azure przy użyciu operacji wypychania i ściągania podczas `OnNavigatedTo` wykonuje program obsługi zdarzeń.
3. (Opcjonalnie) Wyświetl zaktualizowane dane za pomocą Eksplorator obiektów SQL Server lub narzędzia REST, takiego jak Fiddler. Ogłoszenie dane zostały zsynchronizowane między bazy danych zaplecza aplikacji mobilnej platformy Azure i Magazyn lokalny.
4. W aplikacji kliknij pole wyboru obok kilka elementów, aby zakończyć je w magazynie lokalnym.

   `UpdateCheckedTodoItem` wywołania `SyncAsync` do synchronizacji każdego elementu przy użyciu zaplecza aplikacji mobilnej. `SyncAsync` wywołuje operacje wypychania i ściągania. Jednak **przy każdym wykonaniu ściągnięcia względem tabeli, który klient wprowadził zmiany do, powiadomienie wypychane jest zawsze wykonywana automatycznie**. Takie zachowanie gwarantuje, że wszystkie tabele w magazynie lokalnym oraz relacje pozostają spójne. To zachowanie może spowodować nieoczekiwane wypychania.  Aby uzyskać więcej informacji na temat tego zachowania, zobacz [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

## <a name="api-summary"></a>Podsumowanie interfejsu API
Aby obsługiwać funkcje offline usług mobilnych, użyliśmy [IMobileServiceSyncTable] interfejs i zainicjować [MobileServiceClient.SyncContext] [ synccontext] z lokalne bazy danych SQLite. W trybie offline, zwykłych operacji CRUD dla aplikacji mobilnych pracować tak, jakby aplikacji jest nadal połączony, podczas gdy operacje są wykonywane względem magazynu lokalnego. Następujące metody są używane do synchronizowania lokalnego magazynu z serwerem:

* **[PushAsync]**  , ponieważ ta metoda jest elementem członkowskim [IMobileServicesSyncContext], zmiany w ramach wszystkich tabel są przekazywane do zaplecza. Tylko rekordy ze zmianami lokalnymi są wysyłane do serwera.
* **[PullAsync]**  ściągnięcia została uruchomiona ze [IMobileServiceSyncTable]. Gdy śledzone zmiany w tabeli, niejawne wypychania jest uruchamiany aby upewnić się, że wszystkie tabele w magazynie lokalnym oraz relacje pozostają spójne. *PushOtherTables* parametr określa, czy inne tabele w kontekście są przekazywane w niejawne wypychania. *Zapytania* Parametr przyjmuje [IMobileServiceTableQuery<T> ] [ IMobileServiceTableQuery] lub ciągu zapytania OData, aby filtrować zwracanych danych. *QueryId* parametr jest używany do definiowania synchronizacja przyrostowa. Aby uzyskać więcej informacji, zobacz [w trybie Offline na synchronizowanie danych w usłudze Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]**  aplikacji okresowo powinna wywołać tę metodę, aby wyczyścić stare dane z magazynu lokalnego. Użyj *wymusić* parametr, gdy trzeba Przeczyść wszystkie zmiany, które nie zostały zsynchronizowane.

Aby uzyskać więcej informacji na temat tych pojęć, zobacz [w trybie Offline na synchronizowanie danych w usłudze Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Więcej informacji
Poniższe tematy zawierają dodatkowe informacje na temat funkcji synchronizacji w trybie offline z funkcji Mobile Apps:

* [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]
* [Porada zestawu SDK .NET usługi Azure Mobile Apps][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Tworzenie aplikacji systemu windows]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: https://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: https://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: https://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
