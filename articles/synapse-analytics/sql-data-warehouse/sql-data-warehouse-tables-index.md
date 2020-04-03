---
title: Indeksowanie tabel
description: Zalecenia i przykłady indeksowania tabel w puli Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: d5acc2b69ed521af4fd4777dc9f3496290078379
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583266"
---
# <a name="indexing-tables-in-synapse-sql-pool"></a>Indeksowanie tabel w puli SQL Synapse

Zalecenia i przykłady indeksowania tabel w puli Synapse SQL.

## <a name="index-types"></a>Typy indeksów

Pula SQL Synapse oferuje kilka opcji indeksowania, w tym [indeksy klastrowanego magazynu kolumn,](/sql/relational-databases/indexes/columnstore-indexes-overview) [indeksy klastrowane i indeksy nieklastrowane](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described)oraz opcję nieindeksową znaną również jako [sterty.](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes)  

Aby utworzyć tabelę z indeksem, zobacz tworzenie [tabeli (puli SYNAPSE SQL)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) dokumentacji.

## <a name="clustered-columnstore-indexes"></a>Indeksy klastrowanego magazynu kolumn

Domyślnie pula Synapse SQL tworzy indeks klastrowanego magazynu kolumn, gdy w tabeli nie określono żadnych opcji indeksu. Klastrowane tabele magazynu kolumn oferują zarówno najwyższy poziom kompresji danych, jak i najlepszą ogólną wydajność zapytań.  Klastrowane tabele magazynu kolumn zazwyczaj przewyższają tabele indeksu klastrowanego lub sterty i są zwykle najlepszym wyborem dla dużych tabel.  Z tych powodów klastrowany magazyn kolumn jest najlepszym miejscem do rozpoczęcia, gdy nie masz pewności, jak indeksować tabelę.  

Aby utworzyć tabelę magazynu kolumn klastrowanych, wystarczy określić indeks klastra kolumn w klauzuli WITH lub pozostawić klauzulę WITH wyłączona:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Istnieje kilka scenariuszy, w których klastrowany magazyn kolumn może nie być dobrym rozwiązaniem:

- Tabele magazynu kolumn nie obsługują varchar(max), nvarchar(max) i varbinary(max). Zamiast tego należy wziąć pod uwagę sterty lub indeks klastrowany.
- Tabele magazynu kolumn mogą być mniej wydajne dla danych przejściowych. Rozważsz sterty, a może nawet tabele tymczasowe.
- Małe tabele z mniej niż 60 milionami wierszy. Należy wziąć pod uwagę tabele sterty.

## <a name="heap-tables"></a>Tabele sterty

Podczas tymczasowego lądowania danych w puli Synapse SQL, może się okazać, że przy użyciu tabeli sterty sprawia, że ogólny proces szybciej. Jest to spowodowane obciążenia do sterty są szybsze niż do tabel indeksu i w niektórych przypadkach kolejny odczyt można wykonać z pamięci podręcznej.  Jeśli ładujesz dane tylko do etapu przed uruchomieniem więcej przekształceń, ładowanie tabeli do tabeli sterty jest znacznie szybsze niż ładowanie danych do tabeli magazynu kolumn klastrowanych. Ponadto ładowanie danych do [tabeli tymczasowej](sql-data-warehouse-tables-temporary.md) ładuje się szybciej niż ładowanie tabeli do magazynu trwałego.  

W przypadku małych tabel odnośników mniej niż 60 milionów wierszy często tabele sterty mają sens.  Tabele magazynu kolumn klastra zaczynają osiągać optymalną kompresję, gdy istnieje więcej niż 60 milionów wierszy.

Aby utworzyć tabelę sterty, wystarczy określić heap w with klauzuli:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Indeksy klastrowane i nieklastrowane

Indeksy klastrowane mogą przewyższać tabele magazynu kolumn klastrowanych, gdy pojedynczy wiersz musi zostać szybko pobrany. W przypadku kwerend, w których pojedyncze lub bardzo nieliczne wyszukiwanie wierszy jest wymagane do wykonywania z najwyższą szybkością, należy wziąć pod uwagę indeks klastra lub indeks pomocniczy nieklastrowany. Wadą przy użyciu indeksu klastrowanego jest to, że tylko kwerendy, które przynoszą korzyści, to te, które używają filtru wysoce selektywnego w kolumnie indeksu klastrowanego. Aby poprawić filtr w innych kolumnach, do innych kolumn można dodać indeks nieklastrowany. Jednak każdy indeks, który jest dodawany do tabeli dodaje zarówno spacji i czasu przetwarzania do obciążeń.

Aby utworzyć tabelę indeksów klastrowanych, wystarczy określić INDEKS KLASTRA w klauzuli WITH:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Aby dodać indeks nieklastrowany do tabeli, należy użyć następującej składni:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Optymalizacja indeksów klastrowanego magazynu kolumn

Klastrowane tabele magazynu kolumn są zorganizowane w dane na segmenty.  Wysoka jakość segmentu ma kluczowe znaczenie dla uzyskania optymalnej wydajności zapytań w tabeli magazynu kolumn.  Jakość segmentu może być mierzona liczbą wierszy w skompresowanej grupie wierszy.  Jakość segmentu jest najbardziej optymalna, gdy istnieje co najmniej 100K wierszy na skompresowaną grupę wierszy i zyskaj wydajność, ponieważ liczba wierszy na grupę wierszy zbliża się do 1 048 576 wierszy, co jest największą liczbą wierszy, które może zawierać grupa wierszy.

Poniższy widok można utworzyć i użyć w systemie do obliczania średnich wierszy na grupę wierszy i identyfikowania wszystkich suboptykonalnych indeksów magazynu kolumn klastra.  Ostatnia kolumna w tym widoku generuje instrukcję SQL, która może służyć do odbudowy indeksów.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,    COUNT(DISTINCT rg.[partition_number])                    AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,    CEILING    ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Teraz, gdy utworzono widok, uruchom tę kwerendę, aby zidentyfikować tabele z grupami wierszy z wierszami mniejszymi niż 100K. Oczywiście, jeśli szukasz bardziej optymalnej jakości segmentu, możesz zwiększyć próg 100K.

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Po uruchomieniu kwerendy można rozpocząć przeglądanie danych i analizowanie wyników. W tej tabeli wyjaśniono, na co zwrócić uwagę w analizie grupy wierszy.

| Kolumna | Jak korzystać z tych danych |
| --- | --- |
| [table_partition_count] |Jeśli tabela jest podzielona na partycje, można oczekiwać, aby zobaczyć wyższe Otwarte liczby grup wierszy. Każda partycja w dystrybucji może teoretycznie mieć grupę otwartych wierszy skojarzone z nim. Uwzględnij to w swojej analizie. Mała tabela, która została podzielona na partycje, może zostać zoptymalizowana przez całkowite usunięcie partycjonowania, ponieważ poprawiłoby to kompresję. |
| [row_count_total] |Całkowita liczba wierszy dla tabeli. Na przykład można użyć tej wartości do obliczenia procentu wierszy w stanie skompresowanym. |
| [row_count_per_distribution_MAX] |Jeśli wszystkie wiersze są równomiernie rozłożone ta wartość będzie docelowa liczba wierszy na dystrybucję. Porównaj tę wartość z compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Całkowita liczba wierszy w formacie magazynu kolumn dla tabeli. |
| [COMPRESSED_rowgroup_rows_AVG] |Jeśli średnia liczba wierszy jest znacznie mniejsza niż maksymalna liczba wierszy dla grupy wierszy, rozważ użycie CTAS lub ALTER INDEX REBUILD w celu ponownego skompresji danych |
| [COMPRESSED_rowgroup_count] |Liczba grup wierszy w formacie magazynu kolumn. Jeśli ta liczba jest bardzo wysoka w stosunku do tabeli, jest to wskaźnik, że gęstość magazynu kolumn jest niska. |
| [COMPRESSED_rowgroup_rows_DELETED] |Wiersze są logicznie usuwane w formacie magazynu kolumn. Jeśli liczba jest wysoka w stosunku do rozmiaru tabeli, należy rozważyć ponownetworzenie partycji lub odbudowanie indeksu, ponieważ usuwa je fizycznie. |
| [COMPRESSED_rowgroup_rows_MIN] |Użyj tego w połączeniu z kolumnami AVG i MAX, aby zrozumieć zakres wartości dla grup wierszy w magazynie kolumn. Niska liczba powyżej progu obciążenia (102 400 na rozkład wyrównany do partycji) sugeruje, że optymalizacje są dostępne w obciążeniu danych |
| [COMPRESSED_rowgroup_rows_MAX] |Jak wyżej |
| [OPEN_rowgroup_count] |Otwarte grupy wierszy są normalne. Można by oczekiwać, że jedna grupa wierszy OPEN na dystrybucję tabeli (60). Nadmierne liczby sugerują ładowanie danych między partycjami. Dokładnie sprawdź strategię partycjonowania, aby upewnić się, że jest ono |
| [OPEN_rowgroup_rows] |Każda grupa wierszy może mieć 1 048 576 wierszy jako maksimum. Użyj tej wartości, aby zobaczyć, jak pełne są aktualnie otwarte grupy wierszy |
| [OPEN_rowgroup_rows_MIN] |Otwarte grupy wskazują, że dane są ładowane do tabeli lub że poprzednie obciążenie rozlało się na pozostałe wiersze do tej grupy wierszy. Kolumny MIN, MAX i AVG służy do określania ilości danych w grupach wierszy OPEN. Dla małych tabel może to być 100% wszystkich danych! W takim przypadku ALTER INDEX REBUILD wymusić dane do magazynu kolumn. |
| [OPEN_rowgroup_rows_MAX] |Jak wyżej |
| [OPEN_rowgroup_rows_AVG] |Jak wyżej |
| [CLOSED_rowgroup_rows] |Spójrz na zamknięte wiersze grupy wierszy jako sprawdzanie poczytalności. |
| [CLOSED_rowgroup_count] |Liczba zamkniętych grup wierszy powinna być niska, jeśli w ogóle są widoczne. Zamknięte grupy wierszy można konwertować na skompresowane grupy wierszy za pomocą indeksu ALTER ... REORGANIZUJ. Jednak zwykle nie jest to wymagane. Zamknięte grupy są automatycznie konwertowane na grupy wierszy magazynu kolumn przez proces "przemijacz krotki" w tle. |
| [CLOSED_rowgroup_rows_MIN] |Zamknięte grupy wierszy powinny mieć bardzo wysoki współczynnik wypełnienia. Jeśli współczynnik wypełnienia dla zamkniętej grupy wierszy jest niski, wymagana jest dalsza analiza magazynu kolumn. |
| [CLOSED_rowgroup_rows_MAX] |Jak wyżej |
| [CLOSED_rowgroup_rows_AVG] |Jak wyżej |
| [Rebuild_Index_SQL] |SQL, aby odbudować indeks magazynu kolumn dla tabeli |

## <a name="causes-of-poor-columnstore-index-quality"></a>Przyczyny niskiej jakości indeksu magazynu kolumn

Jeśli zidentyfikowano tabele o niskiej jakości segmentu, chcesz zidentyfikować główną przyczynę.  Poniżej znajduje się kilka innych typowych przyczyn niskiej jakości segmentu:

1. Ciśnienie pamięci podczas budowy indeksu
2. Duża ilość operacji DML
3. Małe lub strużka operacji obciążenia
4. Zbyt wiele partycji

Te czynniki mogą powodować indeks magazynu kolumn mają znacznie mniej niż optymalne 1 milion wierszy na grupę wierszy. Mogą one również powodować wiersze, aby przejść do grupy wierszy delta zamiast skompresowanej grupy wierszy.

### <a name="memory-pressure-when-index-was-built"></a>Ciśnienie pamięci podczas budowy indeksu

Liczba wierszy na skompresowaną grupę wierszy jest bezpośrednio związana z szerokością wiersza i ilością pamięci dostępnej do przetworzenia grupy wierszy.  Jeśli wiersze są zapisywane w tabelach magazynu kolumn przy dużym wykorzystaniu pamięci, może to spowodować obniżenie jakości segmentów w magazynie kolumn.  W związku z tym najlepszym rozwiązaniem jest, aby dać sesji, która zapisuje do tabel indeksu magazynu kolumn dostęp do jak najwięcej pamięci, jak to możliwe.  Ponieważ istnieje kompromis między pamięcią a współbieżnością, wskazówki dotyczące alokacji pamięci prawo zależy od danych w każdym wierszu tabeli, jednostek magazynu danych przydzielonych do systemu i liczby gniazd współbieżności można podać do sesji, która zapisuje dane do tabeli.

### <a name="high-volume-of-dml-operations"></a>Duża ilość operacji DML

Duża liczba operacji DML, które aktualizują i usuwa wiersze mogą wprowadzać nieefektywność do magazynu kolumn. Jest to szczególnie ważne, gdy większość wierszy w grupie wierszy są modyfikowane.

- Usunięcie wiersza ze skompresowanej grupy wierszy tylko logicznie oznacza wiersz jako usunięty. Wiersz pozostaje w skompresowanej grupie wierszy, dopóki partycja lub tabela nie zostanie przebudowana.
- Wstawianie wiersza powoduje dodanie wiersza do wewnętrznej tabeli magazynu wierszy o nazwie grupa wierszy różnicowych. Wstawiony wiersz nie jest konwertowany na magazyn kolumn, dopóki grupa wierszy różnicowych nie jest pełna i jest oznaczona jako zamknięta. Grupy wierszy są zamykane po osiągnięciu maksymalnej pojemności 1 048 576 wierszy.
- Aktualizowanie wiersza w formacie magazynu kolumn jest przetwarzane jako usunięcie logiczne, a następnie wstawianie. Wstawiony wiersz może być przechowywany w magazynie delta.

Operacje aktualizacji i wstawiania wsadowe, które przekraczają próg zbiorczy 102 400 wierszy na dystrybucję wyrównaną do partycji, przechodzą bezpośrednio do formatu magazynu kolumn. Jednak przy założeniu dystrybucji parzyste, należy zmodyfikować więcej niż 6.144 milionów wierszy w jednej operacji, aby to nastąpić. Jeśli liczba wierszy dla danej dystrybucji wyrównanej partycji jest mniejsza niż 102 400, wiersze przejść do magazynu delta i pozostać tam, dopóki nie zostaną wstawione lub zmodyfikowane wystarczające wiersze, aby zamknąć grupę wierszy lub indeks został przebudowany.

### <a name="small-or-trickle-load-operations"></a>Małe lub strużka operacji obciążenia

Małe obciążenia, które przepływają do puli SQL Synapse są również czasami nazywane obciążeniami ściekowymi. Zazwyczaj reprezentują one prawie stały strumień danych są pozyskiwania przez system. Jednak ponieważ ten strumień jest w pobliżu ciągłego woluminu wierszy nie jest szczególnie duży. Częściej niż nie dane są znacznie poniżej progu wymaganego dla bezpośredniego obciążenia do formatu magazynu kolumn.

W takich sytuacjach często lepiej jest wylądować dane najpierw w magazynie obiektów blob platformy Azure i pozwolić mu gromadzić się przed załadowaniem. Technika ta jest często znana jako *mikro-batching*.

### <a name="too-many-partitions"></a>Zbyt wiele partycji

Inną rzeczą do rozważenia jest wpływ partycjonowania na tabelach magazynu kolumn klastrowanych.  Przed partycjonowanie, Synapse PULI SQL już dzieli dane na 60 baz danych.  Partycjonowanie dodatkowo dzieli dane.  Jeśli partycji danych, a następnie należy wziąć pod uwagę, że **każda** partycja potrzebuje co najmniej 1 mln wierszy, aby korzystać z indeksu klastrowanego magazynu kolumn.  Jeśli podzielasz tabelę na 100 partycji, tabela potrzebuje co najmniej 6 miliardów wierszy, aby korzystać z indeksu klastrowanego magazynu kolumn (60 dystrybucji *100 partycji* 1 milion wierszy). Jeśli tabela 100 partycji nie ma 6 miliardów wierszy, zmniejsz liczbę partycji lub rozważ użycie tabeli sterty.

Po załadowaniu tabel z niektórych danych, wykonaj poniższe kroki, aby zidentyfikować i odbudować tabele z sub-optymalne indeksy klastrowanego magazynu kolumn.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Przebudowa indeksów w celu poprawy jakości segmentu

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Krok 1: Identyfikowanie lub tworzenie użytkownika, który używa odpowiedniej klasy zasobów

Jednym z szybkich sposobów na natychmiastowe podniesienie jakości segmentu jest odbudowanie indeksu.  SQL zwrócony przez powyższy widok zwraca alter index rebuild instrukcji, które mogą służyć do odbudowy indeksów. Podczas przebudowy indeksów, upewnij się, że przydzielić wystarczającą ilość pamięci do sesji, która odbudowuje indeks.  Aby to zrobić, należy zwiększyć klasę zasobów użytkownika, który ma uprawnienia do odbudowania indeksu w tej tabeli do zalecanego minimum.

Poniżej znajduje się przykład jak przydzielić więcej pamięci do użytkownika, zwiększając ich klasy zasobów. Aby pracować z klasami zasobów, zobacz [Klasy zasobów do zarządzania obciążeniem](resource-classes-for-workload-management.md).

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Krok 2: Przebudowanie indeksów klastrowanego magazynu kolumn z użytkownikiem klasy zasobów

Zaloguj się jako użytkownik z kroku 1 (np. LoadUser), który jest teraz przy użyciu wyższej klasy zasobów i wykonać alter index instrukcji. Upewnij się, że ten użytkownik ma uprawnienie ALTER do tabel, w których indeks jest przebudowywany. Te przykłady pokazują, jak odbudować cały indeks magazynu kolumn lub jak odbudować pojedynczą partycję. W dużych tabelach jest bardziej praktyczne, aby odbudować indeksy pojedynczej partycji naraz.

Alternatywnie zamiast przebudowy indeksu, można skopiować tabelę do nowej tabeli [przy użyciu CTAS](sql-data-warehouse-develop-ctas.md). W którą stronę jest najlepsza? W przypadku dużych ilości danych, CTAS jest zwykle szybszy niż [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql). W przypadku mniejszych ilości danych alter index jest łatwiejszy w użyciu i nie wymaga wymiany tabeli.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

Odbudowanie indeksu w puli SQL Synapse jest operacją w trybie offline.  Aby uzyskać więcej informacji na temat odbudowy indeksów, zobacz sekcję ALTER INDEX REBUILD w [kolumnie Indeksy defragmentacji](/sql/relational-databases/indexes/columnstore-indexes-defragmentation)i [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql).

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Krok 3: Sprawdź, czy poprawiła się jakość segmentu klastrowanego magazynu kolumn

Uruchom ponownie kwerendę, która zidentyfikowała tabelę o niskiej jakości segmentu i sprawdź, czy jakość segmentu uległa poprawie.  Jeśli jakość segmentu nie poprawiła się, może się okazać, że wiersze w tabeli są bardzo szerokie.  Należy rozważyć użycie wyższej klasy zasobów lub DWU podczas przebudowy indeksów.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Przebudowywanie indeksów z CTAS i przełączaniem partycji

W tym przykładzie użyto [instrukcji CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) i przełączania partycji w celu odbudowania partycji tabeli.

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Switch IN the rebuilt data with TRUNCATE_TARGET option
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2 WITH (TRUNCATE_TARGET = ON);
```

Aby uzyskać więcej informacji na temat ponownego tworzenia partycji przy użyciu CTAS, zobacz [Używanie partycji w puli programu Synapse SQL](sql-data-warehouse-tables-partition.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tworzenia tabel, zobacz [Tworzenie tabel](sql-data-warehouse-tables-overview.md).
