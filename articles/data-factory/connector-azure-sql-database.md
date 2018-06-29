---
title: Kopiowanie danych do lub z bazą danych SQL Azure przy użyciu fabryki danych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z obsługiwanej źródłowej magazyny danych do bazy danych SQL Azure lub z bazy danych SQL do zbiornika obsługiwane magazyny danych przy użyciu fabryki danych.
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
ms.date: 05/05/2018
ms.author: jingwang
ms.openlocfilehash: 5287a1d1f09a7057590b455c14aa7f70128ad7fa
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37053646"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Kopiowanie danych do lub z bazą danych SQL Azure przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you use:"]
> * [W wersji 1](v1/data-factory-azure-sql-connector.md)
> * [Bieżąca wersja](connector-azure-sql-database.md)

W tym artykule opisano sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z lub do bazy danych SQL Azure. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować dane z lub do bazy danych SQL Azure żadnych obsługiwanych ujścia magazynu danych. I można skopiować danych z dowolnego źródła obsługiwanych magazynu danych do bazy danych SQL Azure. Lista magazynów danych, które są obsługiwane jako źródła lub wychwytywanie przez działanie kopiowania, zobacz [obsługiwane formaty i magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik bazy danych SQL Azure obsługuje następujące funkcje:

- Kopiowanie danych przy użyciu uwierzytelniania programu SQL i aplikacji usługi Azure Active Directory (Azure AD) tokenu uwierzytelniania przy użyciu nazwy głównej usługi lub zarządzane tożsamości usługi (MSI).
- Jako źródła pobierania danych przy użyciu zapytania SQL lub procedurę składowaną.
- Jako zbiorniku Aby dołączyć dane do tabeli docelowej lub wywołaj procedurę składowaną z niestandardowej logiki podczas kopiowania.

> [!IMPORTANT]
> Po skopiowaniu danych za pomocą obsługi integracji fabryki danych Azure, skonfiguruj [zapory serwera Azure SQL](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) tak, aby dostęp do serwera usług Azure.
> Po skopiowaniu danych przy użyciu środowiska uruchomieniowego integracji siebie, należy skonfigurować zapory serwera Azure SQL umożliwiają odpowiedni zakres adresów IP. Ten zakres obejmuje IP na komputerze, który służy do łączenia z bazą danych SQL Azure.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do łącznika usługi Azure SQL Database.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Te właściwości są obsługiwane dla bazy danych SQL Azure połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** musi mieć ustawioną właściwość **AzureSqlDatabase**. | Yes |
| Parametry połączenia | Podaj informacje wymagane do połączenia z wystąpieniem bazy danych SQL Azure dla **connectionString** właściwości. Oznacz to pole jako **SecureString** Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| servicePrincipalId | Określ identyfikator aplikacji klienta. | Tak, gdy użytkownik korzysta z nazwy głównej usługi uwierzytelniania usługi Azure AD. |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako **SecureString** Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Tak, gdy użytkownik korzysta z nazwy głównej usługi uwierzytelniania usługi Azure AD. |
| dzierżawa | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Pobrać, ustawiając kursor myszy w prawym górnym rogu portalu Azure. | Tak, gdy użytkownik korzysta z nazwy głównej usługi uwierzytelniania usługi Azure AD. |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. Mogą używać środowiska uruchomieniowego integracji Azure lub runtime siebie integracji, jeśli w magazynie danych znajduje się w sieci prywatnej. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. | Nie |

Różnymi typami uwierzytelniania można znaleźć w poniższych sekcjach na warunki wstępne i przykłady JSON odpowiednio:

- [Uwierzytelnianie SQL](#sql-authentication)
- [Azure AD aplikacji token uwierzytelniania: nazwy głównej usługi](#service-principal-authentication)
- [Azure AD aplikacji token uwierzytelniania: tożsamość usługi zarządzania](#managed-service-identity-authentication)

### <a name="sql-authentication"></a>Uwierzytelnianie SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Przykład połączonej usługi, w którym jest używane uwierzytelnianie SQL

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

2. **[Administrator usługi Azure Active Directory do udostępnienia](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  dla serwera Azure SQL w portalu Azure, jeśli jeszcze tego nie zrobiono. Administrator usługi Azure AD musi mieć usługi Azure AD użytkownika lub grupy usługi Azure AD, ale nie może być nazwy głównej usługi. Ten krok odbywa się tak, aby w następnym kroku, można za pomocą usługi Azure AD identity utworzyć główną użytkownika zawartej bazy danych dla usługi.

3. **[Utwórz użytkowników zawartej bazy danych](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  dla nazwy głównej usługi. Połączenie z bazą danych z lub do których chcesz skopiować dane za pomocą takich narzędzi jak SSMS, przy użyciu tożsamości usługi Azure AD, który ma co najmniej uprawnienie ALTER ANY użytkownika. Uruchom T-SQL: 
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Przyznaj nazwy głównej usługi wymaganych uprawnień** w zwykły sposób dla użytkowników SQL lub innym osobom. Uruchom poniższy kod:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. **Konfigurowanie usługi baza danych SQL Azure połączone** w fabryce danych Azure.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Przykład połączonej usługi, w którym jest używane uwierzytelnianie głównej usługi

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

Fabryka danych mogą być skojarzone z [zarządzane tożsamość usługi](data-factory-service-identity.md) reprezentujący fabryki określonych danych. Ta tożsamość usługi służy do uwierzytelniania bazy danych SQL Azure. Fabryka wyznaczonych mogą uzyskiwać dostęp do i kopiowanie danych z lub z bazą danych przy użyciu tej tożsamości.

Aby używać uwierzytelniania tokenu aplikacji MSI na podstawie usługi Azure AD, wykonaj następujące kroki:

1. **Utwórz grupę w usłudze Azure AD.** Należy do grupy fabryki MSI.

    a. Znajdź tożsamość usługi fabryka danych z portalu Azure. Przejdź z fabryką danych **właściwości**. Skopiuj identyfikator tożsamości usługi.

    b. Zainstaluj [programu Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modułu. Zaloguj się przy użyciu `Connect-AzureAD` polecenia. Uruchom następujące polecenia, aby utworzyć grupę, a następnie dodaj jako członka fabryki danych MSI.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. **[Administrator usługi Azure Active Directory do udostępnienia](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  dla serwera Azure SQL w portalu Azure, jeśli jeszcze tego nie zrobiono. Administrator usługi Azure AD może być użytkownika usługi Azure AD lub grupy usługi Azure AD. Przyznanie grupie msi rolę administratora, pomiń kroki 3 i 4. Administrator będą mieli pełny dostęp do bazy danych.

3. **[Utwórz użytkowników zawartej bazy danych](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  dla grupy usługi Azure AD. Połączenie z bazą danych z lub do których chcesz skopiować dane za pomocą takich narzędzi jak SSMS, przy użyciu tożsamości usługi Azure AD, który ma co najmniej uprawnienie ALTER ANY użytkownika. Uruchom T-SQL: 
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

4. **Przyznaj grupie usługi Azure AD wymaganych uprawnień** w zwykły sposób użytkowników SQL i innych użytkowników. Na przykład uruchom następujący kod:

    ```sql
    EXEC sp_addrolemember [role name], [your AAD group name];
    ```

5. **Konfigurowanie usługi baza danych SQL Azure połączone** w fabryce danych Azure.

#### <a name="linked-service-example-that-uses-msi-authentication"></a>Przykład połączonej usługi, który korzysta z uwierzytelniania MSI

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje, zobacz [zestawów danych](https://docs.microsoft.com/en-us/azure/data-factory/concepts-datasets-linked-services) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych bazy danych SQL Azure.

Aby skopiować dane z lub do bazy danych SQL Azure, należy ustawić **typu** właściwości zestawu danych na **AzureSqlTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** musi mieć ustawioną właściwość dataset **AzureSqlTable**. | Yes |
| tableName | Nazwa tabeli lub widoku w wystąpieniu bazy danych SQL Azure, odnoszący się do połączonej usługi. | Yes |

#### <a name="dataset-properties-example"></a>Przykład właściwości zestawu danych

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę obsługiwanych przez usługi Azure SQL Database źródłowy i odbiorczy właściwości.

### <a name="azure-sql-database-as-the-source"></a>Baza danych SQL Azure jako źródła

Aby skopiować dane z bazy danych SQL Azure, należy ustawić **typu** właściwości w źródle działanie kopiowania **SqlSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** musi mieć ustawioną właściwość źródła działanie kopiowania **SqlSource**. | Yes |
| sqlReaderQuery | Użyj niestandardowych zapytania SQL można odczytać danych. Przykład: `select * from MyTable`. | Nie |
| sqlReaderStoredProcedureName | Nazwa procedury przechowywanej, która odczytuje dane z tabeli źródłowej. Ostatniej instrukcji SQL musi być instrukcji SELECT w procedurze składowanej. | Nie |
| storedProcedureParameters | Parametry dla procedury składowanej.<br/>Dozwolone wartości to par nazw ani wartości. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametry procedury składowanej. | Nie |

### <a name="points-to-note"></a>Uwagi na

- Jeśli **sqlReaderQuery** określono **SqlSource**, odbywa się działanie kopii tego zapytania względem źródła bazy danych SQL Azure umożliwiają pobieranie danych. Lub możesz określić procedury składowanej. Określ **sqlReaderStoredProcedureName** i **storedProcedureParameters** Jeśli procedura składowana pobiera parametry.
- Jeśli nie określisz, albo **sqlReaderQuery** lub **sqlReaderStoredProcedureName**, kolumn zdefiniowanych w **struktury** części zestawu danych JSON są używane do Utwórz zapytanie. `select column1, column2 from mytable` działa w odniesieniu do bazy danych SQL Azure. Jeśli nie ma definicji zestawu danych **struktury**, wybrano wszystkich kolumn z tabeli.
- Jeśli używasz **sqlReaderStoredProcedureName**, nadal należy określić manekina **tableName** właściwość w zestawie danych JSON.

#### <a name="sql-query-example"></a>Przykład zapytania SQL

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "SqlSource",
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
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "SqlSource",
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

### <a name="azure-sql-database-as-the-sink"></a>Baza danych SQL Azure jako obiekt sink

Aby skopiować dane do bazy danych SQL Azure, należy ustawić **typu** właściwość w działaniu kopiowania sink do **SqlSink**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **zbiornika** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** musi mieć ustawioną właściwość zbiornika działanie kopiowania **SqlSink**. | Yes |
| writeBatchSize | Wstawia dane do tabeli SQL, gdy osiągnie rozmiar buforu **writeBatchSize**.<br/> Dozwolone wartości to **całkowitą** (liczba wierszy). | Nie. Wartość domyślna to 10 000. |
| writeBatchTimeout | Czas oczekiwania dla partii wstawić na zakończenie zanim upłynie limit czasu operacji.<br/> Dozwolone wartości to **timespan**. Przykład: "00: 30:00" (30 minut). | Nie |
| preCopyScript | Określ zapytanie SQL dla aktywności kopiowania do uruchomienia przed zapisaniem danych do bazy danych SQL Azure. Jest tylko wywoływana raz na kopii Uruchom. Ta właściwość służy do oczyszczania załadowanych danych. | Nie |
| sqlWriterStoredProcedureName | Nazwa procedury składowanej, który definiuje sposób stosowania źródła danych do tabeli docelowej. Przykładem jest upserts lub przekształcanie za pomocą logiki biznesowej. <br/><br/>Procedura składowana jest **wywoływane na partię**. Dla operacji, które tylko uruchomione jeden raz i nie mają nic wspólnego z źródła danych, użyj `preCopyScript` właściwości. Przykład działania mają delete i obcięcia. | Nie |
| storedProcedureParameters |Parametry dla procedury składowanej.<br/>Dozwolone wartości to pary nazw i wartości. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametry procedury składowanej. | Nie |
| sqlWriterTableType | Określ nazwę typu tabeli do użycia w procedurze składowanej. Działanie kopiowania udostępnia dane jest przenoszony w tabeli tymczasowej o tym typie tabeli. Kod procedury składowanej można następnie scalić dane są kopiowane z istniejącymi danymi. | Nie |

> [!TIP]
> Po skopiowaniu danych do usługi Azure SQL Database działanie kopiowania dołącza dane do tabeli ujścia domyślnie. Aby wykonać upsert lub dodatkowe reguły biznesowe, należy użyć procedury składowanej w **SqlSink**. Dowiedz się więcej szczegółów z [wywoła procedurę składowaną z SQL Sink](#invoking-stored-procedure-for-sql-sink).

#### <a name="append-data-example"></a>Dołącz przykładowe dane

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

#### <a name="invoke-a-stored-procedure-during-copy-for-upsert-example"></a>Wywołanie procedury składowanej podczas kopiowania, na przykład upsert

Dowiedz się więcej szczegółów z [wywoła procedurę składowaną z SQL Sink](#invoking-stored-procedure-for-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="identity-columns-in-the-target-database"></a>Kolumny tożsamości w docelowej bazie danych

W tej sekcji przedstawiono sposób kopiowania danych z tabeli źródłowej bez kolumny tożsamości do docelowej tabeli zawierającej kolumnę tożsamości.

#### <a name="source-table"></a>Tabela źródłowa

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

#### <a name="destination-table"></a>Tabela docelowa

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

> [!NOTE]
> Tabela docelowa ma kolumny tożsamości.

#### <a name="source-dataset-json-definition"></a>Źródło zestawu danych JSON definicji

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

#### <a name="destination-dataset-json-definition"></a>Docelowy zestaw danych JSON definicji

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

> [!NOTE]
> Tabela źródłowa i docelowa mają różne schemat. 

Element docelowy ma dodatkową kolumnę z tożsamością. W tym scenariuszu, należy określić **struktury** właściwości w definicji zestawu danych docelowego, który nie zawiera kolumny tożsamości.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Wywołaj procedurę składowaną z zbiornika SQL

Po skopiowaniu danych do bazy danych SQL Azure można również skonfigurować i wywołanie procedury składowanej określony użytkownik z dodatkowymi parametrami.

Podczas kopiowania wbudowane mechanizmy nie służą do celów, można użyć procedury składowanej. Zwykle są używane podczas upsert, insert i update lub dodatkowe operacje, które należy wykonać przed ostatnim wstawiania źródła danych do tabeli docelowej. Przykłady dodatkowe operacje są kolumny scalania, wyszukiwanie dodatkowe wartości i wstawia go do więcej niż jednej tabeli.

Poniższy przykład przedstawia sposób użycia procedury składowanej celu upsert do tabeli w bazie danych SQL Azure. Założono, że wprowadzania danych i sink **Marketing** Tabela każdy ma trzy kolumny: **ProfileID**, **stanu**, i **kategorii**. Czy upsert na podstawie **ProfileID** kolumny i zastosować je tylko dla określonej kategorii.

#### <a name="output-dataset"></a>Wyjściowy zestaw danych

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Zdefiniuj **SqlSink** sekcji w przypadku działania kopiowania:

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

W bazie danych, należy zdefiniować procedury składowanej z taką samą nazwę jak **SqlWriterStoredProcedureName**. Ten program obsługi danych wejściowych z określonego źródła i scala w tabeli wyników. Nazwa parametru procedury składowanej powinna być taka sama jak **tableName** zdefiniowany w zestawie danych.

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
AS
BEGIN
  MERGE [dbo].[Marketing] AS target
  USING @Marketing AS source
  ON (target.ProfileID = source.ProfileID and target.Category = @category)
  WHEN MATCHED THEN
      UPDATE SET State = source.State
  WHEN NOT MATCHED THEN
      INSERT (ProfileID, State, Category)
      VALUES (source.ProfileID, source.State, source.Category)
END
```

W bazie danych, określ typ tabeli z taką samą nazwę jak **sqlWriterTableType**. Schemat tabeli powinna być taka sama jak schemat zwrócony przez dane wejściowe.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL,
)
```

Funkcja procedury składowanej korzysta z [zwracającej tabelę parametrów](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-azure-sql-database"></a>Mapowanie typu danych dla bazy danych SQL Azure

Podczas kopiowania danych z lub do bazy danych SQL Azure, następujące mapowania są używane z bazą danych SQL Azure typów danych do typów danych tymczasowych fabryki danych Azure. Zobacz [schemat i dane typu mapowania](copy-activity-schema-and-type-mapping.md) Aby dowiedzieć się, jak działanie kopiowania mapy typ źródła: schemat i dane do ujścia.

| Typ danych bazy danych SQL Azure | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| bigint |Int64 |
| dane binarne |Byte[] |
| bitowe |Wartość logiczna |
| char |Ciąg, Char] |
| data |DateTime |
| Data/godzina |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| Atrybut FILESTREAM (varbinary(max)) |Byte[] |
| Liczba zmiennoprzecinkowa |podwójne |
| image |Byte[] |
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
| tinyint |Bajt |
| Unikatowy identyfikator |Identyfikator GUID |
| varbinary |Byte[] |
| varchar |Ciąg, Char] |
| xml |Xml |

## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane formaty i magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
