---
title: Projektowanie tabel — Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Wprowadzenie do projektowania tabel w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/15/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 06bdd21363aee8202ce7178f157f01a5c26e3a52
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65851592"
---
# <a name="designing-tables-in-azure-sql-data-warehouse"></a>Projektowanie tabel w usłudze Azure SQL Data Warehouse

Dowiedz się, podstawowe pojęcia dotyczące projektowania tabel w usłudze Azure SQL Data Warehouse. 

## <a name="determine-table-category"></a>Określenia kategorii tabeli 

A [schemat gwiazdy](https://en.wikipedia.org/wiki/Star_schema) organizuje dane w tabelach faktów i wymiarów. Niektóre tabele są używane do integracji lub przemieszczania danych, przed przesuwa się do tabeli faktów i wymiarów. Podczas projektowania tabeli należy zdecydować, czy dane w tabeli jest powiązana faktów, wymiary lub Integracja z tabeli. Ta decyzja informuje struktury odpowiedniej tabeli i dystrybucji. 

- **Tabele faktów** zawierają dane ilościowe, które są często generowane w systemie transakcyjnych, a następnie załadowane do magazynu danych. Na przykład sklepu generuje transakcji sprzedaży, codziennie, a następnie ładuje dane do tabeli faktów magazynu danych do analizy.

- **Tabele wymiarów** zawierają dane atrybutów, które mogą ulec zmianie, ale zazwyczaj zmienia się rzadko. Na przykład nazwę i adres klienta są przechowywane w tabeli wymiarów i zaktualizować tylko wtedy, gdy profil klienta zmieni. Aby zminimalizować rozmiar to duża tabela faktów, nazwę i adres klienta nie muszą być w każdym wierszu tabeli faktów. Zamiast tego tabeli faktów i tabela wymiarów mogą udostępniać identyfikator klienta. Zapytania mogą dołączyć do dwóch tabel, aby skojarzyć profil klienta i transakcji. 

- **Tabele integracji** służą do integrowania lub przemieszczania danych. Można utworzyć tabeli usługi integracji, jak zwykłą tabelę, tabeli zewnętrznej lub tabeli tymczasowej. Można na przykład, ładowanie danych do tabeli przejściowej, wykonywanie przekształceń na danych w środowisku tymczasowym i następnie wstawianie do tabeli produkcyjnej.

## <a name="schema-and-table-names"></a>Nazwy schematu i tabeli
Schematy są dobrym sposobem na grupy tabel, używane w podobny sposób razem.  Jeśli migrujesz wielu baz danych z rozwiązania w lokalnej usłudze SQL Data Warehouse, więc sprawdza się najlepiej przeprowadzić migrację wszystkich faktów, wymiarów i tabel integracji do jednego schematu w usłudze SQL Data Warehouse. Na przykład można przechowywać wszystkie tabele w [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap) przykładowego magazynu danych w ramach jednego schematu o nazwie tabeli wwi. Poniższy kod tworzy [schemat zdefiniowany przez użytkownika](/sql/t-sql/statements/create-schema-transact-sql) wywołuje procedurę wwi.

```sql
CREATE SCHEMA wwi;
```

Aby wyświetlić organizacji tabel w usłudze SQL Data Warehouse, można użyć jako prefiksy nazwy tabel faktów, wymiary i int. W poniższej tabeli przedstawiono niektóre nazwy schematu i tabeli dla WideWorldImportersDW.  

| Tabela WideWorldImportersDW  | Typ tabeli | SQL Data Warehouse |
|:-----|:-----|:------|:-----|
| Miejscowość | Wymiar | wwi.DimCity |
| Zamówienie | Fakt | wwi.FactOrder |


## <a name="table-persistence"></a>Tabela stanów trwałych 

Tabele przechowywania danych trwale w usłudze Azure Storage, tymczasowo w usłudze Azure Storage albo w magazynie danych, zewnętrzne w stosunku do magazynu danych.

### <a name="regular-table"></a>Zwykłą tabelę

Zwykłą tabelę przechowuje dane w usłudze Azure Storage jako część magazynu danych. W tabeli i dane są zachowywane niezależnie od tego, czy sesja jest otwarty.  W tym przykładzie tworzy zwykłą tabelę zawierającą dwie kolumny. 

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Tabela tymczasowa
Tabela tymczasowa istnieje tylko na czas trwania sesji. Można użyć tabeli tymczasowej, aby uniemożliwić innym użytkownikom wyświetlanie wyników tymczasowej, a także zmniejszyć zapotrzebowanie na oczyszczanie.  Tabele tymczasowe korzystanie z magazynu lokalnego, która oferuje wydajne.  Aby uzyskać więcej informacji, zobacz [tabele tymczasowe](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Tabela zewnętrzna
Tabela zewnętrzna wskazuje danych znajdujących się w usłudze Azure Storage blob lub Azure Data Lake Store. Gdy jest używana w połączeniu z instrukcji CREATE TABLE AS SELECT, wybierając z tabeli zewnętrznej importuje dane do usługi SQL Data Warehouse. Tabele zewnętrzne w związku z tym są przydatne do ładowania danych. Samouczek ładowania, zobacz [przy użyciu technologii PolyBase do ładowania danych z usługi Azure blob storage](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="data-types"></a>Typy danych
Usługa SQL Data Warehouse obsługuje najczęściej używane typy danych. Aby uzyskać listę obsługiwanych typów danych, zobacz [typy danych w dokumentacji polecenia CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) w instrukcji CREATE TABLE. Aby uzyskać wskazówki na temat korzystania z typów danych, zobacz [typy danych](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Rozproszone tabele
Podstawową cechą usługi SQL Data Warehouse to sposób, można przechowywać i działają w tabelach w [dystrybucje](massively-parallel-processing-mpp-architecture.md#distributions).  Usługa SQL Data Warehouse obsługuje trzy metody dystrybucji danych, działanie okrężne (ustawienie domyślne) i wyznaczania wartości skrótu i replikowane.

### <a name="hash-distributed-tables"></a>Tabele rozproszone wyznaczania wartości skrótu
Rozproszona Tabela skrótów dystrybuuje wiersze na podstawie wartości w kolumnie dystrybucji. Rozproszona Tabela skrótów zaprojektowano w celu osiągnięcia wysokiej wydajności dla zapytań dotyczących dużych tabel. Istnieje kilka czynników, które należy wziąć pod uwagę podczas wybierania kolumn dystrybucji. 

Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące rozproszone tabele projektowania](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Zreplikowane tabele
Replikowanej tabeli ma pełną kopię tabel dostępnych w każdym węźle obliczeniowym. Kwerendy będą działać szybko zreplikowane tabele, ponieważ sprzężeń w zreplikowanych tabelach nie wymagają przenoszenia danych. Replikacja wymaga dodatkowego magazynu i nie jest możliwe w przypadku dużych tabel. 

Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące zreplikowane tabele projektowania](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Tabele wykorzystujące działanie okrężne
Tabela działanie okrężne dystrybuuje wiersze tabeli równomiernie we wszystkich dystrybucjach. Wiersze są dystrybuowane losowo. Trwa ładowanie danych do tabeli działanie okrężne jest szybkie.  Zapytania mogą jednak wymagać więcej przenoszenia danych niż inne metody dystrybucji. 

Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące rozproszone tabele projektowania](sql-data-warehouse-tables-distribute.md).

### <a name="common-distribution-methods-for-tables"></a>Typowe metody dystrybucji tabel
Kategoria tabeli często określa, którą opcję wybrać do dystrybucji w tabeli. 

| Kategoria tabeli | Opcja zalecanych dystrybucji |
|:---------------|:--------------------|
| Fakt           | Za pomocą dystrybucji skrótów klastrowanego indeksu magazynu kolumn. Dwie tabele skrótów są połączone w tej samej kolumnie dystrybucji zwiększa wydajność. |
| Wymiar      | Użycie replikacji dla mniejszych tabel. Jeśli tabele są zbyt duże, aby przechowywać w każdym węźle obliczeniowym, należy użyć rozproszonego wyznaczania wartości skrótu. |
| Przygotowanie        | Użyj działania okrężnego dla tabeli przemieszczania. Obciążenia za pomocą instrukcji CTAS jest szybkie. Gdy dane znajdują się w tabeli przemieszczania, użyj INSERT... Zaznacz, aby przenieść dane do tabel produkcyjnych. |

## <a name="table-partitions"></a>Partycje tabel
Tabeli partycjonowanej magazyny i wykonuje operacje na wiersze tabeli, zgodnie z zakresami danych. Na przykład tabela może być dzielone według dnia, miesiąca lub roku. Może poprawić wydajność zapytań przez usunięcie partycji, co ogranicza skanowania zapytań do danych w ramach partycji. Można również utrzymania danych poprzez przełączanie partycji. Ponieważ dane w usłudze SQL Data Warehouse została już wysłana, zbyt dużej liczby partycji może zmniejszyć wydajność zapytań. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące partycjonowania](sql-data-warehouse-tables-partition.md).  Podczas przełączania do tabeli partycji partycje, które nie są puste, rozważ użycie opcji TRUNCATE_TARGET w swojej [instrukcji ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) instrukcji, jeśli istniejące dane zostaną obcięte. Poniżej przełączników kodu w przekształcone dane dziennego do SalesFact zastąpienie istniejących danych. 

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

## <a name="columnstore-indexes"></a>Indeksy magazynu kolumn
Domyślnie usługa SQL Data Warehouse przechowuje tabelę jako klastrowany indeks magazynu kolumn. Ta forma magazynowania danych uzyskuje kompresji dużej ilości danych i wydajności zapytań w dużych tabel.  Klastrowany indeks magazynu kolumn jest zwykle najlepszym wyborem, ale w niektórych przypadkach indeksu klastrowanego lub sterty jest strukturą odpowiedniego magazynu.  Tabeli stosu może być szczególnie przydatne w przypadku ładowania danych przejściowych, takich jak tabeli przejściowej, która jest przekształcana na tabeli końcowej.

Aby uzyskać listę funkcji magazynu kolumn, zobacz [co nowego w indeksach magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-what-s-new). Aby poprawić wydajność indeksu magazynu kolumn, zobacz [maksymalizowania i jakości w indeksach magazynu kolumn](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>Statystyki
Optymalizator zapytań używa statystyki na poziomie kolumny, podczas tworzenia planu wykonania zapytania. Aby poprawić wydajność zapytań, ważne jest musi mieć statystyki dotyczące poszczególnych kolumn, szczególnie kolumny używane w sprzężeniach zapytania. [Tworzenie statystyk](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistics) odbywa się automatycznie.  Jednak zaktualizowanie statystyk nie odbywa się automatycznie. Aktualizowanie statystyk po znacznej liczby wierszy są dodane lub zmienione. Na przykład aktualizowanie statystyk po załadowaniu. Aby uzyskać więcej informacji, zobacz [wskazówki statystyki](sql-data-warehouse-tables-statistics.md).

## <a name="commands-for-creating-tables"></a>Polecenia do tworzenia tabel
Można utworzyć tabeli jako nowej, pustej tabeli. Można również utworzyć i wypełnić tabelę z wynikami instrukcji select. Poniżej przedstawiono polecenia T-SQL do tworzenia tabeli.

| Instrukcję języka T-SQL | Opis |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | Tworzy pustą tabelę, definiując wszystkie kolumny w tabeli i opcje. |
| [TWORZENIE ZEWNĘTRZNEJ TABELI](/sql/t-sql/statements/create-external-table-transact-sql) | Tworzy tabelę zewnętrzną. Definicja tabeli są przechowywane w usłudze SQL Data Warehouse. Dane tabeli są przechowywane w usłudze Azure Blob storage lub Azure Data Lake Store. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | Wypełnia nową tabelę z wynikami instrukcji select. Tabela kolumny i typy danych są oparte na wyniki instrukcji select. Aby zaimportować dane, tej instrukcji można wybrać z tabeli zewnętrznej. |
| [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | Tworzy nową tabelę zewnętrznego przez wyeksportowanie wyników instrukcji select w lokalizacji zewnętrznej.  Lokalizacja jest usługi Azure Blob storage lub Azure Data Lake Store. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Wyrównywanie danych źródłowych w magazynie danych

Tabele magazynu danych są wypełniane przez ładowanie danych z innego źródła danych. Aby wykonać pomyślne obciążenie, liczbę i typy danych kolumn w danych źródłowych muszą być dopasowane definicję tabeli w magazynie danych. Pobieranie danych, aby zorganizować projekt może być najtrudniejsze część projektowania tabel. 

Jeżeli dane pochodzą z wielu magazynów danych, możesz przenieść dane do magazynu danych i zapisać ją w tabeli usługi integracji. Po umieszczeniu danych w tabeli integracji, można użyć możliwości usługi SQL Data Warehouse do wykonywania operacji przekształcania. Po przygotowaniu danych można wstawić jej do tabel produkcyjnych.

## <a name="unsupported-table-features"></a>Funkcje nieobsługiwaną tabelę
Usługa SQL Data Warehouse obsługuje wiele, ale nie wszystkich, tabeli funkcji oferowanych przez inne bazy danych.  Na poniższej liście przedstawiono niektóre z funkcji tabeli, które nie są obsługiwane w usłudze SQL Data Warehouse.

- Klucz podstawowy, kluczy obcych unikatowe, sprawdzanie poprawności [ograniczenia tabeli](/sql/t-sql/statements/alter-table-table-constraint-transact-sql)

- [Kolumny obliczane](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql)
- [Indeksowane widoki](/sql/relational-databases/views/create-indexed-views)
- [Sekwencja](/sql/t-sql/statements/create-sequence-transact-sql)
- [Kolumny rozrzedzone](/sql/relational-databases/tables/use-sparse-columns)
- Klucze zastępczy. Wdrożenia przy użyciu [tożsamości](sql-data-warehouse-tables-identity.md).
- [Synonimy](/sql/t-sql/statements/create-synonym-transact-sql)
- [Wyzwalacze](/sql/t-sql/statements/create-trigger-transact-sql)
- [Unikatowe indeksy](/sql/t-sql/statements/create-index-transact-sql)
- [Typy zdefiniowane przez użytkownika](/sql/relational-databases/native-client/features/using-user-defined-types)

## <a name="table-size-queries"></a>Zapytania o rozmiar tabeli
Prostym sposobem identyfikacji miejsca i wiersze używane przez tabelę w każdej z 60 dystrybucji jest użycie [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Jednak za pomocą polecenia DBCC może być dość ograniczenie.  Dynamicznych widoków zarządzania (DMV) wyświetlić więcej szczegółów niż polecenia DBCC. Rozpocznij od utworzenia tego widoku.

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

Ta kwerenda zwraca wiersze i miejsca przez tabelę.  Dzięki temu można zobaczyć, które tabele to zgodna z największych tabel, oraz czy jest działanie okrężne replikowane, lub hash - rozproszonych.  W przypadku tabel z dystrybucji skrótów zapytanie Wyświetla kolumny dystrybucji.  

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

### <a name="table-space-by-distribution-type"></a>Obszar tabel przez typ dystrybucji

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

### <a name="distribution-space-summary"></a>Podsumowanie miejsca dystrybucji

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
Po utworzeniu tabel w magazynie danych, następnym krokiem jest do ładowania danych do tabeli.  Samouczek ładowania, zobacz [ładowania danych do usługi SQL Data Warehouse](load-data-wideworldimportersdw.md).
