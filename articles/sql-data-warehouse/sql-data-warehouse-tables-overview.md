---
title: Projektowanie tabelach - Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Wprowadzenie do projektowania tabel w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: d299ff0d8e719040d503852af6056d9d87738b7d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31527943"
---
# <a name="designing-tables-in-azure-sql-data-warehouse"></a>Projektowanie tabelach w magazynie danych SQL Azure

Dowiedz się, podstawowe pojęcia dotyczące projektowania tabel w usłudze Azure SQL Data Warehouse. 

## <a name="determine-table-category"></a>Określenia kategorii tabeli 

A [schemat gwiazdy](https://en.wikipedia.org/wiki/Star_schema) organizuje dane w tabelach faktów i wymiarów. Niektóre tabele są używane do integracji lub przemieszczania danych przed przesyłane do tabeli faktów lub wymiaru. Podczas projektowania tabeli zdecyduj, czy dane w tabeli, należy w faktów, wymiarów lub tabeli integracji. Ta decyzja informuje struktura tabeli odpowiednie i dystrybucji. 

- **Tabele faktów** zawierają ilościowe dane, które są często generowane w transakcyjnych systemu, a następnie ładowany do magazynu danych. Na przykład sklepu generuje transakcje sprzedaży każdego dnia, a następnie ładuje dane do tabeli faktów magazynu danych do analizy.

- **Tabele wymiarów** zawierają dane atrybutów, które mogą ulec zmianie, ale zazwyczaj zmienia się rzadko. Na przykład nazwę i adres odbiorcy są przechowywane w tabeli wymiarów i aktualizacji, tylko jeśli odbiorcy zmian w profilu. Aby zminimalizować rozmiar tabeli faktów dużych, nazwę i adres odbiorcy nie trzeba w każdym wierszu tabeli faktów. Zamiast tego tabeli faktów i tabeli wymiarów mogą udostępniać identyfikatora klienta Zapytanie może dołączać dwóch tabel, aby skojarzyć profil klienta i transakcji. 

- **Tabele integracji** służą do integracji lub przemieszczania danych. Można utworzyć tabeli integracji jako zwykłą tabelę, tabelę zewnętrzną lub tabeli tymczasowej. Można na przykład, ładowanie danych do tabeli tymczasowej, wykonać przekształcenia danych tymczasowych i następnie wstawianie danych do tabeli produkcji.

## <a name="schema-and-table-names"></a>Nazwy schematu i tabeli
W usłudze SQL Data Warehouse magazyn danych jest typ bazy danych. Wszystkie tabele w magazynie danych są zawarte w tej samej bazy danych.  Nie można przyłączyć tabel na wiele magazynów danych. To zachowanie różni się od programu SQL Server, który obsługuje sprzężenia między bazami danych. 

W bazie danych programu SQL Server może używać faktów i wymiarów, lub integracji dla nazwy schematu. W przypadku migracji bazy danych programu SQL Server z magazynem danych SQL, działa najlepiej do migracji wszystkie tabele faktów, wymiarów i integracja z jednego schematu w usłudze SQL Data Warehouse. Na przykład można przechowywać wszystkie tabele w [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap) hurtowni danych przykładowych w ramach jednego schematu o nazwie wwi. Poniższy kod tworzy [użytkownika schematu](/sql/t-sql/statements/create-schema-transact-sql) o nazwie wwi.

```sql
CREATE SCHEMA wwi;
```

Aby pokazać organizacji tabel w usłudze SQL Data Warehouse, int, faktów i wymiar może być używany jako prefiksy do nazwy tabeli. W poniższej tabeli przedstawiono niektóre nazwy schematu i tabeli dla WideWorldImportersDW. Porównuje nazw w programie SQL Server z nazw w usłudze SQL Data Warehouse. 

| Tabela WideWorldImportersDW  | Typ tabeli | Oprogramowanie SQL Server | SQL Data Warehouse |
|:-----|:-----|:------|:-----|
| Miasto | Wymiar | Dimension.City | wwi.DimCity |
| Kolejność | Fakt | Fact.Order | wwi.FactOrder |


## <a name="table-persistence"></a>Trwałość tabeli 

Dane przechowywane w tabelach trwale w usłudze Azure Storage, tymczasowo w usłudze Azure Storage albo w magazynie danych zewnętrznych w magazynie danych.

### <a name="regular-table"></a>Zwykłą tabelę

Zwykłą tabelę przechowuje dane w magazynie Azure, jako część magazynu danych. W tabeli i dane utrwalić niezależnie od tego, czy sesja jest otwarty.  W tym przykładzie tworzy zwykłą tabelę z kolumnami. 

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Tabeli tymczasowej
Czas trwania sesji istnieje tylko tabeli tymczasowej. Używając tabeli tymczasowej uniemożliwienia innych celów tymczasowych wyników i ograniczyć potrzebę oczyszczania.  Ponieważ tabele tymczasowe również korzystać z lokalnego magazynu, umożliwiając im szybsza dla niektórych operacji.  Aby uzyskać więcej informacji, zobacz [tabel tymczasowych](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Tabela zewnętrzna
Wskazuje tabelę zewnętrzną danych znajdujących się w obiektu blob magazynu Azure lub usługi Azure Data Lake Store. W przypadku używania w połączeniu z instrukcji CREATE TABLE AS SELECT, wybierając z tabeli zewnętrznej importuje dane do usługi SQL Data Warehouse. Tabele zewnętrzne w związku z tym są przydatne do ładowania danych. Samouczek ładowania, zobacz [PolyBase używana do ładowania danych z magazynu obiektów blob Azure](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="data-types"></a>Typy danych
Magazyn danych SQL obsługuje najczęściej używane typy danych. Aby uzyskać listę obsługiwanych typów danych, zobacz [typów danych w odwołaniu do utworzenia tabeli](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) w instrukcji CREATE TABLE. Minimalizując rozmiar typów danych o pomaga zwiększyć wydajność przeszukiwania. Aby uzyskać instrukcje na używanie typów danych, zobacz [typy danych](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Rozproszone tabele
Podstawowych funkcji usługi SQL Data Warehouse jest sposób można przechowywać i działającego w tabelach przez 60 [dystrybucje](massively-parallel-processing-mpp-architecture.md#distributions).  Tabele są dystrybuowane za pomocą metody okrężnego, skrótu lub replikacji.

### <a name="hash-distributed-tables"></a>Tabele rozproszonych wyznaczania wartości skrótu
Skrót dystrybucji dystrybuuje wiersze na podstawie wartości w kolumnie dystrybucji. Tabela rozpowszechniane skrót zaprojektowano w celu osiągnięcia wysokiej wydajności dla zapytanie sprzęga na dużych tabel. Istnieje kilka czynników, które mają wpływ na wybór kolumny dystrybucji. 

Aby uzyskać więcej informacji, zobacz [projektowania wskazówek dla tabel rozproszonej](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Zreplikowane tabele
Zreplikowanej tabeli ma pełną kopię tabel dostępnych w każdym węźle obliczeń. Uruchom szybkie na zapytania zreplikowane tabele, ponieważ sprzężenia w zreplikowanych tabelach nie wymaga przenoszenia danych. Replikacja wymaga dodatkowego magazynu i nie jest praktyczne dużych tabel. 

Aby uzyskać więcej informacji, zobacz [projekt, wskazówki dotyczące zreplikowanych tabel](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Tabele okrężnego
Tabeli okrężnego dystrybuuje wiersze tabeli równomiernie we wszystkich dystrybucji. Wiersze są dystrybuowane losowo. Ładowanie danych do tabeli okrężnego jest szybkie.  Jednak kwerend może wymagać więcej przenoszenia danych od innych metod dystrybucji. 

Aby uzyskać więcej informacji, zobacz [projektowania wskazówek dla tabel rozproszonej](sql-data-warehouse-tables-distribute.md).


### <a name="common-distribution-methods-for-tables"></a>Typowe metody dystrybucji tabel
Kategorii tabeli często określa się opcji do wyboru dystrybucji tabeli. 

| Kategoria tabeli | Opcja zalecana dystrybucji |
|:---------------|:--------------------|
| Fakt           | Za pomocą skrótu dystrybucji klastrowany indeks magazynu kolumn. Dwie tabele wyznaczania wartości skrótu są połączone w tej samej kolumnie dystrybucji zwiększa wydajność. |
| Wymiar      | Użycie replikowane mniejszych tabel. Jeśli tabela jest zbyt duży, aby przechowywać w każdym węźle obliczeń, użyj skrótu rozproszonych. |
| Przemieszczanie        | Użyj okrężnego dla tabeli przemieszczania. Obciążenia o CTAS jest szybkie. Po zaimportowaniu danych w tabeli przemieszczania, użyj INSERT... Wybierz, aby przenieść dane do tabel produkcji. |

## <a name="table-partitions"></a>Partycje tabel
Tabeli partycjonowanej przechowuje i wykonuje operacje w wierszach tabeli zgodnie z zakresy danych. Na przykład tabeli może być dzielony na partycje według dnia, miesiąca lub roku. Można zwiększyć wydajność przeszukiwania poprzez usunięcie partycji, co ogranicza skanowania zapytania do danych w partycji. Można również zarządzać danymi za pomocą przełączanie partycji. Ponieważ dane w magazynie danych SQL została już wysłana, zbyt wiele partycji może zmniejszyć wydajność kwerend. Aby uzyskać więcej informacji, zobacz [partycjonowania wskazówki](sql-data-warehouse-tables-partition.md).

## <a name="columnstore-indexes"></a>Indeksy magazynu kolumn
Domyślnie usługa SQL Data Warehouse przechowuje tabeli jako klastrowany indeks magazynu kolumn. Ten formularz magazyn danych uzyskuje kompresji danych wysokiej i wydajność zapytań, na dużych tabel.  Klastrowany indeks magazynu kolumn jest zwykle najlepszym wyborem, ale w niektórych przypadkach indeksu klastrowanego lub sterty jest strukturą odpowiedniego magazynu.

Aby uzyskać listę funkcji magazynu kolumn, zobacz [Nowości dla indeksów magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-whats-new). Aby zwiększyć wydajność indeksu magazynu kolumn, zobacz [maksymalizacja jakości i dla indeksów magazynu kolumn](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>Statystyki
Optymalizator zapytań używa statystyki na poziomie kolumny, podczas tworzenia planu wykonania zapytania. Aby zwiększyć wydajność zapytań, ważne jest tworzenie statystyk dotyczących poszczególnych kolumn, szczególnie kolumn używana w sprzężeniach zapytania. Tworzenie i aktualizowanie statystyk nie odbywa się automatycznie. [Tworzenie statystyk](/sql/t-sql/statements/create-statistics-transact-sql) po utworzeniu tabeli. Aktualizowanie statystyk po znacznej liczby wierszy są dodane lub zmienione. Na przykład aktualizowanie statystyk po obciążenia. Aby uzyskać więcej informacji, zobacz [wskazówki statystyki](sql-data-warehouse-tables-statistics.md).

## <a name="commands-for-creating-tables"></a>Polecenia służące do tworzenia tabel
Można utworzyć tabelę jako nową, pustą tabelę. Można również tworzyć i wypełnianie tabeli z wynikami instrukcji select. Poniżej przedstawiono polecenia T-SQL do tworzenia tabeli.

| Instrukcja T-SQL | Opis |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | Tworzy pustą tabelę, definiując wszystkie kolumny tabeli i opcje. |
| [TWORZENIE TABELI ZEWNĘTRZNEJ](/sql/t-sql/statements/create-external-table-transact-sql) | Tworzy tabelę zewnętrzną. Definicja tabeli jest przechowywany w usłudze SQL Data Warehouse. Dane tabeli są przechowywane w magazynie obiektów Blob Azure lub usługi Azure Data Lake Store. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | Wypełnia nową tabelę z wynikami instrukcji select. Typy danych i kolumn tabeli są oparte na wynikach instrukcji select. Aby zaimportować dane, tej instrukcji można wybrać z tabeli zewnętrznej. |
| [UTWÓRZ WYBIERZ AS TABELI ZEWNĘTRZNEJ](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | Tworzy nową tabelę zewnętrznego przez eksportowanie wyników instrukcję select do lokalizacji zewnętrznej.  Lokalizacja jest magazynu obiektów Blob platformy Azure lub usługi Azure Data Lake Store. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Wyrównywanie danych źródłowych w magazynie danych

Tabel magazynu danych są wypełniane podczas ładowania danych z innego źródła danych. Do wykonania pomyślnego obciążenia, liczbę i typy danych kolumn w danych źródłowych muszą być wyrównane z definicji tabeli w magazynie danych. Pobieranie danych, aby były wyrównane może być częścią najtrudniejsze projektowanie tabelach. 

Jeśli wiele magazynów danych pochodzą dane, można przenosić dane do magazynu danych i zapisz go w tabeli integracji. Po danych w tabeli integracji można użyć możliwości usługi SQL Data Warehouse w celu wykonania operacji przekształcania. Po danych jest gotowy, można wstawić je do tabel w środowisku produkcyjnym.

## <a name="unsupported-table-features"></a>Funkcje nieobsługiwane tabeli
Magazyn danych SQL obsługuje wiele, ale nie wszystkie funkcje tabeli oferowanych przez inne bazy danych.  Poniższa lista zawiera niektóre z funkcji tabeli, które nie są obsługiwane w usłudze SQL Data Warehouse.

- Klucz podstawowy, kluczy obcych Unique, sprawdzanie [ograniczenia tabeli](/sql/t-sql/statements/alter-table-table-constraint-transact-sql)

- [Kolumny obliczane](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql)
- [Indeksowane widoki](/sql/relational-databases/views/create-indexed-views)
- [Sekwencja](/sql/t-sql/statements/create-sequence-transact-sql)
- [Kolumny rozrzedzone](/sql/relational-databases/tables/use-sparse-columns)
- [Surogat klucze](). Wdrożenia z [tożsamości](sql-data-warehouse-tables-identity.md).
- [Synonimy](/sql/t-sql/statements/create-synonym-transact-sql)
- [Wyzwalacze](/sql/t-sql/statements/create-trigger-transact-sql)
- [Unikatowe indeksy](/sql/t-sql/statements/create-index-transact-sql)
- [Typy definiowane przez użytkownika](/sql/relational-databases/native-client/features/using-user-defined-types)

## <a name="table-size-queries"></a>Zapytania o rozmiar tabeli
Prosty sposób identyfikacji miejsca i używane przez tabelę w każdym z 60 dystrybucje wierszy jest użycie [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Jednak przy użyciu polecenia DBCC może być dość ograniczenie.  Dynamicznych widoków zarządzania (widoków DMV) wyświetlić więcej szczegółów niż polecenia DBCC. Rozpocznij od utworzenia tego widoku.

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

### <a name="table-space-summary"></a>Podsumowanie obszaru tabel

To zapytanie zwraca wiersze i miejsca przez tabelę.  Umożliwia znaleźć tabel, które są największych tabel i czy są okrężnego, replikowane, lub skrótu - rozproszonych.  Rozpowszechniane skrót tabel zapytanie zawiera kolumny dystrybucji.  

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

### <a name="table-space-by-distribution-type"></a>Obszar tabel według typu dystrybucji

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

### <a name="table-space-by-index-type"></a>Obszar tabel według typu indeksu

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

### <a name="distribution-space-summary"></a>Miejsce dystrybucji podsumowania

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

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu tabel dla magazynu danych, następnym krokiem jest ładowanie danych do tabeli.  Samouczek ładowania, zobacz [podczas ładowania danych do usługi SQL Data Warehouse](load-data-wideworldimportersdw.md).