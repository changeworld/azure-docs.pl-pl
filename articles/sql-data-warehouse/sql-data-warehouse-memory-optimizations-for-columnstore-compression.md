---
title: Poprawianie wydajności indeksu magazynu kolumn - Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Zmniejsz wymagania dotyczące pamięci lub zwiększ ilość dostępnej pamięci, aby zmaksymalizować liczbę wierszy indeksu magazynu kolumn kompresuje do każdej grupy wierszy.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: ''
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 03/15/2018
ms.author: barbkess
ms.openlocfilehash: 6484f457731bb1b97f01df339eaf1b0aebf924c1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="maximizing-rowgroup-quality-for-columnstore"></a>Maksymalizacja jakości i dla magazynu kolumn

Jakość i zależy od liczby wierszy w grupy wierszy. Zmniejsz wymagania dotyczące pamięci lub zwiększ ilość dostępnej pamięci, aby zmaksymalizować liczbę wierszy indeksu magazynu kolumn kompresuje do każdej grupy wierszy.  Użycia tych metod w celu zwiększenia kompresji i zbadać wydajność dla indeksów magazynu kolumn.

## <a name="why-the-rowgroup-size-matters"></a>Dlaczego rozmiar i ma znaczenie
Ponieważ indeks magazynu kolumn skanowania tabeli za pomocą skanowania segmentów kolumny z poszczególnych rowgroups, maksymalizacja liczby wierszy w każdym i zwiększa wydajność zapytań. Gdy rowgroups ma dużą liczbę wierszy, kompresji danych zwiększa, co oznacza, że jest mniejsza ilość danych do odczytu z dysku.

Aby uzyskać więcej informacji o rowgroups, zobacz [przewodnik indeksy magazynu kolumn](https://msdn.microsoft.com/library/gg492088.aspx).

## <a name="target-size-for-rowgroups"></a>Rozmiar docelowy rowgroups
Aby uzyskać najlepszą wydajność zapytań celem jest zwiększenie liczby wierszy na grupy wierszy w indeksie magazynu kolumn. I może mieć maksymalnie 1 048 576 wierszy. Ma nic złego, nie ma maksymalną liczbę wierszy na grupy wierszy. Indeksy magazynu kolumn uzyskać dobrą wydajność, gdy rowgroups zawiera co najmniej 100 000 wierszy.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Rowgroups można pobrać usuwane podczas kompresji

Podczas zbiorczego obciążenia lub magazynu kolumn odbudowywanie indeksu czasami nie ma wystarczającej ilości pamięci do skompresowania wszystkie wiersze, które są przeznaczone dla każdej grupy wierszy. W przypadku wykorzystania pamięci indeksy magazynu kolumn trim rozmiary i dlatego może się powieść kompresji do magazynu kolumn. 

Jeśli jest za mało pamięci do skompresowania co najmniej 10 000 wierszy do każdej grupy wierszy, SQL Data Warehouse generuje błąd.

Aby uzyskać więcej informacji dotyczących ładowania zbiorczego, zobacz [ładowanie zbiorcze do klastrowanego indeksu magazynu kolumn](https://msdn.microsoft.com/en-us/library/dn935008.aspx#Bulk load into a clustered columnstore index).

## <a name="how-to-monitor-rowgroup-quality"></a>Jak monitorować i jakość

Brak udostępnia przydatne informacje, takie jak liczba wierszy w rowgroups i przyczynę przycinanie, jeśli został przycinanie DMV (sys.dm_pdw_nodes_db_column_store_row_group_physical_stats). Można utworzyć następującego widoku jako wygodny sposób na zapytania DMV ten można pobrać informacji na temat przycinanie i.

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

Trim_reason_desc informuje, czy został przycięty grupy wierszy (trim_reason_desc = NO_TRIM oznacza nie było żadnych przycinanie i grupę wierszy jest jakość). Skutkiem przycinania oznaczać przedwczesny przycinanie grupy wierszy:
- BULKLOAD: Z tego powodu przycinania jest używany, gdy przychodzący partii wierszy obciążenia ma mniej niż 1 milion wierszy. Aparat spowoduje utworzenie grupy wierszy skompresowany, jeśli jest większa niż 100 000 wierszy wstawiane (zamiast wstawianie do magazynu delta), ale ustawia przycinania Przyczyna BULKLOAD. W tym scenariuszu należy rozważyć zwiększenie obciążenia partii okna gromadzone więcej wierszy. Ponadto obliczyć ponownie Twoje schemat partycjonowania, aby upewnić się, że nie jest on zbyt szczegółowego jako grupy wierszy nie może obejmować granice partycji.
- MEMORY_LIMITATION: Tworzenie grup wiersza o 1 milion wierszy, określoną ilość pamięci roboczej jest wymagana przez aparat. Gdy dostępna pamięć sesji ładowania jest mniejsza od wymaganej pamięci pracy, grupy wierszy przedwcześnie uzyskać usuwane. W poniższych sekcjach opisano sposób szacowania pamięci wymaganej i przydzielić więcej pamięci.
- DICTIONARY_SIZE: Z tego powodu przycinania wskazuje, że przycinanie i wystąpił, ponieważ wystąpił co najmniej jednej kolumny typu string z ciągami Kardynalność szerokości i/lub wysokiego. Rozmiar słownika może zawierać maksymalnie 16 MB pamięci i jest skompresowany po osiągnięciu tego limitu grupę wierszy. Jeśli zostanie uruchomione w tej sytuacji, należy wziąć pod uwagę izolowanie kolumnie powodować problemy w osobnej tabeli.

## <a name="how-to-estimate-memory-requirements"></a>Sposób oszacować wymagania dotyczące pamięci

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

Maksymalna wymagana ilość pamięci do skompresowania i jeden wynosi około

- 72 MB +
- \#wiersze \* \#kolumn \* 8 bajtów +
- \#wiersze \* \#krótki ciąg kolumn \* 32 bajtów +
- \#długi ciąg kolumn \* 16 MB dla słownika kompresji

gdzie krótki ciąg kolumn używać typów danych ciąg < = 32 bajtów i użyj długi ciąg kolumn danych typu ciąg > 32 bajtów.

Długie ciągi są kompresowane za pomocą metody kompresji przeznaczone do kompresowania tekstu. Ta metoda kompresji używa *słownika* do przechowywania wzorce tekstu. Maksymalny rozmiar słownik jest 16 MB. Istnieje tylko jeden słownika dla każdej kolumny i długi ciąg.

Szczegółowe omówienie wymagań pamięci magazynu kolumn, zobacz wideo [skalowania usługi Azure SQL Data Warehouse: wskazówki i konfiguracji](https://myignite.microsoft.com/videos/14822).

## <a name="ways-to-reduce-memory-requirements"></a>Sposoby Zmniejsz wymagania dotyczące pamięci

Następujące techniki umożliwiają zmniejszyć wymagania dotyczące pamięci kompresowania rowgroups w indeksach magazynu kolumn.

### <a name="use-fewer-columns"></a>Użyj mniejszej liczby kolumn
Jeśli to możliwe projektowanie tabeli z mniejszą liczbą kolumn. Podczas kompresowania i do magazynu kolumn, indeks magazynu kolumn kompresuje osobno każdy z segmentów kolumny. W związku z tym wymagania dotyczące pamięci do skompresowania i zwiększyć jako liczba wzrasta kolumn.


### <a name="use-fewer-string-columns"></a>Użyj mniejszej liczby kolumn ciągu
Kolumny danych typu ciąg wymaga więcej pamięci niż liczbowych i typy danych Data. Aby zmniejszyć wymagania dotyczące pamięci, rozważ usunięcie kolumny ciągu z tabel faktów i umieszczania ich w mniejszych tabele wymiarów.

Wymagania dotyczące pamięci dodatkowe ciąg kompresji:

- Typy danych w ciągu maksymalnie 32 znaki może wymagać 32 bajtów dodatkowe na wartość.
- Typy danych ciąg z więcej niż 32 znaki są kompresowane przy użyciu metod słownika.  Każda kolumna i może wymagać maksymalnie dodatkowe 16 MB do tworzenia słownika.

### <a name="avoid-over-partitioning"></a>Unikaj nadmiernego partycjonowania

Indeksy magazynu kolumn utworzyć rowgroups co najmniej jednej partycji. W usłudze SQL Data Warehouse liczba partycji rozwoju szybko ponieważ dane są przesyłane i każdej dystrybucji jest podzielona na partycje. Jeśli tabela zawiera zbyt wiele partycji, może nie być wystarczająco dużo wierszy, aby wypełnić rowgroups. Brak wierszy nie tworzy wykorzystania pamięci podczas kompresji, ale prowadzi do rowgroups, który nie będzie najlepszą wydajność zapytań magazynu kolumn.

Kolejny powód, aby uniknąć nadmiernego partycjonowania jest pamięci w czasie ładowania wierszy do indeksu magazynu kolumn dla partycjonowanej tabeli. Podczas ładowania większej liczby partycji można odebrać przychodzącego wiersze, które są przechowywane w pamięci, dopóki każda partycja ma wystarczająco dużo wierszy do skompresowania. Zbyt wiele partycji mających tworzy wykorzystania pamięci dodatkowe.

### <a name="simplify-the-load-query"></a>Uprość zapytanie obciążenia

Bazy danych może mieć przydział pamięci dla zapytania wśród wszystkich operatorów w zapytaniu. Jeśli zapytanie obciążenia sortowania złożonego i sprzężeń, zostanie zmniejszona pamięci dostępnej dla kompresji.

Projektowanie zapytania ładowania skupić się tylko na podczas ładowania zapytania. Jeśli musisz uruchomić przekształcenia danych, należy uruchomić je oddzielnie od zapytania obciążenia. Na przykład przemieszczanie danych w tabeli sterty, uruchom przekształceń, a następnie załadować do indeksu magazynu kolumn tabeli przemieszczania. Można również najpierw załadować dane, a następnie używać systemu MPP do przekształcania danych.

### <a name="adjust-maxdop"></a>Dostosuj MAXDOP

Każdy dystrybucji kompresuje rowgroups do magazynu kolumn równoległe, gdy jest dostępna więcej niż jednego rdzenia procesora CPU na dystrybucji. Równoległość wymaga dodatkowych zasobów pamięci, co może prowadzić do wykorzystania pamięci i przycinanie grupy wierszy.

Aby zmniejszyć wykorzystanie pamięci, wskazówkę zapytania MAXDOP służy do wymusić operacji ładowania do pracy w trybie serial w ramach poszczególnych dystrybucji.

```
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQUota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Sposoby przydzielenie większej ilości pamięci

Rozmiar wartości DWU i klasa zasobów użytkownika razem określić ilość pamięci dostępnej dla zapytania użytkownika. Aby zwiększyć przydział pamięci dla zapytania obciążenia, należy zwiększyć liczbę jednostek dwu lub zwiększ klasy zasobów.

- Aby zwiększyć liczbę jednostek dwu, zobacz [sposób skalowania wydajności?](quickstart-scale-compute-portal.md)
- Aby zmienić klasy zasobów dla zapytania, zobacz [zmienić przykład klasy zasobów użytkownika](resource-classes-for-workload-management.md#change-a-users-resource-class).

Na przykład na DWU 100 użytkownika w klasie zasobu smallrc służy 100 MB pamięci dla poszczególnych dystrybucji. Aby uzyskać więcej informacji, zobacz [współbieżność w usłudze SQL Data Warehouse](resource-classes-for-workload-management.md).

Załóżmy, że należy określić, czy potrzebujesz 700 MB pamięci można pobrać rozmiarów i wysokiej jakości. Poniższe przykłady pokazują, jak można uruchomić kwerendę obciążenia z wystarczającą ilość pamięci.

- Korzystając z DWU 1000 i mediumrc, Twoje przydział pamięci jest 800 MB
- Korzystając z DWU 600 i largerc, Twoje przydział pamięci jest 800 MB.


## <a name="next-steps"></a>Kolejne kroki

Aby znaleźć więcej sposobów poprawy wydajności w usłudze SQL Data Warehouse, zobacz [wydajności — omówienie](sql-data-warehouse-overview-manage-user-queries.md).

<!--Image references-->

<!--Article references-->


<!--MSDN references-->

<!--Other Web references-->
