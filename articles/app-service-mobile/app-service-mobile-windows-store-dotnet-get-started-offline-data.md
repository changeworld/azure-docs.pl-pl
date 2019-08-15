---
title: Włącz synchronizację w trybie offline dla aplikacji platforma uniwersalna systemu Windows (platformy UWP) z Mobile Apps | Microsoft Docs
description: Dowiedz się, jak używać aplikacji mobilnej platformy Azure do buforowania i synchronizowania danych w trybie offline w aplikacji platforma uniwersalna systemu Windows (platformy UWP).
documentationcenter: windows
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 4970a80b911a1efbc308d48ac4b8a50f774b4d04
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "67551933"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Włączanie synchronizacji w trybie offline dla aplikacji systemu Windows
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center jest inwestować w nowe i zintegrowane usługi centralne dla opracowywania aplikacji mobilnych. Deweloperzy mogą używaćusług kompilowania, **testowania** i **dystrybucji** , aby skonfigurować ciągłą integrację i potok dostarczania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie swojej aplikacji przy użyciu usług **analizy** i **diagnostyki** oraz angażować się z użytkownikami za pomocą usługi wypychania. Deweloperzy mogą również korzystać z **uwierzytelniania** w celu uwierzytelniania użytkowników i usługi **danych** w celu utrwalania i synchronizowania danych aplikacji w chmurze. Wyewidencjonuj [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-windows-store-dotnet-get-started-offline-data) dzisiaj.
>

## <a name="overview"></a>Omówienie
W tym samouczku pokazano, jak dodać obsługę offline do aplikacji platforma uniwersalna systemu Windows (platformy UWP) przy użyciu zaplecza aplikacji mobilnej platformy Azure. Synchronizacja w trybie offline umożliwia użytkownikom końcowym korzystanie z aplikacji mobilnej — wyświetlanie, Dodawanie lub modyfikowanie danych — nawet w przypadku braku połączenia sieciowego. Zmiany są przechowywane w lokalnej bazie danych. Gdy urządzenie przewróci do trybu online, te zmiany są synchronizowane ze zdalną zapleczem.

W tym samouczku zostanie zaktualizowany projekt aplikacji platformy UWP z samouczka [Tworzenie aplikacji systemu Windows] w celu obsługi funkcji offline usługi Azure Mobile Apps. Jeśli nie używasz pobranego projektu szybkiego startu serwera, musisz dodać pakiety rozszerzenia dostępu do danych do projektu. Aby uzyskać więcej informacji o pakietach rozszerzeń serwera, zobacz [Work with the .NET zaplecz Server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Aby dowiedzieć się więcej na temat funkcji synchronizacji w trybie offline, zobacz temat [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

## <a name="requirements"></a>Wymagania  
W tym samouczku wymagane są następujące wymagania wstępne:

* Visual Studio 2013 uruchomiona na Windows 8.1 lub nowszej.
* Zakończenie [tworzenia aplikacji systemu Windows][Tworzenie aplikacji systemu Windows].
* [Magazyn usługi Microsoft Azure Mobile Services SQLite][sqlite store nuget]
* [platforma uniwersalna systemu Windows opracowywanie oprogramowania SQLite](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizowanie aplikacji klienckiej w celu obsługi funkcji w trybie offline
Funkcje offline aplikacji mobilnych platformy Azure umożliwiają współdziałanie z lokalną bazą danych w przypadku scenariusza w trybie offline. Aby korzystać z tych funkcji w aplikacji, należy zainicjować Interfejs [SyncContext][synccontext] to a local store. Then reference your table through the [IMobileServiceSyncTable][IMobileServiceSyncTable] . Oprogramowania SQLite jest używany jako magazyn lokalny na urządzeniu.

1. Zainstaluj [środowisko uruchomieniowe oprogramowania SQLite dla platforma uniwersalna systemu Windows](https://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. W programie Visual Studio Otwórz Menedżera pakietów NuGet dla projektu aplikacji platformy UWP, który został ukończony w samouczku [Tworzenie aplikacji systemu Windows] .
    Wyszukaj i zainstaluj pakiet NuGet **Microsoft. Azure. Mobile. Client. SQLiteStore** .
3. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy pozycję **odwołania** > **Dodaj odwołanie...** >**Uniwersalne okna** , A następnie włącz program **SQLite dla programu Platforma uniwersalna systemu Windows** i **środowiska uruchomieniowego Visual C++ 2015 dla aplikacji platforma uniwersalna systemu Windows**. >

    ![Dodawanie odwołania platformy UWP oprogramowania SQLite][1]
4. Otwórz plik MainPage.XAML.cs i Usuń oznaczenie komentarza `#define OFFLINE_SYNC_ENABLED` definicji.
5. W programie Visual Studio naciśnij klawisz **F5** , aby skompilować i uruchomić aplikację kliencką. Aplikacja działa tak samo, jak przed włączeniem synchronizacji w trybie offline. Jednak lokalna baza danych jest teraz wypełniana danymi, które mogą być używane w scenariuszu w trybie offline.

## <a name="update-sync"></a>Aktualizowanie aplikacji w celu rozłączenia z zapleczem
W tej sekcji można przerwać połączenie z zapleczem aplikacji mobilnej w celu symulowania sytuacji w trybie offline. Po dodaniu elementów danych program obsługi wyjątków informuje o tym, że aplikacja jest w trybie offline. W tym stanie nowe elementy dodane w magazynie lokalnym są synchronizowane z zapleczem aplikacji mobilnej po następnym uruchomieniu wypychania w stanie połączonym.

1. Edytuj App.xaml.cs w projekcie udostępnionym. Skomentuj inicjowanie **MobileServiceClient** i Dodaj następujący wiersz, który używa nieprawidłowego adresu URL aplikacji mobilnej:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Zachowanie w trybie offline można także zademonstrować, wyłączając sieci Wi-Fi i sieć komórkową na urządzeniu lub korzystając z trybu samolotowego.
2. Naciśnij klawisz **F5** , aby skompilować i uruchomić aplikację. Zwróć uwagę, że synchronizacja nie powiodła się po uruchomieniu aplikacji.
3. Wprowadź nowe elementy i zwróć uwagę, że wypychanie kończy się niepowodzeniem ze stanem [CancelledByNetworkError] za każdym razem, gdy klikniesz przycisk **Zapisz**. Nowe elementy do wykonania znajdują się jednak w magazynie lokalnym, dopóki nie będą mogły zostać wypchnięte do zaplecza aplikacji mobilnej.  W aplikacji produkcyjnej, jeśli pominięto te wyjątki, aplikacja kliencka działa tak, jakby nadal była połączona z zapleczem aplikacji mobilnej.
4. Zamknij aplikację i uruchom ją ponownie, aby sprawdzić, czy nowe utworzone elementy są utrwalane w magazynie lokalnym.
5. Obowiązkowe W programie Visual Studio Otwórz **Eksplorator serwera**. Przejdź do bazy danych w **usłudze Azure**->**SQL**Database. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie **Otwórz w Eksplorator obiektów SQL Server**. Teraz możesz przejść do tabeli bazy danych SQL i jej zawartości. Sprawdź, czy dane w bazie danych zaplecza nie uległy zmianie.
6. Obowiązkowe Użyj narzędzia REST, takiego jak programu Fiddler lub Poster, aby wysłać zapytanie do zaplecza mobilnego przy użyciu zapytania GET `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`w formularzu.

## <a name="update-online-app"></a>Aktualizowanie aplikacji w celu ponownego połączenia zaplecza aplikacji mobilnej
W tej sekcji należy ponownie połączyć aplikację z zapleczem aplikacji mobilnej. Te zmiany symulują ponowne połączenie sieciowe w aplikacji.

Po pierwszym uruchomieniu aplikacji `OnNavigatedTo` program obsługi zdarzeń wywołuje. `InitLocalStoreAsync` Ta metoda umożliwia wywołanie `SyncAsync` synchronizacji lokalnego magazynu z bazą danych zaplecza. Aplikacja próbuje przeprowadzić synchronizację przy uruchamianiu.

1. Otwórz App.XAML.cs w projekcie udostępnionym i Usuń komentarz z `MobileServiceClient` poprzedniej inicjalizacji, aby użyć poprawnego adresu URL aplikacji mobilnej.
2. Naciśnij klawisz **F5** , aby skompilować i uruchomić aplikację. Aplikacja synchronizuje lokalne zmiany z zapleczem aplikacji mobilnej platformy Azure przy użyciu operacji wypychania i ściągania, gdy program obsługi zdarzeń zostanie `OnNavigatedTo` wykonany.
3. Obowiązkowe Wyświetl zaktualizowane dane przy użyciu Eksplorator obiektów SQL Server lub narzędzia REST, takiego jak programu Fiddler. Zauważ, że dane zostały zsynchronizowane między bazą danych zaplecza aplikacji mobilnej Azure a magazynem lokalnym.
4. W aplikacji kliknij pole wyboru obok kilku elementów, aby zakończyć je w magazynie lokalnym.

   `UpdateCheckedTodoItem`wywołania `SyncAsync` synchronizacji każdego ukończonego elementu z zapleczem aplikacji mobilnej. `SyncAsync`wywołuje zarówno wypychanie, jak i ściąganie. Jednak zawsze, **gdy wykonujesz ściąganie względem tabeli, do której wprowadzono zmiany, wypchnięcie jest zawsze wykonywane automatycznie**. Takie zachowanie gwarantuje, że wszystkie tabele w magazynie lokalnym wraz z relacjami pozostają spójne. Takie zachowanie może spowodować nieoczekiwane wypchnięcie.  Aby uzyskać więcej informacji na temat tego zachowania, zobacz [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

## <a name="api-summary"></a>Podsumowanie interfejsu API
Aby zapewnić obsługę funkcji w trybie offline usług Mobile Services, został użyty Interfejs [IMobileServiceSyncTable] i zainicjowany [MobileServiceClient. SyncContext][synccontext] z lokalną bazą danych programu SQLite. W trybie offline normalne operacje CRUD dla Mobile Apps działają tak, jakby aplikacja była nadal połączona, gdy operacje wystąpią w magazynie lokalnym. Do synchronizowania lokalnego magazynu z serwerem służą następujące metody:

* **[PushAsync]** Ponieważ ta metoda jest członkiem [IMobileServicesSyncContext], zmiany we wszystkich tabelach są wypychane do zaplecza. Do serwera są wysyłane tylko rekordy ze zmianami lokalnymi.
* **[PullAsync]** Ściąganie rozpoczyna się od [IMobileServiceSyncTable]. W przypadku śledzenia zmian w tabeli niejawne wypychanie jest uruchamiane w celu upewnienia się, że wszystkie tabele w magazynie lokalnym wraz z relacjami pozostają spójne. Parametr *pushOtherTables* określa, czy inne tabele w kontekście są wypychane w niejawnym wypchnięciu. Parametr *zapytania* przyjmuje [IMobileServiceTableQuery\<> T][IMobileServiceTableQuery] lub ciąg zapytania OData do filtrowania zwracanych danych. Parametr *QueryId* jest używany do definiowania synchronizacji przyrostowej. Aby uzyskać więcej informacji, zobacz [synchronizowanie danych w trybie offline na platformie Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]** Aplikacja powinna okresowo wywoływać tę metodę, aby przeczyścić nieodświeżone dane z magazynu lokalnego. Użyj parametru *Force* , gdy musisz przeczyścić wszystkie zmiany, które nie zostały jeszcze zsynchronizowane.

Aby uzyskać więcej informacji na temat tych pojęć, zobacz [Synchronizacja danych w trybie offline w usłudze Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Więcej informacji
W poniższych tematach znajdują się dodatkowe informacje na temat funkcji synchronizacji w trybie offline Mobile Apps:

* [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]
* [Azure Mobile Apps .NET SDK porady][8]

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
[Tworzenie aplikacji systemu Windows]: app-service-mobile-windows-store-dotnet-get-started.md
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
