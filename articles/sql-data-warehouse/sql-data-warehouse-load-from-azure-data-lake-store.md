---
title: 'Samouczek: Ładowanie z usługi Azure Data Lake Storage Gen1 do usługi Azure SQL Data Warehouse | Dokumentacja firmy Microsoft'
description: Tabele zewnętrzne technologii PolyBase umożliwia ładowanie danych z usługi Azure Data Lake Storage Gen1 do usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 32ac5b0841365acfc0a52e343eafc4f3760dffaa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61476140"
---
# <a name="load-data-from-azure-data-lake-storage-gen1-to-sql-data-warehouse"></a>Ładowanie danych z usługi Azure Data Lake Storage Gen1 do usługi SQL Data Warehouse
Tabele zewnętrzne technologii PolyBase umożliwia ładowanie danych z usługi Azure Data Lake Storage Gen1 do usługi Azure SQL Data Warehouse. Mimo że można uruchomić zapytania ad hoc dotyczących danych przechowywanych w Data Lake Storage Gen1, zaleca się importowanie danych do magazynu danych SQL, aby uzyskać najlepszą wydajność.

> [!div class="checklist"]
> * Tworzenie obiektów bazy danych, wymagane do załadowania z Data Lake Storage Gen1.
> * Podłącz do katalogu Data Lake Storage Gen1.
> * Ładowanie danych do usługi Azure SQL Data Warehouse.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="before-you-begin"></a>Przed rozpoczęciem
Zanim rozpoczniesz ten samouczek, pobierz i zainstaluj najnowszą wersję programu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

Aby uruchomić tego samouczka, potrzebne są:

* Usługa Azure aplikacji usługi Active Directory do uwierzytelniania do usługi. Aby utworzyć, wykonaj [uwierzytelniania usługi Active directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)

>[!NOTE] 
> Potrzebne są identyfikator klienta, klucza i OAuth 2.0 Token wartości punktu końcowego aplikacji usługi Active Directory, połączyć się z konta usługi Data Lake Storage Gen1 z usługi SQL Data Warehouse. Szczegóły dotyczące sposobu uzyskania tych wartości znajdują się w link powyżej. Dla rejestracji aplikacji Azure Active Directory należy użyć Identyfikatora aplikacji jako identyfikator klienta.
> 

* Usługi Azure SQL Data Warehouse. Zobacz [tworzenie i query i Azure SQL Data Warehouse](create-data-warehouse-portal.md).

* Konta Data Lake Storage Gen1. Zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-get-started-portal.md). 

##  <a name="create-a-credential"></a>Tworzenie poświadczeń
Aby uzyskać dostęp do konta usługi Data Lake Storage Gen1, należy utworzyć klucz główny bazy danych, aby zaszyfrować klucz tajny poświadczenie użyte w następnym kroku. Następnie utworzysz Database Scoped Credential, która przechowuje poświadczenia nazwy głównej usługi w usłudze AAD. Dla osób, które używanych technologii PolyBase połączyć się z systemu Windows Azure Storage Blobs należy pamiętać, że składnia poświadczenia innego.

Aby połączyć Data Lake Storage Gen1, należy najpierw **pierwszy** tworzenie aplikacji usługi Azure Active Directory Utwórz klucz dostępu i udzielić dostępu aplikacji do zasobu Data Lake Storage Gen1. Aby uzyskać instrukcje, zobacz [uwierzytelnianie w usłudze Azure Data Lake magazynu Gen1 przy użyciu usługi Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLSG1Credential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- It should look something like this:
CREATE DATABASE SCOPED CREDENTIAL ADLSG1Credential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Tworzenie zewnętrznego źródła danych
Użyj tego [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) polecenie, aby przechowywać lokalizacji danych. 

```sql
-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage Gen1.
-- LOCATION: Provide Data Lake Storage Gen1 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorageGen1
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<datalakestoregen1accountname>.azuredatalakestore.net',
    CREDENTIAL = ADLSG1Credential
);
```

## <a name="configure-data-format"></a>Skonfiguruj format danych
Aby zaimportować dane z Data Lake Storage Gen1, należy określić External File Format. Ten obiekt definiuje, jak pliki są zapisywane w Data Lake Storage Gen1.
Pełną listę można znaleźć w naszej dokumentacji języka T-SQL [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)

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
Teraz, gdy został określony format źródła i plików danych, możesz przystąpić do tworzenia tabel zewnętrznych. Tabele zewnętrzne są interakcje z danymi zewnętrznymi. Parametr lokalizacji można określić plik lub katalog. Jeśli Określa katalog, zostaną załadowane wszystkie pliki w katalogu.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the Data Lake Storage Gen1 root folder.
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
,   DATA_SOURCE = AzureDataLakeStorageGen1
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Zagadnienia dotyczące tabeli zewnętrznej
Tworzenie tabeli zewnętrznej jest łatwe, ale istnieją pewne różnice, które muszą zostać rozpatrzone.

Tabele zewnętrzne są silnie typizowane. Oznacza to, że każdy wiersz danych w trakcie pozyskiwania musi spełniać definicja schematu tabeli.
Jeśli wiersz jest niezgodna z definicją schematu, wiersz jest odrzucane od obciążenia.

Opcje REJECT_TYPE i REJECT_VALUE umożliwiają definiowanie, ile wierszy lub jaki procent danych musi znajdować się w tabeli końcowej. Podczas ładowania po osiągnięciu wartości Odrzuć obciążenia kończy się niepowodzeniem. Najczęstszą przyczyną odrzuconych wierszy jest niezgodność definicji schematu. Na przykład jeśli kolumna niepoprawnie otrzymuje schematu int, jeśli dane w pliku jest ciągiem, każdy wiersz zakończy się niepowodzeniem do załadowania.

Data Lake Storage Gen1 używa kontroli dostępu na podstawie ról (RBAC) w celu kontrolowania dostępu do danych. Oznacza to, że jednostka usługi musi mieć uprawnienia odczytu do katalogów określonych w parametrze lokalizacja i element podrzędny elementu końcowego katalog i pliki. Dzięki temu przy użyciu programu PolyBase uwierzytelniania i ładowania danych. 

## <a name="load-the-data"></a>Ładowanie danych
Ładowanie danych przed użyciem Data Lake Storage Gen1 [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) instrukcji. 

CTAS tworzy nową tabelę i wypełnia wyniki instrukcji select. CTAS definiuje nowej tabeli, aby mieć te same kolumny i typy danych jak wyniki instrukcji select. Po wybraniu wszystkich kolumn z tabeli zewnętrznej, nowa tabela jest replika kolumn i typy danych w tabeli zewnętrznej.

W tym przykładzie tworzymy dystrybuowanej tabeli wyznaczania wartości skrótu z naszych zewnętrznych DimProduct_external tabeli o nazwie DimProduct.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Optymalizowanie kompresja magazynu kolumn
Domyślnie usługa SQL Data Warehouse przechowuje tabelę jako klastrowany indeks magazynu kolumn. Po zakończeniu ładowania niektórych wierszy danych może nie można skompresować do magazynu kolumn.  Brak z różnych powodów dlaczego jest to możliwe. Aby dowiedzieć się więcej, zobacz [Zarządzaj indeksami magazynu kolumn](sql-data-warehouse-tables-index.md).

Aby zoptymalizować wydajność zapytań i kompresja magazynu kolumn po załadowaniu, Odbuduj tabelę, aby wymusić indeksu magazynu kolumn, aby skompresować wszystkie wiersze.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Optymalizowanie statystyki
Najlepiej utworzyć statystyki jednokolumnową od razu po załadowaniu. Istnieją pewne opcje dla statystyk. Na przykład jeśli tworzysz jednokolumnową statystyk dla każdej z kolumn może potrwać długo przebudować wszystkie statystyki. Jeśli wiesz, że niektóre kolumny nie będą znajdować się w predykatach zapytania, możesz pominąć tworzenie statystyk na podstawie tych kolumn.

Jeśli zdecydujesz się utworzyć statystyki pojedynczej kolumny dla każdej z kolumn każdej tabeli, możesz użyć przykładowego kodu procedury składowanej `prc_sqldw_create_stats` w [statystyki](sql-data-warehouse-tables-statistics.md) artykułu.

Poniższy przykład jest dobry punkt wyjścia do tworzenia statystyk. Tworzy jednokolumnową statystyk dotyczących poszczególnych kolumn w tabeli wymiarów i dotyczących poszczególnych przyłączany kolumn w tabelach faktów. Można dodać jednego lub wielu kolumnach statystyki do innych kolumn w tabeli faktów później.

## <a name="achievement-unlocked"></a>Osiągnięcie odblokować!
Pomyślnie załadowano dane do usługi Azure SQL Data Warehouse. Dobra robota!

## <a name="next-steps"></a>Kolejne kroki 
W tym samouczku utworzono tabele zewnętrzne w celu zdefiniowania struktury danych przechowywanych w Data Lake Storage Gen1, a następnie używane do ładowania danych do magazynu danych instrukcji PolyBase CREATE TABLE AS SELECT. 

Zostały wykonane następujące zadania:
> [!div class="checklist"]
> * Wymagane do załadowania z Data Lake Storage Gen1 obiekty utworzoną bazę danych.
> * Połączone z katalogiem Data Lake Storage Gen1.
> * Załadowane dane do usługi Azure SQL Data Warehouse.
> 

Trwa ładowanie danych jest pierwszym krokiem tworzenia rozwiązania magazynu danych przy użyciu SQL Data Warehouse. Zapoznaj się z naszym zasoby do tworzenia aplikacji.

> [!div class="nextstepaction"]
>[Dowiedz się, jak tworzyć tabele w usłudze SQL Data Warehouse](sql-data-warehouse-tables-overview.md)




