---
title: Poprawianie wydajności indeksu magazynu kolumn w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Zmniejsz wymagania dotyczące pamięci lub zwiększ ilość dostępnej pamięci, aby zmaksymalizować liczbę wierszy indeksu magazynu kolumn kompresuje w każdej grupie wierszy.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: ec85bcc764ba7a7ae6341e0490530c31fdb5a02b
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595463"
---
# <a name="maximizing-rowgroup-quality-for-columnstore"></a>Maksymalizacja jakości i magazynu kolumn

Jakość i jest określana przez liczbę wierszy w grupie wierszy. Zwiększenie ilości dostępnej pamięci można zmaksymalizować liczbę wierszy indeksu magazynu kolumn kompresuje w każdej grupie wierszy.  Metody te można stosować do zwiększenia szybkości kompresji i wykonywania zapytań względem wydajności w indeksach magazynu kolumn.

## <a name="why-the-rowgroup-size-matters"></a>Dlaczego jest ważna wielkości grupy wierszy:
Ponieważ indeks magazynu kolumn skanowania tabeli za pomocą skanowania segmentów kolumny z poszczególnych rowgroups, maksymalizacja liczby wierszy w każdej grupie wierszy zwiększa wydajność zapytań. Gdy rowgroups ma dużą liczbę wierszy, kompresji danych zwiększa, co oznacza, że ilość danych odczytanych z dysku.

Aby uzyskać więcej informacji na temat rowgroups zobacz [Przewodnik po indeksach magazynu kolumn](https://msdn.microsoft.com/library/gg492088.aspx).

## <a name="target-size-for-rowgroups"></a>Rozmiar docelowy rowgroups
Aby uzyskać najlepszą wydajność zapytań celem jest zwiększenie liczby wierszy na grupę wierszy w indeksie magazynu kolumn. Grupy wierszy nie może przekraczać 1 048 576 wierszy. To można ma maksymalną liczbę wierszy na grupę wierszy. Indeksy magazynu kolumn uzyskać wysoką wydajność, gdy rowgroups ma co najmniej 100 000 wierszy.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Rowgroups można uzyskać obcięte podczas kompresji

Podczas zbiorcze obciążenie lub magazynu kolumn indeksu ponownej kompilacji czasami nie ma wystarczającej ilości pamięci skompresować wszystkie wiersze wyznaczony w każdej grupie wierszy. W przypadku dużego wykorzystania pamięci indeksów magazynu kolumn przycina rozmiarów i aby pomyślnie przeprowadzić operację kompresji do magazynu kolumn. 

Usługa SQL Data Warehouse generuje błąd, w przypadku braku pamięci w celu skompresowania co najmniej 10 000 wierszy w każdej grupie wierszy.

Aby uzyskać więcej informacji na temat ładowania zbiorczego, zobacz [ładowanie zbiorcze do indeksu klastrowanego magazynu kolumn](https://msdn.microsoft.com/library/dn935008.aspx#Bulk ).

## <a name="how-to-monitor-rowgroup-quality"></a>Jak monitorować jakość grupy wierszy:

DMV sys.dm_pdw_nodes_db_column_store_row_group_physical_stats ([sys.dm_db_column_store_row_group_physical_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql) zawiera definicję widoku dopasowania bazy danych SQL w usłudze SQL Data Warehouse), udostępnia przydatne informacje np. liczby wierszy w grupy wierszy i przyczynę przycinania, jeśli został przycinania. Możesz utworzyć następujący widok jako wygodny sposób ten widok DMV, aby uzyskać informacje dotyczące przycinania i zapytania.

```sql
create view dbo.vCS_rg_physical_stats
as 
with cte
as
(
select   tb.[name]                    AS [logical_table_name]
,        rg.[row_group_id]            AS [row_group_id]
,        rg.[state]                   AS [state]
,        rg.[state_desc]              AS [state_desc]
,        rg.[total_rows]              AS [total_rows]
,        rg.[trim_reason_desc]        AS trim_reason_desc
,        mp.[physical_name]           AS physical_name
FROM    sys.[schemas] sm
JOIN    sys.[tables] tb               ON  sm.[schema_id]          = tb.[schema_id]                             
JOIN    sys.[pdw_table_mappings] mp   ON  tb.[object_id]          = mp.[object_id]
JOIN    sys.[pdw_nodes_tables] nt     ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[dm_pdw_nodes_db_column_store_row_group_physical_stats] rg      ON  rg.[object_id]     = nt.[object_id]
                                                                            AND rg.[pdw_node_id]   = nt.[pdw_node_id]
                                        AND rg.[distribution_id]    = nt.[distribution_id]                                          
)
select *
from cte;
```

Trim_reason_desc informuje, czy została przycięta grupy wierszy (trim_reason_desc = NO_TRIM oznacza wystąpił brak przycinania i grupę wierszy jest optymalne jakości). Przyczyny przycinania wskazują przedwczesne przycinanie grupy wierszy:
- BULKLOAD: Z tego powodu przycinania jest używany, gdy przychodzący partii wierszy dla obciążenia było mniej niż 1 milion wierszy. Aparat tworzenia skompresowanych grup wierszy, jeśli istnieje większa niż 100 000 wierszy, jest wstawiany (w przeciwieństwie do wstawiania do magazynu delta), ale ustawia BULKLOAD Przyczyna przycinania. W tym scenariuszu należy rozważyć zwiększenie obciążenia usługi batch do uwzględnienia większej liczby wierszy. Ponadto to ponowne ocenienie schematu partycji w celu zapewnienia, że nie jest zbyt szczegółową jako granice partycji nie mogą rozciągać się grupy wierszy.
- MEMORY_LIMITATION: Aby utworzyć grupy wierszy z 1 milion wierszy, pewna ilość pamięci pracy jest wymagany przez aparat. Gdy dostępna pamięć sesji ładowania jest mniejsza niż wymaganej ilości pamięci w pracy, grupy wierszy przedwcześnie Pobierz spacje. W poniższych sekcjach opisano sposób szacowania pamięci wymaganej i przydzielanie większej ilości pamięci.
- DICTIONARY_SIZE: Z tego powodu przycinania wskazuje, że przycinania i wystąpił, ponieważ wystąpił co najmniej jedną kolumnę ciągu z ciągami o szerokim lub wysokiej kardynalności. Rozmiar słownika jest ograniczony do 16 MB w pamięci, a po osiągnięciu tego limitu grupę wierszy jest skompresowany. Jeśli napotkasz ten problem, należy wziąć pod uwagę izolowanie problematyczne kolumny w osobnej tabeli.

## <a name="how-to-estimate-memory-requirements"></a>Jak oszacować wymagania dotyczące pamięci

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

Maksymalna wymaganej ilości pamięci do kompresowania i jeden to około

- 72 MB +
- \#wiersze \* \#kolumn \* 8 bajtów +
- \#wiersze \* \#krótki ciąg kolumn \* 32 bajty +
- \#Long ciągu columns \* 16 MB dla słownika kompresji

gdzie krótki ciąg kolumn użyć typów danych ciągu < = 32 bajtów i użyj long ciągu kolumny danych typu ciąg > 32-bitowej.

Długie ciągi są kompresowane za pomocą metody kompresji przeznaczone do kompresowania tekstu. Ta metoda kompresji używa *słownika* do przechowywania wzorców tekstu. Maksymalny rozmiar słownik to 16 MB. Istnieje tylko jeden słownika dla każdej kolumny długi ciąg w grupy wierszy.

Szczegółowe omówienie wymagań dotyczących pamięci magazynu kolumn, można znaleźć w temacie wideo [skalowania usługi Azure SQL Data Warehouse: Konfiguracja i wskazówki dotyczące](https://channel9.msdn.com/Events/Ignite/2016/BRK3291).

## <a name="ways-to-reduce-memory-requirements"></a>Sposoby, aby zmniejszyć wymagania dotyczące pamięci

Następujące techniki umożliwiają zmniejszyć wymagania dotyczące pamięci kompresowania rowgroups w indeksach magazynu kolumn.

### <a name="use-fewer-columns"></a>Użyj mniejszej liczby kolumn
Jeśli to możliwe Projektuj tabelę z mniejszą liczbę kolumn. Podczas kompresowania i do magazynu kolumn, indeks magazynu kolumn kompresuje osobno każdy z segmentów kolumny. W związku z tym wymagania dotyczące pamięci do kompresowania i zwiększyć jako liczba wzrasta kolumn.


### <a name="use-fewer-string-columns"></a>Użyj mniejszej liczby kolumn ciągów
Kolumny danych typu ciąg wymaga więcej pamięci niż numerycznych i typów danych daty. Aby zmniejszyć wymagania dotyczące pamięci, rozważ usunięcie kolumny ciągu z tabel faktów i umieszczania ich w mniejszych tabele wymiarów.

Dodatkowa pamięć wymagania dotyczące ciągu kompresji:

- Danych typu ciąg, maksymalnie 32 znaki mogą wymagać 32 bajty dodatkowe na wartość.
- Typy danych ciągu z więcej niż 32 znaki są kompresowane, przy użyciu metod słownika.  Każda kolumna w grupy wierszy może wymagać maksymalnie dodatkowe 16 MB do tworzenia słownika.

### <a name="avoid-over-partitioning"></a>Unikaj nadmiernego partycjonowania

Indeksy magazynu kolumn, utworzyć rowgroups co najmniej jednej partycji. W usłudze SQL Data Warehouse liczby partycji rośnie szybko, ponieważ dane są rozmieszczane i każdej dystrybucji są podzielone na partycje. Jeśli tabela ma zbyt wiele partycji, nie może być wystarczającą liczbę wierszy, aby wypełnić grupy wierszy. Brak wierszy nie powoduje utworzenia dużego wykorzystania pamięci podczas kompresji, ale prowadzi do grupy wierszy, które nie osiągnąć najlepszą wydajność zapytań magazynu kolumn.

Kolejny powód, aby uniknąć nadmiernego partycjonowania jest pamięci w czasie ładowania wierszy do indeksu magazynu kolumn dla partycjonowanej tabeli. Podczas ładowania liczby partycji może odbierać przychodzące wiersze, które są przechowywane w pamięci, dopóki każda partycja zawiera wystarczającą liczbę wierszy do skompresowania. Zbyt duża liczba partycji presję dodatkowej pamięci.

### <a name="simplify-the-load-query"></a>Uprość zapytanie obciążenia

Baza danych współużytkuje przydział pamięci dla zapytania wśród wszystkich operatorów w zapytaniu. Gdy zapytanie obciążenia ma sortowania złożonego i sprzężeń, zmniejszono ilość pamięci dostępnej dla kompresji.

Zaprojektuj zapytanie obciążenia, aby skoncentrować się tylko na podczas ładowania zapytania. Jeśli musisz uruchomić przekształcenia na danych, należy uruchomić je oddzielnie od zapytania obciążenia. Na przykład przemieszczanie danych w tabeli stosu, uruchamianie przekształceń i następnie załadować tabeli przejściowej do indeksu magazynu kolumn. Można również najpierw załadować dane, a następnie użyć systemu MPP do przekształcania danych.

### <a name="adjust-maxdop"></a>Dostosuj MAXDOP

Każdej dystrybucji kompresuje grupy wierszy do magazynu kolumn w sposób równoległy, gdy jest dostępna więcej niż jednego rdzenia procesora CPU na dystrybucję. Równoległości wymaga dodatkowych zasobów pamięci, co może prowadzić do wykorzystania pamięci i przycinania grupy wierszy.

Aby zmniejszyć wykorzystanie pamięci, wskazówki zapytania MAXDOP służy również do wymuszenia operacji ładowania do pracy w trybie szeregowe w ramach każdej dystrybucji.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Sposoby przydzielanie większej ilości pamięci

Rozmiar jednostek DWU i klasa zasobów użytkownika razem określają ilość pamięci dostępnej dla zapytania użytkownika. Aby zwiększyć przydział pamięci dla zapytania, obciążenia, możesz zwiększyć liczbę jednostek dwu lub zwiększyć klasy zasobów.

- Zwiększenie liczby jednostek dwu, zobacz [sposób skalować wydajność?](quickstart-scale-compute-portal.md)
- Aby zmienić klasy zasobów dla zapytania, zobacz [zmienić przykład klasy zasobów użytkownika](resource-classes-for-workload-management.md#change-a-users-resource-class).

## <a name="next-steps"></a>Następne kroki

Aby znaleźć więcej sposobów na zwiększenie wydajności w usłudze SQL Data Warehouse, zobacz [wydajności — omówienie](sql-data-warehouse-overview-manage-user-queries.md).

