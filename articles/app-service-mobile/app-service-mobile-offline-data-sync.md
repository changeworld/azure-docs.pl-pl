---
title: Synchronizacja danych offline w usłudze Azure Mobile Apps | Dokumentacja firmy Microsoft
description: Przewodnik koncepcyjny i przegląd funkcji synchronizacji danych offline usługi Azure Mobile Apps
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
ms.openlocfilehash: ab8fb4a567e4c4a7bf1e884999a4e403a98547a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128019"
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps
## <a name="what-is-offline-data-sync"></a>Co to jest synchronizacja danych offline?
Synchronizacja danych offline jest klientem a serwerem funkcji zestawu SDK usługi Azure Mobile Apps, która ułatwia deweloperom tworzenie aplikacji, które będą funkcjonalne bez połączenia sieciowego.

Gdy Twoja aplikacja jest w trybie offline, można nadal tworzyć i modyfikować dane, które są zapisywane w magazynie lokalnym. Gdy aplikacja jest wróci do trybu online, może synchronizować zmiany lokalne, z zapleczem aplikacji mobilnej platformy Azure. Funkcja obejmuje również obsługę wykrywania konfliktów, gdy ten sam rekord zostanie zmieniony zarówno klient, jak i wewnętrznej bazy danych. Następnie można obsłużyć konflikty na serwerze lub kliencie.

Synchronizacja offline ma kilka zalet:

* Skróć czas reakcji aplikacji, buforując dane serwera lokalnie na urządzeniu
* Twórz odporne aplikacje, które będą pozostawały użyteczne, gdy istnieją problemy z siecią
* Zezwolić użytkownikom końcowym tworzyć i modyfikować dane nawet wtedy, gdy jest Brak dostępu do sieci, obsługi scenariuszy niewielkiego lub żadnego połączenia
* Synchronizowanie danych na wielu urządzeniach i wykrywania konfliktów, gdy ten sam rekord zostanie zmodyfikowany za pomocą dwóch urządzeń
* Ograniczenie użycia sieci w dużym opóźnieniem lub mierzonych sieciach

Następujące samouczki pokazują, jak dodawanie synchronizacji offline dla klientów mobilnych za pomocą usługi Azure Mobile Apps:

* [Android: Włączanie synchronizacji offline]
* [Apache Cordova: Włączanie synchronizacji offline](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: Włączanie synchronizacji offline]
* [Xamarin iOS: Włączanie synchronizacji offline]
* [Xamarin dla systemu Android: Włączanie synchronizacji offline]
* [Zestaw narzędzi Xamarin.Forms: Włączanie synchronizacji offline](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Platforma Universal Windows: Włączanie synchronizacji offline]

## <a name="what-is-a-sync-table"></a>Co to jest tabela synchronizacji?
Aby uzyskać dostęp do endpoint "/ tabele", zestawów SDK klienta usługi Azure Mobile obejmują interfejsy takich jak `IMobileServiceTable` (zestaw SDK klienta platformy .NET) lub `MSTable` (klient z systemem iOS). Te interfejsy API połączyć się bezpośrednio z zaplecza aplikacji mobilnej platformy Azure i zakończyć się niepowodzeniem, jeśli urządzenie klienckie nie ma połączenia sieciowego.

Aby zapewnić obsługę pracy w trybie offline, zamiast tego należy używać aplikacji *zsynchronizuj spis* interfejsów API, takich jak `IMobileServiceSyncTable` (zestaw SDK klienta platformy .NET) lub `MSSyncTable` (klient z systemem iOS). Wszystkie te same operacje CRUD (tworzenia, odczytu, Update, Delete) działają na synchronizacji tabeli interfejsów API, z wyjątkiem teraz mogą odczytywać lub zapisać *magazynu lokalnego*. Przed wykonaniem żadnych operacji tabeli synchronizacji magazynu lokalnego musi zostać zainicjowany.

## <a name="what-is-a-local-store"></a>Co to jest Magazyn lokalny?
Magazyn lokalny jest warstwę trwałości danych na urządzeniu klienckim. Zestawy SDK klienta usługi Azure Mobile Apps udostępnia domyślną implementację magazynu lokalnego. Windows, Xamarin i systemu Android opiera się na bazy danych SQLite. W systemach iOS jest on oparty na danych podstawowych.

Aby użyć implementacji oparty na programie SQLite na Windows Phone lub Microsoft Store, musisz zainstalować rozszerzenie bazy danych SQLite. Aby uzyskać więcej informacji, zobacz [Platforma Universal Windows: Włączanie synchronizacji offline]. Systemy android i iOS są dostarczane z wersję bazy danych SQLite w system operacyjny urządzenia, więc nie można odwoływać się do własnej wersji bazy danych SQLite.

Deweloperzy mogą także implementować własne magazynu lokalnego. Na przykład jeśli chcesz przechowywać dane w postaci zaszyfrowanej na kliencie mobilnym, można zdefiniować lokalny magazyn, który używa SQLCipher na potrzeby szyfrowania.

## <a name="what-is-a-sync-context"></a>Czym jest kontekst synchronizacji?
A *kontekst synchronizacji* jest skojarzony z obiektem klientów urządzeń przenośnych (takie jak `IMobileServiceClient` lub `MSClient`) i śledzi zmiany wprowadzone z tabelami synchronizacji. Utrzymuje kontekst synchronizacji *kolejki operacji*, który przechowuje listy uporządkowanej operacje CUD (Tworzenie, Update, Delete), później są wysyłane do serwera.

Magazyn lokalny jest skojarzony z kontekstem synchronizacji, takie jak za pomocą metody inicjowania `IMobileServicesSyncContext.InitializeAsync(localstore)` w [Zestaw SDK klienta platformy .NET].

## <a name="how-sync-works"></a>Jak w trybie offline działa synchronizacja
Korzystając z tabel synchronizacji, kod klienta określa, kiedy lokalne zmiany są synchronizowane z zapleczem aplikacji mobilnej platformy Azure. Nic nie są wysyłane do wewnętrznej bazy danych do momentu wywołania *wypychania* zmian lokalnych. Podobnie, magazynu lokalnego jest wypełniana przy użyciu nowych danych, tylko wtedy, gdy wywołanie *ściągnięcia* danych.

* **Wypychanie**: Wypychania jest operacją w kontekście synchronizacji i wysyła wszystkie zmiany CUD od czasu ostatniej replikacji wypychanej. Należy pamiętać, że nie jest możliwe do wysyłania tylko pojedyncza tabela zmian, ponieważ w przeciwnym razie operacje mogą być wysyłane poza kolejnością. Wypychane wykonuje szereg wywołania REST do zaplecza aplikacji mobilnej platformy Azure, która z kolei zmienia serwera bazy danych.
* **Ściągnij**: Ściągnięcia odbywa się na konkretnych tabel i można dostosować, używając zapytanie, aby pobrać tylko podzestaw danych serwera. Zestawy SDK klienta usługi Azure Mobile wstawić dane wynikowe do magazynu lokalnego.
* **Niejawne Wypchnięć**: Jeśli ściągnięcia jest wykonywane względem tabeli, która ma oczekujące aktualizacje lokalnych, ściągnięcia najpierw wykonuje `push()` kontekstu synchronizacji. To zamówienie minimalizuje konflikty między zmiany, które już są umieszczane w kolejce, a nowych danych z serwera.
* **Synchronizacja przyrostowa**: pierwszy parametr do operacji ściągania jest *nazwa_zapytania* używany tylko na komputerze klienckim. Jeśli używasz nazwę kwerendy innych niż null, zestaw SDK usług Azure Mobile wykonuje *synchronizacja przyrostowa*. Każdorazowo operacji ściągania zwraca zestaw wyników, najnowsze `updatedAt` sygnaturę czasową od tego zestawu wyników są przechowywane w tabelach systemu lokalnego zestawu SDK. Operacji ściągania kolejnych pobierać tylko rekordy po tej sygnaturze czasowej.

  Korzystać z synchronizacji przyrostowej, serwer musi zwracać istotnych `updatedAt` wartości, a także musi obsługiwać sortowania według tego pola. Jednak ponieważ zestaw SDK dodaje swoje własne sortowania według pola updatedAt, nie można użyć zapytań ściągania, który ma swój własny `orderBy` klauzuli.

  Nazwa zapytania może być dowolny ciąg, który wybierzesz, ale muszą być unikatowe dla każdego zapytania logiczne w aplikacji.
  W przeciwnym razie ściągnięcia innej operacji może spowodować zastąpienie tą samą sygnaturą czasową synchronizacja przyrostowa i zapytań może zwracać nieprawidłowe wyniki.

  Jeśli zapytanie zawiera parametr, jeden ze sposobów tworzenia unikatowych nazwa_zapytania jest wartość parametru.
  Na przykład w przypadku filtrowania na identyfikator użytkownika, nazwę kwerendy może być w następujący sposób (w języku C#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Jeśli chcesz zrezygnować z synchronizacja przyrostowa, przekazać `null` jako identyfikator zapytania. W tym przypadku pobierane są wszystkie rekordy na każde wywołanie `PullAsync`, który jest potencjalnie nieefektywne.
* **Przeczyszczanie**: Można wyczyścić zawartość za pomocą magazynu lokalnego `IMobileServiceSyncTable.PurgeAsync`.
  Trwałe usuwanie może być konieczne, jeśli zawierają stare dane w bazie danych klienta, czy chcesz odrzucić wszystkie oczekujące zmiany.

  Przeczyszczanie usuwa tabelę z magazynu lokalnego. W przypadku operacji oczekiwanie na synchronizację z bazą danych serwera przeczyszczanie zgłasza wyjątek, chyba że *wymuszenie przeczyszczenia* zestaw parametrów.

  Jako przykład nieaktualnych danych na komputerze klienckim Załóżmy, że w tym przykładzie "Lista czynności do wykonania" urządzenia 1 pobiera tylko elementy, które nie zostały zakończone. Czynność do wykonania "Kup mleka" jest oznaczony jako ukończone na serwerze przez inne urządzenie. Jednak urządzenia 1 środki, nieopłacone todoitem "Buy mleka" w magazynie lokalnym ponieważ ciągnie tylko elementy, które nie są oznaczone jako ukończone. Przeczyszczanie powoduje wyczyszczenie tego elementu starych.

## <a name="next-steps"></a>Kolejne kroki
* [iOS: Włączanie synchronizacji offline]
* [Xamarin iOS: Włączanie synchronizacji offline]
* [Xamarin dla systemu Android: Włączanie synchronizacji offline]
* [Platforma Universal Windows: Włączanie synchronizacji offline]

<!-- Links -->
[Zestaw SDK klienta platformy .NET]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Włączanie synchronizacji offline]: app-service-mobile-android-get-started-offline-data.md
[iOS: Włączanie synchronizacji offline]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: Włączanie synchronizacji offline]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin dla systemu Android: Włączanie synchronizacji offline]: app-service-mobile-xamarin-android-get-started-offline-data.md
[Platforma Universal Windows: Włączanie synchronizacji offline]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
