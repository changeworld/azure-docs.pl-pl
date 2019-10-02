---
title: Dostrajanie wydajności z Azure SQL Data Warehouse uporządkowany klastrowany indeks magazynu kolumn | Microsoft Docs
description: Zalecenia i zagadnienia, które należy znać w przypadku używania uporządkowanego klastrowanego indeksu magazynu kolumn w celu zwiększenia wydajności zapytań.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: 74a1a2218020718a05c9d01de96ddf4fccb35eb4
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802564"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Dostrajanie wydajności z uporządkowanym klastrowanym indeksem magazynu kolumn  

Gdy użytkownicy wysyłają zapytanie do tabeli magazynu kolumn w Azure SQL Data Warehouse, optymalizator sprawdza wartości minimalne i maksymalne przechowywane w każdym segmencie.  Segmenty, które znajdują się poza granicami predykatu zapytania, nie są odczytywane z dysku do pamięci.  Zapytanie może zwiększyć wydajność, jeśli liczba segmentów do odczytu i ich łączny rozmiar jest mały.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Uporządkowany a nieuporządkowany klastrowany indeks magazynu kolumn 
Domyślnie dla każdej tabeli magazynu danych platformy Azure, która została utworzona bez opcji indeksu, składnik wewnętrzny (Konstruktor indeksowania) tworzy na nim nieuporządkowany klastrowany indeks magazynu kolumn (WIK).  Dane w każdej kolumnie są kompresowane do oddzielnego segmentu grupy wierszy WIK.  Istnieją metadane dla każdego zakresu wartości segmentu, dlatego segmenty, które znajdują się poza granicami predykatu zapytania, nie są odczytywane z dysku podczas wykonywania zapytania.  WIK oferuje najwyższy poziom kompresji danych i zmniejsza rozmiar segmentów do odczytu, dzięki czemu zapytania mogą działać szybciej. Jednak ponieważ Konstruktor indeksu nie sortuje danych przed ich kompresowaniem do segmentów, mogą wystąpić segmenty z nakładającymi się zakresami wartości, co sprawia, że zapytania odczytują więcej segmentów z dysku i trwają dłużej.  

Podczas tworzenia uporządkowanej WIK, aparat Azure SQL Data Warehouse sortuje dane w pamięci według kluczy zamówienia, zanim Konstruktor indeksów kompresuje go do segmentów indeksu.  Posortowane dane, nakładające się segmenty, zmniejszają się, umożliwiając wykonywanie zapytań o bardziej wydajny sposób eliminacji segmentów, co zwiększa wydajność, ponieważ liczba segmentów odczytywanych z dysku jest mniejsza.  Jeśli wszystkie dane można sortować jednocześnie w pamięci, można uniknąć nakładania się segmentów.  W przypadku dużej ilości danych w tabelach magazynu danych ten scenariusz nie jest często wykonywany.  

Aby sprawdzić zakresy segmentów dla kolumny, Uruchom to polecenie z nazwą tabeli i nazwą kolumny:

```sql
SELECT o.name, pnp.index_id, cls.row_count, pnp.data_compression_desc, pnp.pdw_node_id, 
pnp.distribution_id, cls.segment_id, cls.column_id, cls.min_data_id, cls.max_data_id, cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
   JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table_Name>' and cols.name = '<Column_Name>' 
ORDER BY o.name, pnp.distribution_id, cls.min_data_id

```

## <a name="data-loading-performance"></a>Wydajność ładowania danych

Wydajność ładowania danych do uporządkowanej tabeli WIK jest podobna do ładowania danych do tabeli partycjonowanej.  
Ładowanie danych do uporządkowanej tabeli WIK może zająć więcej czasu niż ładowanie danych do nieuporządkowanej tabeli WIK z powodu sortowania danych.  

Poniżej przedstawiono przykładowe porównanie wydajności ładowania danych do tabel z różnymi schematami.
![Performance_comparison_data_loading @ no__t-1
 
## <a name="reduce-segment-overlapping"></a>Zmniejsz nakładające się segmenty
Poniżej znajdują się opcje umożliwiające dalsze zmniejszenie nakładających się segmentów podczas tworzenia uporządkowanej WIK dla nowej tabeli za pośrednictwem CTAS lub istniejącej tabeli zawierającej dane:

- Użyj większej klasy zasobów, aby umożliwić sortowanie większej ilości danych jednocześnie w pamięci, zanim Konstruktor indeksów kompresuje je do segmentów.  Raz w segmencie indeksu nie można zmienić fizycznej lokalizacji danych.  Nie ma sortowania danych w ramach segmentu ani między segmentami.  

- Użyj niższego stopnia równoległości (na przykład stopień RÓWNOLEGŁOŚCI = 1).  Każdy wątek używany do uporządkowanego tworzenia WIK działa w ramach podzestawu danych i sortuje go lokalnie.  Nie ma sortowania globalnego dla danych posortowanych według różnych wątków.  Użycie równoległych wątków może skrócić czas tworzenia uporządkowanej WIK, ale generuje więcej nakładających się segmentów niż przy użyciu jednego wątku. 
- Przed załadowaniem danych do tabel Azure SQL Data Warehouse należy je wstępnie sortować według kluczy sortowania.

## <a name="create-ordered-cci-on-large-tables"></a>Tworzenie uporządkowanej WIK w dużych tabelach
Tworzenie uporządkowanej WIK jest operacją offline.  W przypadku tabel bez partycji dane nie będą dostępne dla użytkowników, dopóki nie zostanie ukończony uporządkowany proces tworzenia WIK.   W przypadku partycjonowanych tabel, ponieważ aparat tworzy uporządkowaną partycję WIK według partycji, użytkownicy mogą nadal uzyskiwać dostęp do danych w partycjach, w których uporządkowane tworzenie WIK nie jest w toku.   Za pomocą tej opcji można zminimalizować przestoje podczas tworzenia uporządkowanej WIK w dużych tabelach: 

1.  Utwórz partycje w docelowej dużej tabeli (zwanej tabelą A).
2.  Tworzenie pustej uporządkowanej tabeli WIK (zwanej tabelą B) za pomocą tej samej tabeli i schematu partycji co tabela A.
3.  Przełącz jedną partycję z tabeli A na tabelę B.
4.  Uruchom polecenie ALTER INDEX < Ordered_CCI_Index > Skompiluj ponownie w tabeli B w celu odbudowania partycji przełączonej.  
5.  Powtórz kroki 3 i 4 dla każdej partycji w tabeli A.
6.  Po przełączeniu wszystkich partycji z tabeli A do tabeli B i zostały one ponownie skompilowane, Porzuć tabelę A i Zmień nazwę tabeli B na tabelę A. 

## <a name="examples"></a>Przykłady

**A. Aby sprawdzić uporządkowane kolumny i numer porządkowy zamówienia:**
```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Aby zmienić numer porządkowy kolumny, Dodaj lub Usuń kolumny z listy Order lub aby zmienić z WIK na uporządkowaną WIK:**
```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Następne kroki
Więcej porad programistycznych znajdziesz w artykule [Omówienie programowania w usłudze SQL Data Warehouse](sql-data-warehouse-overview-develop.md).
