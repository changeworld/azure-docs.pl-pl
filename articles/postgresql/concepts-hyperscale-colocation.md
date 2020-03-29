---
title: Kolokacji tabeli — hiperskala (Citus) — usługa Azure Database for PostgreSQL
description: Jak przechowywać powiązane informacje razem dla szybszych zapytań
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7e4073ec45f4c21f33d20924a9948e72f961c7f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74967341"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>Współlokowanie tabeli w bazie danych azure dla postgreSQL — hiperskala (Citus)

Kolokacja oznacza przechowywanie powiązanych informacji razem w tych samych węzłach. Zapytania mogą być szybkie, gdy wszystkie niezbędne dane są dostępne bez ruchu sieciowego. Kolocowanie powiązanych danych w różnych węzłach umożliwia efektywne uruchamianie zapytań równolegle w każdym węźle.

## <a name="data-colocation-for-hash-distributed-tables"></a>Współlokowanie danych dla tabel rozproszonych skrótami

W usłudze Azure Database for PostgreSQL — Hiperskala (Citus) wiersz jest przechowywany w niezależnej części, jeśli skrót wartości w kolumnie dystrybucji mieści się w zakresie mieszania fragmentu. Fragmenty o tym samym zakresie skrótu są zawsze umieszczane w tym samym węźle. Wiersze z równymi wartościami kolumn rozkładu są zawsze w tym samym węźle w tabelach.

![Odłamki](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>Praktyczny przykład kolokacji

Należy wziąć pod uwagę następujące tabele, które mogą być częścią analizy sieci web wielu dzierżawców SaaS:

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

Teraz chcemy odpowiedzieć na pytania, które mogą być wydawane przez pulpit nawigacyjny skierowany do klienta. Przykładowe zapytanie to "Zwraca liczbę wizyt w ubiegłym tygodniu dla wszystkich stron, począwszy od '/blog' w dzierżawie sześć."

Jeśli nasze dane były w opcji wdrażania z jednym serwerem, możemy łatwo wyrazić nasze zapytanie przy użyciu bogatego zestawu operacji relacyjnych oferowanych przez SQL:

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

Tak długo, jak [zestaw roboczy](https://en.wikipedia.org/wiki/Working_set) dla tej kwerendy mieści się w pamięci, tabela jednego serwera jest odpowiednim rozwiązaniem. Rozważmy możliwości skalowania modelu danych za pomocą opcji wdrażania w skali hiperskali (Citus).

### <a name="distribute-tables-by-id"></a>Rozmieszczaj tabele według identyfikatora

Zapytania z jednym serwerem zaczynają spowalniać wraz ze wzrostem liczby dzierżawców i danych przechowywanych dla każdej dzierżawy. Zestaw roboczy przestaje być dopasowywanie w pamięci i procesora CPU staje się wąskim gardłem.

W takim przypadku możemy fragmenty danych w wielu węzłach przy użyciu hiperskali (Citus). Pierwszym i najważniejszym wyborem, który musimy podjąć, gdy zdecydujemy się na niezależną odłamek, jest kolumna dystrybucji. Zacznijmy od naiwnego wyboru użycia `event_id` tabeli zdarzeń `page_id` i `page` tabeli:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

Gdy dane są rozproszone między różnymi pracownikami, nie możemy wykonać sprzężenia, tak jak w jednym węźle PostgreSQL. Zamiast tego musimy wydać dwa zapytania:

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

Następnie wyniki z dwóch kroków muszą być połączone przez aplikację.

Uruchamianie kwerend musi konsultować się z danymi w fragmentach rozproszonych między węzłami.

![Nieefektywne zapytania](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

W takim przypadku dystrybucja danych powoduje istotne wady:

-   Obciążenie z wykonywania zapytań o każdy fragment i uruchamianie wielu zapytań.
-   Obciążenie Q1 zwraca wiele wierszy do klienta.
-   Q2 staje się duży.
-   Konieczność pisania zapytań w wielu krokach wymaga zmian w aplikacji.

Dane są rozproszone, dzięki czemu kwerendy mogą być równoległe. Jest to korzystne tylko wtedy, gdy ilość pracy, która wykonuje kwerenda jest znacznie większa niż obciążenie związane z wykonywaniem kwerend przez wiele fragmentów.

### <a name="distribute-tables-by-tenant"></a>Rozdzielanie tabel według dzierżawy

W hiperskali (Citus) wiersze o tej samej wartości kolumny dystrybucji są gwarantowane w tym samym węźle. Począwszy od początku, możemy `tenant_id` tworzyć nasze tabele z jako kolumna dystrybucji.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Teraz Hyperscale (Citus) może odpowiedzieć na oryginalne zapytanie z jednym serwerem bez modyfikacji (Q1):

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

Ze względu na filtr i sprzężenia na tenant_id Hyperscale (Citus) wie, że na całe zapytanie można odpowiedzieć przy użyciu zestawu współlokowanych fragmentów, które zawierają dane dla tej konkretnej dzierżawy. Pojedynczy węzeł PostgreSQL może odpowiedzieć na kwerendę w jednym kroku.

![Lepsze zapytanie](media/concepts-hyperscale-colocation/colocation-better-query.png)

W niektórych przypadkach kwerendy i schematy tabel muszą zostać zmienione, aby uwzględnić identyfikator dzierżawy w unikatowych ograniczeniach i warunkach sprzężenia. Ta zmiana jest zwykle prosta.

## <a name="next-steps"></a>Następne kroki

- Zobacz, jak dane dzierżawy są współlokowane w [samouczku dla wielu dzierżaw](tutorial-design-database-hyperscale-multi-tenant.md).
