---
title: Projektowanie tabel
description: Wprowadzenie do projektowania tabel w puli SQL Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/15/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 4c5964bc944cd50e05d548eb731450a4944e854d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631261"
---
# <a name="design-tables-in-synapse-sql-pool"></a>Tabele projektowania w puli sql synapse

Ten artykuł zawiera kluczowe pojęcia wprowadzające do projektowania tabel w puli SQL.

## <a name="determine-table-category"></a>Określanie kategorii tabeli

[Schemat gwiazdy](https://en.wikipedia.org/wiki/Star_schema) organizuje dane w tabelach faktów i wymiarów. Niektóre tabele są używane do integracji lub przemieszczania danych, zanim zostaną przesunie się do tabeli faktów lub wymiarów. Podczas projektowania tabeli zdecyduj, czy dane tabeli należą do tabeli faktów, wymiarów czy integracji. Niniejsza decyzja informuje o odpowiedniej strukturze i dystrybucji tabeli.

- **Tabele faktów** zawierają dane ilościowe, które są często generowane w systemie transakcyjnym, a następnie ładowane do puli SQL. Na przykład firma detaliczna generuje transakcje sprzedaży codziennie, a następnie ładuje dane do tabeli faktów puli SQL do analizy.

- **Tabele wymiarów** zawierają dane atrybutów, które mogą ulec zmianie, ale zwykle zmieniają się rzadko. Na przykład nazwa i adres klienta są przechowywane w tabeli wymiarów i aktualizowane tylko wtedy, gdy profil klienta się zmieni. Aby zminimalizować rozmiar dużej tabeli faktów, nazwa i adres odbiorcy nie muszą znajdować się w każdym wierszu tabeli faktów. Zamiast tego tabela faktów i tabela wymiarów mogą współużytkować identyfikator klienta. Kwerenda może dołączyć do dwóch tabel, aby skojarzyć profil klienta i transakcje.

- **Tabele integracji** zapewniają miejsce do integracji lub przemieszczania danych. Tabelę integracji można utworzyć jako zwykłą tabelę, tabelę zewnętrzną lub tabelę tymczasową. Na przykład można załadować dane do tabeli przemieszczania, wykonać przekształcenia na danych w przemieszczania, a następnie wstawić dane do tabeli produkcyjnej.

## <a name="schema-and-table-names"></a>Nazwy schematów i tabel

Schematy są dobrym sposobem na grupowanie tabel, używane w podobny sposób, razem.  Jeśli migrujesz wiele baz danych z rozwiązania prem do puli SQL, najlepiej jest przeprowadzić migrację wszystkich tabel faktów, wymiarów i integracji do jednego schematu w puli SQL.

Na przykład można przechowywać wszystkie tabele w [wideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) przykładowej puli SQL w jednym schemacie o nazwie wwi. Poniższy kod tworzy [schemat zdefiniowany przez użytkownika](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) o nazwie wwi.

```sql
CREATE SCHEMA wwi;
```

Aby wyświetlić organizację tabel w puli SQL, można użyć fakt, dim i int jako prefiksy do nazw tabel. W poniższej tabeli przedstawiono niektóre nazwy schematów i tabel dla programu WideWorldImportersDW.  

| WideWorldImportersDW tabela  | Typ tabeli | Pula SQL |
|:-----|:-----|:------|:-----|
| Miasto | Wymiar | Wwi. DimCity (DimCity) |
| Zamówienie | Fact | Wwi. FactOrder (FactOrder) |

## <a name="table-persistence"></a>Trwałość tabeli

Tabele przechowują dane na stałe w usłudze Azure Storage, tymczasowo w usłudze Azure Storage lub w magazynie danych zewnętrznym do puli SQL.

### <a name="regular-table"></a>Zwykła tabela

Zwykła tabela przechowuje dane w usłudze Azure Storage jako część puli SQL. Tabela i dane utrzymują się niezależnie od tego, czy sesja jest otwarta.  Poniższy przykład tworzy zwykłą tabelę z dwiema kolumnami.

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Tabela tymczasowa

Tabela tymczasowa istnieje tylko na czas trwania sesji. Można użyć tabeli tymczasowej, aby uniemożliwić innym użytkownikom wyświetlanie tymczasowych wyników, a także zmniejszyć potrzebę oczyszczania.  

Tabele tymczasowe wykorzystują magazyn lokalny, aby zapewnić efektywną wydajność.  Aby uzyskać więcej informacji, zobacz [Tabele tymczasowe](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Tabela zewnętrzna

Tabela zewnętrzna wskazuje dane znajdujące się w obiekcie blob usługi Azure Storage lub sklepie Azure Data Lake Store. W połączeniu z instrukcją CREATE TABLE AS SELECT wybranie z tabeli zewnętrznej powoduje importowanie danych do puli SQL.

W związku z tym tabele zewnętrzne są przydatne do ładowania danych. Aby zapoznać się z samouczkiem ładowania, zobacz [PolyBase do ładowania danych z magazynu obiektów blob platformy Azure](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="data-types"></a>Typy danych

Pula SQL obsługuje najczęściej używane typy danych. Aby uzyskać listę obsługiwanych typów danych, zobacz [typy danych w odwołaniu DO TWORZENIA TABELI](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes) w instrukcji CREATE TABLE. Aby uzyskać wskazówki dotyczące używania typów danych, zobacz [Typy danych](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Rozproszone tabele

Podstawową cechą puli SQL jest sposób, w jaki może przechowywać i działać na tabelach w [różnych dystrybucjach.](massively-parallel-processing-mpp-architecture.md#distributions)  Pula SQL obsługuje trzy metody dystrybucji danych: okrężne (domyślne), mieszanie i replikowane.

### <a name="hash-distributed-tables"></a>Tabele dystrybuowane przy użyciu skrótu

Tabela rozproszona mieszania dystrybuuje wiersze na podstawie wartości w kolumnie dystrybucji. Tabela rozproszona mieszania jest przeznaczony do osiągnięcia wysokiej wydajności dla zapytań w dużych tabelach. Istnieje kilka czynników, które należy wziąć pod uwagę przy wyborze kolumny dystrybucji.

Aby uzyskać więcej informacji, zobacz [Wskazówki dotyczące projektowania tabel rozproszonych](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Zreplikowane tabele

Replikowana tabela ma pełną kopię tabeli dostępnej w każdym węźle obliczeniowym. Kwerendy są uruchamiane szybko w tabelach replikowanych, ponieważ sprzężenia w tabelach replikowanych nie wymagają przenoszenia danych. Replikacja wymaga dodatkowego miejsca do magazynowania i nie jest praktyczna w przypadku dużych tabel.

Aby uzyskać więcej informacji, zobacz [Wskazówki dotyczące projektowania tabel replikowanych](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Stoły okrężne

Tabela okrężna rozmieszcza wiersze tabeli równomiernie we wszystkich dystrybucjach. Wiersze są rozdzielane losowo. Ładowanie danych do tabeli okrężne jest szybkie.  Należy pamiętać, że kwerendy mogą wymagać więcej przenoszenia danych niż inne metody dystrybucji.

Aby uzyskać więcej informacji, zobacz [Wskazówki dotyczące projektowania tabel rozproszonych](sql-data-warehouse-tables-distribute.md).

### <a name="common-distribution-methods-for-tables"></a>Typowe metody dystrybucji dla tabel

Kategoria tabeli często określa, którą opcję wybrać do dystrybucji tabeli.

| Kategoria tabeli | Zalecana opcja dystrybucji |
|:---------------|:--------------------|
| Fact           | Użyj dystrybucji skrótu z indeksem klastrowanego magazynu kolumn. Zwiększa wydajność, gdy dwie tabele mieszania są połączone w tej samej kolumnie dystrybucji. |
| Wymiar      | Użyj replikowane dla mniejszych tabel. Jeśli tabele są zbyt duże do przechowywania w każdym węźle obliczeniowym, należy użyć mieszania rozproszone. |
| Przygotowanie        | Użyj okrężnego dla tabeli przemieszczania. Obciążenie ctas jest szybkie. Gdy dane są w tabeli przemieszczania, należy użyć INSERT... WYBIERZ, aby przenieść dane do tabel produkcyjnych. |

## <a name="table-partitions"></a>Partycje tabeli

Tabela podzielona na partycje przechowuje i wykonuje operacje w wierszach tabeli zgodnie z zakresami danych. Na przykład tabela może być podzielony na partycje według dnia, miesiąca lub roku. Można zwiększyć wydajność kwerendy poprzez eliminację partycji, co ogranicza skanowanie kwerendy do danych w ramach partycji. Można również zachować dane za pomocą przełączania partycji. Ponieważ dane w magazynie danych SQL jest już rozmieszczona, zbyt wiele partycji może spowolnić wydajność kwerendy. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące partycjonowania](sql-data-warehouse-tables-partition.md).  Podczas przełączania partycji do partycji tabeli, które nie są puste, należy rozważyć użycie opcji TRUNCATE_TARGET w instrukcji [TABELA ALTER,](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) jeśli istniejące dane mają być obcięty. Poniższy kod przełącza się w przekształconych danych dziennych do SalesFact zastąpienie istniejących danych.

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

## <a name="columnstore-indexes"></a>Indeksy magazynu kolumn

Domyślnie pula SQL przechowuje tabelę jako indeks klastrowanego magazynu kolumn. Ta forma magazynu danych osiąga wysoką kompresję danych i wydajność zapytań w dużych tabelach.  

Indeks klastrowanego magazynu kolumn jest zwykle najlepszym wyborem, ale w niektórych przypadkach indeks klastrowany lub sterty jest odpowiednią strukturą magazynu.  

> [!TIP]
> Tabela sterty może być szczególnie przydatna do ładowania danych przejściowych, takich jak tabela przejściowa, która jest przekształcana w tabelę końcową.

Aby uzyskać listę funkcji magazynu kolumn, zobacz [Co nowego w indeksach magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-what-s-new). Aby poprawić wydajność indeksu magazynu kolumn, zobacz [Maksymalizacja jakości grup wierszy dla indeksów magazynu kolumn](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>Statystyki

Optymalizator kwerendy używa statystyk na poziomie kolumny, gdy tworzy plan wykonywania kwerendy.

Aby zwiększyć wydajność kwerendy, ważne jest, aby mieć statystyki dla poszczególnych kolumn, zwłaszcza kolumn używanych w sprzężeniach kwerendy. [Tworzenie statystyk](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic) odbywa się automatycznie.  

Aktualizowanie statystyk nie odbywa się automatycznie. Aktualizuj statystyki po dodaniu lub zmianie znacznej liczby wierszy. Na przykład zaktualizować statystyki po załadowaniu. Aby uzyskać więcej informacji, zobacz [Wskazówki dotyczące statystyk](sql-data-warehouse-tables-statistics.md).

## <a name="primary-key-and-unique-key"></a>Klucz podstawowy i klucz unikatowy

Klucz podstawowy jest obsługiwany tylko wtedy, gdy są używane nieklastrowane i niewymnaczone.  Ograniczenie UNIQUE jest obsługiwane tylko z nieegzekwowane jest używany.  Sprawdź [ograniczenia tabeli puli SQL](sql-data-warehouse-table-constraints.md).

## <a name="commands-for-creating-tables"></a>Polecenia do tworzenia tabel

Tabelę można utworzyć jako nową pustą tabelę. Można również utworzyć i wypełnić tabelę z wynikami instrukcji select. Poniżej przedstawiono polecenia T-SQL służące do tworzenia tabeli.

| Instrukcja T-SQL | Opis |
|:----------------|:------------|
| [UTWÓRZ TABELĘ](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | Tworzy pustą tabelę, definiując wszystkie kolumny i opcje tabeli. |
| [TWORZENIE TABELI ZEWNĘTRZNEJ](/sql/t-sql/statements/create-external-table-transact-sql) | Tworzy tabelę zewnętrzną. Definicja tabeli jest przechowywana w puli SQL. Dane tabeli są przechowywane w magazynie obiektów Blob platformy Azure lub w magazynie usługi Azure Data Lake Store. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | Wypełnia nową tabelę wynikami instrukcji select. Kolumny tabeli i typy danych są oparte na wynikach instrukcji select. Aby zaimportować dane, ta instrukcja można wybrać z tabeli zewnętrznej. |
| [TWORZENIE TABELI ZEWNĘTRZNEJ JAKO WYBIERZ](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | Tworzy nową tabelę zewnętrzną, eksportując wyniki instrukcji select do lokalizacji zewnętrznej.  Lokalizacja jest magazyn obiektów Blob platformy Azure lub usługi Azure Data Lake Store. |

## <a name="aligning-source-data-with-the-sql-pool"></a>Wyrównywanie danych źródłowych do puli SQL

Tabele puli SQL są wypełniane przez ładowanie danych z innego źródła danych. Aby wykonać pomyślne obciążenie, liczba i typy danych kolumn w danych źródłowych muszą być zgodne z definicją tabeli w puli SQL. Wprowadzenie danych do wyrównania może być najtrudniejszą częścią projektowania tabel.

Jeśli dane pochodzą z wielu magazynów danych, należy załadować dane do puli SQL i przechowywać je w tabeli integracji. Gdy dane znajdują się w tabeli integracji, można użyć mocy puli SQL do wykonywania operacji transformacji. Po przygotowaniu danych można je wstawić do tabel produkcyjnych.

## <a name="unsupported-table-features"></a>Nieobsługiwały funkcje tabeli

Pula SQL obsługuje wiele, ale nie wszystkie, funkcji tabeli oferowanych przez inne bazy danych.  Na poniższej liście przedstawiono niektóre funkcje tabeli, które nie są obsługiwane w puli SQL:

- Klucz obcy, Sprawdź [ograniczenia tabeli](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Kolumny obliczane](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Widoki indeksowane](/sql/relational-databases/views/create-indexed-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Sequence](/sql/t-sql/statements/create-sequence-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Rozrzedzone kolumny](/sql/relational-databases/tables/use-sparse-columns?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- Klucze zastępcze. Zaimplementuj za pomocą [pliku Identity](sql-data-warehouse-tables-identity.md).
- [Synonimy](/sql/t-sql/statements/create-synonym-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Wyzwalacze](/sql/t-sql/statements/create-trigger-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Unikalne indeksy](/sql/t-sql/statements/create-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Typy zdefiniowane przez użytkownika](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="table-size-queries"></a>Kwerendy rozmiaru tabeli

Jednym z prostych sposobów identyfikowania miejsca i wierszy zużywanych przez tabelę w każdej z 60 dystrybucji, jest użycie [PDW_SHOWSPACEUSED DBCC](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Jednak za pomocą poleceń DBCC może być dość ograniczające.  Dynamiczne widoki zarządzania (DMV) pokazują więcej szczegółów niż polecenia DBCC. Zacznij od utworzenia tego widoku:

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
WHERE pn.[type] = 'COMPUTE'
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

### <a name="table-space-summary"></a>Podsumowanie miejsca w tabeli

Ta kwerenda zwraca wiersze i spację według tabeli.  Pozwala zobaczyć, które tabele są największe tabele i czy są one okrężne, replikowane lub mieszania -distributed.  W przypadku tabel rozproszonych mieszania kwerenda pokazuje kolumnę dystrybucyjną.  

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

### <a name="table-space-by-index-type"></a>Spacja tabeli według typu indeksu

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

### <a name="distribution-space-summary"></a>Podsumowanie przestrzeni dystrybucyjnej

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

Po utworzeniu tabel dla puli SQL następnym krokiem jest załadowanie danych do tabeli.  Aby zapoznać się z samouczkiem ładowania, zobacz [Ładowanie danych do puli SQL](load-data-wideworldimportersdw.md).
