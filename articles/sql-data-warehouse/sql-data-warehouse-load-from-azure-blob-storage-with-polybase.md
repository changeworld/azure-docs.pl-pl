---
title: Ładowanie danych detalicznych firmy Contoso do Azure SQL Data Warehouse | Microsoft Docs
description: Użyj poleceń Base i T-SQL, aby załadować dwie tabele z danych detalicznych firmy Contoso do Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b96b65b7dd38900fccb8d5d3a9133f37ee93949f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "67595514"
---
# <a name="load-contoso-retail-data-to-azure-sql-data-warehouse"></a>Załaduj dane detaliczne firmy Contoso do Azure SQL Data Warehouse

W ramach tego samouczka nauczysz się używać poleceń wielobazowych i T-SQL do ładowania dwóch tabel z danych detalicznych firmy Contoso do Azure SQL Data Warehouse. 

W tym samouczku wykonasz następujące instrukcje:

1. Konfigurowanie bazy danych do załadowania z usługi Azure Blob Storage
2. Ładowanie danych publicznych do bazy danych
3. Wykonaj optymalizacje po zakończeniu ładowania.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Do uruchomienia tego samouczka potrzebne jest konto platformy Azure, które ma już SQL Data Warehouse. Jeśli nie masz obsługiwanego magazynu danych, zobacz [tworzenie SQL Data Warehouse i Ustawianie reguły zapory na poziomie serwera][Create a SQL Data Warehouse].

## <a name="1-configure-the-data-source"></a>1. Konfigurowanie źródła danych
Baza kodu używa zewnętrznych obiektów T-SQL do definiowania lokalizacji i atrybutów danych zewnętrznych. Definicje obiektów zewnętrznych są przechowywane w SQL Data Warehouse. Dane są przechowywane zewnętrznie.

### <a name="11-create-a-credential"></a>1.1. Utwórz poświadczenie
**Pomiń ten krok** , Jeśli ładujesz dane publiczne contoso. Nie potrzebujesz bezpiecznego dostępu do danych publicznych, ponieważ są one już dostępne dla wszystkich użytkowników.

**Nie pomijaj tego kroku** , jeśli używasz tego samouczka jako szablonu do ładowania własnych danych. Aby uzyskać dostęp do danych za pomocą poświadczeń, użyj następującego skryptu, aby utworzyć poświadczenia z zakresem bazy danych, a następnie użyć ich podczas definiowania lokalizacji źródła danych.

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

### <a name="12-create-the-external-data-source"></a>1.2. Tworzenie zewnętrznego źródła danych
To polecenie [Utwórz zewnętrzne źródło danych][CREATE EXTERNAL DATA SOURCE] służy do przechowywania lokalizacji danych i typu danych. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [!IMPORTANT]
> Jeśli chcesz, aby kontenery usługi Azure Blob Storage były publiczne, pamiętaj, że jako właściciel danych zostanie naliczona opłata za naliczanie opłat za dane wychodzące, gdy dane opuszczają centrum danych. 
> 
> 

## <a name="2-configure-data-format"></a>2. Konfiguruj format danych
Dane są przechowywane w plikach tekstowych w usłudze Azure Blob Storage, a każde pole jest oddzielone ogranicznikiem. W programie SSMS Uruchom następujące polecenie [Create External File Format][CREATE EXTERNAL FILE FORMAT] , aby określić format danych w plikach tekstowych. Dane firmy Contoso są nieskompresowane i rozdzielane potokami.

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
Teraz, po określeniu źródła danych i formatu pliku, możesz utworzyć tabele zewnętrzne. 

### <a name="31-create-a-schema-for-the-data"></a>3.1. Utwórz schemat dla danych.
Aby utworzyć miejsce do przechowywania danych firmy Contoso w bazie danych, Utwórz schemat.

```sql
CREATE SCHEMA [asb]
GO
```

### <a name="32-create-the-external-tables"></a>3.2. Utwórz tabele zewnętrzne.
Uruchom następujący skrypt, aby utworzyć tabele zewnętrzne DimProduct i FactOnlineSales. W tym miejscu Wystarczy zdefiniować nazwy kolumn i typy danych oraz powiązać je z lokalizacją i formatem plików magazynu obiektów blob platformy Azure. Definicja jest przechowywana w SQL Data Warehouse, a dane nadal znajdują się w Azure Storage Blob.

Parametr **Location** jest folderem w folderze głównym w Azure Storage BLOB. Każda tabela znajduje się w innym folderze.

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
Istnieją różne sposoby uzyskiwania dostępu do danych zewnętrznych.  Możesz badać dane bezpośrednio z tabel zewnętrznych, ładować dane do nowych tabel w magazynie danych lub dodawać zewnętrzne dane do istniejących tabel magazynu danych.  

### <a name="41-create-a-new-schema"></a>4.1. Utwórz nowy schemat
CTAS tworzy nową tabelę zawierającą dane.  Najpierw Utwórz schemat dla danych firmy Contoso.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="42-load-the-data-into-new-tables"></a>4.2. Załaduj dane do nowych tabel
Aby załadować dane z usługi Azure Blob Storage do tabeli magazynu danych, użyj instrukcji [CREATE TABLE as Select (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] . Ładowanie za pomocą CTAS korzysta z utworzonych tabel zewnętrznych o jednoznacznie określonym typie. Aby załadować dane do nowych tabel, użyj jednej instrukcji [CTAs][CTAS] na tabelę. 
 
CTAS tworzy nową tabelę i wypełnia ją wynikami instrukcji SELECT. CTAS definiuje nową tabelę w taki sposób, aby zawierała te same kolumny i typy danych co wyniki instrukcji SELECT. W przypadku wybrania wszystkich kolumn z tabeli zewnętrznej Nowa tabela będzie repliką kolumn i typów danych w tabeli zewnętrznej.

W tym przykładzie tworzymy zarówno wymiar, jak i tabelę faktów jako tabele rozproszone w postaci skrótów. 

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="43-track-the-load-progress"></a>4,3 śledzenie postępu ładowania
Postęp ładowania można śledzić przy użyciu dynamicznych widoków zarządzania (widoków DMV). 

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

## <a name="5-optimize-columnstore-compression"></a>5. Optymalizowanie kompresji magazynu kolumn
Domyślnie SQL Data Warehouse przechowuje tabelę jako klastrowany indeks magazynu kolumn. Po zakończeniu ładowania niektóre wiersze danych mogą nie zostać skompresowane do magazynu kolumn.  Istnieją różne przyczyny, dla których może się to zdarzyć. Aby dowiedzieć się więcej, zobacz [Zarządzanie indeksami magazynu kolumn][manage columnstore indexes].

Aby zoptymalizować wydajność zapytań i kompresję magazynu kolumn po załadowaniu, należy ponownie skompilować tabelę, aby wymusić, że indeks magazynu kolumn będzie kompresowany ze wszystkimi wierszami. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Aby uzyskać więcej informacji na temat obsługi indeksów magazynu kolumn, zobacz artykuł [Zarządzanie indeksami magazynu kolumn][manage columnstore indexes] .

## <a name="6-optimize-statistics"></a>6. Optymalizowanie statystyk
Najlepiej jest utworzyć statystyki pojedynczej kolumny bezpośrednio po załadowaniu. Jeśli wiesz, że niektóre kolumny nie będą znajdować się w predykatach zapytań, możesz pominąć tworzenie statystyk dla tych kolumn. W przypadku tworzenia statystyk z jedną kolumną dla każdej kolumny może upłynąć dużo czasu, aby ponownie skompilować wszystkie statystyki. 

Jeśli zdecydujesz się utworzyć statystykę jednokolumnową dla każdej kolumny każdej tabeli, możesz użyć przykładowego `prc_sqldw_create_stats` kodu procedury składowanej w artykule [Statystyka][statistics] .

Poniższy przykład jest dobrym punktem wyjścia do tworzenia statystyk. Tworzy statystykę jednokolumnową dla każdej kolumny w tabeli wymiarów i dla każdej kolumny sprzężenia w tabelach faktów. Można zawsze dodawać pojedyncze lub wielokolumnowe statystyki do innych kolumn tabeli faktów w późniejszym czasie.

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

## <a name="achievement-unlocked"></a>Nieodblokowane osiągnięcie!
Dane publiczne zostały pomyślnie załadowane do Azure SQL Data Warehouse. Wspaniałe zadanie!

Teraz możesz zacząć wysyłać zapytania do tabel, aby eksplorować dane. Uruchom następujące zapytanie, aby dowiedzieć się o całkowitej sprzedaży za markę:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Następne kroki
Aby załadować pełny zestaw danych, uruchom przykład Załaduj [pełny magazyn danych firmy Contoso](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) z repozytorium Microsoft SQL Server Samples.

Więcej porad programistycznych znajdziesz w artykule [Omówienie programowania w usłudze SQL Data Warehouse][SQL Data Warehouse development overview].

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
[CREATE EXTERNAL DATA SOURCE]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
[Load the full Contoso Retail Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
