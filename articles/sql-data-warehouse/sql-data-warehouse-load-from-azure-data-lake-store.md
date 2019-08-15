---
title: Załaduj samouczek z Azure Data Lake Storage do Azure SQL Data Warehouse | Microsoft Docs
description: Użyj wielobazowych tabel zewnętrznych do ładowania danych z Azure Data Lake Storage do Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 08/08/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 3db355cf5782620bda3a9e04afbee073c8929856
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935122"
---
# <a name="load-data-from-azure-data-lake-storage-to-sql-data-warehouse"></a>Załaduj dane z Azure Data Lake Storage do SQL Data Warehouse
Użyj wielobazowych tabel zewnętrznych do ładowania danych z Azure Data Lake Storage do Azure SQL Data Warehouse. Mimo że można uruchamiać zapytania ad hoc dotyczące danych przechowywanych w Data Lake Storage, zalecamy zaimportowanie danych do SQL Data Warehouse w celu uzyskania najlepszej wydajności.

> [!div class="checklist"]
> * Utwórz obiekty bazy danych wymagane do załadowania z Data Lake Storage.
> * Nawiązywanie połączenia z katalogiem Data Lake Storage.
> * Ładowanie danych do usługi Azure SQL Data Warehouse.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="before-you-begin"></a>Przed rozpoczęciem
Zanim rozpoczniesz ten samouczek, pobierz i zainstaluj najnowszą wersję programu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

Aby uruchomić ten samouczek, potrzebne są:

* Azure Active Directory aplikacji do użycia na potrzeby uwierzytelniania między usługami. Aby utworzyć, postępuj zgodnie z [uwierzytelnianiem usługi Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)

* Azure SQL Data Warehouse. Zobacz [Tworzenie i wykonywanie zapytań oraz Azure SQL Data Warehouse](create-data-warehouse-portal.md).

* Konto Data Lake Storage. Zobacz [wprowadzenie do Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md). 

##  <a name="create-a-credential"></a>Utwórz poświadczenie
Aby uzyskać dostęp do konta Data Lake Storage, musisz utworzyć klucz główny bazy danych w celu zaszyfrowania klucza tajnego poświadczeń, który został użyty w następnym kroku. Następnie utworzysz poświadczenia w zakresie bazy danych. Podczas uwierzytelniania za pomocą nazw głównych usługi poświadczenia bazy danych przechowują poświadczenia jednostki usługi skonfigurowane w usłudze AAD. Możesz również użyć klucza konta magazynu w poświadczeniu o zakresie bazy danych dla Gen2. 

Aby połączyć się z Data Lake Storage przy użyciu jednostek usługi, należy **najpierw** utworzyć aplikację Azure Active Directory, utworzyć klucz dostępu i udzielić aplikacji dostępu do konta Data Lake Storage. Aby uzyskać instrukcje, zobacz [uwierzytelnianie w Azure Data Lake Storage przy użyciu Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B (for service principal authentication): Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    -- Always use the OAuth 2.0 authorization endpoint (v1)
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- B (for Gen2 storage key authentication): Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;

-- It should look something like this when authenticating using service principals:
CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Tworzenie zewnętrznego źródła danych
To polecenie [Utwórz zewnętrzne źródło danych](/sql/t-sql/statements/create-external-data-source-transact-sql) służy do przechowywania lokalizacji danych. 

```sql
-- C (for Gen1): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen1 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<datalakestoregen1accountname>.azuredatalakestore.net',
    CREDENTIAL = ADLSCredential
);

-- C (for Gen2): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen2 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION='abfs[s]://<container>@<AzureDataLake account_name>.dfs.core.windows.net', -- Please note the abfss endpoint for when your account has secure transfer enabled
    CREDENTIAL = ADLSCredential
);
```

## <a name="configure-data-format"></a>Konfiguruj format danych
Aby zaimportować dane z Data Lake Storage, należy określić format pliku zewnętrznego. Ten obiekt definiuje sposób pisania plików w Data Lake Storage.
Aby zapoznać się z pełną listą, zapoznaj się z dokumentacją języka T-SQL [Create External File Format](/sql/t-sql/statements/create-external-file-format-transact-sql)

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store missing values as default for datatype.

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

## <a name="create-the-external-tables"></a>Tworzenie tabel zewnętrznych
Teraz, po określeniu źródła danych i formatu pliku, możesz utworzyć tabele zewnętrzne. Tabele zewnętrzne są sposobem korzystania z danych zewnętrznych. Parametr Location może określać plik lub katalog. W przypadku wybrania katalogu zostaną załadowane wszystkie pliki w katalogu.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the Data Lake Storage root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStorage
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Zagadnienia dotyczące tabel zewnętrznych
Tworzenie tabeli zewnętrznej jest proste, ale istnieje kilka wszystkie szczegóły, które należy omówić.

Tabele zewnętrzne mają silną typ. Oznacza to, że każdy wiersz danych do pozyskiwania musi spełniać definicję schematu tabeli.
Jeśli wiersz nie jest zgodny z definicją schematu, wiersz zostanie odrzucony od obciążenia.

Opcje REJECT_TYPE i REJECT_VALUE umożliwiają zdefiniowanie liczby wierszy lub wartości procentowej danych, które muszą być obecne w końcowej tabeli. Podczas ładowania, jeśli zostanie osiągnięta wartość odrzucenia, ładowanie nie powiedzie się. Najbardziej typową przyczyną odrzuconych wierszy jest niezgodność definicji schematu. Na przykład jeśli kolumna ma nieprawidłowo określony schemat int, gdy dane w pliku są ciągiem, każdy wiersz nie zostanie załadowany.

Data Lake Storage Gen1 używa Access Control opartego na rolach (RBAC) do kontrolowania dostępu do danych. Oznacza to, że jednostka usługi musi mieć uprawnienia do odczytu do katalogów zdefiniowanych w parametrze Location i do elementów podrzędnych katalogu końcowego i plików. Dzięki temu można uwierzytelniać i ładować dane przy podstawie podstawowej. 

## <a name="load-the-data"></a>Ładowanie danych
Aby załadować dane z Data Lake Storage Użyj instrukcji [CREATE TABLE as Select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) . 

CTAS tworzy nową tabelę i wypełnia ją wynikami instrukcji SELECT. CTAS definiuje nową tabelę w taki sposób, aby zawierała te same kolumny i typy danych co wyniki instrukcji SELECT. W przypadku wybrania wszystkich kolumn z tabeli zewnętrznej Nowa tabela jest repliką kolumn i typów danych w tabeli zewnętrznej.

W tym przykładzie tworzymy tabelę rozproszoną z mieszaniem o nazwie DimProduct z naszej zewnętrznej tabeli DimProduct_external.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Optymalizowanie kompresji magazynu kolumn
Domyślnie SQL Data Warehouse przechowuje tabelę jako klastrowany indeks magazynu kolumn. Po zakończeniu ładowania niektóre wiersze danych mogą nie zostać skompresowane do magazynu kolumn.  Istnieje wiele powodów, dla których może się to zdarzyć. Aby dowiedzieć się więcej, zobacz [Zarządzanie indeksami magazynu kolumn](sql-data-warehouse-tables-index.md).

Aby zoptymalizować wydajność zapytań i kompresję magazynu kolumn po załadowaniu, należy ponownie skompilować tabelę, aby wymusić, że indeks magazynu kolumn będzie kompresowany ze wszystkimi wierszami.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Optymalizowanie statystyk
Najlepiej jest utworzyć statystyki pojedynczej kolumny bezpośrednio po załadowaniu. Istnieją pewne możliwości statystyczne. Na przykład w przypadku tworzenia statystyk z jedną kolumną dla każdej kolumny może upłynąć dużo czasu, aby ponownie skompilować wszystkie statystyki. Jeśli wiesz, że niektóre kolumny nie będą znajdować się w predykatach zapytań, możesz pominąć tworzenie statystyk dla tych kolumn.

Jeśli zdecydujesz się utworzyć statystykę jednokolumnową dla każdej kolumny każdej tabeli, możesz użyć przykładowego `prc_sqldw_create_stats` kodu procedury składowanej w artykule [Statystyka](sql-data-warehouse-tables-statistics.md) .

Poniższy przykład jest dobrym punktem wyjścia do tworzenia statystyk. Tworzy statystykę jednokolumnową dla każdej kolumny w tabeli wymiarów i dla każdej kolumny sprzężenia w tabelach faktów. Można zawsze dodawać pojedyncze lub wielokolumnowe statystyki do innych kolumn tabeli faktów w późniejszym czasie.

## <a name="achievement-unlocked"></a>Nieodblokowane osiągnięcie!
Dane zostały pomyślnie załadowane do Azure SQL Data Warehouse. Wspaniałe zadanie!

## <a name="next-steps"></a>Następne kroki 
W tym samouczku utworzono tabele zewnętrzne w celu zdefiniowania struktury danych przechowywanych w Data Lake Storage Gen1, a następnie użyto CREATE TABLE instrukcji "SELECT Base" w celu załadowania danych do magazynu danych. 

Zostały wykonane następujące zadania:
> [!div class="checklist"]
> * Utworzono obiekty bazy danych wymagane do załadowania z Data Lake Storage Gen1.
> * Połączono z katalogiem Data Lake Storage Gen1.
> * Załadowano dane do Azure SQL Data Warehouse.
>

Ładowanie danych to pierwszy krok tworzenia rozwiązania magazynu danych przy użyciu SQL Data Warehouse. Zapoznaj się z naszymi zasobami programistycznymi.

> [!div class="nextstepaction"]
> [Dowiedz się, jak opracowywać tabele w SQL Data Warehouse](sql-data-warehouse-tables-overview.md)
