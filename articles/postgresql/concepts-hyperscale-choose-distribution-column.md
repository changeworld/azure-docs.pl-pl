---
title: Wybieranie kolumn dystrybucji — hiperskala (Citus) — usługa Azure Database for PostgreSQL
description: Dowiedz się, jak wybrać kolumny dystrybucji w typowych scenariuszach hiperskali w usłudze Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 8ced9767d81affceef851820ee587f4f3dd24deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975673"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus"></a>Wybieranie kolumn dystrybucyjnych w usłudze Azure Database for PostgreSQL — hiperskala (Citus)

Wybór kolumny dystrybucji każdej tabeli jest jedną z najważniejszych decyzji modelowania, które podejmiesz. Usługa Azure Database for PostgreSQL — hiperskali (Citus) przechowuje wiersze w fragmentach na podstawie wartości kolumny dystrybucji wierszy.

Poprawny wybór grupuje powiązane dane razem w tych samych węzłach fizycznych, co sprawia, że zapytania są szybkie i dodaje obsługę wszystkich funkcji SQL. Niepoprawny wybór sprawia, że system działa wolno i nie obsługuje wszystkich funkcji SQL w węzłach.

W tym artykule przedstawiono porady kolumny dystrybucji dla dwóch najczęściej hiperskala (Citus) scenariuszy.

### <a name="multi-tenant-apps"></a>Aplikacje z wieloma dzierżawami

Architektura wielodostępna używa formy hierarchicznego modelowania bazy danych do dystrybucji zapytań między węzłami w grupie serwerów. Górna część hierarchii danych jest znana jako *identyfikator dzierżawy* i musi być przechowywana w kolumnie w każdej tabeli.

Hiperskala (Citus) sprawdza zapytania, aby zobaczyć, jaki identyfikator dzierżawy obejmują i znajduje pasujący fragment tabeli. Kieruje kwerendę do pojedynczego węzła roboczego, który zawiera fragment. Uruchamianie kwerendy ze wszystkimi odpowiednimi danymi umieszczonymi w tym samym węźle jest nazywane kolokacji.

Na poniższym diagramie przedstawiono kolokacji w modelu danych wielu dzierżawców. Zawiera dwie tabele, Konta i kampanie, każda dystrybuowana przez `account_id`. Cieniowane pola reprezentują fragmenty. Zielone fragmenty są przechowywane razem w jednym węźle procesu roboczego, a niebieskie fragmenty są przechowywane w innym węźle procesu roboczego. Zwróć uwagę, jak kwerenda sprzężenia między kontami i kampaniami ma\_wszystkie niezbędne dane razem w jednym węźle, gdy obie tabele są ograniczone do tego samego identyfikatora konta.

![Kolokacja wielodostępna](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Aby zastosować ten projekt we własnym schemacie, należy zidentyfikować, co stanowi dzierżawę w aplikacji. Typowe wystąpienia obejmują firmę, konto, organizację lub klienta. Nazwa kolumny będzie wyglądać `company_id` `customer_id`jak lub . Sprawdź każdy z zapytań i zadać sobie pytanie, czy to działa, jeśli miał dodatkowe klauzule WHERE, aby ograniczyć wszystkie tabele zaangażowane do wierszy z tym samym identyfikatorem dzierżawy?
Zapytania w modelu wielodostępnym są ograniczone do dzierżawy. Na przykład zapytania dotyczące sprzedaży lub zapasów są objęte zakresem w określonym magazynie.

#### <a name="best-practices"></a>Najlepsze rozwiązania

-   **Partycja tabel rozproszonych\_przez typową kolumnę identyfikatora dzierżawy.** Na przykład w aplikacji SaaS, gdzie dzierżawcy\_są firmy, identyfikator dzierżawy może być identyfikator firmy.\_
-   **Konwertuj małe tabele między dzierżawcami na tabele odwołań.** Gdy wielu dzierżawców współużytkuje małą tabelę informacji, rozpowszechniaj ją jako tabelę odwołań.
-   **Ogranicz filtrowanie wszystkich\_zapytań aplikacji według identyfikatora dzierżawy.** Każda kwerenda powinna żądać informacji dla jednej dzierżawy w czasie.

Przeczytaj [samouczek z wieloma dzierżawami,](./tutorial-design-database-hyperscale-multi-tenant.md) aby zapoznać się z przykładem tworzenia tego rodzaju aplikacji.

### <a name="real-time-apps"></a>Aplikacje w czasie rzeczywistym

Architektura wielodostępna wprowadza strukturę hierarchiczną i używa kolokacji danych do kierowania zapytań na dzierżawę. Z drugiej strony architektury w czasie rzeczywistym zależą od określonych właściwości dystrybucji ich danych, aby osiągnąć wysoce równoległe przetwarzanie.

Używamy "identyfikator jednostki" jako termin dla kolumn dystrybucji w modelu czasu rzeczywistego. Typowe jednostki to użytkownicy, hosty lub urządzenia.

Zapytania w czasie rzeczywistym zazwyczaj pytają o agregaty liczbowe pogrupowane według daty lub kategorii. Hiperskala (Citus) wysyła te zapytania do każdego niezależnego fragmentu w celu częściowego uzyskania wyników i zestawi ostateczną odpowiedź w węźle koordynatora. Zapytania działają najszybciej, gdy jak najwięcej węzłów przyczynić się, jak to możliwe, a gdy żaden węzeł nie musi wykonywać nieproporcjonalnie dużo pracy.

#### <a name="best-practices"></a>Najlepsze rozwiązania

-   **Wybierz kolumnę o wysokiej kardynalności jako kolumnę dystrybucji.** Dla porównania pole Stan w tabeli zamówień o wartościach Nowe, Płatne i Wysłane jest złym wyborem kolumny dystrybucji. Przyjęto tylko te kilka wartości, co ogranicza liczbę fragmentów, które mogą przechowywać dane i liczbę węzłów, które mogą je przetwarzać. Wśród kolumn o wysokiej kardynalności warto również wybrać te kolumny, które są często używane w klauzulach grupy według lub jako klucze sprzężenia.
-   **Wybierz kolumnę z równomiernym rozkładem.** Jeśli rozmieszczasz tabelę w kolumnie pochylony do niektórych wspólnych wartości, dane w tabeli ma tendencję do gromadzenia się w niektórych fragmentów. Węzły, które przechowują te fragmenty kończy się robi więcej pracy niż inne węzły.
-   **Rozesłaj tabele faktów i wymiarów w ich wspólnych kolumnach.**
    Tabela faktów może mieć tylko jeden klucz dystrybucji. Tabele, które łączą się z innym kluczem, nie zostaną przydzielone z tabelą faktów. Wybierz jeden wymiar do kolokacji na podstawie tego, jak często jest połączony i rozmiar wierszy łączących.
-   **Zmień niektóre tabele wymiarów w tabele odwołań.** Jeśli tabeli wymiarów nie można współlokować z tabelą faktów, można zwiększyć wydajność kwerendy, rozmieszczając kopie tabeli wymiarów do wszystkich węzłów w formie tabeli odwołań.

Przeczytaj [samouczek pulpitu nawigacyjnego](./tutorial-design-database-hyperscale-realtime.md) w czasie rzeczywistym, aby dowiedzieć się, jak utworzyć tego rodzaju aplikację.

### <a name="time-series-data"></a>Dane szeregów czasowych

W obciążeniu szeregów czasowych aplikacje pytają o najnowsze informacje podczas archiwizowania starych informacji.

Najczęstszym błędem w modelowaniu informacji o szeregach czasowych w hiperskali (Citus) jest użycie sygnatury czasowej jako kolumny dystrybucji. Rozkład mieszania na podstawie czasu dystrybuuje czasy pozornie losowo na różne fragmenty, a nie utrzymywanie zakresów czasu razem w fragmenty. Zapytania, które obejmują czas zazwyczaj odwołać zakresy czasu, na przykład najnowsze dane. Ten typ dystrybucji mieszania prowadzi do narzutu sieciowego.

#### <a name="best-practices"></a>Najlepsze rozwiązania

-   **Nie wybieraj sygnatury czasowej jako kolumny dystrybucji.** Wybierz inną kolumnę dystrybucji. W aplikacji z wieloma dzierżawami użyj identyfikatora dzierżawy lub w aplikacji w czasie rzeczywistym użyj identyfikatora jednostki.
-   **Zamiast tego użyj partycjonowania tabeli PostgreSQL.** Partycjonowanie tabeli służy do podziału dużej tabeli uporządkowanych danych czasowych na wiele tabel dziedziczonych z każdą tabelą zawierającą różne zakresy czasu. Dystrybucja tabeli podzielonej na partycje po przekroju w hiperskali (Citus) tworzy fragmenty dla tabel dziedziczonych.

Przeczytaj [samouczek szeregów czasowych,](https://aka.ms/hyperscale-tutorial-timeseries) aby dowiedzieć się, jak utworzyć tego rodzaju aplikację.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [współlokacja](concepts-hyperscale-colocation.md) między rozproszonymi danymi pomaga szybko uruchamiać zapytania.
