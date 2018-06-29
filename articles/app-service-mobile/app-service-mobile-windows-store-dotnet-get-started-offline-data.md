---
title: Włączanie synchronizacji w trybie offline dla aplikacji uniwersalnych platformy systemu Windows (UWP) z usługą Mobile Apps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać aplikacji mobilnej Azure do pamięci podręcznej i synchronizacji danych w trybie offline w aplikacji Windows platformy Uniwersalnej.
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
ms.openlocfilehash: 3df6951129f7beda7970e394ffdd32c7e02304dd
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060311"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Włączanie synchronizacji w trybie offline dla aplikacji systemu Windows
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Przegląd
W tym samouczku przedstawiono sposób dodawania obsługi w trybie offline do aplikacji systemu Windows platformy Uniwersalnej przy użyciu zaplecza aplikacji mobilnej Azure. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacji mobilnej — przeglądanie, dodawanie lub modyfikowanie danych — nawet wtedy, gdy istnieje połączenie sieciowe. Zmiany są przechowywane w lokalnej bazie danych. Gdy urządzenie jest w trybie online, te zmiany są synchronizowane z zdalnego wewnętrznej bazy danych.

W tym samouczku zaktualizować projekt aplikacji platformy uniwersalnej systemu Windows z tego samouczka [tworzenie aplikacji systemu Windows] do obsługi funkcji w trybie offline z usługą Azure Mobile Apps. Jeśli nie używasz szybki start pobrany Projekt serwera, należy dodać pakietów rozszerzenia dostępu do danych do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzenia serwera, zobacz [pracować z serwera wewnętrznej bazy danych .NET SDK usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Aby dowiedzieć się więcej na temat funkcji synchronizacji w trybie offline, zobacz temat [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

## <a name="requirements"></a>Wymagania  
Ten samouczek wymaga następujące wymagania wstępne:

* Visual Studio 2013 uruchomiony w systemie Windows 8.1 lub nowszym.
* Zakończenie [tworzenie aplikacji systemu Windows][tworzenie aplikacji systemu windows].
* [Azure Mobile Services SQLite magazynu][sqlite store nuget]
* [SQLite do tworzenia aplikacji platformy uniwersalnej systemu Windows](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizowanie aplikacji klienta do obsługi funkcji w trybie offline
Funkcje platformy Azure w trybie offline aplikacji mobilnej pozwala na interakcję z lokalnej bazy danych podczas pracy w trybie offline scenariuszu. Aby korzystać z tych funkcji w aplikacji, należy zainicjować [SyncContext] [ synccontext] do lokalnego magazynu. Następnie odwołać się do tabeli za pomocą [IMobileServiceSyncTable][IMobileServiceSyncTable] interfejsu. SQLite jest używany w magazynie lokalnym urządzenia.

1. Zainstaluj [środowiska wykonawczego SQLite dla platformy uniwersalnej systemu Windows](http://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. W programie Visual Studio, otwórz Menedżera pakietów NuGet dla projektu aplikacji platformy uniwersalnej systemu Windows, które wykonane w [tworzenie aplikacji systemu Windows] samouczka.
    Wyszukiwanie i instalowanie **Microsoft.Azure.Mobile.Client.SQLiteStore** pakietu NuGet.
3. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **odwołania** > **Dodawanie odwołania...** > **Aplikacja uniwersalna systemu Windows** > **rozszerzenia**, następnie włączyć zarówno **SQLite dla platformy uniwersalnej systemu Windows** i **2015 środowiska uruchomieniowego Visual C++ dla uniwersalnych systemu Windows Aplikacje platformy**.

    ![Dodaj odwołanie do bazy danych SQLite platformy uniwersalnej systemu Windows][1]
4. Otwórz plik MainPage.xaml.cs i Usuń komentarz `#define OFFLINE_SYNC_ENABLED` definicji.
5. W programie Visual Studio, naciśnij klawisz **F5** klawisz, aby ponownie skompilować i uruchomić aplikację klienta. Aplikacja działa tak samo jak przed włączeniem synchronizacji w trybie offline. Jednak lokalnej bazy danych jest teraz wypełniona dane, które mogą być używane w scenariuszu w trybie offline.

## <a name="update-sync"></a>Aktualizowanie aplikacji, aby zakończyć połączenie z wewnętrznej bazy danych
W tej sekcji możesz przerwać połączenie do zaplecza aplikacji mobilnej do symulowania sytuacji w trybie offline. Po dodaniu elementów danych z programu obsługi wyjątków informuje, że dana aplikacja jest w trybie offline. W tym stanie nowe elementy dodane w lokalnym magazynie i będą synchronizowane z zaplecza aplikacji mobilnej przy następnym uruchomieniu wypychania jest niepodłączony.

1. Edytuj App.xaml.cs w projekcie udostępnionym. Komentarz inicjowanie **MobileServiceClient** i Dodaj następujący wiersz, który korzysta z aplikacji mobilnej nieprawidłowy adres URL:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Można również pokazują zachowania w trybie offline, wyłączając sieci Wi-Fi i sieci komórkowych na urządzeniu lub tryb samolotowy.
2. Naciśnij klawisz **F5** Aby skompilować i uruchomić aplikację. Zwróć uwagę, synchronizacji nie powiodło się podczas odświeżania, gdy aplikacja jest uruchamiana.
3. Wprowadź nowe elementy i zwróć uwagę, że wypychania kończy się niepowodzeniem z [CancelledByNetworkError] stanu każdorazowo po kliknięciu **zapisać**. Jednak nowych elementów todo istnieje w lokalnym magazynie dopóki nie może zostać przeniesiony do zaplecza aplikacji mobilnej.  W aplikacji produkcyjnej Jeśli pominąć te wyjątki aplikacji klienckiej zachowuje się tak, jakby nadal jest podłączony do zaplecza aplikacji mobilnej.
4. Zamknij aplikację i uruchom ponownie, aby sprawdzić, czy nowe elementy utworzone zostały utrwalone w magazynie lokalnym.
5. (Opcjonalnie) W programie Visual Studio Otwórz **Eksploratora serwera**. Przejdź do bazy danych w **Azure**->**baz danych SQL**. Kliknij prawym przyciskiem myszy bazę danych i wybierz **Otwórz w Eksploratorze obiektów SQL Server**. Możesz teraz przejść do tabeli bazy danych SQL i jego zawartość. Sprawdź, czy dane w bazie danych zaplecza nie uległy zmianie.
6. (Opcjonalnie) Zapytania z zaplecze aplikacji mobilnej, za pomocą zapytania GET w formularzu za pomocą narzędzia REST, takie jak Fiddler lub Postman `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Aktualizowanie aplikacji ponownie połączyć zaplecza aplikacji mobilnej
W tej sekcji ponowne łączenie aplikacji z zapleczem aplikacji mobilnej. Te zmiany zasymulować ponowne połączenie sieciowe w aplikacji.

Przy pierwszym uruchomieniu aplikacji, `OnNavigatedTo` wywołań obsługi zdarzeń `InitLocalStoreAsync`. Ta metoda z kolei wywołuje `SyncAsync` do synchronizacji z bazy danych zaplecza magazynu lokalnego. Aplikacja próbuje synchronizować podczas uruchamiania.

1. Otwórz App.xaml.cs w projekcie udostępnionym i Usuń komentarz z poprzednich inicjowanie `MobileServiceClient` używać prawidłowego adresu URL aplikacji mobilnej.
2. Naciśnij klawisz **F5** klawisz, aby ponownie skompilować i uruchomić aplikację. Aplikacja przeprowadza synchronizację zmian z zaplecza aplikacji mobilnej Azure przy użyciu operacji wypychania i ściągania podczas `OnNavigatedTo` wykonuje program obsługi zdarzeń.
3. (Opcjonalnie) Wyświetlanie zaktualizowanych danych przy użyciu Eksplorator obiektów SQL Server lub narzędzia REST, takiego jak Fiddler. Powiadomienie danych został zsynchronizowany między bazą danych zaplecza aplikacji mobilnej Azure i lokalnego magazynu.
4. W aplikacji kliknij pole wyboru obok kilka elementów, aby zakończyć je w lokalnym magazynie.

   `UpdateCheckedTodoItem` wywołania `SyncAsync` do elementu synchronizacja każdego z zaplecza aplikacji mobilnej. `SyncAsync` wywołuje wypychania i ściągania. Jednak **przy każdym wykonaniu ściągania przed tabelę, w której klient wprowadził zmiany do wypychania jest zawsze wykonywana automatycznie**. Takie zachowanie gwarantuje, że wszystkie tabele w lokalnym magazynie wraz z relacji zachować spójność. To zachowanie może spowodować nieoczekiwane wypychania.  Aby uzyskać więcej informacji dotyczących tego zachowania, zobacz [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

## <a name="api-summary"></a>Podsumowanie interfejsu API
Do obsługi w trybie offline funkcji usługi mobilne, użyliśmy [IMobileServiceSyncTable] interfejsu i zainicjować [MobileServiceClient.SyncContext] [ synccontext] z lokalne bazy danych SQLite. W trybie offline, normalnych operacji CRUD Mobile Apps pracować tak, jakby aplikacji jest nadal połączony, podczas gdy operacje są wykonywane przed magazynu lokalnego. Następujące metody są używane do synchronizacji z serwerem lokalnym magazynie:

* **[PushAsync]**  , ponieważ ta metoda jest elementem członkowskim [IMobileServicesSyncContext], zmiany we wszystkich tabelach są przenoszone do wewnętrznej bazy danych. Tylko rekordy z lokalne zmiany są wysyłane do serwera.
* **[PullAsync]**  ściąganie jest uruchamiany z [IMobileServiceSyncTable]. Gdy są śledzone zmiany w tabeli, niejawne wypychania jest uruchamiane aby upewnić się, że wszystkie tabele w lokalnym magazynie wraz z relacji zachować spójność. *PushOtherTables* parametr określa, czy inne tabele w tym kontekście są przenoszone w niejawnych wypychania. *Zapytania* przyjmuje parametr [IMobileServiceTableQuery<T> ] [ IMobileServiceTableQuery] lub ciągu zapytania OData do filtrowania danych zwróconych. *Identyfikatora kwerendy* parametr jest używany do definiowania synchronizacji przyrostowej. Aby uzyskać więcej informacji, zobacz [w trybie Offline synchronizacji danych w usłudze Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]**  aplikacji należy okresowo wywoływać tej metody, aby wyczyścić stare dane z magazynu lokalnego. Użyj *wymusić* parametru, gdy chcesz przeczyścić wszystkie zmiany, które nie zostały jeszcze zsynchronizowane.

Aby uzyskać więcej informacji dotyczących tych pojęć, zobacz [w trybie Offline synchronizacji danych w usłudze Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Więcej informacji
Poniższe tematy zawierają dodatkowe informacje na temat funkcji synchronizacji w trybie offline, aplikacji mobilnej:

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
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921
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
