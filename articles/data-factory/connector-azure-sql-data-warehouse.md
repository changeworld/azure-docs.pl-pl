---
title: Kopiowanie danych do i z usługi Azure SQL Data Warehouse przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z obsługiwanej źródłowej sklepami, aby Magazyn danych SQL Azure lub usługi SQL Data Warehouse sklepów zbiornika obsługiwanych przy użyciu fabryki danych.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/28/2018
ms.author: jingwang
ms.openlocfilehash: 42ffdbf117b3f522e27e6e46628231ddb8221018
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051631"
---
#  <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Kopiowanie danych do i z usługi Azure SQL Data Warehouse przy użyciu fabryki danych Azure 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you're using:"]
> * [Version1 ](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Bieżąca wersja](connector-azure-sql-data-warehouse.md)

W tym artykule opisano sposób korzystania działanie kopiowania w fabryce danych Azure, aby skopiować dane do lub z usługi Azure SQL Data Warehouse. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Wszystkie obsługiwanych ujścia magazynu danych można skopiować danych z magazynu danych SQL Azure. I można skopiować danych z dowolnego źródła obsługiwanych magazynu danych Azure SQL Data Warehouse. Lista magazynów danych, które są obsługiwane jako źródła lub wychwytywanie przez działanie kopiowania, zobacz [obsługiwane formaty i magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik usługi Azure SQL Data Warehouse obsługuje następujące funkcje:

- Kopiowanie danych przy użyciu uwierzytelniania programu SQL i aplikacji usługi Azure Active Directory (Azure AD) tokenu uwierzytelniania przy użyciu nazwy głównej usługi lub zarządzane tożsamości usługi (MSI).
- Jako źródła pobierania danych przy użyciu zapytania SQL lub procedurę składowaną.
- Jako zbiorniku ładowanie danych przy użyciu programu PolyBase lub zbiorczego wstawiania. Firma Microsoft zaleca PolyBase w celu poprawy wydajności kopii.

> [!IMPORTANT]
> Należy pamiętać, że aparat PolyBase obsługuje tylko uwierzytelnianie SQL, ale nie usługi Azure AD authentication.

> [!IMPORTANT]
> Po skopiowaniu danych za pomocą obsługi integracji fabryki danych Azure, skonfiguruj [zapory serwera Azure SQL](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) tak, aby dostęp do serwera usług platformy Azure.
> Po skopiowaniu danych przy użyciu środowiska uruchomieniowego integracji siebie, należy skonfigurować zapory serwera Azure SQL umożliwiają odpowiedni zakres adresów IP. Ten zakres obejmuje IP na komputerze, który służy do łączenia z bazą danych SQL Azure.

## <a name="get-started"></a>Rozpoczęcie pracy

> [!TIP]
> Aby uzyskać najlepszą wydajność, użyj programu PolyBase, aby załadować dane do usługi Azure SQL Data Warehouse. [Użyj programu PolyBase, aby załadować dane do usługi Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) sekcja zawiera szczegółowe informacje. Aby uzyskać wskazówki z przypadkiem użycia, zobacz [załadować 1 TB do usługi Azure SQL Data Warehouse z fabryką danych Azure w obszarze 15 minut](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które definiują jednostek fabryki danych określonej do łącznika usługi Azure SQL Data Warehouse.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Obsługiwane są następujące właściwości dla usługi Azure SQL Data Warehouse połączone:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną **AzureSqlDW**. | Yes |
| Parametry połączenia | Podaj informacje wymagane do połączenia z wystąpieniem usługi Azure SQL Data Warehouse dla **connectionString** właściwości. Oznacz to pole jako **SecureString** Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| servicePrincipalId | Określ identyfikator aplikacji klienta. | Tak, gdy użytkownik korzysta z nazwy głównej usługi uwierzytelniania usługi Azure AD. |
| servicePrincipalKey | Określ klucz aplikacji. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Tak, gdy użytkownik korzysta z nazwy głównej usługi uwierzytelniania usługi Azure AD. |
| dzierżawa | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Można go pobrać, ustawiając kursor myszy w prawym górnym rogu portalu Azure. | Tak, gdy użytkownik korzysta z nazwy głównej usługi uwierzytelniania usługi Azure AD. |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. (Jeśli w magazynie danych znajduje się w sieci prywatnej), można użyć środowiska uruchomieniowego integracji Azure lub runtime siebie integracji. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. | Nie |

Różnymi typami uwierzytelniania można znaleźć w poniższych sekcjach na warunki wstępne i przykłady JSON odpowiednio:

- [Uwierzytelnianie SQL](#sql-authentication)
- Azure AD aplikacji token uwierzytelniania: [nazwy głównej usługi](#service-principal-authentication)
- Azure AD aplikacji token uwierzytelniania: [zarządzane tożsamości usługi](#managed-service-identity-authentication)

### <a name="sql-authentication"></a>Uwierzytelnianie SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Przykład połączonej usługi, w którym jest używane uwierzytelnianie SQL

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi

Aby używać uwierzytelniania tokenu aplikacji usługi na podstawie podmiot zabezpieczeń usługi Azure AD, wykonaj następujące kroki:

1. **[Tworzenie aplikacji usługi Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)**  z portalu Azure. Zanotuj nazwę aplikacji i następujące wartości, które definiują połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. **[Administrator usługi Azure Active Directory do udostępnienia](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  dla serwera Azure SQL w portalu Azure, jeśli jeszcze tego nie zrobiono. Administrator usługi Azure AD może być użytkownika usługi Azure AD lub grupy usługi Azure AD. Przyznanie grupie msi rolę administratora, pomiń kroki 3 i 4. Administrator będą mieli pełny dostęp do bazy danych.

3. **[Utwórz użytkowników zawartej bazy danych](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  dla nazwy głównej usługi. Połącz w magazynie danych z lub do których chcesz skopiować dane za pomocą takich narzędzi jak SSMS, przy użyciu tożsamości usługi Azure AD, który ma co najmniej uprawnienie ALTER ANY użytkownika. Uruchom T-SQL:
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Przyznaj nazwy głównej usługi wymaganych uprawnień** w zwykły sposób dla użytkowników SQL lub innym osobom. Uruchom poniższy kod:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. **Konfigurowanie usługi Azure SQL Data Warehouse połączone** w fabryce danych Azure.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Przykład połączonej usługi, w którym jest używane uwierzytelnianie głównej usługi

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-service-identity-authentication"></a>Uwierzytelnianie tożsamości usługi zarządzanej

Fabryka danych mogą być skojarzone z [zarządzane tożsamość usługi](data-factory-service-identity.md) reprezentujący określonego fabryki. Ta tożsamość usługi służy do uwierzytelniania usługi Azure SQL Data Warehouse. Fabryka wyznaczonych mogą uzyskiwać dostęp do i skopiować dane z lub do danych magazynu przy użyciu tej tożsamości.

> [!IMPORTANT]
> Należy pamiętać, że program PolyBase nie jest obecnie obsługiwany dla uwierzytelniania MSI.

Aby używać uwierzytelniania tokenu aplikacji MSI na podstawie usługi Azure AD, wykonaj następujące kroki:

1. **Utwórz grupę w usłudze Azure AD.** Należy do grupy fabryki MSI.

    a. Znajdź tożsamość usługi fabryka danych z portalu Azure. Przejdź z fabryką danych **właściwości**. Skopiuj identyfikator tożsamości usługi.

    b. Zainstaluj [programu Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modułu. Zaloguj się przy użyciu `Connect-AzureAD` polecenia. Uruchom następujące polecenia, aby utworzyć grupę, a następnie dodaj jako członka fabryki danych MSI.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. **[Administrator usługi Azure Active Directory do udostępnienia](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  dla serwera Azure SQL w portalu Azure, jeśli jeszcze tego nie zrobiono.

3. **[Utwórz użytkowników zawartej bazy danych](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  dla grupy usługi Azure AD. Połącz w magazynie danych z lub do których chcesz skopiować dane za pomocą takich narzędzi jak SSMS, przy użyciu tożsamości usługi Azure AD, który ma co najmniej uprawnienie ALTER ANY użytkownika. Uruchom T-SQL. 
    
    ```sql
    CREATE USER [your Azure AD group name] FROM EXTERNAL PROVIDER;
    ```

4. **Przyznaj grupie usługi Azure AD wymaganych uprawnień** w zwykły sposób użytkowników SQL i innych użytkowników. Na przykład uruchom poniższy kod.

    ```sql
    EXEC sp_addrolemember [role name], [your Azure AD group name];
    ```

5. **Konfigurowanie usługi Azure SQL Data Warehouse połączone** w fabryce danych Azure.

#### <a name="linked-service-example-that-uses-msi-authentication"></a>Przykład połączonej usługi, który korzysta z uwierzytelniania MSI

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje, zobacz [zestawów danych](https://docs.microsoft.com/en-us/azure/data-factory/concepts-datasets-linked-services) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Azure SQL Data Warehouse.

Aby skopiować dane z lub do magazynu danych SQL Azure, należy ustawić **typu** właściwości zestawu danych na **AzureSqlDWTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** musi mieć ustawioną właściwość dataset **AzureSqlDWTable**. | Yes |
| tableName | Nazwa tabeli lub widoku w wystąpieniu usługi Azure SQL Data Warehouse, odnoszący się do połączonej usługi. | Yes |

#### <a name="dataset-properties-example"></a>Przykład właściwości zestawu danych

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę obsługiwanych przez Magazyn danych SQL Azure źródłowy i odbiorczy właściwości.

### <a name="azure-sql-data-warehouse-as-the-source"></a>Usługa Azure SQL Data Warehouse jako źródło

Aby skopiować dane z usługi Azure SQL Data Warehouse, ustaw **typu** właściwości w źródle działanie kopiowania **SqlDWSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** musi mieć ustawioną właściwość źródła działanie kopiowania **SqlDWSource**. | Yes |
| sqlReaderQuery | Użyj niestandardowych zapytania SQL można odczytać danych. Przykład: `select * from MyTable`. | Nie |
| sqlReaderStoredProcedureName | Nazwa procedury przechowywanej, która odczytuje dane z tabeli źródłowej. Ostatniej instrukcji SQL musi być instrukcji SELECT w procedurze składowanej. | Nie |
| storedProcedureParameters | Parametry dla procedury składowanej.<br/>Dozwolone wartości to par nazw ani wartości. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametry procedury składowanej. | Nie |

### <a name="points-to-note"></a>Uwagi na

- Jeśli **sqlReaderQuery** określono **SqlSource**, odbywa się działanie kopii tego zapytania względem źródła magazynu danych SQL Azure umożliwiają pobieranie danych. Lub możesz określić procedury składowanej. Określ **sqlReaderStoredProcedureName** i **storedProcedureParameters** Jeśli procedura składowana pobiera parametry.
- Jeśli nie określisz, albo **sqlReaderQuery** lub **sqlReaderStoredProcedureName**, kolumn zdefiniowanych w **struktury** części zestawu danych JSON są używane do Utwórz zapytanie. `select column1, column2 from mytable` Uruchamiany przed magazyn danych SQL Azure. Jeśli nie ma definicji zestawu danych **struktury**, wybrano wszystkich kolumn z tabeli.
- Jeśli używasz **sqlReaderStoredProcedureName**, nadal należy określić manekina **tableName** właściwość w zestawie danych JSON.

#### <a name="sql-query-example"></a>Przykład zapytania SQL

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>Przykład procedury składowanej

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="stored-procedure-definition"></a>Definicja procedury składowanej

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-data-warehouse-as-sink"></a> Usługa Azure SQL Data Warehouse jako obiekt sink

Aby skopiować dane do usługi Azure SQL Data Warehouse, należy ustawić typ ujścia w przypadku działania kopiowania do **SqlDWSink**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **zbiornika** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** musi mieć ustawioną właściwość zbiornika działanie kopiowania **SqlDWSink**. | Yes |
| allowPolyBase | Wskazuje, czy do korzystania z programu PolyBase, jeśli ma to zastosowanie, zamiast mechanizmu BULKINSERT. <br/><br/> Zaleca się załadowanie danych do usługi SQL Data Warehouse przy użyciu programu PolyBase. Zobacz [Użyj programu PolyBase, aby załadować dane do usługi Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) sekcji dla ograniczenia i szczegółów.<br/><br/>Dozwolone wartości to **True** i **False** (ustawienie domyślne).  | Nie |
| Usługi | Grupy właściwości, które można określić, kiedy **allowPolybase** właściwość jest ustawiona na **true**. | Nie |
| rejectValue | Określa liczbę lub odsetek wierszy, które można odrzucić przed zapytanie nie powiedzie się.<br/><br/>Dowiedz się więcej o opcjach Odrzuć przez aparat PolyBase w sekcji argumenty [Tworzenie tabeli zewnętrznej (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Dozwolone wartości to 0 (domyślnie), 1, 2, itp. |Nie |
| dla właściwości rejectType | Określa, czy **rejectValue** opcja jest wartość literału lub wartość procentowa.<br/><br/>Dozwolone wartości to **wartość** (ustawienie domyślne) i **procent**. | Nie |
| rejectSampleValue | Określa liczbę wierszy do pobrania przed PolyBase ponownie oblicza procent odrzuconych wierszy.<br/><br/>Dozwolone wartości to 1, 2, itp. | Tak, jeśli **dla właściwości rejectType** jest **procent**. |
| useTypeDefault | Określa sposób obsługi brakujących wartości w rozdzielane pliki tekstowe, jeśli PolyBase pobiera dane z pliku tekstowego.<br/><br/>Dowiedz się więcej o tej właściwości z sekcji argumenty w [utworzyć EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Dozwolone wartości to **True** i **False** (ustawienie domyślne). | Nie |
| writeBatchSize | Wstawia dane do tabeli SQL, gdy osiągnie rozmiar buforu **writeBatchSize**. Dotyczy tylko, jeśli używany nie jest program PolyBase.<br/><br/>Dozwolone wartości to **całkowitą** (liczba wierszy). | Nie. Wartość domyślna to 10 000. |
| writeBatchTimeout | Czas na zakończenie zanim upłynie limit czasu operacji wstawiania wsadowego oczekiwania. Dotyczy tylko, jeśli używany nie jest program PolyBase.<br/><br/>Dozwolone wartości to **timespan**. Przykład: "00: 30:00" (30 minut). | Nie |
| preCopyScript | Określ zapytanie SQL dla aktywności kopiowania do uruchomienia przed zapisaniem danych do usługi Azure SQL Data Warehouse w każdym przebiegu. Ta właściwość służy do oczyszczania załadowanych danych. | Nie | (#repeatability podczas kopiowania). | Instrukcja zapytania. | Nie |

#### <a name="sql-data-warehouse-sink-example"></a>Przykład ujścia magazynu danych SQL

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

Dowiedz się więcej o sposobie używania programu PolyBase do efektywnie załadowania SQL Data Warehouse w następnej sekcji.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Użyj programu PolyBase, aby załadować dane do magazynu danych SQL Azure

Przy użyciu [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) jest wydajny sposób załadować dużej ilości danych do usługi Azure SQL Data Warehouse z wysokiej przepływności. Przy użyciu programu PolyBase zamiast domyślnego mechanizmu BULKINSERT zobaczysz duże korzyści w przepływności. Zobacz [dotyczące wydajności](copy-activity-performance.md#performance-reference) szczegółowe porównanie. Aby uzyskać wskazówki z przypadkiem użycia, zobacz [załadować 1 TB w usłudze Azure SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-load-sql-data-warehouse).

* Jeśli źródło danych znajduje się w magazynie obiektów Blob Azure lub usługi Azure Data Lake Store i format jest zgodny z PolyBase, bezpośrednio kopiowania do magazynu danych SQL Azure przy użyciu programu PolyBase. Aby uzyskać więcej informacji, zobacz  **[kierowanie kopiowania za pomocą programu PolyBase](#direct-copy-by-using-polybase)**.
* Jeśli Twoje źródła magazynu danych i format pierwotnie nie jest obsługiwany przez aparat PolyBase, użyj **[przemieszczane kopiowania przy użyciu programu PolyBase](#staged-copy-by-using-polybase)** zamiast funkcji. Funkcja kopiowania przemieszczanego zapewnia również lepszą przepustowość. Automatycznie konwertuje dane w formacie zgodnym z PolyBase. I przechowuje dane w magazynie obiektów Blob Azure. Następnie ładuje dane do usługi SQL Data Warehouse.

> [!IMPORTANT]
> Należy pamiętać, że program PolyBase nie jest obecnie obsługiwany dla tokenu uwierzytelniania na podstawie MSI aplikacji usługi Azure AD.

### <a name="direct-copy-by-using-polybase"></a>Bezpośrednie kopiowania przy użyciu programu PolyBase

Aparat PolyBase magazynu danych SQL obsługuje bezpośrednio obiektów Blob platformy Azure i usługi Azure Data Lake Store. Używa nazwy głównej usługi jako źródła, a ma wymagania format określonego pliku. Jeśli źródło danych spełnia kryteria opisane w tej sekcji, użyj programu PolyBase, aby skopiować bezpośrednio z magazynu danych źródła do usługi Azure SQL Data Warehouse. W przeciwnym razie użyj [przemieszczane kopiowania przy użyciu programu PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Aby skopiować dane wydajnie z usługi Data Lake Store SQL Data Warehouse, Dowiedz się więcej o [fabryki danych Azure ułatwia nawet wygodny do ujawniania wgląd w dane dotyczące danych podczas korzystania z usługi Data Lake Store z usługi SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Jeśli nie są spełnione wymagania, fabryki danych Azure sprawdza ustawienia i automatycznie powraca do mechanizmu BULKINSERT przenoszenia danych.

1. **Źródła połączona usługa** jest typu **AzureStorage** lub **AzureDataLakeStore** z uwierzytelnianiem główną usługi.
2. **Wejściowy zestaw danych** jest typu **AzureBlob** lub **AzureDataLakeStoreFile**. Typ formatu w obszarze `type` właściwości **OrcFormat**, **ParquetFormat**, lub **TextFormat**, z następujących konfiguracji:

   1. `rowDelimiter` musi być **\n**.
   2. `nullValue` ustawiono **pusty ciąg** (""), lub `treatEmptyAsNull` ustawiono **true**.
   3. `encodingName` ustawiono **utf-8**, która jest wartością domyślną.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader`, i `skipLineCount` nie są określone.
   5. `compression` może być **bez kompresji**, **GZip**, lub **Deflate**.

    ```json
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
    },
    ```

3. Brak nie `skipHeaderLineCount` w obszarze **BlobSource** lub **AzureDataLakeStore** dla aktywności kopiowania w potoku.
4. Brak nie `sliceIdentifierColumnName` w obszarze **SqlDWSink** dla aktywności kopiowania w potoku. Program PolyBase gwarantuje, że wszystkie dane są aktualizowane, lub nie jest aktualizowana w jednym przebiegu. Aby osiągnąć **powtarzalności**, użyj `sqlWriterCleanupScript`.

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>Kopiuj przygotowanego przy użyciu programu PolyBase

Jeśli źródło danych nie spełnia kryteriów w poprzedniej sekcji, włączać dane kopiowanie za pośrednictwem tymczasowego przemieszczania wystąpienia magazynu obiektów Blob platformy Azure. Nie można go Azure Premium Storage. W takim przypadku fabryki danych Azure automatycznie uruchamia przekształcenia danych w celu spełnienia wymagań formatu danych programu PolyBase. Następnie używa PolyBase, aby załadować dane do usługi SQL Data Warehouse. Na koniec czyści jej tymczasowe dane z magazynu obiektów blob. Zobacz [przemieszczane kopiowania](copy-activity-performance.md#staged-copy) Aby uzyskać więcej informacji o kopiowaniu danych za pomocą przemieszczania wystąpienia magazynu obiektów Blob platformy Azure.

Aby użyć tej funkcji, należy utworzyć [połączonej usługi magazynu Azure](connector-azure-blob-storage.md#linked-service-properties) odwołujący się do konta magazynu platformy Azure z magazynu tymczasowego obiektu blob. Następnie określ `enableStaging` i `stagingSettings` właściwości dla działania kopiowania, jak pokazano w poniższym kodzie:

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

## <a name="best-practices-for-using-polybase"></a>Najlepsze rozwiązania dotyczące przy użyciu programu PolyBase

W poniższych sekcjach przedstawiono najlepsze rozwiązania w zakresie oprócz wymienione w [najlepsze rozwiązania dotyczące usługi Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Uprawnienia wymagane bazy danych

Aby użyć programu PolyBase, użytkownik, który ładuje dane do magazynu danych SQL musi mieć [uprawnienia "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) w docelowej bazie danych. Jednym ze sposobów osiągnięcia, który jest dodanie użytkownik jest członkiem **db_owner** roli. Dowiedz się, jak to zrobić w [omówienie SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limits"></a>Rozmiar wiersza i danych typu limity

Program PolyBase obciążenia są ograniczone do wierszy jest mniejszy niż 1 MB. Nie można ich załadować VARCHR(MAX), NVARCHAR(MAX) lub VARBINARY(MAX). Aby uzyskać więcej informacji, zobacz [poziomów wydajności usługi SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Źródło danych zawiera wiersze większa niż 1 MB, można w pionie podzielone tabel źródłowych kilka małych sieci. Upewnij się, że największy rozmiar każdego wiersza nie przekracza limit. Mniejsze tabele można następnie ładowane przy użyciu programu PolyBase i scalane w usłudze Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Klasa zasobów magazynu danych SQL

Aby uzyskać najlepsze możliwe przepływności, przypisać klasę zasobu o większych użytkownikowi, który ładuje dane do usługi SQL Data Warehouse przy użyciu programu PolyBase.

### <a name="tablename-in-azure-sql-data-warehouse"></a>**tableName** w magazynie danych SQL Azure

W poniższej tabeli przedstawiono przykłady sposobu określania **tableName** właściwość w zestawie danych JSON. Przedstawia on kilku kombinacji nazwy schematu i tabeli.

| Schemat bazy danych | Nazwa tabeli | **tableName** właściwości JSON |
| --- | --- | --- |
| dbo | MyTable | MyTable lub dbo. MyTable lub [dbo]. [MyTable] |
| dbo1 | MyTable | dbo1. MyTable lub [dbo1]. [MyTable] |
| dbo | My.Table | [My.Table] lub [dbo]. [My.Table] |
| dbo1 | My.Table | [dbo1]. [My.Table] |

Jeśli zostanie wyświetlony następujący błąd, problem może być wybrana dla **tableName** właściwości. Można znaleźć w poprzedniej tabeli poprawne sposobu na określenie wartości **tableName** właściwości JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Kolumn z wartościami domyślnymi

Obecnie funkcja PolyBase w fabryce danych akceptuje tylko taką samą liczbę kolumn w tabeli docelowej. Przykładem jest tabelę z czterech kolumn, gdy jeden z nich jest zdefiniowana z wartości domyślnej. Dane wejściowe nadal musi mieć cztery kolumny. Trzy kolumny zestawu danych wejściowych zwraca błąd podobny do następującego:

```
All columns of the table must be specified in the INSERT BULK statement.
```

Wartość NULL jest specjalny rodzaj wartości domyślnej. W przypadku wartości Null kolumny danych wejściowych w obiekcie blob dla tej kolumny może być pusta. Ale nie może być Brak wejściowego zestawu danych. Program PolyBase wstawia wartości NULL dla brakujących wartości w usłudze Azure SQL Data Warehouse.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapowanie typu danych dla usługi Azure SQL Data Warehouse

Podczas kopiowania danych z lub do magazynu danych SQL Azure, z typów danych Azure SQL Data Warehouse są używane następujące mapowania do typów danych tymczasowych fabryki danych Azure. Zobacz [schemat i dane typu mapowania](copy-activity-schema-and-type-mapping.md) Aby dowiedzieć się, jak działanie kopiowania mapy typ źródła: schemat i dane do ujścia.

| Typ danych w usłudze Azure SQL Data Warehouse | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| bigint | Int64 |
| dane binarne | Byte[] |
| bitowe | Wartość logiczna |
| char | Ciąg, Char] |
| data | DateTime |
| Data/godzina | DateTime |
| datetime2 | DateTime |
| Datetimeoffset | DateTimeOffset |
| Decimal | Decimal |
| Atrybut FILESTREAM (varbinary(max)) | Byte[] |
| Liczba zmiennoprzecinkowa | podwójne |
| image | Byte[] |
| int | Int32 |
| oszczędność pieniędzy | Decimal |
| nchar | Ciąg, Char] |
| ntext | Ciąg, Char] |
| numeryczne | Decimal |
| nvarchar | Ciąg, Char] |
| rzeczywiste | Pojedyncze |
| ROWVERSION | Byte[] |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | Decimal |
| sql_variant | Obiekt * |
| tekst | Ciąg, Char] |
| time | Zakres czasu |
| sygnatura czasowa | Byte[] |
| tinyint | Bajt |
| Unikatowy identyfikator | Identyfikator GUID |
| varbinary | Byte[] |
| varchar | Ciąg, Char] |
| xml | Xml |

## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane formaty i magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
