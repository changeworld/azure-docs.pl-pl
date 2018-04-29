---
title: Ładowanie danych sprzedaży detalicznej Contoso Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Polecenia T-SQL i PolyBase można załadować tabel z danych sprzedaży detalicznej Contoso do usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 8a1c10755d6f0273b05660e39f3c59b37352d427
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="load-contoso-retail-data-to-azure-sql-data-warehouse"></a>Ładowanie danych sprzedaży detalicznej Contoso Azure SQL Data Warehouse

Polecenia T-SQL i PolyBase można załadować tabel z danych sprzedaży detalicznej Contoso do usługi Azure SQL Data Warehouse. Załadować pełny zestaw danych, należy uruchomić przykład [załadować pełne hurtowni danych sprzedaży detalicznej Contoso](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) z repozytorium przykłady serwera SQL firmy Microsoft.

W tym samouczku obejmują:

1. Konfigurowanie programu PolyBase, aby załadować z magazynu obiektów blob platformy Azure
2. Ładowanie danych publicznej do bazy danych
3. Optymalizacje należy wykonać po zakończeniu obciążenia.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby uruchomić tego samouczka, należy konta platformy Azure, która ma już bazę danych SQL Data Warehouse. Jeśli nie masz jeszcze to, zobacz [Tworzenie usługi SQL Data Warehouse][Create a SQL Data Warehouse].

## <a name="1-configure-the-data-source"></a>1. Konfigurowanie źródła danych
Aparat PolyBase używa obiektów zewnętrznych T-SQL w celu zdefiniowania lokalizacji i atrybuty danych zewnętrznych. Definicje obiektu zewnętrznego są przechowywane w usłudze SQL Data Warehouse. Dane są przechowywane zewnętrznie.

### <a name="11-create-a-credential"></a>1.1. Utwórz poświadczenia
**Pomiń ten krok** ładowania danych publicznych firmy Contoso. Bezpieczny dostęp do danych publicznych nie jest konieczne, ponieważ jest już dostępne dla wszystkich użytkowników.

**Nie, Pomiń ten krok** korzystając z tego samouczka jako szablon dla ładowania danych użytkownika. Dostęp do danych za pomocą poświadczeń, użyj następującego skryptu do tworzenia poświadczeń o zakresie bazy danych, a następnie użyć, określając lokalizację źródła danych.

```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

Przejdź do kroku 2.

### <a name="12-create-the-external-data-source"></a>1.2. Tworzenie zewnętrznego źródła danych
Użyj tej [Tworzenie zewnętrznego źródła danych] [ CREATE EXTERNAL DATA SOURCE] polecenia do przechowywania lokalizacji danych i typu danych. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [!IMPORTANT]
> Jeśli użytkownik chce udostępnić z obiektów blob platformy azure kontenery magazynu, należy pamiętać, że właściciel danych zostanie naliczona danych opłaty za wyjście po danych pozostawia centrum danych. 
> 
> 

## <a name="2-configure-data-format"></a>2. Skonfiguruj format danych
Dane są przechowywane w plikach tekstowych w magazynie obiektów blob platformy Azure, a poszczególne pola są rozdzielone ogranicznika. Uruchom to [utworzyć EXTERNAL FILE FORMAT] [ CREATE EXTERNAL FILE FORMAT] polecenie, aby określić format danych w plikach tekstowych. Dane firmy Contoso jest nieskompresowanych i rozdzielane potoku.

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat 
WITH 
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE 
                    )
);
``` 

## <a name="3-create-the-external-tables"></a>3. Tworzenie tabel zewnętrznych
Teraz, gdy został określony format źródła i plików danych, możesz przystąpić do tworzenia tabel zewnętrznych. 

### <a name="31-create-a-schema-for-the-data"></a>3.1. Tworzenie schematu dla danych.
Aby utworzyć miejsce do przechowywania danych firmy Contoso w bazie danych, należy utworzyć schemat.

```sql
CREATE SCHEMA [asb]
GO
```

### <a name="32-create-the-external-tables"></a>3.2. Utwórz tabele zewnętrzne.
Uruchom ten skrypt, aby utworzyć DimProduct i FactOnlineSales tabel zewnętrznych. Wszystkie robimy tutaj jest zdefiniowanie nazwy kolumn i typy danych i powiązanie lokalizacji i format plików magazynu obiektów blob platformy Azure. Definicja jest przechowywany w magazynie danych SQL, a dane są nadal obiektu Blob magazynu Azure.

**Lokalizacji** parametr to folder, w folderze głównym w obiekcie Blob magazynu Azure. Są w innym folderze.

```sql

--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales 
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="4-load-the-data"></a>4. Ładowanie danych
Ma innego sposobu dostępu do danych zewnętrznych.  Można zapytania na danych bezpośrednio z tabeli zewnętrznej, załadować dane do nowych tabel bazy danych lub Dodaj dane zewnętrzne do istniejących tabel bazy danych.  

### <a name="41-create-a-new-schema"></a>4.1. Tworzenie nowego schematu
CTAS tworzy nową tabelę, która zawiera dane.  Najpierw należy utworzyć schemat dla danych firmy contoso.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="42-load-the-data-into-new-tables"></a>4.2. Ładowanie danych do nowych tabel
Ładowanie danych z magazynu obiektów blob platformy Azure i zapisać ją w tabeli wewnątrz bazy danych, użyj [CREATE TABLE AS SELECT (Transact-SQL)] [ CREATE TABLE AS SELECT (Transact-SQL)] instrukcji. Ładowanie z CTAS wykorzystuje jednoznacznie tabel zewnętrznych, które właśnie utworzony. Aby załadować dane do nowych tabel, zastosuj jedną [CTAS] [ CTAS] instrukcji na tabelę. 
 
CTAS tworzy nową tabelę i wypełnia wyników w instrukcji select. CTAS definiuje nowa tabela na tej samej kolumny i typy danych wyników w instrukcji select. Po wybraniu wszystkich kolumn z tabeli zewnętrznej nowa tabela będzie repliki kolumn i typy danych w tabeli zewnętrznej.

W tym przykładzie tworzymy zarówno wymiaru, jak i tabela faktów jako skrótów rozproszonej tabel. 

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="43-track-the-load-progress"></a>4.3 śledzić postęp obciążenia
Można śledzić postęp obciążenia przy użyciu dynamicznych widoków zarządzania (widoków DMV). 

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files, 
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE 
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="5-optimize-columnstore-compression"></a>5. Optymalizacja magazynu kolumn kompresji
Domyślnie usługa SQL Data Warehouse przechowuje tabeli jako klastrowany indeks magazynu kolumn. Po zakończeniu obciążenia niektóre wiersze danych może nie można skompresować do magazynu kolumn.  Brak z różnych powodów, dlaczego jest to możliwe. Aby dowiedzieć się więcej, zobacz [Zarządzaj indeksami magazynu kolumn][manage columnstore indexes].

Aby zoptymalizować wydajność zapytań i ich kompresji magazynu kolumn po załadowaniu, Odbuduj tabelę, aby wymusić indeksu magazynu kolumn do skompresowania wszystkie wiersze. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Aby uzyskać więcej informacji na temat zachowania indeksy magazynu kolumn, zobacz [Zarządzaj indeksami magazynu kolumn] [ manage columnstore indexes] artykułu.

## <a name="6-optimize-statistics"></a>6. Optymalizacja statystyki
Najlepiej utworzyć statystyki dla pojedynczej kolumny natychmiast po załadowaniu. Dostępne są niektóre opcje wyboru statystyk. Na przykład jeśli tworzenie statystyk pojedynczej kolumny w każdej kolumnie go może zająć dużo czasu odbudować wszystkie statystyki. Jeśli wiesz, że niektóre kolumny nie będą znajdować się w predykatach kwerendy, można pominąć tworzenie statystyk na podstawie tych kolumn.

Jeśli zdecydujesz się tworzenie statystyk pojedynczej kolumny w każdej kolumnie każdej tabeli, można użyć procedury składowanej przykładowy kod `prc_sqldw_create_stats` w [statystyki] [ statistics] artykułu.

Poniższy przykład jest dobry punkt wyjścia do tworzenia statystyk. Tworzy statystyki pojedynczej kolumny w każdej kolumnie w tabeli wymiarów i w każdej kolumnie łącząca w tabelach faktów. Można dodać jednego lub wielu kolumn statystyki do kolumn tabeli faktów później.

```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>Osiągnięcia odblokowane!
Publiczne dane zostały pomyślnie załadowane do magazynu danych SQL Azure. Dobra robota!

Można teraz rozpocząć tworzenie zapytań o tabele przy użyciu zapytania podobne do poniższych:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Kolejne kroki
Do ładowania pełne danych magazynu danych sprzedaży detalicznej firmy Contoso, użyć skryptu w więcej porad programistycznych, zobacz [omówienie programowania w usłudze SQL Data Warehouse][SQL Data Warehouse development overview].

<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[manage columnstore indexes]: sql-data-warehouse-tables-index.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CREATE EXTERNAL DATA SOURCE]: https://msdn.microsoft.com/en-us/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/en-us/library/dn935026.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Load the full Contoso Retail Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
