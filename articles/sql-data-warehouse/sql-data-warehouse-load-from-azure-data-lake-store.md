---
title: 'Samouczek: Ładowanie z usługi Azure Data Lake — magazyn Azure SQL Data Warehouse | Dokumentacja firmy Microsoft'
description: Użyj programu PolyBase tabel zewnętrznych do ładowania danych z usługi Azure Data Lake Store do usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/12/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 3c6907e8eb4ae4bbfae76a5a220d670427afd703
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="load-data-from-azure-data-lake-store-to-sql-data-warehouse"></a>Ładowanie danych z usługi Azure Data Lake Store SQL Data Warehouse
Użyj programu PolyBase tabel zewnętrznych do ładowania danych z usługi Azure Data Lake Store do usługi Azure SQL Data Warehouse. Mimo że można uruchomić zapytania ad hoc na dane przechowywane w ADLS, zaleca się importowania danych do magazynu danych SQL, aby uzyskać najlepszą wydajność.

> [!div class="checklist"]
> * Tworzenie obiektów bazy danych, wymagane do załadowania z usługi Azure Data Lake Store.
> * Nawiązać katalog usługi Azure Data Lake Store.
> * Ładowanie danych do usługi Azure SQL Data Warehouse.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="before-you-begin"></a>Przed rozpoczęciem
Zanim rozpoczniesz ten samouczek, pobierz i zainstaluj najnowszą wersję programu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

Aby uruchomić ten samouczek, potrzebne są:

* Azure aplikacji usługi Active Directory do użycia na potrzeby uwierzytelniania do usługi. Aby utworzyć, wykonaj [uwierzytelniania usługi Active directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)

>[!NOTE] 
> Potrzebujesz Identyfikatora klienta, klucz i wartość tokenu punktu końcowego OAuth2.0 aplikacji Active Directory do nawiązania połączenia z usługi Azure Data Lake z magazynu danych SQL. Szczegóły dotyczące sposobu uzyskania tych wartości znajdują się w łącze powyżej. Dla rejestracji aplikacji Azure Active Directory należy użyć Identyfikatora aplikacji jako identyfikator klienta.
> 

* Magazyn danych Azure SQL. Zobacz [tworzenie i zapytań i magazyn danych SQL Azure](create-data-warehouse-portal.md).

* Azure Data Lake Store. Zobacz [wprowadzenie do usługi Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md). 

##  <a name="create-a-credential"></a>Utwórz poświadczenia
Aby uzyskać dostęp do usługi Azure Data Lake Store, należy utworzyć klucz główny bazy danych, aby zaszyfrować klucz tajny poświadczenie użyte w następnym kroku. Następnie można utworzyć poświadczenia o zakresie bazy danych, która przechowuje poświadczenia główne usługi skonfigurowane w usłudze AAD. Dla osób, które użyto który można podłączyć do obiektów blob magazynu Azure z systemem Windows, należy pamiętać, że składnia poświadczeń różnych PolyBase.

Aby połączyć się z usługi Azure Data Lake Store, należy najpierw **pierwszy** tworzenie aplikacji Azure Active Directory Utwórz klucz dostępu i umożliwić aplikacji dostęp do zasobów usługi Azure Data Lake. Aby uzyskać instrukcje, zobacz [Uwierzytelnij, aby usługi Azure Data Lake magazynu przy użyciu usługi Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/en-us/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/en-us/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- It should look something like this:
CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Tworzenie zewnętrznego źródła danych
Użyj tej [Tworzenie zewnętrznego źródła danych](/sql/t-sql/statements/create-external-data-source-transact-sql) polecenia do przechowywania lokalizacji danych. 

```sql
-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Store.
-- LOCATION: Provide Azure Data Lake accountname and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<AzureDataLake account_name>.azuredatalakestore.net',
    CREDENTIAL = ADLCredential
);
```

## <a name="configure-data-format"></a>Skonfiguruj format danych
Aby zaimportować dane z ADLS, należy określić External File Format. Ten obiekt definiuje, jak pliki są zapisywane w ADLS.
Pełną listę można znaleźć w naszej dokumentacji T-SQL [utworzyć FORMAT pliku zewnętrznego](/sql/t-sql/statements/create-external-file-format-transact-sql)

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
-- LOCATION: Folder under the ADLS root folder.
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
,   DATA_SOURCE = AzureDataLakeStore
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Zagadnienia dotyczące tabeli zewnętrznej
Tworzenie tabeli zewnętrznej jest proste, ale istnieją pewne różnice, które muszą zostać omówione.

Tabele zewnętrzne są silnie typizowane. Oznacza to, że każdy wiersz danych jest pozyskanych muszą spełniać definicja schematu tabeli.
Jeśli wiersz jest niezgodny z definicji schematu, wiersz został odrzucony z obciążenia.

Opcje REJECT_TYPE i REJECT_VALUE umożliwiają definiowanie, ile wierszy lub wartość procentowa danych musi być obecny w końcowym tabeli. Podczas ładowania po osiągnięciu wartości Odrzuć obciążenia kończy się niepowodzeniem. Najczęstszą przyczyną odrzuconych wierszy jest niezgodność definicji schematu. Na przykład kolumny niepoprawnie podano schematu int, gdy dane w pliku jest ciągiem, każdy wiersz zakończy się niepowodzeniem do załadowania.

 Azure Data Lake store używa kontroli dostępu na podstawie ról (RBAC) w celu kontroli dostępu do danych. Oznacza to, że nazwy głównej usługi musi mieć uprawnienia odczytu do katalogów określonych w parametrze lokalizacji i element podrzędny elementu końcowego katalogów i plików. Dzięki temu PolyBase w celu uwierzytelniania i załadować danych. 

## <a name="load-the-data"></a>Ładowanie danych
Aby załadować dane z użycia usługi Azure Data Lake Store [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) instrukcji. 

CTAS tworzy nową tabelę i wypełnia wyników w instrukcji select. CTAS definiuje nowa tabela na tej samej kolumny i typy danych wyników w instrukcji select. Po wybraniu wszystkich kolumn z tabeli zewnętrznej nowa tabela jest repliką kolumn i typy danych w tabeli zewnętrznej.

W tym przykładzie tworzymy tabeli rozproszonej wyznaczania wartości skrótu o nazwie DimProduct z naszych zewnętrznych DimProduct_external tabeli.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Optymalizacja magazynu kolumn kompresji
Domyślnie usługa SQL Data Warehouse przechowuje tabeli jako klastrowany indeks magazynu kolumn. Po zakończeniu obciążenia niektóre wiersze danych może nie można skompresować do magazynu kolumn.  Brak z różnych powodów, dlaczego jest to możliwe. Aby dowiedzieć się więcej, zobacz [Zarządzaj indeksami magazynu kolumn](sql-data-warehouse-tables-index.md).

Aby zoptymalizować wydajność zapytań i ich kompresji magazynu kolumn po załadowaniu, Odbuduj tabelę, aby wymusić indeksu magazynu kolumn do skompresowania wszystkie wiersze.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Optymalizacja statystyki
Najlepiej utworzyć statystyki dla pojedynczej kolumny natychmiast po załadowaniu. Dostępne są niektóre opcje wyboru statystyk. Na przykład jeśli tworzenie statystyk pojedynczej kolumny w każdej kolumnie go może zająć dużo czasu odbudować wszystkie statystyki. Jeśli wiesz, że niektóre kolumny nie będą znajdować się w predykatach kwerendy, można pominąć tworzenie statystyk na podstawie tych kolumn.

Jeśli zdecydujesz się tworzenie statystyk pojedynczej kolumny w każdej kolumnie każdej tabeli, można użyć procedury składowanej przykładowy kod `prc_sqldw_create_stats` w [statystyki](sql-data-warehouse-tables-statistics.md) artykułu.

Poniższy przykład jest dobry punkt wyjścia do tworzenia statystyk. Tworzy statystyki pojedynczej kolumny w każdej kolumnie w tabeli wymiarów i w każdej kolumnie łącząca w tabelach faktów. Można dodać jednego lub wielu kolumn statystyki do kolumn tabeli faktów później.

## <a name="achievement-unlocked"></a>Osiągnięcia odblokowane!
Dane zostały pomyślnie załadowane do magazynu danych SQL Azure. Dobra robota!

## <a name="next-steps"></a>Kolejne kroki 
W tym samouczku utworzono tabel zewnętrznych do definiowania struktury danych przechowywanych w usłudze Azure Data Lake Store, a następnie użyć instrukcji PolyBase CREATE TABLE AS SELECT ładowanie danych do magazynu danych. 

Zostały wykonane następujące zadania:
> [!div class="checklist"]
> * Utworzono bazę danych obiekty wymagane do załadowania z usługi Azure Data Lake Store.
> * Połączenie z katalogiem usługi Azure Data Lake Store.
> * Załadować dane do magazynu danych SQL Azure.
> 

Podczas ładowania danych jest pierwszy krok projektowania rozwiązania magazynu danych przy użyciu usługi SQL Data Warehouse. Zapoznaj się z naszym zasoby projektowe.

> [!div class="nextstepaction"]
>[Dowiedz się, jak tworzenie tabel w usłudze SQL Data Warehouse](sql-data-warehouse-tables-overview.md)




