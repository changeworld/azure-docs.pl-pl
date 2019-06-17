---
title: Wybierz kolumny dystrybucji w usłudze Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza)
description: Dobrych wyborów dla kolumn dystrybucji w typowych scenariuszach w hiperskali
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: e9fba14b8979f739fd29bc277e32fb544221d08a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65078988"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Wybierz kolumny dystrybucji w usłudze Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza)

Wybór kolumny dystrybucji każdej tabeli jest **jedną z najważniejszych** modelowania decyzji. W Hiperskali przechowuje wierszy we fragmentach, na podstawie wartości kolumny dystrybucji wiersze.

Odpowiedni wybór grup powiązanych danych razem na tym samym węzłów fizycznych, dzięki czemu zapytania szybki i dodanie obsługi dla wszystkich funkcji programu SQL. Nieprawidłowy wybór sprawia, że system będzie działał wolno i nie obsługują wszystkie funkcje SQL w węzłach.

W tej sekcji przedstawiono rozkład porady kolumny dla dwóch najbardziej typowych scenariuszy w Hiperskali.

### <a name="multi-tenant-apps"></a>Aplikacje z wieloma dzierżawami

Architektury wielodostępnej korzysta z formy hierarchicznej modelowania, aby rozdystrybuować zapytania węzły w grupie serwera w bazie danych.  Hierarchii danych jest znany jako *identyfikator dzierżawy*i musi być przechowywany w kolumnie dla każdej tabeli.

W Hiperskali bada zapytań, aby wyświetlić identyfikator dzierżawy, które obejmują i umożliwia znalezienie pasującego fragmentu tabeli. Kieruje zapytania do węzła pojedynczego procesu roboczego, który zawiera fragmentu. Uruchamianie zapytania przy użyciu wszystkich odpowiednich danych umieszczony na tym samym węźle nosi nazwę wspólną lokalizację.

Na poniższym diagramie przedstawiono wspólną lokalizację, w modelu danych z wieloma dzierżawami. Zawiera on dwie tabele, konta i kampanii, każda dystrybucja `account_id`. Zacieniony pola reprezentują fragmentów, z którego kolor każdy reprezentuje węzeł procesu roboczego, który go zawiera. Zielony fragmenty są przechowywane razem na węzeł jednego procesu roboczego i niebieski na innym. Zwróć uwagę, jak zapytanie sprzężenia między kontami i kampanii miałby wszystkich niezbędnych danych ze sobą w jednym węźle po ograniczeniu obu tabel do tego samego konta\_identyfikator.

![wielodostępne wspólnej](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Aby zastosować ten projekt w własny schemat, należy zidentyfikować, co stanowi dzierżawy w aplikacji. Typowe wystąpienia obejmują firmy, konto, organizację lub klientów. Nazwa kolumny będzie to wyglądać `company_id` lub `customer_id`. Zbadanie wszystkich zapytań, i spróbuj odpowiedzieć sobie: będzie działać, jakby dodatkowe klauzulach WHERE, aby ograniczyć wszystkich tabel, których dotyczy do wierszy z tym samym Identyfikatorem dzierżawy?
Zapytania w modelu wielodostępnym są ograniczone do dzierżawy, na przykład zapytań dotyczących sprzedaży lub magazynu będzie ograniczone w niektórych magazynie.

#### <a name="best-practices"></a>Najlepsze rozwiązania

-   **Partycja rozproszone tabele według wspólnych dzierżawy\_kolumny identyfikatora.** Na przykład w aplikacji SaaS, w których firmy, dzierżawa dzierżaw\_identyfikator będzie prawdopodobnie firmy\_identyfikator.
-   **Tabele odwołań przekonwertować małe tabele w wielu dzierżawach.** Wiele dzierżaw współużytkuje to mały tabelę z informacjami o, dystrybuować je jako tabeli referencyjnej.
-   **Ograniczenia filtru wszystkich aplikacja wykonuje zapytania przez dzierżawcę\_identyfikator.** Każda kwerenda powinien zażądać informacji dla jednej dzierżawy w danym momencie.

Odczyt [samouczek wielodostępnych](./tutorial-design-database-hyperscale-multi-tenant.md) przykładem tworzenia tego rodzaju aplikacji.

### <a name="real-time-apps"></a>Aplikacje w czasie rzeczywistym

Architektury wielodostępnej wprowadza hierarchicznej struktury i korzysta z danych wspólną lokalizację, aby zapytania na dzierżawę. Z drugiej strony architektury w czasie rzeczywistym są zależne od właściwości dystrybucji ich danych w celu osiągnięcia wysokiej równoległego przetwarzania.

Używamy "Identyfikatorem jednostki" jako termin dla dystrybucji kolumn w modelu w czasie rzeczywistym. Typowe jednostki są użytkownicy, hostów lub urządzeń.

Zapytania w czasie rzeczywistym jest zazwyczaj poprosić o liczbowe wartości zagregowane, pogrupowane według daty lub kategorii. W Hiperskali wysyła zapytania do każdego fragmentu, aby uzyskać wyniki częściowe i składa końcowego odpowiedzi na węźle koordynatora. Zapytania wykonywane najszybciej po zgodnie z wieloma węzłami współtworzyć, jak to możliwe, a nie pojedynczego węzła należy wykonać nieproporcjonalnie duża ilość pracy.

#### <a name="best-practices"></a>Najlepsze rozwiązania

-   **Wybierz kolumnę o dużej kardynalności, jako kolumna dystrybucji.** Dla porównania \"stan\" pole w tabeli zamówienia z wartościami "new", "płatnych" i "wysłane" zły wybór kolumny dystrybucji. Zakłada tylko kilka wartości, co ogranicza liczbę fragmentów, które mogą zawierać dane, a liczba węzłów, które mogą go przetworzyć. Wśród kolumn o dużej kardynalności warto również wybrać te, które są często używane klauzul group by lub join kluczy.
-   **Wybierz kolumnę z rozdzielenie.** Jeśli dystrybuować tabelę według kolumny nierówne niektóre typowe wartości danych w tabeli zostaną mają tendencję do są gromadzone w niektórych fragmentów. Wykonując więcej pracy niż inne węzły ostatecznie węzłów zawierający tych fragmentach.
-   **Dystrybucja tabel faktów i wymiarów na ich wspólnych kolumn.**
    Tabela faktów, może mieć tylko jeden klucz dystrybucji. Tabele, które odbędzie się inny klucz nie będą wspólnie przechowywane z tabelą faktów. Wybierz jeden wymiar razem na taśmie, na podstawie częstotliwości jest dołączony i rozmiar przyłączany wierszy.
-   **Zmienić niektóre tabele wymiarów w tabelach odwołań.** Jeśli tabela wymiarów nie może być wspólnie przechowywane z tabelą faktów, może poprawić wydajność zapytań, przekazując kopii tabeli wymiarów, aby wszystkie węzły w postaci tabeli referencyjnej.

Odczyt [samouczek pulpitu nawigacyjnego w czasie rzeczywistym](./tutorial-design-database-hyperscale-realtime.md) przykładem tworzenia tego rodzaju aplikacji.

### <a name="timeseries-data"></a>Szeregi czasowe danych

W obciążeniu szeregów czasowych aplikacje zapytania aktualnymi informacjami podczas archiwizowania stare informacje.

Najbardziej powszechnym modelowanie informacji Szeregi czasowe w Hiperskali korzysta z sygnatury czasowej, sama jako kolumna dystrybucji. Dystrybucji skrótów, na podstawie czasu będzie dystrybuować razy pozornie w losowo wybranym momencie w różnych fragmentach niż utrzymywanie zakresy czasu razem we fragmentach. Zapytania obejmujące czasu ogólnie odwoływać się do zakresów czasu (na przykład najnowszych danych), więc takie dystrybucji skrótów doprowadziłoby do błędów sieci obciążenie.

#### <a name="best-practices"></a>Najlepsze rozwiązania

-   **Nie należy wybierać sygnaturę czasową jako kolumna dystrybucji.** Wybierz kolumnę innej dystrybucji. W aplikacji z wieloma dzierżawami Użyj Identyfikatora dzierżawy lub w aplikacji w czasie rzeczywistym za pomocą identyfikatora jednostki.
-   **Za pomocą PostgreSQL Partycjonowanie tabel raz zamiast tego.** Użyj Partycjonowanie tabel, aby Podziel duże tabelę danych uporządkowanej w czasie na wielu tabel dziedziczonych przez poszczególne zawierający zakresów w innym czasie.  Dystrybucja Postgres są podzielone na partycje tabeli w Hiperskali tworzy fragmentów dla tabeli dziedziczenia.

Odczyt [samouczek Szeregi czasowe](https://aka.ms/hyperscale-tutorial-timeseries) przykładem tworzenia tego rodzaju aplikacji.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak [wspólnej](concepts-hyperscale-colocation.md) między pomaga danych rozproszonych zapytań działają szybko
