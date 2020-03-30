---
title: Synchronizacja danych w trybie offline
description: Odwołanie koncepcyjne i omówienie funkcji synchronizacji danych w trybie offline dla aplikacji Azure Mobile Apps
author: conceptdev
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.openlocfilehash: 0cc4309fa57a29997bdd2f650634efd0723e6965
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458753"
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps

## <a name="what-is-offline-data-sync"></a>Co to jest synchronizacja danych w trybie offline?
Synchronizacja danych w trybie offline to funkcja SDK klienta i serwera usługi Azure Mobile Apps, która ułatwia deweloperom tworzenie aplikacji, które działają bez połączenia sieciowego.

Gdy aplikacja jest w trybie offline, nadal można tworzyć i modyfikować dane, które są zapisywane w magazynie lokalnym. Gdy aplikacja jest z powrotem w trybie online, można synchronizować zmiany lokalne z wewnętrznej bazy danych aplikacji azure mobile. Funkcja ta obejmuje również obsługę wykrywania konfliktów, gdy ten sam rekord jest zmieniany zarówno na kliencie, jak i w wewnętrznej bazy danych. Konflikty mogą być następnie obsługiwane na serwerze lub na kliencie.

Synchronizacja w trybie offline ma kilka zalet:

* Zwiększ szybkość reakcji aplikacji, buforując dane serwera lokalnie na urządzeniu
* Tworzenie niezawodnych aplikacji, które pozostają przydatne w przypadku problemów z siecią
* Zezwalaj użytkownikom końcowym na tworzenie i modyfikowanie danych nawet wtedy, gdy nie ma dostępu do sieci, obsługując scenariusze z niewielką lub żadną łącznością
* Synchronizowanie danych na wielu urządzeniach i wykrywanie konfliktów, gdy ten sam rekord jest modyfikowany przez dwa urządzenia
* Ograniczanie korzystania z sieci w sieciach o dużym opóźnieniu lub pomiarach

Poniższe samouczki pokazują, jak dodać synchronizację w trybie offline do klientów mobilnych przy użyciu aplikacji azure mobile:

* [Android: włącz synchronizację w trybie offline]
* [Apache Cordova: Włącz synchronizację w trybie offline](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: Włącz synchronizację w trybie offline]
* [Xamarin iOS: Włącz synchronizację w trybie offline]
* [System Android platformy Xamarin: włącz synchronizację w trybie offline]
* [Xamarin.Forms: Włącz synchronizację w trybie offline](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Uniwersalna platforma systemu Windows: włącz synchronizację w trybie offline]

## <a name="what-is-a-sync-table"></a>Co to jest tabela synchronizacji?
Aby uzyskać dostęp do punktu końcowego "/tabel", zestawów SDK klienta usługi Azure Mobile udostępnia interfejsy, takie jak `IMobileServiceTable` (net client SDK) lub `MSTable` (klient systemu iOS). Te interfejsy API łączą się bezpośrednio z zaplecza aplikacji Azure Mobile i zakończyć się niepowodzeniem, jeśli urządzenie klienckie nie ma połączenia sieciowego.

Aby obsługiwać użycie w trybie offline, aplikacja powinna zamiast `IMobileServiceSyncTable` tego używać interfejsów API `MSSyncTable` *tabeli synchronizacji,* takich jak (.NET client SDK) lub (klient systemu iOS). Wszystkie te same operacje CRUD (Tworzenie, Odczyt, Aktualizacja, Usuwanie) działają na interfejsach API tabeli synchronizacji, z wyjątkiem teraz, gdy czytają z lokalnego magazynu lub zapisują je w *magazynie lokalnym.* Przed wykonaniem jakichkolwiek operacji tabeli synchronizacji należy zainicjować magazyn lokalny.

## <a name="what-is-a-local-store"></a>Co to jest sklep lokalny?
Magazyn lokalny to warstwa trwałości danych na urządzeniu klienckim. Zestawy SDK klienta usługi Azure Mobile Apps zapewniają domyślną implementację magazynu lokalnego. W systemach Windows, Xamarin i Android jest oparty na SQLite. W uos iOS jest on oparty na podstawowych danych.

Aby korzystać z implementacji sqlite w systemie Windows Phone lub Microsoft Store, należy zainstalować rozszerzenie SQLite. Aby uzyskać więcej informacji, zobacz [Uniwersalna platforma systemu Windows: Włącz synchronizację w trybie offline]. Android i iOS są dostarczane z wersją SQLite w samym systemie operacyjnym urządzenia, więc nie jest konieczne odwoływanie się do własnej wersji SQLite.

Deweloperzy mogą również implementować własny sklep lokalny. Na przykład jeśli chcesz przechowywać dane w formacie zaszyfrowanym na kliencie mobilnym, można zdefiniować magazyn lokalny, który używa SQLCipher do szyfrowania.

## <a name="what-is-a-sync-context"></a>Co to jest kontekst synchronizacji?
*Kontekst synchronizacji* jest skojarzony z obiektem `IMobileServiceClient` `MSClient`klienta mobilnego (takim jak lub ) i śledzi zmiany wprowadzone za pomocą tabel synchronizacji. Kontekst synchronizacji utrzymuje *kolejkę operacji,* która przechowuje uporządkowaną listę operacji CUD (Tworzenie, Aktualizacja, Usuwanie), która jest później wysyłana do serwera.

Magazyn lokalny jest skojarzony z kontekstem synchronizacji `IMobileServicesSyncContext.InitializeAsync(localstore)` przy użyciu metody inicjowania, takiej jak [sdk klienta .NET.]

## <a name="how-offline-synchronization-works"></a><a name="how-sync-works"></a>Jak działa synchronizacja w trybie offline
Podczas korzystania z tabel synchronizacji kod klienta kontroluje, gdy lokalne zmiany są synchronizowane z zaplecza aplikacji azure mobile. Nic nie jest wysyłane do wewnętrznej bazy danych, dopóki nie zostanie wywołanie *wypchnięcia* lokalnych zmian. Podobnie magazyn lokalny jest wypełniany nowymi danymi tylko wtedy, gdy istnieje wywołanie *danych ściągania.*

* **Push:** Push jest operacją w kontekście synchronizacji i wysyła wszystkie zmiany CUD od ostatniego wypychania. Należy zauważyć, że nie jest możliwe wysyłanie tylko zmiany poszczególnych tabeli, ponieważ w przeciwnym razie operacje mogą być wysyłane poza kolejnością. Wypychanie wykonuje serię wywołań REST do wewnętrznej bazy danych aplikacji Azure Mobile, co z kolei modyfikuje bazę danych serwera.
* **Ściąganie:** Ściąganie jest wykonywane na podstawie tabeli i można je dostosować za pomocą kwerendy w celu pobrania tylko podzbioru danych serwera. Zestawy SDK klienta usługi Azure Mobile następnie wstawiają wynikowe dane do magazynu lokalnego.
* **Niejawne wypychania:** Jeśli ściąganie jest wykonywane względem tabeli, `push()` która oczekuje na aktualizacje lokalne, ściąganie najpierw wykonuje w kontekście synchronizacji. To wypychanie pomaga zminimalizować konflikty między zmianami, które są już w kolejce i nowe dane z serwera.
* **Synchronizacja przyrostowa:** pierwszym parametrem operacji ściągania jest *nazwa kwerendy,* która jest używana tylko na kliencie. Jeśli używasz nazwy kwerendy innej niż null, zestaw SDK azure mobile wykonuje *synchronizację przyrostową.* Za każdym razem, gdy operacja ściągania `updatedAt` zwraca zestaw wyników, ostatni sygnatura czasowa z tego zestawu wyników jest przechowywana w tabelach systemu lokalnego zestawu SDK. Kolejne operacje ściągania pobierają tylko rekordy po tym sygnaturze czasowej.

  Aby użyć synchronizacji przyrostowej, serwer `updatedAt` musi zwracać znaczące wartości i musi również obsługiwać sortowanie według tego pola. Jednak ponieważ SDK dodaje swój własny sort w polu updatedAt, nie można `orderBy` użyć kwerendy ściągającej, która ma własną klauzulę.

  Nazwa kwerendy może być dowolny ciąg wybrać, ale musi być unikatowy dla każdej kwerendy logicznej w aplikacji.
  W przeciwnym razie różne operacje ściągania może zastąpić ten sam sygnatura czasowa synchronizacji przyrostowej i kwerendy mogą zwracać niepoprawne wyniki.

  Jeśli kwerenda ma parametr, jednym ze sposobów utworzenia unikatowej nazwy kwerendy jest włączenie wartości parametru.
  Na przykład, jeśli filtrujesz na userid, nazwa kwerendy może być następująca (w języku C#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Jeśli chcesz zrezygnować z synchronizacji przyrostowej, `null` przekaż jako identyfikator kwerendy. W takim przypadku wszystkie rekordy są pobierane przy każdym wywołaniu , `PullAsync`co jest potencjalnie nieefektywne.
* **Czyszczenie:** Zawartość lokalnego sklepu można wyczyścić `IMobileServiceSyncTable.PurgeAsync`za pomocą .
  Przeczyszczanie może być konieczne, jeśli masz przestarzałe dane w bazie danych klienta lub jeśli chcesz odrzucić wszystkie oczekujące zmiany.

  Czyszczenie czyści tabelę z lokalnego sklepu. Jeśli istnieją operacje oczekujące na synchronizację z bazą danych serwera, przeczyszczanie zgłasza wyjątek, chyba że ustawiono parametr *przeczyszczania siły.*

  Jako przykład starych danych na kliencie, załóżmy w przykładzie "lista zadań do wykonania", Device1 ściąga tylko elementy, które nie zostały ukończone. Todoitem "Kup mleko" jest oznaczony na serwerze przez inne urządzenie. Jednak Device1 nadal ma todoitem "Kup mleko" w lokalnym sklepie, ponieważ ciągnie tylko przedmioty, które nie są oznaczone jako kompletne. Przeczyszczanie czyści ten nieświeży element.

## <a name="next-steps"></a>Następne kroki
* [iOS: Włącz synchronizację w trybie offline]
* [Xamarin iOS: Włącz synchronizację w trybie offline]
* [System Android platformy Xamarin: włącz synchronizację w trybie offline]
* [Uniwersalna platforma systemu Windows: włącz synchronizację w trybie offline]

<!-- Links -->
[SDK klienta platformy .NET]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: włącz synchronizację w trybie offline]: app-service-mobile-android-get-started-offline-data.md
[iOS: Włącz synchronizację w trybie offline]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: Włącz synchronizację w trybie offline]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[System Android platformy Xamarin: włącz synchronizację w trybie offline]: app-service-mobile-xamarin-android-get-started-offline-data.md
[Uniwersalna platforma systemu Windows: włącz synchronizację w trybie offline]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
