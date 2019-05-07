---
title: Rozproszone dane w usłudze Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza)
description: Tabele i fragmentów rozpowszechniane w grupie serwerów.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 9020ee690d93a1b477471fac4a482a909fca5935
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077338"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Rozproszone dane w usłudze Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza)

W tym artykule opisano typy trzy tabeli w Hiperskali (Citus).
Pokazuje, jak rozproszone tabele są przechowywane jako fragmentów i sposób fragmentów są umieszczane w węzłach.

## <a name="table-types"></a>Typy tabel

Istnieją trzy typy tabel w grupie serwerów na dużą skalę, każdy obiekt używany do różnych celów.

### <a name="type-1-distributed-tables"></a>Typu 1: rozproszone tabele

Pierwszy typ i najczęściej występujące *rozproszonych* tabel. Wydaje się normalnych tabel do instrukcji SQL, ale poziomie *partycjonowane* w węzłach procesu roboczego. Oznacza to, że wiersze w tabeli są przechowywane w różnych węzłach w tabelach fragment o nazwie *fragmentów*.

W Hiperskali działa nie tylko SQL, ale instrukcji DDL w całym klastrze, więc zmiany schematu tabeli rozproszonych dokonuje kaskadowych można zaktualizować wszystkich tabeli fragmentów dla pracowników.

#### <a name="distribution-column"></a>Kolumna dystrybucji

W Hiperskali przypisuje wierszy do fragmentów przy użyciu konsolidatorze fragmentowania. Sposób deterministyczny w oparciu o wartość kolumny tabeli o nazwie w przydziału *kolumny dystrybucji.* Podczas dystrybucji tabelę, administrator klastra należy wyznaczyć tej kolumny.
Tworzenie właściwym wyborem jest ważne w przypadku wydajności i funkcjonalności.

### <a name="type-2-reference-tables"></a>Typu 2: tabele odwołań

Tabela odwołania jest typu rozproszona tabela, której cała zawartość jest skoncentrowana do jednego fragmentu. Fragment jest replikowana na każdy pracownik, więc kwerendy dla każdego pracownika dostęp informacje lokalnie, bez obciążenie sieci, żądać wierszy z innego węzła. Tabele odwołań mieć żadnej kolumny dystrybucji, ponieważ nie ma potrzeby do rozróżniania osobnych fragmentach każdego wiersza.

Tabele odwołań są zwykle małe i są używane do przechowywania danych, które są istotne dla kwerendy uruchamianie na dowolnym węźle procesu roboczego. Na przykład wyliczonych wartości, takich jak Stany kolejności lub kategorie produktów.

### <a name="type-3-local-tables"></a>Typ 3: lokalne tablice

Gdy używasz w Hiperskali, węzeł koordynatora, z którymi są nawiązywane połączenia jest regularne bazy danych PostgreSQL. Można utworzyć zwykłej tabele dla koordynatora i zrezygnować z fragmentu je.

Dobrym kandydatem do tabel lokalnych będą małe administracyjne tabel, które nie są używane w zapytaniach sprzężenia. Na przykład tabela użytkowników do uwierzytelniania i logowania do aplikacji.

## <a name="shards"></a>Fragmentów

Poprzedniej sekcji opisano, jak rozproszone tabele są przechowywane jako fragmentów na węzłach procesu roboczego. W tej sekcji pobiera więcej szczegóły techniczne.

`pg_dist_shard` Tabeli metadanych dla koordynatora zawiera wiersz dla każdego fragmentu w każdej tabeli rozproszonych w systemie. Wiersz jest zgodny z Identyfikatorem fragmentu z zakresu liczb całkowitych w obszarze wyznaczania wartości skrótu (shardminvalue, shardmaxvalue):

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

Jeśli węzeł koordynujący chce, aby określić, który fragment zawiera wiersz `github_events`, miesza wartość kolumny dystrybucji, w tym wierszu i sprawdza, który fragment\'s zakres zawiera wartość skrótu. (Zakresy są zdefiniowane tak, aby obraz funkcji skrótu ich Unii rozłącznej).

### <a name="shard-placements"></a>Takie umieszczenie fragmentów

Załóżmy, że w tym fragmencie 102027 jest skojarzona z danego wiersza. Wiersz będzie odczytu lub zapisu w tabeli o nazwie `github_events_102027` w jednym pracowników. Których procesów roboczych? Który jest całkowicie tabel metadanych, i mapowania fragmentów do procesu roboczego jest znany jako fragment *umieszczania*.

Węzeł koordynatora ponownie zapisuje zapytania na fragmenty, które odwołują się do określonych tabel, takich jak `github_events_102027`, i uruchamia tych fragmentów dla odpowiednich procesów roboczych. Oto przykład kwerendy, uruchom w tle można odnaleźć węzła zawierający 102027 identyfikator fragmentu.

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

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak [wybierz kolumnę dystrybucji](concepts-hyperscale-choose-distribution-column.md) dla rozproszone tabele
