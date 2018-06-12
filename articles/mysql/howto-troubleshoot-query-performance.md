---
title: Jak rozwiązywać problemy z wydajność zapytań w bazie danych systemu Azure dla programu MySQL
description: W tym artykule opisano sposób użycia WYJAŚNIJ Rozwiązywanie problemów z wydajnością kwerendy w bazie danych Azure dla programu MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 72b047c37ac88e4b33c8723f8df14c6794e84399
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266180"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mysql"></a>Jak używać WYJAŚNIJ do profilu wydajności zapytań w bazie danych Azure dla programu MySQL
**WYJAŚNIĆ** jest przydatnym narzędziem zoptymalizować zapytania. WYJAŚNIONO, że instrukcja może być używana do uzyskania informacji na temat sposobu wykonywania instrukcji SQL. Następujące dane wyjściowe przedstawiono przykład wykonywania instrukcji wyjaśnienia.

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

Jak widać w tym przykładzie wartość *klucz* ma wartość NULL. Te dane wyjściowe oznacza MySQL nie można znaleźć żadnych indeksów zoptymalizowane pod kątem zapytania i wykonuje skanowanie pełne tabeli. Umożliwia optymalizowanie tego zapytania przez dodanie indeksu na **identyfikator** kolumny.

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

Nowe wyjaśnienia pokazuje, że MySQL teraz używa indeksu ograniczenia liczby wierszy do 1, który z kolei znacznie skrócony czas wyszukiwania.
 
## <a name="covering-index"></a>Obejmujące indeksu
Indeks obejmujący obejmuje wszystkie kolumny zapytania w indeksie, aby ograniczyć pobieranie wartości z tabel danych. Oto ilustracja poniżej **GROUP BY** instrukcji.
 
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

Jak wynika z danych wyjściowych, MySQL nie używa żadnych indeksów, ponieważ nie są dostępne żadne odpowiednie indeksy. Zawiera także *przy użyciu tymczasowego; Przy użyciu pliku sortowania*, co oznacza, że MySQL tworzy tabeli tymczasowej do zaspokojenia **GROUP BY** klauzuli.
 
Tworzenie indeksu w kolumnie **c2** wyłącznie sprawia, że nie różnica i MySQL nadal potrzebuje do utworzenia tabeli tymczasowej:

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

W takim przypadku **objęte usługą indeksu** zarówno **c1** i **c2** można tworzyć, według którego Dodawanie wartość **c2**"bezpośrednio w indeks Usuń dodatkowe wyszukiwania danych.

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

Jak pokazano na powyższym WYJAŚNIJ, MySQL teraz używa indeksu objęte usługą i unikanie tworzenia tabeli tymczasowej. 

## <a name="combined-index"></a>Łączna indeksu
Łączna indeksu składa się wartości z wielu kolumn i mogą zostać uwzględnione tablicę wiersze są sortowane według wartości kolumn indeksowanego łączenia. Ta metoda może być przydatna w **GROUP BY** instrukcji.

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

Wykonuje MySQL *sortowania pliku* powolna operacja, która jest dość, szczególnie gdy obsługuje sortowanie wiele wierszy. Aby zoptymalizować tego zapytania, można utworzyć połączonego indeks dla obu kolumn, które są sortowane.

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

WYJAŚNIENIE teraz pokazuje, że MySQL jest w stanie używać indeksu złożonego, aby uniknąć dodatkowych sortowania, ponieważ indeks jest już sortowana.
 
## <a name="conclusion"></a>Podsumowanie
 
Przy użyciu WYJAŚNIJ i innego typu indeksów może znacznie zwiększyć wydajność. Tak, ponieważ ma indeks na tabeli nie musi oznaczać, że MySQL można użyć jej do zapytania. Zawsze sprawdzić za pomocą wyjaśnienie założeń i zoptymalizować zapytania przy użyciu indeksów.


## <a name="next-steps"></a>Kolejne kroki
- Aby równorzędnej odpowiedzi na pytania najbardziej zainteresowanych lub post nowe pytanie/odpowiedź, odwiedź stronę [MSDN forum](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) lub [przepełnienie stosu](https://stackoverflow.com/questions/tagged/azure-database-mysql).
