---
title: Kopiowanie danych do/z usługi Azure SQL Data Warehouse przy użyciu fabryki danych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z magazynów obsługiwanych źródłowych do usługi Azure SQL Data Warehouse (lub) z magazynu danych SQL sklepów zbiornika obsługiwane przy użyciu fabryki danych.
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
ms.openlocfilehash: c862f269a8e32814dfb6d311706e65b57d52d1bb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34617080"
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Kopiowanie danych do i z usługi Azure SQL Data Warehouse przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Wersja 2 — wersja zapoznawcza](connector-azure-sql-data-warehouse.md)

W tym artykule omówiono sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane do i z usługi Azure SQL Data Warehouse. Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [łącznika usługi Azure SQL Data Warehouse w wersji 1](v1/data-factory-azure-sql-data-warehouse-connector.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Kopiowanie danych z/do usługi Azure SQL Data Warehouse żadnych obsługiwanych ujścia magazynu danych lub skopiować dane z dowolnego źródła obsługiwanych magazynu danych do usługi Azure SQL Data Warehouse. Lista magazynów danych, które są obsługiwane jako źródła/wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik usługi Azure SQL Data Warehouse obsługuje:

- Kopiowanie danych przy użyciu **uwierzytelniania SQL**, i **token uwierzytelniania aplikacji usługi Azure Active Directory** z nazwy głównej usługi lub zarządzane tożsamości usługi (MSI).
- Jako źródło pobierania danych przy użyciu zapytania SQL lub procedurę składowaną.
- Jako zbiornika, ładowanie danych przy użyciu **PolyBase** lub wstawiania zbiorczego. Jest **zalecane** w celu poprawy wydajności kopii.

> [!IMPORTANT]
> Uwaga PolyBase obsługuje tylko SQL authentcation, ale nie uwierzytelniania usługi Azure Active Directory.

> [!IMPORTANT]
> Po skopiowaniu danych przy użyciu środowiska uruchomieniowego integracji Azure, skonfiguruj [zapory serwera SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) do [Zezwalaj usługom Azure na dostęp do serwera](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Po skopiowaniu danych przy użyciu środowiska uruchomieniowego integracji Self-hosted, należy skonfigurować zapory serwera SQL Azure umożliwiają odpowiedni zakres adresów IP, w tym IP na komputerze, który służy do łączenia z bazą danych SQL Azure.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do łącznika usługi Azure SQL Data Warehouse.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Obsługiwane są następujące właściwości dla usługi Azure SQL Data Warehouse połączone:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **AzureSqlDW** | Yes |
| Parametry połączenia |Podaj informacje wymagane do połączenia z wystąpieniem usługi Azure SQL Data Warehouse właściwości connectionString. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| servicePrincipalId | Określ identyfikator aplikacji klienta. | Tak, podczas korzystania z jednostki usługi uwierzytelniania w usłudze AAD. |
| servicePrincipalKey | Określ klucz aplikacji. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Tak, podczas korzystania z jednostki usługi uwierzytelniania w usłudze AAD. |
| dzierżawa | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Można go pobrać, ustawiając kursor myszy w prawym górnym rogu portalu Azure. | Tak, podczas korzystania z jednostki usługi uwierzytelniania w usłudze AAD. |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. (Jeśli w magazynie danych znajduje się w sieci prywatnej), można użyć środowiska uruchomieniowego integracji Azure lub Self-hosted integracji w czasie wykonywania. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. |Nie |

Różnymi typami uwierzytelniania można znaleźć w poniższych sekcjach na warunki wstępne i przykłady JSON odpowiednio:

- [Przy użyciu uwierzytelniania programu SQL](#using-sql-authentication)
- [Przy użyciu uwierzytelniania tokenu usługi AAD aplikacji - nazwy głównej usługi](#using-service-principal-authentication)
- [Przy użyciu uwierzytelniania tokenu usługi AAD aplikacji - tożsamość usługi zarządzanej](#using-managed-service-identity-authentication)

### <a name="using-sql-authentication"></a>Przy użyciu uwierzytelniania programu SQL

**Przykład połączonej usługi przy użyciu uwierzytelniania programu SQL:**

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

### <a name="using-service-principal-authentication"></a>Przy użyciu uwierzytelniania głównej usługi

Aby użyć usługi głównej na podstawie aplikacji token uwierzytelniania w usłudze AAD, wykonaj następujące kroki:

1. **[Tworzenie aplikacji usługi Azure Active Directory przy użyciu portalu Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application).**  Zanotuj nazwę aplikacji i następujące wartości, których można użyć do zdefiniowania połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. **[Administrator usługi Azure Active Directory do udostępnienia](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  dla serwera SQL Azure w portalu Azure, jeśli nie zostało to jeszcze zrobione. Administrator usługi AAD może być AAD użytkownika lub grupy usługi AAD. Przyznanie grupie msi rolę administratora, pomiń krok 3 i 4 poniżej, jak administrator może mieć pełny dostęp do bazy danych.

3. **Utwórz użytkowników zawartej bazy danych dla nazwy głównej usługi**, łącząc w magazynie danych z i do których chcesz skopiować dane za pomocą takich narzędzi jak SSMS przy użyciu usługi AAD tożsamości o co najmniej ALTER żadnych uprawnień i wykonywanie T-SQL . Dowiedz się więcej informacji na temat użytkowników zawartej bazy danych z [tutaj](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Przyznaj nazwy głównej usługi wymaganych uprawnień** w zwykły sposób dla użytkowników programu SQL, np., wykonując poniżej:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. W ADF należy skonfigurować usługę Azure SQL Data Warehouse połączone.


**Przykład połączonej usługi przy użyciu uwierzytelniania głównej usługi:**

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

### <a name="using-managed-service-identity-authentication"></a>Uwierzytelnianie tożsamości zarządzanych usług

Fabryka danych może być skojarzony z [zarządzane tożsamości usługi (MSI)](data-factory-service-identity.md), reprezentuje tej fabryki danych. Ta tożsamość usługi służy do uwierzytelniania usługi Azure SQL Data Warehouse, dzięki czemu Ta fabryka wyznaczonych do dostępu i skopiować dane z/do magazynu danych.

> [!IMPORTANT]
> Należy pamiętać, że program PolyBase nie jest obecnie obsługiwany dla MSI authentcation.

Aby użyć MSI na podstawie uwierzytelniania tokenu usługi AAD aplikacji, wykonaj następujące kroki:

1. **Utwórz grupę w usłudze Azure AD i dołącz je fabryki MSI grupy**.

    a. Znajdź tożsamość usługi fabryka danych z portalu Azure. Przejdź z fabryką danych -> Właściwości -> kopiowania **Identyfikatora tożsamości usługi**.

    b. Zainstaluj [programu Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modułu, zalogować się przy użyciu `Connect-AzureAD` polecenia i uruchom następujące polecenia, aby utworzyć grupę, a następnie dodaj jako członka fabryki danych MSI.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. **[Administrator usługi Azure Active Directory do udostępnienia](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  dla serwera SQL Azure w portalu Azure, jeśli nie zostało to jeszcze zrobione.

3. **Utwórz użytkownika zawartej bazy danych dla grupy usługi AAD**, łącząc w magazynie danych z i do których chcesz skopiować dane za pomocą takich narzędzi jak SSMS przy użyciu usługi AAD tożsamości o co najmniej ALTER żadnych uprawnień i wykonywanie T-SQL. Dowiedz się więcej informacji na temat użytkowników zawartej bazy danych z [tutaj](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

4. **Przyznaj grupie AAD wymaganych uprawnień** w zwykły sposób dla użytkowników programu SQL, np., wykonując poniżej:

    ```sql
    EXEC sp_addrolemember [role name], [your AAD group name];
    ```

5. W ADF należy skonfigurować usługę Azure SQL Data Warehouse połączone.

**Przykład połączonej usługi przy użyciu uwierzytelniania MSI:**

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

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Azure SQL Data Warehouse.

Aby skopiować dane z i do usługi Azure SQL Data Warehouse, ustaw właściwość Typ zestawu danych do **AzureSqlDWTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type zestawu danych: **AzureSqlDWTable** | Yes |
| tableName |Nazwa tabeli lub widoku w wystąpieniu usługi Azure SQL Data Warehouse, odnoszący się do połączonej usługi. | Yes |

**Przykład:**

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

### <a name="azure-sql-data-warehouse-as-source"></a>Usługa Azure SQL Data Warehouse jako źródło

Aby skopiować dane z usługi Azure SQL Data Warehouse, należy ustawić typ źródła w przypadku działania kopiowania do **SqlDWSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania: **SqlDWSource** | Yes |
| sqlReaderQuery |Użyj niestandardowych zapytania SQL można odczytać danych. Przykład: `select * from MyTable`. |Nie |
| sqlReaderStoredProcedureName |Nazwa procedury przechowywanej, która odczytuje dane z tabeli źródłowej. Ostatniej instrukcji SQL musi być instrukcji SELECT w procedurze składowanej. |Nie |
| storedProcedureParameters |Parametry dla procedury składowanej.<br/>Dozwolone wartości to: par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametry procedury składowanej. |Nie |

**Informacje, które należy zwrócić uwagę:**

- Jeśli **sqlReaderQuery** określono dla SqlSource, odbywa się działanie kopii tego zapytania względem źródła magazynu danych SQL Azure umożliwiają pobieranie danych. Można również określić procedury składowanej, podając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli jest to procedura składowana pobiera parametry).
- Jeśli nie określisz "sqlReaderQuery" lub "sqlReaderStoredProcedureName" kolumn określona w sekcji "structure" zestawu danych JSON służą do utworzenia kwerendy (`select column1, column2 from mytable`) w celu uruchomienia usługi Azure SQL Data Warehouse. Jeśli definicji zestawu danych nie ma "structure", wszystkie kolumny są wybierane w tabeli.
- Jeśli używasz **sqlReaderStoredProcedureName**, nadal należy określić manekina **tableName** właściwość w zestawie danych JSON.

**Przykład: przy użyciu zapytania SQL**

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

**Przykład: przy użyciu procedury składowanej**

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

**Definicja procedury składowanej:**

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

### <a name="azure-sql-data-warehouse-as-sink"></a>Usługa Azure SQL Data Warehouse jako obiekt sink

Aby skopiować dane do usługi Azure SQL Data Warehouse, należy ustawić typ ujścia w działaniu kopiowania do **SqlDWSink**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **zbiornika** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość typu sink działania kopiowania: **SqlDWSink** | Yes |
| allowPolyBase |Wskazuje, czy do użycia zamiast mechanizmu BULKINSERT PolyBase (jeśli jest to wymagane). <br/><br/> **Przy użyciu programu PolyBase jest zalecanym sposobem ładowanie danych do usługi SQL Data Warehouse.** Zobacz [Użyj programu PolyBase, aby załadować dane do usługi Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) sekcji dla ograniczenia i szczegółów.<br/><br/>Dozwolone wartości to: **True**, i **False** (ustawienie domyślne).  |Nie |
| Usługi |Grupy właściwości, które można określić, kiedy **allowPolybase** właściwość jest ustawiona na **true**. |Nie |
| rejectValue |Określa liczbę lub odsetek wierszy, które można odrzucić przed zapytanie nie powiedzie się.<br/><br/>Dowiedz się więcej o opcjach Odrzuć PolyBase **argumenty** sekcji [Tworzenie tabeli zewnętrznej (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) tematu. <br/><br/>Dozwolone wartości to: 0 (domyślnie), 1, 2,... |Nie |
| dla właściwości rejectType |Określa, czy opcja rejectValue jest określona jako wartość literału lub wartość procentowa.<br/><br/>Dozwolone wartości to: **wartość** (ustawienie domyślne) i **procent**. |Nie |
| rejectSampleValue |Określa liczbę wierszy do pobrania przed PolyBase ponownie oblicza procent odrzuconych wierszy.<br/><br/>Dozwolone wartości to: 1, 2,... |Tak, jeśli **dla właściwości rejectType** jest **procent** |
| useTypeDefault |Określa sposób obsługi brakujących wartości w rozdzielane pliki tekstowe, jeśli PolyBase pobiera dane z pliku tekstowego.<br/><br/>Dowiedz się więcej o tej właściwości z sekcji argumenty w [utworzyć EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Dozwolone wartości to: **True**, **False** (ustawienie domyślne). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu. Ma zastosowanie tylko wtedy, gdy aparat PolyBase nie jest używany.<br/><br/>Dozwolone wartości to: liczba całkowita (liczba wierszy). |Nie (domyślna to 10 000) |
| writeBatchTimeout |Czas na ukończenie zanim upłynie limit czasu operacji wstawiania wsadowego oczekiwania. Ma zastosowanie tylko wtedy, gdy aparat PolyBase nie jest używany.<br/><br/>Dozwolone wartości to: timespan. Przykład: "00: 30:00" (30 minut). |Nie |
| preCopyScript |Określ zapytanie SQL dla aktywności kopiowania do wykonania przed zapisaniem danych do usługi Azure SQL Data Warehouse w każdym przebiegu. Ta właściwość służy do oczyszczania danych wstępnie załadowane. |Nie |(#repeatability podczas kopiowania). |Instrukcja zapytania. |Nie |

**Przykład:**

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

Dowiedz się więcej na temat korzystania z programu PolyBase do załadowania do usługi SQL Data Warehouse wydajnie z następnej sekcji.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Użyj programu PolyBase, aby załadować dane do magazynu danych SQL Azure

Przy użyciu **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** jest wydajny sposób ładowania dużych ilości danych do magazynu danych SQL Azure z wysokiej przepływności. Przy użyciu programu PolyBase zamiast domyślnego mechanizmu BULKINSERT widać duże korzyści w przepływności. Zobacz [skopiuj numer odwołania wydajności](copy-activity-performance.md#performance-reference) z szczegółowe porównanie. Aby uzyskać wskazówki z przypadkiem użycia, zobacz [załadować 1 TB do usługi Azure SQL Data Warehouse z fabryką danych Azure w obszarze 15 minut](connector-azure-sql-data-warehouse.md).

* Jeśli źródło danych jest w **obiektów Blob platformy Azure lub usługi Azure Data Lake Store**i format jest zgodny z PolyBase, można skopiować bezpośrednio do usługi Azure SQL Data Warehouse przy użyciu programu PolyBase. Zobacz **[bezpośrednich kopii przy użyciu programu PolyBase](#direct-copy-using-polybase)** ze szczegółami.
* Jeśli Twoje źródła magazynu danych i format nie jest początkowo obsługiwana przez aparat PolyBase, możesz użyć **[przemieszczane kopiowania przy użyciu programu PolyBase](#staged-copy-using-polybase)** funkcji zamiast tego. Udostępnia również możesz lepszą przepustowość automatycznie konwersji danych do formatu zgodnego PolyBase i przechowywanie danych w magazynie obiektów Blob platformy Azure. Następnie ładuje dane do usługi SQL Data Warehouse.

> [!IMPORTANT]
> Należy pamiętać, że program PolyBase nie jest obecnie obsługiwany dla tożsamości usługi zarządzania (MSI) na podstawie authentcation token usługi AAD aplikacji.

### <a name="direct-copy-using-polybase"></a>Bezpośrednie kopiowania przy użyciu programu PolyBase

Aparat PolyBase magazynu danych SQL obsługuje bezpośrednio obiektów Blob platformy Azure i usługi Azure Data Lake Store (przy użyciu nazwy głównej usługi) jako źródło i wymagania format określonego pliku. Jeśli źródło danych spełnia kryteria opisane w tej sekcji, możesz bezpośrednio skopiować z magazynu danych źródła do usługi Azure SQL Data Warehouse przy użyciu programu PolyBase. W przeciwnym razie można użyć [przemieszczane kopiowania przy użyciu programu PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Aby skopiować dane z usługi Data Lake Store SQL Data Warehouse wydajnie, Dowiedz się więcej o [fabryki danych Azure ułatwia nawet wygodny do ujawniania wgląd w dane dotyczące danych podczas korzystania z usługi Data Lake Store z usługi SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Jeśli nie zostały spełnione wymagania, fabryki danych Azure sprawdza ustawienia i automatycznie powraca do mechanizmu BULKINSERT przenoszenia danych.

1. **Źródło połączona usługa** jest typu: **AzureStorage** lub **AzureDataLakeStore** z uwierzytelnianiem główną usługi.
2. **Wejściowy zestaw danych** jest typu: **AzureBlob** lub **AzureDataLakeStoreFile**i wpisz w formacie `type` właściwości **OrcFormat** , **ParquetFormat**, lub **TextFormat** z następujących konfiguracji:

   1. `rowDelimiter` musi być **\n**.
   2. `nullValue` ustawiono **pusty ciąg** (""), lub `treatEmptyAsNull` ustawiono **true**.
   3. `encodingName` ustawiono **utf-8**, która jest **domyślne** wartość.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader`, i `skipLineCount` nie zostały określone.
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

3. Brak nie `skipHeaderLineCount` w obszarze **BlobSource** lub **AzureDataLakeStore** dla działania kopiowania w potoku.
4. Brak nie `sliceIdentifierColumnName` w obszarze **SqlDWSink** dla działania kopiowania w potoku. (PolyBase gwarantuje, że wszystkie dane są aktualizowane lub nic nie jest aktualizowana w jednym przebiegu. Aby osiągnąć **powtarzalności**, można użyć `sqlWriterCleanupScript`).

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

### <a name="staged-copy-using-polybase"></a>Kopiuj przygotowanego przy użyciu programu PolyBase

Źródło danych nie spełnia kryteriów wprowadzone w poprzedniej sekcji, umożliwia kopiowanie danych za pośrednictwem tymczasowego przemieszczania magazynu obiektów Blob Azure (nie może być magazyn w warstwie Premium). W takim przypadku fabryki danych Azure automatycznie wykonuje przekształcenia danych spełnia wymagania dotyczące formatu danych PolyBase, a następnie użyj programu PolyBase, aby załadować dane do usługi SQL Data Warehouse i następnie wyczyść tymczasowego danych z magazynu obiektów Blob. Zobacz [przemieszczane kopiowania](copy-activity-performance.md#staged-copy) szczegółowe informacje na temat jak kopiowanie danych za pośrednictwem tymczasowych obiektów Blob platformy Azure działa na ogół.

Aby użyć tej funkcji, należy utworzyć [połączonej usługi magazynu Azure](connector-azure-blob-storage.md#linked-service-properties) odwołujący się do konta magazynu Azure, które ma magazynu tymczasowego obiektu blob, następnie określ `enableStaging` i `stagingSettings` właściwości dla działania kopiowania, jak pokazano w poniższym kodzie:

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

## <a name="best-practices-when-using-polybase"></a>Najlepsze rozwiązania w sytuacji, gdy przy użyciu programu PolyBase

Poniższe sekcje zawierają dodatkowe wskazówki te, które są wymienione w [najlepsze rozwiązania dotyczące usługi Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Uprawnienia wymagane bazy danych

Aby użyć programu PolyBase, wymaga użytkownika używane do ładowania danych do usługi SQL Data Warehouse [uprawnienia "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) w docelowej bazie danych. Jest jednym ze sposobów osiągnięcia, które można dodać tego użytkownika jako członka roli "db_owner". Dowiedz się, jak to zrobić, postępując [w tej sekcji](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Rozmiar wiersza i danych typu ograniczenia

Program Polybase obciążenia są ograniczone do ładowania wierszy, zarówno mniejszy niż **1 MB** i nie można załadować VARCHR(MAX), NVARCHAR(MAX) lub VARBINARY(MAX). Zapoznaj się [tutaj](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Jeśli masz dane źródłowe z wierszami o rozmiarze większym niż 1 MB, można podzielić tabel źródłowych w pionie na kilka małych sieci, gdy największy rozmiar wiersza dla każdego z nich nie przekracza limit. Mniejsze tabele następnie można załadować przy użyciu programu PolyBase i scalane w usłudze Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Klasa zasobów magazynu danych SQL

Aby uzyskać najlepsze możliwe przepływności, należy wziąć pod uwagę przypisywanie większych klasa zasobów do użytkownika używane do ładowania danych do usługi SQL Data Warehouse przy użyciu programu PolyBase.

### <a name="tablename-in-azure-sql-data-warehouse"></a>tableName w magazynie danych SQL Azure

Poniższa tabela zawiera przykłady dotyczące sposobu określania **tableName** właściwość w zestawie danych JSON dla różnych kombinacji nazwy schematu i tabeli.

| Schemat bazy danych | Nazwa tabeli | Właściwość tableName JSON |
| --- | --- | --- |
| dbo |MyTable |MyTable lub dbo. MyTable lub [dbo]. [MyTable] |
| dbo1 |MyTable |dbo1. MyTable lub [dbo1]. [MyTable] |
| dbo |My.Table |[My.Table] lub [dbo]. [My.Table] |
| dbo1 |My.Table |[dbo1]. [My.Table] |

Jeśli zostanie wyświetlony następujący błąd, może to być problem z wartość określona dla właściwości tableName. Poniższa tabela dla poprawne sposobu na określenie wartości dla właściwości tableName JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Kolumn z wartościami domyślnymi

Obecnie funkcja PolyBase w fabryce danych akceptuje tylko taką samą liczbę kolumn w tabeli docelowej. Przykład tabeli z kolumnami cztery i jeden z nich jest zdefiniowana z wartością domyślną. Dane wejściowe nadal powinien zawierać cztery kolumny. Udostępnia zestaw danych wejściowych 3 kolumny spowoduje uzyskanie błąd podobny do następującego:

```
All columns of the table must be specified in the INSERT BULK statement.
```

Wartość NULL jest specjalny rodzaj wartości domyślnej. W przypadku wartości Null kolumny danych wejściowych (w obiekcie blob) dla tej kolumny może być pusta (nie może być brakuje wejściowy zestaw danych). Program PolyBase wstawia wartość NULL w przypadku ich w magazynie danych SQL Azure.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapowanie typu danych dla usługi Azure SQL Data Warehouse

Podczas kopiowania danych z/na magazyn danych SQL Azure, z typów danych Azure SQL Data Warehouse są używane następujące mapowania do typów danych tymczasowych fabryki danych Azure. Zobacz [schemat i dane typu mapowania](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typu źródłowego: schemat i dane sink.

| Typ danych w usłudze Azure SQL Data Warehouse | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| bigint |Int64 |
| Binarne |Byte[] |
| bitowe |Wartość logiczna |
| char |Ciąg, Char] |
| data |DateTime |
| Data/godzina |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| Atrybut FILESTREAM (varbinary(max)) |Byte[] |
| Liczba zmiennoprzecinkowa |O podwójnej precyzji |
| Obraz |Byte[] |
| int |Int32 |
| oszczędność pieniędzy |Decimal |
| nchar |Ciąg, Char] |
| ntext |Ciąg, Char] |
| numeryczne |Decimal |
| nvarchar |Ciąg, Char] |
| rzeczywiste |Pojedyncze |
| ROWVERSION |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Obiekt * |
| tekst |Ciąg, Char] |
| time |Zakres czasu |
| sygnatura czasowa |Byte[] |
| tinyint |Bajtów |
| Unikatowy identyfikator |Identyfikator GUID |
| varbinary |Byte[] |
| varchar |Ciąg, Char] |
| xml |Xml |

## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
