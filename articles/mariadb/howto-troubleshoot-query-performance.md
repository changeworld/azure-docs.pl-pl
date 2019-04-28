---
title: Jak rozwiązywać problemy z wydajnością zapytań w usłudze Azure Database dla serwera MariaDB
description: W tym artykule opisano, jak rozwiązywać problemy z wydajnością zapytań w usłudze Azure Database dla serwera MariaDB za pomocą wyjaśnienia.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 672635c8d8c84fa16c106ae79e97332fd740928d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60745166"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mariadb"></a>Jak za pomocą wyjaśnienia profil wydajności zapytań w usłudze Azure Database dla serwera MariaDB
**WYJAŚNIONO** jest przydatnym narzędziem do optymalizacji zapytań. WYJAŚNIĆ, że instrukcja może być używana w celu uzyskania informacji o jak instrukcje SQL są wykonywane. Następujące dane wyjściowe zawierają przykład wykonywania instrukcji wyjaśnienia.

```sql
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 10.00
        Extra: Using where
```

Jak widać w tym przykładzie wartość *klucz* ma wartość NULL. Te dane wyjściowe oznacza MariaDB nie można odnaleźć żadnych indeksów, zoptymalizowane pod kątem zapytania i wykona pełne skanowanie tabeli. Umożliwia optymalizowanie tego zapytania, dodając indeksu na **identyfikator** kolumny.

```sql
mysql> ALTER TABLE tb1 ADD KEY (id);
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ref
possible_keys: id
          key: id
      key_len: 4
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
```

Nowe wyjaśnienia pokazuje, że MariaDB używa teraz indeksu ograniczenia liczby wierszy do 1, co z kolei znacznie skrócony czas wyszukiwania.
 
## <a name="covering-index"></a>Obejmujących indeksu
Indeks obejmujący składa się ze wszystkich kolumn z zapytania w indeksie, aby ograniczyć pobieranie wartości z tabelami danych. Oto ilustrację poniżej **GROUP BY** instrukcji.
 
```sql
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

Jak wynika z danych wyjściowych, MariaDB nie używa żadnych indeksów, żadne odpowiednie indeksy nie są dostępne. Pokazuje także *przy użyciu tymczasowego; Przy użyciu pliku sortowania*, co oznacza, że MariaDB tworzy tymczasową tabelę do zaspokojenia **GROUP BY** klauzuli.
 
Tworzenie indeksu w kolumnie **c2** samodzielnie sprawia, że nie różnicy i MariaDB nadal konieczna utworzyć tabelę tymczasową:

```sql 
mysql> ALTER TABLE tb1 ADD KEY (c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

W tym przypadku **ustawy indeksu** zarówno **c1** i **c2** można tworzyć, według których dodanie wartości **c2**"bezpośrednio w indeksie, aby Dodatkowo eliminują wyszukiwania danych.

```sql 
mysql> ALTER TABLE tb1 ADD KEY covered(c1,c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: covered
          key: covered
      key_len: 108
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using index
```

Jak pokazano na powyższym wyjaśnienia, MariaDB teraz używa indeksu objęte usługą i unikanie tworzenia tabeli tymczasowej. 

## <a name="combined-index"></a>Połączone indeksu
Indeksu złożonego składa się wartości z wielu kolumn i jest uznawana za tablicę wierszy, które są sortowane według złączenie wartości indeksowanych kolumn. Ta metoda może być przydatne w **GROUP BY** instrukcji.

```sql
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using filesort
```

Wykonuje MariaDB *sortowania pliku* operacji, która jest dość wolne, szczególnie gdy obsługuje sortowanie wielu wierszy. W celu zoptymalizowania tego zapytania, można utworzyć połączone indeks na obie kolumny, które są sortowane.

```sql 
mysql> ALTER TABLE tb1 ADD KEY my_sort2 (c1, c2);
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: NULL
          key: my_sort2
      key_len: 108
          ref: NULL
         rows: 10
     filtered: 11.11
        Extra: Using where; Using index
```

WYJAŚNIENIE teraz pokazuje, że MariaDB jest mogli używać indeksu złożonego, aby uniknąć dodatkowych sortowanie, ponieważ jest już sortowana indeksu.
 
## <a name="conclusion"></a>Podsumowanie
 
Przy użyciu wyjaśnienia i innego typu indeksów może znacząco zwiększyć wydajność. Po prostu, ponieważ masz indeksu w tabeli nie musi oznaczać, że MariaDB można go używać w zapytaniach. Zawsze sprawdzić za pomocą wyjaśnienie założeń i optymalizowania zapytań przy użyciu indeksów.

## <a name="next-steps"></a>Kolejne kroki
- Aby znaleźć elementu równorzędnego odpowiedzi na pytania dotyczące najbardziej interesujących lub zadać nowe pytanie/odpowiedź, odwiedź stronę [forum MSDN dotyczącym](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDatabaseforMariadb) lub [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mariadb).
