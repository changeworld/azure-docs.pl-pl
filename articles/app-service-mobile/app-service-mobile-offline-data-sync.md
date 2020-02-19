---
title: Synchronizacja danych w trybie offline
description: Dokumentacja koncepcyjna i Omówienie funkcji synchronizacji danych w trybie offline dla usługi Azure Mobile Apps
author: conceptdev
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.openlocfilehash: 0cc4309fa57a29997bdd2f650634efd0723e6965
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77458753"
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps

## <a name="what-is-offline-data-sync"></a>Co to jest synchronizacja danych w trybie offline?
Synchronizacja danych w trybie offline to funkcja zestawu SDK klienta i serwera systemu Azure Mobile Apps, która ułatwia deweloperom tworzenie aplikacji, które są funkcjonalne bez połączenia sieciowego.

Gdy aplikacja działa w trybie offline, można nadal tworzyć i modyfikować dane, które są zapisywane w magazynie lokalnym. Gdy aplikacja wraca do trybu online, może synchronizować lokalne zmiany z zapleczem aplikacji mobilnej platformy Azure. Ta funkcja obejmuje również obsługę wykrywania konfliktów w przypadku zmiany tego samego rekordu zarówno na kliencie, jak i w zapleczu. Konflikty mogą być następnie obsługiwane na serwerze lub kliencie.

Synchronizacja w trybie offline ma kilka zalet:

* Zwiększ czas odpowiedzi aplikacji, buforując dane serwera lokalnie na urządzeniu
* Twórz niezawodne aplikacje, które pozostają przydatne w przypadku problemów z siecią
* Zezwalaj użytkownikom końcowym na tworzenie i modyfikowanie danych nawet wtedy, gdy nie ma dostępu do sieci, obsługa scenariuszy z małą lub bez łączności
* Synchronizuj dane na wielu urządzeniach i wykrywaj konflikty, gdy ten sam rekord jest modyfikowany przez dwa urządzenia
* Ograniczanie użycia sieci w przypadku dużych opóźnień lub sieci taryfowych

W poniższych samouczkach pokazano, jak dodać synchronizację offline do klientów mobilnych przy użyciu usługi Azure Mobile Apps:

* [Android: Włączanie synchronizacji w trybie offline]
* [Apache Cordova: Włączanie synchronizacji w trybie offline](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: Włączanie synchronizacji w trybie offline]
* [Xamarin iOS: Włączanie synchronizacji w trybie offline]
* [Xamarin Android: Włączanie synchronizacji w trybie offline]
* [Xamarin. Forms: Włączanie synchronizacji w trybie offline](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Platforma uniwersalna systemu Windows: Włączanie synchronizacji w trybie offline]

## <a name="what-is-a-sync-table"></a>Co to jest tabela synchronizacji?
Aby uzyskać dostęp do punktu końcowego "/Tables", zestawy SDK klienta mobilnego platformy Azure udostępniają takie interfejsy jak `IMobileServiceTable` (zestaw SDK klienta platformy .NET) lub `MSTable` (klient systemu iOS). Te interfejsy API łączą się bezpośrednio z zapleczem aplikacji mobilnej platformy Azure i kończą się niepowodzeniem, jeśli urządzenie klienckie nie ma połączenia sieciowego.

Aby można było korzystać z używania w trybie offline, aplikacja powinna zamiast tego używać interfejsów API *tabel synchronizacji* , takich jak `IMobileServiceSyncTable` (zestaw SDK klienta platformy .NET) lub `MSSyncTable` (klient systemu iOS). Wszystkie te same operacje CRUD (tworzenie, odczytywanie, aktualizowanie, usuwanie) działają względem interfejsów API tabel synchronizacji, z wyjątkiem tego, że są teraz odczytywane z lub zapisu do *magazynu lokalnego*. Przed wykonaniem jakiejkolwiek operacji synchronizacji tabeli należy zainicjować magazyn lokalny.

## <a name="what-is-a-local-store"></a>Co to jest magazyn lokalny?
Magazyn lokalny jest warstwą trwałości danych na urządzeniu klienckim. Zestawy SDK klienta Mobile Apps platformy Azure zapewniają domyślną implementację magazynu lokalnego. W systemach Windows, Xamarin i Android jest oparta na oprogramowaniu SQLite. W systemie iOS jest oparta na podstawowych danych.

Aby użyć implementacji opartej na programie SQLite na Windows Phone lub Microsoft Store, należy zainstalować rozszerzenie SQLite. Aby uzyskać więcej informacji, zobacz [platforma uniwersalna systemu Windows: Włączanie synchronizacji w trybie offline]. Systemy Android i iOS zawierają wersję oprogramowania SQLite w systemie operacyjnym urządzenia, dlatego nie jest konieczne odwoływanie się do własnej wersji oprogramowania SQLite.

Deweloperzy mogą również zaimplementować swój własny magazyn lokalny. Na przykład jeśli chcesz przechowywać dane w szyfrowanym formacie na kliencie mobilnym, możesz zdefiniować magazyn lokalny, który używa elementu SQLCIPHER do szyfrowania.

## <a name="what-is-a-sync-context"></a>Co to jest kontekst synchronizacji?
*Kontekst synchronizacji* jest skojarzony z obiektem klienta mobilnego (takim jak `IMobileServiceClient` lub `MSClient`) i śledzi zmiany wprowadzone w tabelach synchronizacji. Kontekst synchronizacji utrzymuje *kolejkę operacji*, która zachowuje uporządkowaną listę operacji cud (tworzenie, aktualizowanie, usuwanie), które są później wysyłane do serwera.

Magazyn lokalny jest skojarzony z kontekstem synchronizacji przy użyciu metody inicjacji, takiej jak `IMobileServicesSyncContext.InitializeAsync(localstore)` w [Zestaw SDK klienta platformy .NET].

## <a name="how-sync-works"></a>Jak działa synchronizacja w trybie offline
W przypadku korzystania z tabel synchronizacji kod klienta kontroluje, kiedy lokalne zmiany są synchronizowane z zapleczem aplikacji mobilnej platformy Azure. Żadne elementy nie są wysyłane do zaplecza do momentu wywołania operacji *wypychania* zmian lokalnych. Podobnie magazyn lokalny jest wypełniany nowymi danymi tylko wtedy, gdy istnieje wywołanie do *ściągania* danych.

* **Wypychanie**: wypychanie to operacja w kontekście synchronizacji i wysyła wszystkie zmiany cud od momentu ostatniego wypchnięcia. Należy zauważyć, że nie jest możliwe wysyłanie tylko zmian w jednej tabeli, ponieważ operacje w przeciwnym razie mogą być wysyłane poza kolejnością. Wypychanie wykonuje serię wywołań REST do zaplecza aplikacji mobilnej platformy Azure, co z kolei modyfikuje bazę danych serwera.
* **Ściąganie**: Ściąganie odbywa się na podstawie tabeli i można je dostosować za pomocą zapytania, aby pobrać tylko podzestaw danych serwera. Zestawy SDK klienta mobilnego platformy Azure wstawiają dane uzyskane do magazynu lokalnego.
* **Niejawne wypychania**: Jeśli ściąganie jest wykonywane względem tabeli, która ma oczekujące aktualizacje lokalne, ściąganie wykonuje najpierw `push()` w kontekście synchronizacji. Ta wypychanie pomaga zminimalizować konflikty między zmianami, które zostały już dodane do kolejki i nowymi danymi z serwera.
* **Synchronizacja przyrostowa**: pierwszy parametr operacji ściągania jest *nazwą zapytania* , która jest używana tylko przez klienta. Jeśli używasz nazwy zapytania o wartości innej niż null, zestaw Azure Mobile SDK wykonuje *synchronizację przyrostową*. Za każdym razem, gdy operacja ściągania zwraca zestaw wyników, Najnowsza sygnatura czasowa `updatedAt` z tego zestawu wyników jest przechowywana w lokalnych tabelach systemowych zestawu SDK. Kolejne operacje ściągania pobierają tylko rekordy po tej sygnaturze czasowej.

  Aby można było użyć synchronizacji przyrostowej, serwer musi zwrócić znaczące wartości `updatedAt` i musi również obsługiwać sortowanie według tego pola. Ponieważ jednak zestaw SDK dodaje własne sortowanie w polu updatedAt, nie można użyć zapytania ściągania, które ma własną klauzulę `orderBy`.

  Nazwa zapytania może być dowolnym wybranym ciągiem, ale musi być unikatowa dla każdej kwerendy logicznej w aplikacji.
  W przeciwnym razie różne operacje ściągnięcia mogą zastąpić tę samą sygnaturę czasową synchronizacji przyrostowej, a zapytania mogą zwracać nieprawidłowe wyniki.

  Jeśli zapytanie ma parametr, jeden ze sposobów tworzenia unikatowej nazwy zapytania polega na uwzględnieniu wartości parametru.
  Na przykład w przypadku filtrowania według identyfikatora użytkownika nazwa zapytania może być następująca (w C#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Jeśli chcesz zrezygnować z synchronizacji przyrostowej, Przekaż `null` jako identyfikator zapytania. W takim przypadku wszystkie rekordy są pobierane dla każdego wywołania do `PullAsync`, co jest potencjalnie niewydajne.
* **Przeczyszczanie**: możesz wyczyścić zawartość lokalnego magazynu przy użyciu `IMobileServiceSyncTable.PurgeAsync`.
  Przeczyszczanie może być konieczne, jeśli w bazie danych klienta znajdują się stare dane, lub jeśli chcesz odrzucić wszystkie oczekujące zmiany.

  Przeczyszczanie czyści tabelę z magazynu lokalnego. Jeśli istnieją operacje czekające na synchronizację z bazą danych serwera, przeczyszczanie zgłasza wyjątek, chyba że jest ustawiony parametr *Wymuszaj przeczyszczanie* .

  Przykładowo nieodświeżone dane na kliencie Przypuśćmy na przykład "Lista zadań do wykonania", Device1 pobiera tylko elementy, które nie zostały ukończone. TodoItem "wykupuj mleko" zostało oznaczone jako ukończone na serwerze przez inne urządzenie. Jednak Device1 nadal ma TodoItem "Kup mleko" w magazynie lokalnym, ponieważ pobiera tylko elementy, które nie są oznaczone jako ukończone. Przeczyszczanie czyści ten nieodświeżony element.

## <a name="next-steps"></a>Następne kroki
* [iOS: Włączanie synchronizacji w trybie offline]
* [Xamarin iOS: Włączanie synchronizacji w trybie offline]
* [Xamarin Android: Włączanie synchronizacji w trybie offline]
* [Platforma uniwersalna systemu Windows: Włączanie synchronizacji w trybie offline]

<!-- Links -->
[Zestaw SDK klienta platformy .NET]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Włączanie synchronizacji w trybie offline]: app-service-mobile-android-get-started-offline-data.md
[iOS: Włączanie synchronizacji w trybie offline]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: Włączanie synchronizacji w trybie offline]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: Włączanie synchronizacji w trybie offline]: app-service-mobile-xamarin-android-get-started-offline-data.md
[Platforma uniwersalna systemu Windows: Włączanie synchronizacji w trybie offline]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
