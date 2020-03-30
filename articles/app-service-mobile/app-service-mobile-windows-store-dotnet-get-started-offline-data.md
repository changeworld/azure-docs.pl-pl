---
title: Włączanie synchronizacji w trybie offline dla aplikacji platformy uniwersalnej systemu wysiewu
description: Dowiedz się, jak używać aplikacji mobilnej Platformy Azure do buforowania i synchronizowania danych w trybie offline w aplikacji platformy uniwersalnej systemu Windows (UWP).
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 236d4c199a13b02bcd82ae02657bbd35e45f729b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458821"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Włączanie synchronizacji w trybie offline dla aplikacji systemu Windows
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Omówienie
W tym samouczku pokazano, jak dodać obsługę w trybie offline do aplikacji platformy uniwersalnej systemu Windows (UWP) przy użyciu zaplecza aplikacji azure mobile. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną — wyświetlanie, dodawanie lub modyfikowanie danych — nawet wtedy, gdy nie ma połączenia sieciowego. Zmiany są przechowywane w lokalnej bazie danych. Po powrocie urządzenia do trybu online zmiany te są synchronizowane ze zdalnym zapleczem.

W tym samouczku zaktualizujesz projekt aplikacji platformy uniwersalnej systemu Windows z [samouczka Tworzenie aplikacji systemu Windows] do obsługi funkcji usługi Azure Mobile Apps w trybie offline. Jeśli nie używasz pobranego projektu serwera szybkiego startu, należy dodać pakiety rozszerzeń dostępu do danych do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzeń serwera, zobacz [Praca z zestawem SDK serwera wewnętrznej bazy danych .NET dla aplikacji azure mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Aby dowiedzieć się więcej o funkcji synchronizacji w trybie offline, zobacz temat [Synchronizacja danych w trybie offline w aplikacjach mobilnych platformy Azure].

## <a name="requirements"></a>Wymagania  
Ten samouczek wymaga następujących wymagań wstępnych:

* Visual Studio 2013 działa w systemie Windows 8.1 lub nowszym.
* Zakończenie [tworzenia aplikacji systemu Windows][w celu utworzenia aplikacji systemu Windows].
* [Sklep SQLite usług mobilnych platformy Azure][sqlite store nuget]
* [SQLite for Universal Windows Platform development](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizowanie aplikacji klienckiej do obsługi funkcji w trybie offline
Funkcje usługi Azure Mobile App w trybie offline umożliwiają interakcję z lokalną bazą danych w przypadku, gdy jesteś w scenariuszu offline. Aby użyć tych funkcji w aplikacji, należy zainicjować interfejs [SyncContext.][synccontext] to a local store. Then reference your table through the [IMobileServiceSyncTable][IMobileServiceSyncTable] SQLite jest używany jako lokalny sklep na urządzeniu.

1. Zainstaluj [środowisko wykonawcze SQLite dla uniwersalnej platformy windowsowej](https://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. W programie Visual Studio otwórz Menedżera pakietów NuGet dla projektu aplikacji platformy uniwersalnej systemu Windows, który został ukończony w [samouczku Tworzenie aplikacji systemu Windows.]
    Wyszukaj i zainstaluj pakiet **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet.
3. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **odwołania** > **Dodaj odwołanie...** > **Uniwersalne rozszerzenia** **systemu Windows** > , a następnie włącz zarówno **SQLite for Universal Windows Platform,** jak i **Visual C++ 2015 Runtime for Universal Windows Platform apps**.

    ![Dodaj odwołanie do platformy uniwersalnej systemu operacyjnego /pl][1]
4. Otwórz plik MainPage.xaml.cs i odkomentuj definicję. `#define OFFLINE_SYNC_ENABLED`
5. W programie Visual Studio naciśnij klawisz **F5,** aby odbudować i uruchomić aplikację kliencką. Aplikacja działa tak samo, jak przed włączeniem synchronizacji w trybie offline. Jednak lokalna baza danych jest teraz wypełniona danymi, które mogą być używane w scenariuszu offline.

## <a name="update-the-app-to-disconnect-from-the-backend"></a><a name="update-sync"></a>Aktualizowanie aplikacji w celu odłączenia się od wewnętrznej bazy danych
W tej sekcji można przerwać połączenie z zaplecza aplikacji mobilnej do symulacji sytuacji w trybie offline. Po dodaniu elementów danych, program obsługi wyjątków informuje, że aplikacja jest w trybie offline. W tym stanie nowe elementy dodane w magazynie lokalnym i zostaną zsynchronizowane z zaplecza aplikacji mobilnej, gdy wypychanie jest dalej uruchamiane w stanie połączonym.

1. Edytuj App.xaml.cs w projekcie udostępnionym. Skomentuj inicjowanie **MobileServiceClient** i dodaj następujący wiersz, który używa nieprawidłowego adresu URL aplikacji mobilnej:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Można również zademonstrować zachowanie w trybie offline, wyłączając sieci wi-fi i komórkowe na urządzeniu lub korzystając z trybu samolotowego.
2. Naciśnij **klawisz F5,** aby utworzyć i uruchomić aplikację. Zwróć uwagę, że synchronizacja nie powiodła się podczas odświeżania po uruchomieniu aplikacji.
3. Wprowadź nowe elementy i zwróć uwagę, że wypychanie kończy się niepowodzeniem ze stanem [CancelledByNetworkError] za każdym razem, gdy klikniesz przycisk **Zapisz**. Jednak nowe elementy todo istnieją w lokalnym sklepie, dopóki nie mogą być wypychane do zaplecza aplikacji mobilnej.  W aplikacji produkcyjnej, jeśli pominąć te wyjątki aplikacja kliencka zachowuje się tak, jakby nadal jest podłączony do wewnętrznej bazy danych aplikacji mobilnej.
4. Zamknij aplikację i uruchom ją ponownie, aby sprawdzić, czy nowe utworzone elementy są utrwalone w magazynie lokalnym.
5. (Opcjonalnie) W programie Visual Studio otwórz **Eksploratora serwera**. Przejdź do bazy danych w **usłudze Azure**->**SQL Databases**. Kliknij prawym przyciskiem myszy bazę danych i wybierz polecenie **Otwórz w Eksploratorze obiektów programu SQL Server**. Teraz możesz przejść do tabeli bazy danych SQL i jej zawartości. Sprawdź, czy dane w bazie danych wewnętrznej bazy danych nie uległy zmianie.
6. (Opcjonalnie) Użyj narzędzia REST, takiego jak Fiddler lub Listonosz, aby zbadać `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`zaplecze telefonu komórkowego, używając zapytania GET w formularzu .

## <a name="update-the-app-to-reconnect-your-mobile-app-backend"></a><a name="update-online-app"></a>Aktualizowanie aplikacji w celu ponownego połączenia zaplecza aplikacji mobilnej
W tej sekcji ponownie połączyć aplikację z zaplecza aplikacji mobilnej. Te zmiany symulują ponowne połączenie sieci w aplikacji.

Po pierwszym uruchomieniu aplikacji `OnNavigatedTo` program obsługi `InitLocalStoreAsync`zdarzeń wywołuje . Ta metoda z `SyncAsync` kolei wywołuje, aby zsynchronizować magazyn lokalny z bazy danych wewnętrznej bazy danych. Aplikacja próbuje zsynchronizować podczas uruchamiania.

1. Otwórz App.xaml.cs w projekcie udostępnionym i odkomentuj `MobileServiceClient` poprzednią inicjację, aby użyć poprawnego adresu URL aplikacji mobilnej.
2. Naciśnij klawisz **F5,** aby odbudować i uruchomić aplikację. Aplikacja synchronizuje lokalne zmiany z zaplecza aplikacji azure mobile przy `OnNavigatedTo` użyciu operacji wypychania i ściągania podczas wykonywania programu obsługi zdarzeń.
3. (Opcjonalnie) Wyświetlanie zaktualizowanych danych przy użyciu Eksploratora obiektów programu SQL Server lub narzędzia REST, takiego jak Fiddler. Zwróć uwagę, że dane zostały zsynchronizowane między bazą danych zaplecza aplikacji Azure Mobile i magazynem lokalnym.
4. W aplikacji kliknij pole wyboru obok kilku elementów, aby zakończyć je w lokalnym sklepie.

   `UpdateCheckedTodoItem`wywołania, `SyncAsync` aby zsynchronizować każdy ukończony element z zapleczem aplikacji mobilnej. `SyncAsync`połączenia zarówno push, jak i pull. Jednak **za każdym razem, gdy wykonujesz pociągnięcie względem tabeli, do której klient dokonał zmian, wypychanie jest zawsze wykonywane automatycznie.** To zachowanie zapewnia, że wszystkie tabele w magazynie lokalnym wraz z relacjami pozostają spójne. To zachowanie może spowodować nieoczekiwane wypychanie.  Aby uzyskać więcej informacji na temat tego zachowania, zobacz [Synchronizacja danych w trybie offline w aplikacjach mobilnych platformy Azure].

## <a name="api-summary"></a>Podsumowanie interfejsu API
Aby obsługiwać funkcje usługi mobilne w trybie offline, użyliśmy interfejsu [IMobileServiceSyncTable] i zainicjowaliśmy [MobileServiceClient.SyncContext][synccontext] z lokalną bazą danych SQLite. W trybie offline normalne operacje CRUD dla aplikacji mobilnych działają tak, jakby aplikacja była nadal połączona, gdy operacje występują w sklepie lokalnym. Do synchronizowania magazynu lokalnego z serwerem są używane następujące metody:

* **[PushAsync (PushAsync)]** Ponieważ ta metoda jest członkiem [IMobileServicesSyncContext,]zmiany we wszystkich tabelach są wypychane do wewnętrznej bazy danych. Do serwera wysyłane są tylko rekordy ze zmianami lokalnymi.
* **[PullAsync (PullAsync)]** Ściąganie jest uruchamiane z [iMobileServiceSyncTable]. Gdy są śledzone zmiany w tabeli, niejawne wypychanie jest uruchamiany, aby upewnić się, że wszystkie tabele w magazynie lokalnym wraz z relacjami pozostają spójne. *PushOtherTables* parametr określa, czy inne tabele w kontekście są wypychane w niejawnym wypychanie. Parametr *kwerendy* pobiera ciąg zapytania [\<IMobileServiceTableQuery T>][IMobileServiceTableQuery] lub OData w celu filtrowania zwróconych danych. Parametr *queryId* służy do definiowania synchronizacji przyrostowej. Aby uzyskać więcej informacji, zobacz [Synchronizacja danych w trybie offline w usłudze Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[CzystgeAsync]** Aplikacja powinna okresowo wywoływać tę metodę, aby przeczyścić przestarzałe dane z magazynu lokalnego. Użyj parametru *force,* gdy trzeba przeczyścić wszelkie zmiany, które nie zostały jeszcze zsynchronizowane.

Aby uzyskać więcej informacji na temat tych pojęć, zobacz [Synchronizacja danych w trybie offline w aplikacjach mobilnych platformy Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Więcej informacji
Następujące tematy zawierają dodatkowe informacje ogólne dotyczące funkcji synchronizacji w trybie offline aplikacji mobilnych:

* [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]
* [Azure Mobile Apps .NET SDK HOWTO][8]

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
[tworzenie aplikacji systemu Windows]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: https://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: https://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: https://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[Tabela iMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync (PullAsync)]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync (PushAsync)]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[CzystgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
