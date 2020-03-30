---
title: Rozwiązywanie problemów z wydajnością kwerend — usługa Azure Database for MariaDB
description: Dowiedz się, jak używać funkcji OBJ. w celu rozwiązywania problemów z wydajnością kwerend w usłudze Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: b06fe37b63494eb4ee0ca680733a801c26415d67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530057"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mariadb"></a>Jak używać funkcji EXPLAIN do wykonywania zapytań profilowych w usłudze Azure Database for MariaDB
**EXPLAIN** to przydatne narzędzie do optymalizacji zapytań. Instrukcja EXPLAIN może służyć do uzyskania informacji o tym, jak instrukcje SQL są wykonywane. Poniższe dane wyjściowe przedstawia przykład wykonywania explain instrukcji.

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

Jak widać w tym przykładzie, wartość *klucza* jest NULL. To dane wyjściowe oznacza MariaDB nie można znaleźć żadnych indeksów zoptymalizowanych dla kwerendy i wykonuje skanowanie pełnej tabeli. Zoptymalizujmy tę kwerendę, dodając indeks w kolumnie **Identyfikator.**

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

Nowe EXPLAIN pokazuje, że MariaDB teraz używa indeksu, aby ograniczyć liczbę wierszy do 1, co z kolei znacznie skróciło czas wyszukiwania.
 
## <a name="covering-index"></a>Indeks obejmujący
Indeks obejmujący składa się ze wszystkich kolumn kwerendy w indeksie, aby zmniejszyć pobieranie wartości z tabel danych. Oto ilustracja w poniższej instrukcji **GROUP BY.**
 
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

Jak widać na danych wyjściowych, MariaDB nie używa żadnych indeksów, ponieważ nie odpowiednie indeksy są dostępne. Pokazuje również *korzystanie z tymczasowych; Za pomocą sortowania plików*, co oznacza, że MariaDB tworzy tabelę tymczasową, aby spełnić **klauzulę GROUP BY.**
 
Tworzenie indeksu w kolumnie **c2** sam nie ma znaczenia, i MariaDB nadal musi utworzyć tabelę tymczasową:

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

W takim przypadku można utworzyć **indeks objęty** **c1** i **c2,** przy czym dodanie wartości **c2**" bezpośrednio w indeksie w celu wyeliminowania dalszego wyszukiwania danych.

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

Jak pokazuje powyższe wyjaśnienie, MariaDB używa teraz zadaszonego indeksu i unika tworzenia tabeli tymczasowej. 

## <a name="combined-index"></a>Indeks łączony
Połączony indeks składa się z wartości z wielu kolumn i można go uznać za tablicę wierszy, które są sortowane przez łączenie wartości indeksowanych kolumn.Ta metoda może być przydatna w instrukcji **GROUP BY.**

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

MariaDB wykonuje operację *sortowania plików,* która jest dość powolna, zwłaszcza gdy ma do sortowania wielu wierszy. Aby zoptymalizować tę kwerendę, można utworzyć połączony indeks na obu kolumnach, które są sortowane.

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

EXPLAIN teraz pokazuje, że MariaDB jest w stanie użyć połączonego indeksu, aby uniknąć dodatkowego sortowania, ponieważ indeks jest już posortowane.
 
## <a name="conclusion"></a>Podsumowanie
 
Za pomocą EXPLAIN i innego typu indeksy można znacznie zwiększyć wydajność. Posiadanie indeksu w tabeli nie musi oznaczać, że MariaDB będzie mógł go używać do zapytań. Zawsze sprawdzaj poprawność założeń przy użyciu funkcji OBJ. i optymalizuj zapytania przy użyciu indeksów.

## <a name="next-steps"></a>Następne kroki
- Aby znaleźć odpowiedzi na najbardziej zainteresowanych pytań lub opublikować nowe pytanie / odpowiedź, odwiedź [forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDatabaseforMariadb) lub [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mariadb).
