---
title: Dane rozproszone — hiperskala (Citus) — usługa Azure Database for PostgreSQL
description: Dowiedz się więcej o tabelach rozproszonych, tabelach referencyjnych, tabelach lokalnych i fragmentach w usłudze Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: ade7632dc042741a07bdb59e34e30b3fb464e0e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243655"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus"></a>Dane rozproszone w bazie danych platformy Azure dla postgreSQL — hiperskala (Citus)

W tym artykule opisano trzy typy tabel w usłudze Azure Database for PostgreSQL — Hiperskala (Citus).
Pokazuje, jak tabele rozproszone są przechowywane jako fragmenty i sposób, w jaki fragmenty są umieszczane w węzłach.

## <a name="table-types"></a>Typy tabel

Istnieją trzy typy tabel w grupie serwerów w skali hiperskali (Citus), z których każda jest używana do różnych celów.

### <a name="type-1-distributed-tables"></a>Typ 1: Tabele rozproszone

Pierwszy typ i najczęściej to tabele rozproszone. Wydają się być normalne tabele instrukcji SQL, ale są one poziomo podzielonych na partycje między węzłami procesu roboczego. Oznacza to, że wiersze tabeli są przechowywane w różnych węzłach, w tabelach fragmentów o nazwie fragmenty.

Hiperskala (Citus) uruchamia nie tylko instrukcje SQL, ale DDL w całym klastrze.
Zmiana schematu tabeli rozproszonej kaskady, aby zaktualizować wszystkie fragmenty tabeli w procesach pracy.

#### <a name="distribution-column"></a>Kolumna Dystrybucja

Hiperskala (Citus) używa algorytmicznych dzielenia na fragmenty do przypisywania wierszy do fragmentów. Przypisanie jest ustalane na podstawie wartości kolumny tabeli zwanej kolumną dystrybucyjną. Administrator klastra musi wyznaczyć tę kolumnę podczas dystrybucji tabeli.
Dokonanie właściwego wyboru jest ważne dla wydajności i funkcjonalności.

### <a name="type-2-reference-tables"></a>Typ 2: Tabele odwołań

Tabela odwołań jest typem tabeli rozproszonej, której cała zawartość jest skoncentrowana w pojedynczy fragment. Fragment jest replikowany na każdym roboczym. Zapytania dotyczące dowolnego procesu roboczego mogą uzyskiwać dostęp do informacji referencyjnych lokalnie, bez narzutu sieciowego żądającego wierszy z innego węzła. Tabele odwołań nie mają kolumny dystrybucji, ponieważ nie ma potrzeby rozróżniania oddzielnych fragmentów na wiersz.

Tabele odwołań są zazwyczaj małe i są używane do przechowywania danych, które są istotne dla zapytań uruchomionych w dowolnym węźle procesu roboczego. Przykładem są wyliczone wartości, takie jak stany zamówień lub kategorie produktów.

### <a name="type-3-local-tables"></a>Typ 3: Tabele lokalne

W przypadku korzystania z hiperskali (Citus) węzeł koordynatora, z którym się łączysz, jest zwykłą bazą danych PostgreSQL. Można utworzyć zwykłe tabele na koordynatora i nie wybrać, aby ich odłamków.

Dobrym kandydatem do tabel lokalnych będą małe tabele administracyjne, które nie uczestniczą w zapytaniach sprzężenia. Przykładem jest tabela użytkowników do logowania i uwierzytelniania aplikacji.

## <a name="shards"></a>Odłamki

W poprzedniej sekcji opisano, jak tabele rozproszone są przechowywane jako fragmenty w węzłach procesu roboczego. W tej sekcji omówiono więcej szczegółów technicznych.

Tabela `pg_dist_shard` metadanych na koordynatorze zawiera wiersz dla każdego fragmentu każdej tabeli rozproszonej w systemie. Wiersz dopasowuje identyfikator niezależnego fragmentu z zakresem liczby całkowitej w przestrzeni mieszania (shardminvalue, shardmaxvalue).

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

Jeśli węzeł koordynatora chce określić, który `github_events`fragment zawiera wiersz , to skróty wartość kolumny dystrybucji w wierszu. Następnie węzeł sprawdza,\'który zakres shard zawiera wartość mieszaną. Zakresy są zdefiniowane tak, aby obraz funkcji mieszania jest ich unii rozłączne.

### <a name="shard-placements"></a>Miejsca docelowe niezależnego fragmentu

Załóżmy, że niezależnego fragmentu 102027 jest skojarzony z danym wierszem. Wiersz jest odczytywany lub zapisywany w tabeli o nazwie `github_events_102027` w jednym z pracowników. Który pracownik? To zależy wyłącznie od tabel metadanych. Mapowanie niezależnego fragmentu do procesu roboczego jest znane jako umieszczenie niezależnego fragmentu.

Węzeł koordynatora przepisuje kwerendy na fragmenty, `github_events_102027` które odwołują się do określonych tabel, takich jak i uruchamia te fragmenty na odpowiednich procesach pracy. Oto przykład kwerendy uruchamianej za kulisami, aby znaleźć węzeł przytrzymując identyfikator niezależnego fragmentu 102027.

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

    ┌─────────┬───────────┬──────────┐
    │ shardid │ nodename  │ nodeport │
    ├─────────┼───────────┼──────────┤
    │  102027 │ localhost │     5433 │
    └─────────┴───────────┴──────────┘

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [wybrać kolumnę dystrybucyjną](concepts-hyperscale-choose-distribution-column.md) dla tabel rozproszonych.
