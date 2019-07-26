---
title: Projektowanie tabel — Azure SQL Data Warehouse | Microsoft Docs
description: Wprowadzenie do projektowania tabel w Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/15/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: d97326430eebcaea64770e99c26ab593b51d5847
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68476751"
---
# <a name="designing-tables-in-azure-sql-data-warehouse"></a>Projektowanie tabel w Azure SQL Data Warehouse

Poznaj kluczowe pojęcia związane z projektowaniem tabel w Azure SQL Data Warehouse. 

## <a name="determine-table-category"></a>Określanie kategorii tabeli 

[Schemat gwiazdy](https://en.wikipedia.org/wiki/Star_schema) organizuje dane w postaci tabel faktów i wymiarów. Niektóre tabele są używane na potrzeby integracji lub danych przemieszczania przed przeniesieniem ich do tabeli faktów lub wymiarów. Podczas projektowania tabeli należy zdecydować, czy dane tabeli należą do tabeli faktów, wymiarów lub integracji. Ta decyzja informuje o odpowiedniej strukturze i dystrybucji tabeli. 

- **Tabele faktów** zawierają ilościowe dane, które są często generowane w systemie transakcyjnym, a następnie ładowane do magazynu danych. Na przykład firma detaliczna generuje transakcje sprzedaży codziennie, a następnie ładuje dane do tabeli faktów magazynu danych na potrzeby analizy.

- **Tabele wymiarów** zawierają dane atrybutów, które mogą ulec zmianie, ale zazwyczaj zmieniają się rzadko. Na przykład nazwa i adres klienta są przechowywane w tabeli wymiarów i aktualizowane tylko w przypadku zmiany profilu klienta. Aby zminimalizować rozmiar dużej tabeli faktów, nazwa i adres klienta nie muszą znajdować się w każdym wierszu tabeli faktów. Zamiast tego tabela faktów i tabela wymiarów mogą współdzielić identyfikator klienta. Zapytanie może sprzęgać dwie tabele, aby skojarzyć profil i transakcje klienta. 

- **Tabele integracji** zapewniają miejsce do integrowania lub przygotowywania danych. Tabelę integracji można utworzyć jako tabelę regularną, tabelę zewnętrzną lub tabelę tymczasową. Na przykład można załadować dane do tabeli przejściowej, wykonać przekształcenia danych w procesie przejściowym, a następnie wstawić dane do tabeli produkcyjnej.

## <a name="schema-and-table-names"></a>Nazwy schematu i tabeli
Schematy są dobrym sposobem grupowania tabel, które są używane w podobny sposób.  W przypadku migrowania wielu baz danych z rozwiązania Premium do SQL Data Warehouse, najlepiej Sprawdzamy migrację wszystkich tabel faktów, wymiarów i integracji do jednego schematu w SQL Data Warehouse. Można na przykład przechowywać wszystkie tabele w [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap) przykładowego magazynu danych w ramach jednego schematu o nazwie WWI. Poniższy kod tworzy [schemat zdefiniowany przez użytkownika](/sql/t-sql/statements/create-schema-transact-sql) o nazwie WWI.

```sql
CREATE SCHEMA wwi;
```

Aby wyświetlić organizację tabel w SQL Data Warehouse, można użyć faktów, Dim i int jako prefiksów nazw tabel. W poniższej tabeli przedstawiono niektóre z nazw schematu i tabeli dla WideWorldImportersDW.  

| Tabela WideWorldImportersDW  | Typ tabeli | SQL Data Warehouse |
|:-----|:-----|:------|:-----|
| City | Wymiar | wwi.DimCity |
| Zamówienie | Fakt | wwi.FactOrder |


## <a name="table-persistence"></a>Trwałość tabeli 

Tabele przechowują dane trwale w usłudze Azure Storage, tymczasowo w usłudze Azure Storage lub w magazynie danych zewnętrznym z magazynem danych.

### <a name="regular-table"></a>Zwykła tabela

Zwykła tabela przechowuje dane w usłudze Azure Storage jako część magazynu danych. Tabelę i dane pozostają bez względu na to, czy sesja jest otwarta.  Ten przykład tworzy zwykłą tabelę z dwiema kolumnami. 

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Tabela tymczasowa
Tabela tymczasowa istnieje tylko na czas trwania sesji. Możesz użyć tabeli tymczasowej, aby uniemożliwić innym użytkownikom wyświetlanie wyników tymczasowych, a także ograniczyć potrzebę czyszczenia.  Tabele tymczasowe wykorzystują magazyn lokalny do zapewnienia szybkiej wydajności.  Aby uzyskać więcej informacji, zobacz [tabele tymczasowe](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Tabela zewnętrzna
Zewnętrzna tabela wskazuje dane znajdujące się w obiekcie blob usługi Azure Storage lub Azure Data Lake Store. Gdy jest używany w połączeniu z instrukcją CREATE TABLE jako SELECT, wybranie z tabeli zewnętrznej importuje dane do SQL Data Warehouse. Tabele zewnętrzne są zatem przydatne do ładowania danych. Aby zapoznać się z samouczkiem dotyczącym ładowania, zobacz [Korzystanie z bazy danych w celu ładowania dane z usługi Azure Blob Storage](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="data-types"></a>Typy danych
SQL Data Warehouse obsługuje najczęściej używane typy danych. Aby zapoznać się z listą obsługiwanych typów danych, zobacz [typy danych w CREATE TABLE odwołanie](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) w instrukcji CREATE TABLE. Aby uzyskać wskazówki dotyczące używania typów danych, zobacz [typy danych](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Rozproszone tabele
Podstawową funkcją SQL Data Warehouse jest sposób, w jaki można przechowywać i pracować w tabelach w ramach [dystrybucji](massively-parallel-processing-mpp-architecture.md#distributions).  SQL Data Warehouse obsługuje trzy metody dystrybucji danych, działania okrężnego (domyślnego), skrótu i zreplikowane.

### <a name="hash-distributed-tables"></a>Tabele rozproszone z mieszaniem
Rozproszona tabela skrótów dystrybuuje wiersze na podstawie wartości w kolumnie dystrybucja. Rozproszonej tabeli skrótów jest przeznaczony do osiągania wysokiej wydajności dla zapytań w dużych tabelach. Podczas wybierania kolumny dystrybucji należy wziąć pod uwagę kilka czynników. 

Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące projektowania tabel rozproszonych](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Zreplikowane tabele
Replikowana tabela ma pełną kopię tabeli dostępną w każdym węźle obliczeniowym. Zapytania uruchamiają się szybko w zreplikowanych tabelach, ponieważ sprzężenia w zreplikowanych tabelach nie wymagają przenoszenia danych. Replikacja wymaga dodatkowego magazynu, chociaż nie jest to rozwiązanie praktyczne w przypadku dużych tabel. 

Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące projektowania zreplikowanych tabel](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Tabele działające w trybie okrężnym
Tabela z działaniem okrężnym dystrybuuje wiersze tabeli równomiernie między wszystkimi dystrybucjami. Wiersze są dystrybuowane losowo. Ładowanie danych do tabeli okrężnej jest szybkie.  Jednak zapytania mogą wymagać więcej ruchów danych niż inne metody dystrybucji. 

Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące projektowania tabel rozproszonych](sql-data-warehouse-tables-distribute.md).

### <a name="common-distribution-methods-for-tables"></a>Typowe metody dystrybucji dla tabel
Kategoria tabeli często określa, którą opcję należy wybrać do dystrybucji tabeli. 

| Kategoria tabeli | Zalecana opcja dystrybucji |
|:---------------|:--------------------|
| Fakt           | Używaj dystrybucji skrótów z klastrowanym indeksem magazynu kolumn. Zwiększona wydajność, gdy dwie tabele skrótów są przyłączone do tej samej kolumny dystrybucji. |
| Wymiar      | Użyj replikacji dla mniejszych tabel. Jeśli tabele są zbyt duże, aby były przechowywane w poszczególnych węzłach obliczeniowych, użyj rozproszonego tworzenia skrótów. |
| Przemieszczanie        | Użyj działania okrężnego dla tabeli przemieszczania. Ładowanie za pomocą CTAS jest szybkie. Gdy dane są w tabeli przemieszczania, użyj instrukcji INSERT... Wybierz, aby przenieść dane do tabel produkcyjnych. |

## <a name="table-partitions"></a>Partycje tabel
Partycjonowana tabela przechowuje i wykonuje operacje na wierszach tabeli zgodnie z zakresami danych. Na przykład tabela może być partycjonowana według dnia, miesiąca lub roku. Można poprawić wydajność zapytań za pomocą eliminacji partycji, co ogranicza skanowanie zapytania do danych w ramach partycji. Możesz również zachować dane za pomocą przełączania partycji. Ponieważ dane w SQL Data Warehouse są już dystrybuowane, zbyt wiele partycji może spowalniać wydajność zapytań. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące partycjonowania](sql-data-warehouse-tables-partition.md).  Po przełączeniu partycji na partycje tabeli, które nie są puste, należy rozważyć użycie opcji TRUNCATE_TARGET w instrukcji [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) , jeśli istniejące dane mają zostać obcięte. Poniższy kod umożliwia przełączenie danych dziennych na SalesFact zastępowanie wszelkich istniejących danych. 

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

## <a name="columnstore-indexes"></a>Indeksy magazynu kolumn
Domyślnie SQL Data Warehouse przechowuje tabelę jako klastrowany indeks magazynu kolumn. Ta forma magazynu danych zapewnia wysoką kompresję danych i wydajność zapytań w przypadku dużych tabel.  Klastrowany indeks magazynu kolumn jest zazwyczaj najlepszym wyborem, ale w niektórych przypadkach indeks klastrowany lub sterta jest odpowiednią strukturą magazynu.  Tabela sterty może być szczególnie przydatna do ładowania danych przejściowych, takich jak tabela przemieszczania, która jest przekształcana w ostateczną tabelę.

Aby zapoznać się z listą funkcji magazynu kolumn, zobacz [co nowego w indeksach magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-what-s-new). Aby poprawić wydajność indeksu magazynu kolumn, zobacz [maksymalizacja jakości grupy wierszy dla indeksów magazynu kolumn](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>Statystyka
Optymalizator zapytań używa statystyk na poziomie kolumny podczas tworzenia planu wykonywania zapytania. Aby poprawić wydajność zapytań, ważne jest posiadanie statystyk dla poszczególnych kolumn, zwłaszcza kolumn używanych w sprzężeniach zapytań. [Tworzenie statystyk](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistic) odbywa się automatycznie.  Jednak aktualizowanie statystyk nie odbywa się automatycznie. Aktualizacja statystyk po dodaniu lub zmianie znaczącej liczby wierszy. Na przykład zaktualizuj statystyki po załadowaniu. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące statystyk](sql-data-warehouse-tables-statistics.md).

## <a name="commands-for-creating-tables"></a>Polecenia służące do tworzenia tabel
Tabelę można utworzyć jako nową pustą tabelę. Możesz również utworzyć i wypełnić tabelę z wynikami instrukcji SELECT. Poniżej przedstawiono polecenia T-SQL służące do tworzenia tabeli.

| Instrukcja T-SQL | Opis |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | Tworzy pustą tabelę przez zdefiniowanie wszystkich kolumn i opcji tabeli. |
| [TWORZENIE TABELI ZEWNĘTRZNEJ](/sql/t-sql/statements/create-external-table-transact-sql) | Tworzy tabelę zewnętrzną. Definicja tabeli jest przechowywana w SQL Data Warehouse. Dane tabeli są przechowywane w usłudze Azure Blob Storage lub Azure Data Lake Store. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | Wypełnia nową tabelę wynikami instrukcji SELECT. Kolumny tabeli i typy danych są oparte na wynikach instrukcji SELECT. Aby zaimportować dane, ta instrukcja może zostać wybrana z tabeli zewnętrznej. |
| [UTWÓRZ TABELĘ ZEWNĘTRZNĄ JAKO WYBRANĄ](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | Tworzy nową tabelę zewnętrzną przez wyeksportowanie wyników instrukcji SELECT do lokalizacji zewnętrznej.  Lokalizacją jest usługa Azure Blob Storage lub Azure Data Lake Store. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Wyrównywanie danych źródłowych z magazynem danych

Tabele magazynu danych są wypełniane przez załadowanie danych z innego źródła danych. Aby wykonać pomyślne obciążenie, liczba i typy danych kolumn w danych źródłowych muszą być wyrównane z definicją tabeli w magazynie danych. Pobieranie danych do wyrównania może być najtrudniejszą częścią projektowania tabel. 

Jeśli dane pochodzą z wielu magazynów danych, dane można przenieść do magazynu danych i zapisać je w tabeli integracji. Gdy dane są w tabeli integracji, można użyć możliwości SQL Data Warehouse do wykonywania operacji transformacji. Po przygotowaniu danych można je wstawić do tabel produkcyjnych.

## <a name="unsupported-table-features"></a>Nieobsługiwane funkcje tabeli
SQL Data Warehouse obsługuje wiele funkcji tabel oferowanych przez inne bazy danych, ale nie wszystkie.  Na poniższej liście przedstawiono niektóre funkcje tabeli, które nie są obsługiwane w programie SQL Data Warehouse.

- Klucz podstawowy, klucze obce, unikatowe, sprawdzanie [ograniczeń tabeli](/sql/t-sql/statements/alter-table-table-constraint-transact-sql)

- [Kolumny obliczane](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql)
- [Indeksowane widoki](/sql/relational-databases/views/create-indexed-views)
- [Nazwisk](/sql/t-sql/statements/create-sequence-transact-sql)
- [Kolumny rozrzedzone](/sql/relational-databases/tables/use-sparse-columns)
- Klucze zastępcze. Zaimplementuj [tożsamość](sql-data-warehouse-tables-identity.md).
- [Synonimy](/sql/t-sql/statements/create-synonym-transact-sql)
- [Wyzwalacze](/sql/t-sql/statements/create-trigger-transact-sql)
- [Indeksy unikatowe](/sql/t-sql/statements/create-index-transact-sql)
- [Typy zdefiniowane przez użytkownika](/sql/relational-databases/native-client/features/using-user-defined-types)

## <a name="table-size-queries"></a>Zapytania o rozmiar tabeli
Jednym z prostych sposobów identyfikowania miejsca i wierszy używanych przez tabelę w każdym z dystrybucji 60 jest użycie [polecenia DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Jednak użycie poleceń DBCC może być dość ograniczone.  Dynamiczne widoki zarządzania (widoków DMV) pokazują więcej szczegółów niż polecenia DBCC. Zacznij od utworzenia tego widoku.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### <a name="table-space-summary"></a>Podsumowanie obszaru tabeli

To zapytanie zwraca wiersze i spację według tabeli.  Pozwala on zobaczyć, które tabele są największymi tabelami i czy są one działające w sposób okrężny, zreplikowany lub oparty na skrócie.  W przypadku tabel dystrybuowanych przez funkcję mieszania zapytanie pokazuje kolumnę dystrybucji.  

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Przestrzeń tabeli według typu dystrybucji

```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Przestrzeń tabeli według typu indeksu

```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Podsumowanie przestrzeni dystrybucji

```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Następne kroki
Następnym krokiem po utworzeniu tabel dla hurtowni danych jest załadowanie danych do tabeli.  Aby zapoznać się z samouczkiem ładowania, zobacz [ładowanie danych do SQL Data Warehouse](load-data-wideworldimportersdw.md).
