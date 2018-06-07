---
title: Synchronizacja danych w trybie offline w usługi Azure Mobile Apps | Dokumentacja firmy Microsoft
description: Omówienie funkcji synchronizacji danych w trybie offline dla usługi Azure Mobile Apps i koncepcyjny odwołania
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 403b3289a6135a59e888c4a7bf1cd9cbe40c8ac0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597981"
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps
## <a name="what-is-offline-data-sync"></a>Co to jest synchronizacja danych w trybie offline?
Synchronizacja danych w trybie offline jest klientem i serwerem funkcji zestawu SDK usługi Azure Mobile Apps, która ułatwia deweloperom tworzenie aplikacji, które działają bez połączenia sieciowego.

Gdy aplikacja jest w trybie offline, można nadal tworzyć i modyfikować danych, które są zapisywane w lokalnym magazynie. Gdy aplikacja jest znowu w trybie online, lokalne zmiany można było synchronizować z zaplecza aplikacji mobilnej Azure. Funkcja obejmuje również obsługę wykrywania konfliktów, gdy ten sam rekord zostanie zmieniona na zarówno klient, jak i wewnętrznej bazy danych. Następnie można obsłużyć konfliktów w serwera lub klienta.

Synchronizacja w trybie offline ma wiele zalet:

* Zwiększyć czas odpowiedzi aplikacji przez buforowanie danych serwera lokalnie na urządzeniu
* Tworzenie niezawodnych aplikacji, które pozostają przydatne, gdy istnieją problemy z siecią
* Umożliwia użytkownikom tworzenie i modyfikowanie danych, nawet wtedy, gdy nie dostępu do sieci, obsługujące scenariusze niewielkiego lub żadnego łączności z
* Synchronizowanie danych na wielu urządzeniach i Wykryj konflikty, kiedy ten sam rekord jest modyfikowany przez dwa urządzenia
* Ogranicz użycie sieci w sieciach dużymi opóźnieniami lub naliczane

Następujące samouczki przedstawiają sposób dodawania synchronizacji w trybie offline dla klientów mobilnych za pomocą usługi Azure Mobile Apps:

* [System android: Włączanie synchronizacji w trybie offline]
* [Apache Cordova: Włączanie synchronizacji w trybie offline](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: Włączanie synchronizacji w trybie offline]
* [Xamarin iOS: Włączanie synchronizacji w trybie offline]
* [Dla systemu Xamarin Android: Włączanie synchronizacji w trybie offline]
* [Platformy Xamarin.Forms: Synchronizacja w trybie offline Włącz](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Platforma uniwersalna systemu Windows: Włączanie synchronizacji w trybie offline]

## <a name="what-is-a-sync-table"></a>Co to jest tabela synchronizacji?
Aby uzyskać dostęp do punktu końcowego "/ tabel", zestawów SDK klienta usługi Azure Mobile podać interfejsy takich jak `IMobileServiceTable` (klient .NET SDK) lub `MSTable` (klient z systemem iOS). Te interfejsy API połączyć się bezpośrednio z zaplecza aplikacji mobilnej Azure i zakończyć się niepowodzeniem, jeśli urządzenie klienckie nie dysponują połączeniem sieciowym.

Aby zapewnić obsługę pracy w trybie offline, należy zamiast tego użyć aplikacji *synchronizacji tabeli* interfejsów API, takich jak `IMobileServiceSyncTable` (klient .NET SDK) lub `MSSyncTable` (klient z systemem iOS). Wszystkich operacji CRUD (tworzenia, odczytu, aktualizacji, usuwania) pracy projektowej synchronizacji tabeli interfejsów API, z wyjątkiem teraz one odczytywać lub zapisać *lokalnego magazynu*. Aby można było wykonać wszystkie operacje tabeli synchronizacji, musi zostać zainicjowany magazynu lokalnego.

## <a name="what-is-a-local-store"></a>Co to jest Magazyn lokalny?
Magazyn lokalny jest warstwę trwałości danych na urządzeniu klienckim. Zestawy SDK klienta usługi Azure Mobile Apps udostępnia domyślną implementację magazynu lokalnego. W systemach Windows, Xamarin i Android jest on oparty na SQLite. W systemach iOS jest on oparty na danych podstawowych.

Aby używać implementacji oparty na SQLite Windows Phone lub Microsoft Store, należy zainstalować rozszerzenie SQLite. Aby uzyskać więcej informacji, zobacz [Platforma uniwersalna systemu Windows: Włączanie synchronizacji w trybie offline]. Android i iOS składnikiem wersji bazy danych SQLite w system operacyjny urządzenia, więc nie można odwołać własnych wersji bazy danych SQLite.

Deweloperzy mogą także implementować własnych magazynu lokalnego. Na przykład jeśli chcesz przechowywać dane w postaci zaszyfrowanej na klientów urządzeń przenośnych, można zdefiniować magazynu lokalnego, który używa SQLCipher szyfrowania.

## <a name="what-is-a-sync-context"></a>Co to jest kontekst synchronizacji?
A *kontekstu synchronizacji* jest skojarzony z obiektem klientów urządzeń przenośnych (takich jak `IMobileServiceClient` lub `MSClient`) i śledzi zmiany wprowadzone w tabelach synchronizacji. Kontekst synchronizacji zapewnia *kolejka operację*, co pozwala uporządkowaną listę operacji CUD (tworzenia, aktualizacji, usuwania), zostanie wysłane do serwera.

Magazyn lokalny jest skojarzony z kontekstem synchronizacji, takie jak za pomocą metody inicjowania `IMobileServicesSyncContext.InitializeAsync(localstore)` w [Klient .NET SDK].

## <a name="how-sync-works"></a>Jak w trybie offline działania synchronizacji
Korzystając z tabel synchronizacji, kod klienta określa, kiedy lokalne zmiany są synchronizowane z zaplecza aplikacji mobilnej Azure. Nic nie są wysyłane do wewnętrznej bazy danych, dopóki jest to wywołanie *wypychania* zmian lokalnych. Podobnie, Magazyn lokalny jest wypełniane przy użyciu nowych danych tylko wtedy, gdy wywołanie *ściągania* danych.

* **Wypychania**: operacja jest w kontekście synchronizacji i wysyła wszystkie zmiany CUD od ostatniego powiadomienia wypychanego. Należy pamiętać, że nie jest możliwe wysyłanie tylko zmiany pojedynczej tabeli, ponieważ w przeciwnym razie operacje mógł zostać wysłany poza kolejnością. Wypychania wykonuje szereg wywołań REST zaplecza aplikacji mobilnej Azure, która z kolei modyfikuje bazy danych serwera.
* **Ściąganie**: ściągania odbywa się na konkretnych tabel i można dostosować za pomocą zapytanie w celu pobrania tylko podzestaw danych serwera. Zestawy SDK klienta usługi Azure Mobile wstawić do lokalnego magazynu danych.
* **Niejawne Wypchnięć**: w przypadku ściąganie jest wykonywane względem tabeli, która ma oczekujące aktualizacje lokalnej, ściągania najpierw wykonuje `push()` kontekstu synchronizacji. To zamówienie minimalizuje konfliktów między zmiany, które już są umieszczane w kolejce i nowych danych z serwera.
* **Synchronizacja przyrostowa**: pierwszy parametr do operacji ściągania jest *nazwa zapytania* używany tylko na kliencie. Jeśli używasz nazwę inną niż null kwerendy, zestaw SDK usługi Azure Mobile wykonuje *synchronizacja przyrostowa*. Zawsze operacji ściągania zwraca zestaw wyników, najnowsze `updatedAt` sygnatury czasowej z tego zestawu wyników są przechowywane w tabelach systemu lokalnego zestawu SDK. Ściągania kolejnych operacji pobierania rekordów tylko po tym sygnatury czasowej.

  Aby użyć synchronizacji przyrostowej, serwer musi zwracać łatwy do rozpoznania `updatedAt` wartości i muszą również obsługiwać sortowania według tego pola. Jednak ponieważ zestawu SDK dodaje własny sortowania na pole updatedAt, nie można użyć ściągania kwerendę, która ma własną `orderBy` klauzuli.

  Nazwa zapytania może być dowolnym ciągiem, wybrane, ale musi być unikatowa dla każdej logicznej kwerendy w aplikacji.
  W przeciwnym razie ściągania różne operacje można zastąpić tą samą sygnaturą czasową synchronizacji przyrostowej i Twojego zapytania mogą zwracać niepoprawne wyniki.

  Jeśli zapytanie zawiera parametr, wartość parametru jest jednym ze sposobów tworzenia nazwę unikatową kwerendy.
  Na przykład jeśli filtrowania na userid, nazwę kwerendy można w następujący sposób (w języku C#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Jeśli chcesz zrezygnować z synchronizacji przyrostowej, Przekaż `null` jako identyfikator zapytania. W takim przypadku wszystkie rekordy są pobierane przy każdym wywołaniu do `PullAsync`, który jest potencjalnie nieefektywne.
* **Przeczyszczanie**: można wyczyścić zawartość w lokalnym magazynie przy użyciu `IMobileServiceSyncTable.PurgeAsync`.
  Przeczyszczanie może być konieczne, jeśli masz stare dane w bazie danych klienta, czy chcesz odrzucić wszystkie zmiany oczekujące.

  Wyczyść usuwa tabelę z lokalnego magazynu. W przypadku operacji oczekiwanie na synchronizację z bazy danych serwera przeczyszczanie zgłasza wyjątek, chyba że *wymusić przeczyszczania* ustawiono parametr.

  Na przykład stare dane klienta Załóżmy, że w tym przykładzie "Lista czynności do wykonania" Device1 pobiera tylko elementy, które nie zostały zakończone. Czynność do wykonania "Kup mleka" jest oznaczona jako zakończona na serwerze przez inne urządzenie. Jednak Device1 nadal ma todoitem "Kup mleka" w lokalnym magazynie, ponieważ jest on tylko ściąganie elementy, które nie są oznaczone jako ukończone. Przeczyszczanie usuwa ten element stare.

## <a name="next-steps"></a>Kolejne kroki
* [iOS: Włączanie synchronizacji w trybie offline]
* [Xamarin iOS: Włączanie synchronizacji w trybie offline]
* [Dla systemu Xamarin Android: Włączanie synchronizacji w trybie offline]
* [Platforma uniwersalna systemu Windows: Włączanie synchronizacji w trybie offline]

<!-- Links -->
[Klient .NET SDK]: app-service-mobile-dotnet-how-to-use-client-library.md
[System android: Włączanie synchronizacji w trybie offline]: app-service-mobile-android-get-started-offline-data.md
[iOS: Włączanie synchronizacji w trybie offline]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: Włączanie synchronizacji w trybie offline]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Dla systemu Xamarin Android: Włączanie synchronizacji w trybie offline]: app-service-mobile-xamarin-android-get-started-offline-data.md
[Platforma uniwersalna systemu Windows: Włączanie synchronizacji w trybie offline]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
