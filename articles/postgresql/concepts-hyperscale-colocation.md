---
title: Pojęcia serwera w usłudze Azure Database for PostgreSQL
description: Ten artykuł zawiera zagadnienia i wytyczne dotyczące konfigurowania i zarządzanie usługą Azure Database for postgresql w warstwie serwerów.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: d03cfd49887adf1f6a4650e374d3e13eeca735a4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077473"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Wspólnej tabeli w usłudze Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza)

Wspólnej oznacza, zapisując powiązane informacje ze sobą w tej samej węzłów. Zapytania można przejść, szybko w przypadku, gdy wszystkie niezbędne dane są dostępne bez żadnego ruchu sieciowego. Kolokowanie powiązanych danych w różnych węzłach umożliwia zapytania, aby wydajnie uruchamiać równolegle na każdym węźle.

## <a name="data-colocation-for-hash-distributed-tables"></a>Wspólnej danych dla tabel rozproszonych wyznaczania wartości skrótu

W Hiperskali wiersza są przechowywane w jednym fragmencie z Jeśli skrót wartości w kolumnie dystrybucji, który znajduje się w zakresie skrótów fragmentu. Fragmenty z tego samego zakresu wyznaczania wartości skrótu są zawsze umieszczane w tym samym węźle. Wiersze z równomierną dystrybucję wartości kolumn są zawsze w tym samym węźle między tabelami.

![Fragmentów](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>Praktyczny przykład wspólnej

Należy wziąć pod uwagę następujące tabele, które mogą być częścią sieci web z wieloma dzierżawami usługi analytics SaaS:

```sql
CREATE TABLE event (
  tenant_id int,
  event_id bigint,
  page_id int,
  payload jsonb,
  primary key (tenant_id, event_id)
);

CREATE TABLE page (
  tenant_id int,
  page_id int,
  path text,
  primary key (tenant_id, page_id)
);
```

Teraz chcemy do odpowiadania na kwerendy, które mogą być wydane przez pulpit nawigacyjny przeznaczonych dla klientów, takie jak: "Zwraca liczba wizyt w ostatnim tygodniu dla wszystkich stron, rozpoczynając od" / blog "w dzierżawie sześciu."

Jeśli dane w opcji wdrożenia pojedynczego serwera, firma Microsoft może łatwo express naszego zapytania przy użyciu bogatego zestawu operacji relacyjnych oferowanych przez program SQL:

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

Tak długo, jak [zestaw roboczy](https://en.wikipedia.org/wiki/Working_set) dla tego zapytania mieści się w pamięci, tabela jednym serwerze jest to odpowiednie rozwiązanie. Rozważmy jednak możliwości skalowania w modelu danych przy użyciu opcji wdrożenia na dużą skalę.

### <a name="distributing-tables-by-id"></a>Dystrybucja tabel według Identyfikatora

Zapytania na jednym serwerze uruchom spowalniania wzrostem liczby dzierżawców i dane przechowywane dla każdego dzierżawcy. Zestaw roboczy zatrzymuje w pamięci i procesora CPU staje się wąskim gardłem.

W tym przypadku możemy dzielenia danych na wielu węzłach przy użyciu na dużą skalę. Pierwszy i najważniejszy wybór musimy upewnić się, gdy fragmentowania jest kolumna dystrybucji. Zacznijmy od prostego wyboru używania `event_id` dla tabeli zdarzeń i `page_id` dla `page` tabeli:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

Gdy dane są rozprowadzane przez różne procesy robocze, firma Microsoft nie można wykonać sprzężenia, jak firma Microsoft z jednego węzła PostgreSQL. Zamiast tego konieczne będzie wysyłać dwa zapytania:

```sql
-- (Q1) get the relevant page_ids
SELECT page_id FROM page WHERE path LIKE '/blog%' AND tenant_id = 6;

-- (Q2) get the counts
SELECT page_id, count(*) AS count
FROM event
WHERE page_id IN (/*…page IDs from first query…*/)
  AND tenant_id = 6
  AND (payload->>'time')::date >= now() - interval '1 week'
GROUP BY page_id ORDER BY count DESC LIMIT 10;
```

Później wyniki z dwóch kroków muszą zostać połączone przez aplikację.

Uruchamianie zapytania zapoznać się z danych we fragmentach rozproszone w węzłach.

![Niewydajne kwerendy](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

W takim przypadku dystrybucję danych tworzy istotne wady:

-   Obciążenie związane z każdego fragmentu i wykonywania zapytań w wielu zapytań
-   Obciążenie Q1 zwracanie wielu wierszy do klienta
-   Q2 staje się duże
-   Konieczność pisania zapytań w wielu kroków wymaga wprowadzenia zmian w aplikacji

Ponieważ dane są rozprowadzane, zapytania mogą być przetwarzane równolegle. Jednak jest tylko korzystne, jeśli ilość pracy, który wykonuje zapytanie jest znacznie większa niż koszty związane z zapytań wiele fragmentów.

### <a name="distributing-tables-by-tenant"></a>Dystrybucja tabel przez dzierżawcę

W Hiperskali wiersze z taką samą wartość kolumny dystrybucji zagwarantowane jest występowanie na tym samym węźle. Zacznij od nowa, możemy utworzyć naszych tabel z `tenant_id` jako kolumna dystrybucji.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Teraz na dużą skalę może odpowiedzieć oryginalne zapytanie pojedynczego serwera bez żadnych modyfikacji (P1):

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

Z powodu filtru i przyłączenia tenant_id Hiperskali wie, że całe zapytanie należy odpowiedzieć przy użyciu zestawu wspólnie przechowywane fragmentów, które zawierają dane dla tej konkretnej dzierżawy. Jeden węzeł PostgreSQL pozwala uzyskać odpowiedzi na zapytania w jednym kroku.

![lepsze zapytania](media/concepts-hyperscale-colocation/colocation-better-query.png)

W niektórych przypadkach należy zmienić zapytań i schematy tabeli do uwzględnia Identyfikatora dzierżawy w unikatowych ograniczeń, a następnie dołącz warunków. Jednak zazwyczaj jest to prosta zmiana.

## <a name="next-steps"></a>Kolejne kroki

- Zobacz, jak wspólnie przechowywane dane dzierżawy w [samouczek wielu dzierżaw](tutorial-design-database-hyperscale-multi-tenant.md)
