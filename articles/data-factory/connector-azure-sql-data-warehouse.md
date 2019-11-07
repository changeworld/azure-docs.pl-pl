---
title: Kopiowanie danych do i z Azure SQL Data Warehouse przy użyciu Azure Data Factory
description: Informacje o kopiowaniu danych z obsługiwanych magazynów źródłowych do Azure SQL Data Warehouse lub z SQL Data Warehouse do obsługiwanych magazynów ujścia przy użyciu Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: jingwang
ms.openlocfilehash: b64bfd046a42a630e7913c45213053e84377a037
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681151"
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Kopiowanie danych do lub z Azure SQL Data Warehouse przy użyciu Azure Data Factory 
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Bieżąca wersja](connector-azure-sql-data-warehouse.md)

W tym artykule opisano sposób kopiowania danych do i z Azure SQL Data Warehouse. Aby dowiedzieć się więcej na temat Azure Data Factory, Przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik Azure SQL Data Warehouse jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną tabelą macierzy źródłowej/ujścia](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)

W tym Azure SQL Data Warehouse łącznik obsługuje następujące funkcje:

- Kopiowanie danych przy użyciu uwierzytelniania SQL i usługi Azure Active Directory (Azure AD) uwierzytelnianie tokenu aplikacji przy użyciu nazwy głównej usług lub zarządzanych tożsamości dla zasobów platformy Azure.
- Jako źródło Pobierz dane przy użyciu zapytania SQL lub procedury składowanej.
- Jako ujścia Załaduj dane przy użyciu Base lub INSERT BULK. Zalecamy przeprowadzenie lepszej wydajności kopiowania.

> [!IMPORTANT]
> W przypadku kopiowania danych przy użyciu Integration Runtime Azure Data Factory należy skonfigurować [zaporę programu Azure SQL Server](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) , tak aby usługi platformy Azure mogły uzyskiwać dostęp do serwera.
> W przypadku kopiowania danych przy użyciu własnego środowiska Integration Runtime Skonfiguruj zaporę programu Azure SQL Server tak, aby zezwalała na odpowiedni zakres adresów IP. Ten zakres obejmuje adres IP maszyny, który jest używany do nawiązywania połączenia z Azure SQL Database.

## <a name="get-started"></a>Rozpoczęcie pracy

> [!TIP]
> Aby uzyskać najlepszą wydajność, należy użyć bazy danych na potrzeby ładowania do Azure SQL Data Warehouse. Szczegółowe informacje znajdują się w sekcji [use Base, aby załadować dane do Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) . Aby zapoznać się z przewodnikiem dotyczącym przypadku użycia, zobacz [ładowanie 1 TB do Azure SQL Data Warehouse na 15 minut z Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które definiują Data Factory jednostek specyficznych dla łącznika Azure SQL Data Warehouse.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane dla Azure SQL Data Warehouse połączonej usługi:

| Właściwość            | Opis                                                  | Wymagany                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | Właściwość Type musi być ustawiona na wartość **AzureSqlDW**.             | Tak                                                          |
| Przekształcon    | Określ informacje, które są konieczne do nawiązania połączenia z wystąpieniem Azure SQL Data Warehouse dla właściwości **ConnectionString** . <br/>Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory. Klucz nazwy głównej usługi można również umieścić w Azure Key Vault i jeśli jest to uwierzytelnianie SQL, należy ściągnąć konfigurację `password` z parametrów połączenia. Zobacz przykład JSON poniżej tabeli i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. | Tak                                                          |
| servicePrincipalId  | Określ identyfikator klienta aplikacji.                         | Tak, w przypadku używania uwierzytelniania usługi Azure AD z jednostką usługi. |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak, w przypadku używania uwierzytelniania usługi Azure AD z jednostką usługi. |
| dzierżaw              | Określ informacje o dzierżawie (nazwę domeny lub identyfikator dzierżawy), w których znajduje się Twoja aplikacja. Możesz ją pobrać, aktywując wskaźnik myszy w prawym górnym rogu Azure Portal. | Tak, w przypadku używania uwierzytelniania usługi Azure AD z jednostką usługi. |
| Właściwością connectvia          | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub własnego środowiska Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. | Nie                                                           |

W przypadku różnych typów uwierzytelniania zapoznaj się z poniższymi sekcjami dotyczącymi wymagań wstępnych i próbek JSON odpowiednio:

- [Uwierzytelnianie SQL](#sql-authentication)
- Uwierzytelnianie tokenu aplikacji usługi Azure AD: nazwa [główna usługi](#service-principal-authentication)
- Uwierzytelnianie tokenu aplikacji usługi Azure AD: [zarządzane tożsamości dla zasobów platformy Azure](#managed-identity)

>[!TIP]
>Jeśli wystąpi błąd z kodem błędu jako "UserErrorFailedToConnectToSqlServer" i komunikatem "limit sesji dla bazy danych to XXX i został osiągnięty.", Dodaj `Pooling=false` do parametrów połączenia i spróbuj ponownie.

### <a name="sql-authentication"></a>Uwierzytelnianie SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Przykład połączonej usługi korzystającej z uwierzytelniania SQL

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

**Hasło w Azure Key Vault:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Aby skorzystać z uwierzytelniania tokena aplikacji opartego na jednostce usługi Azure AD, wykonaj następujące kroki:

1. **[Utwórz aplikację Azure Active Directoryową](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** z Azure Portal. Zanotuj nazwę aplikacji oraz następujące wartości, które definiują połączoną usługę:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. Jeśli jeszcze tego nie zrobiono, **[zainicjuj Azure Active Directory administratorem](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** usługi Azure SQL server na Azure Portal. Administrator usługi Azure AD może być użytkownikiem usługi Azure AD lub grupą usługi Azure AD. Jeśli grupa ma zarządzaną tożsamość rolę administratora, Pomiń kroki 3 i 4. Administrator będzie miał pełny dostęp do bazy danych programu.

3. **[Utwórz użytkowników zawartej bazy danych](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** dla jednostki usługi. Nawiąż połączenie z magazynem danych z lub, do którego chcesz skopiować dane przy użyciu narzędzi, takich jak program SSMS, z tożsamością usługi Azure AD, która ma co najmniej zmienione uprawnienia użytkownika. Uruchom następujące T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Przyznaj jednostce usługi odpowiednie uprawnienia** , które zwykle są przeznaczone dla użytkowników SQL lub innych. Uruchom Poniższy kod lub zapoznaj się z innymi opcjami [tutaj](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Jeśli chcesz użyć bazy danych, aby załadować dane, zapoznaj się z [wymaganym uprawnieniem](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Skonfiguruj połączoną usługę Azure SQL Data Warehouse** w Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Przykład połączonej usługi korzystającej z uwierzytelniania jednostki usługi

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

### <a name="managed-identity"></a>Zarządzane tożsamości na potrzeby uwierzytelniania zasobów platformy Azure

Fabryka danych może być skojarzona z [zarządzaną tożsamością dla zasobów platformy Azure](data-factory-service-identity.md) , która reprezentuje konkretną fabrykę. Tej tożsamości zarządzanej można użyć do uwierzytelniania Azure SQL Data Warehouse. Wydaną fabrykę mogą uzyskać dostęp do danych z lub do hurtowni danych lub je z niej kopiować przy użyciu tej tożsamości.

Aby korzystać z uwierzytelniania tożsamości zarządzanej, wykonaj następujące kroki:

1. Jeśli jeszcze tego nie zrobiono, **[zainicjuj Azure Active Directory administratorem](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** usługi Azure SQL server na Azure Portal. Administrator usługi Azure AD może być użytkownikiem usługi Azure AD lub grupą usługi Azure AD. Jeśli grupa ma zarządzaną tożsamość rolę administratora, Pomiń kroki 3 i 4. Administrator będzie miał pełny dostęp do bazy danych programu.

2. **[Utwórz użytkowników zawartej bazy danych](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** dla Data Factory tożsamości zarządzanej. Nawiąż połączenie z magazynem danych z lub, do którego chcesz skopiować dane przy użyciu narzędzi, takich jak program SSMS, z tożsamością usługi Azure AD, która ma co najmniej zmienione uprawnienia użytkownika. Uruchom następujące T-SQL. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Przyznaj Data Factoryj zarządzanej tożsamości** , które są potrzebne w zwykły sposób dla użytkowników SQL i innych. Uruchom Poniższy kod lub zapoznaj się z innymi opcjami [tutaj](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Jeśli chcesz użyć bazy danych, aby załadować dane, zapoznaj się z [wymaganym uprawnieniem](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Skonfiguruj połączoną usługę Azure SQL Data Warehouse** w Azure Data Factory.

**Przykład:**

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Azure SQL Data Warehouse.

Aby skopiować dane z lub do Azure SQL Data Warehouse, obsługiwane są następujące właściwości:

| Właściwość  | Opis                                                  | Wymagany                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | Właściwość **Type** zestawu danych musi być ustawiona na wartość **AzureSqlDWTable**. | Tak                         |
| schematy | Nazwa schematu. |Nie dla źródła, tak dla ujścia  |
| tabele | Nazwa tabeli/widoku. |Nie dla źródła, tak dla ujścia  |
| tableName | Nazwa tabeli/widoku ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z poprzednimi wersjami. W przypadku nowych obciążeń Użyj `schema` i `table`. | Nie dla źródła, tak dla ujścia |

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
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujścia Azure SQL Data Warehouse.

### <a name="azure-sql-data-warehouse-as-the-source"></a>Azure SQL Data Warehouse jako źródło

Aby skopiować dane z Azure SQL Data Warehouse, ustaw właściwość **Type** w źródle działania Copy na **SqlDWSource**. W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość                     | Opis                                                  | Wymagany |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | Właściwość **Type** źródła działania Copy musi być ustawiona na wartość **SqlDWSource**. | Tak      |
| sqlReaderQuery               | Użyj niestandardowego zapytania SQL, aby odczytać dane. Przykład: `select * from MyTable`. | Nie       |
| sqlReaderStoredProcedureName | Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. Ostatnia instrukcja SQL musi być instrukcją SELECT w procedurze składowanej. | Nie       |
| storedProcedureParameters    | Parametry procedury składowanej.<br/>Dozwolone wartości to pary nazw lub wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. | Nie       |

### <a name="points-to-note"></a>Punkty do uwagi

- Jeśli **sqlReaderQuery** jest określony dla elementu **Sqlsource**, działanie Copy uruchamia to zapytanie względem źródła Azure SQL Data Warehouse, aby uzyskać dane. Lub można określić procedurę składowaną. Określ **sqlReaderStoredProcedureName** i **storedProcedureParameters** , jeśli procedura składowana pobiera parametry.
- Jeśli nie określisz elementu **sqlReaderQuery** ani **sqlReaderStoredProcedureName**, kolumny zdefiniowane w sekcji **Structure** w formacie JSON zestawu danych są używane do konstruowania zapytania. `select column1, column2 from mytable` działa względem Azure SQL Data Warehouse. Jeśli definicja zestawu danych nie ma **struktury**, wszystkie kolumny są wybierane z tabeli.

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

### <a name="azure-sql-data-warehouse-as-sink"></a>Azure SQL Data Warehouse jako ujścia

Aby skopiować dane do Azure SQL Data Warehouse, ustaw typ ujścia w działaniu Copy na **SqlDWSink**. W sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość          | Opis                                                  | Wymagany                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | Właściwość **Type** ujścia działania Copy musi być ustawiona na wartość **SqlDWSink**. | Tak                                           |
| allowPolyBase     | Wskazuje, czy należy użyć bazy, jeśli ma zastosowanie, a nie mechanizmu BULKINSERT. <br/><br/> Zalecamy załadowanie danych do SQL Data Warehouse przy użyciu bazy. Zobacz sekcję [use Base, aby załadować dane do Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) w celu uzyskania ograniczeń i szczegółów.<br/><br/>Dozwolone wartości to **true** i **false** (wartość domyślna). | Nie                                            |
| polyBaseSettings  | Grupa właściwości, które można określić, gdy właściwość **allowPolybase** ma **wartość true**. | Nie                                            |
| rejectValue       | Określa liczbę lub procent wierszy, które można odrzucić przed zakończeniem wykonywania zapytania.<br/><br/>Dowiedz się więcej o opcjach odrzucania podstawowych elementów w sekcji argumentów [instrukcji CREATE External Table (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Dozwolone wartości to 0 (wartość domyślna), 1, 2 itd. | Nie                                            |
| rejectType        | Określa, czy opcja **rejectValue** jest wartością literału, czy wartością procentową.<br/><br/>Dozwolone wartości to **wartość** (domyślna) i **procent**. | Nie                                            |
| rejectSampleValue | Określa liczbę wierszy do pobrania przed ponownym obliczeniem procentu odrzuconych wierszy.<br/><br/>Dozwolone wartości to 1, 2 itd. | Tak, jeśli **odrzucenie** ma **wartość procentowo**. |
| useTypeDefault    | Określa, jak obsługiwać brakujące wartości w rozdzielanych plikach tekstowych, gdy baza danych pobiera dane z pliku tekstowego.<br/><br/>Więcej informacji na temat tej właściwości znajduje się w sekcji argumentów w temacie [Create External File Format (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Dozwolone wartości to **true** i **false** (wartość domyślna).<br><br> | Nie                                            |
| writeBatchSize    | Liczba wierszy do wstawienia do tabeli SQL **na partię**. Ma zastosowanie tylko wtedy, gdy baza nie jest używana.<br/><br/>Dozwolona wartość to liczba **całkowita** (liczba wierszy). Domyślnie Data Factory dynamicznie określa odpowiedni rozmiar wsadu na podstawie rozmiaru wiersza. | Nie                                            |
| writeBatchTimeout | Czas oczekiwania na zakończenie operacji wstawiania partii przed przekroczeniem limitu czasu. Ma zastosowanie tylko wtedy, gdy baza nie jest używana.<br/><br/>Dozwolona wartość to **TimeSpan**. Przykład: "00:30:00" (30 minut). | Nie                                            |
| preCopyScript     | Określ zapytanie SQL dla działania kopiowania, które ma zostać uruchomione przed zapisaniem danych w Azure SQL Data Warehouse w każdym przebiegu. Ta właściwość służy do czyszczenia wstępnie załadowanych danych. | Nie                                            |
| tableOption | Określa, czy tabela ujścia ma być automatycznie tworzona, jeśli nie istnieje na podstawie schematu źródłowego. Funkcja autotworzenia tabeli nie jest obsługiwana, gdy kopia etapowa jest skonfigurowana w działaniu kopiowania. Dozwolone wartości to: `none` (wartość domyślna), `autoCreate`. |Nie |
| disableMetricsCollection | Data Factory zbiera metryki, takie jak SQL Data Warehouse jednostek dwu na potrzeby optymalizacji wydajności kopiowania i zaleceń. Jeśli chodzi o to zachowanie, określ `true`, aby je wyłączyć. | Nie (wartość domyślna to `false`) |

#### <a name="sql-data-warehouse-sink-example"></a>Przykład SQL Data Warehouse ujścia

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

Dowiedz się więcej o sposobach efektywnego ładowania SQL Data Warehouse w następnej sekcji za pomocą bazy.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Użyj wielobase, aby załadować dane do Azure SQL Data Warehouse

Korzystanie z [bazy danych Base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) jest wydajnym sposobem ładowania dużej ilości dane do Azure SQL Data Warehouse z wysoką przepływność. Zostanie wyświetlony duży wzrost przepływności przy użyciu sieci podstawowej zamiast domyślnego mechanizmu BULKINSERT. Aby zapoznać się z przewodnikiem dotyczącym przypadku użycia, zobacz [ładowanie 1 TB do Azure SQL Data Warehouse](v1/data-factory-load-sql-data-warehouse.md).

* Jeśli dane źródłowe są w **obiekcie blob platformy Azure, Azure Data Lake Storage Gen1 lub Azure Data Lake Storage Gen2**i **Format jest zgodny z bazą**danych, można użyć działania kopiowania, aby bezpośrednio wywołać Azure SQL Data Warehouse bazę danych z źródła. Aby uzyskać szczegółowe informacje, zobacz **[Direct Copy przy użyciu bazy](#direct-copy-by-using-polybase)** .
* Jeśli źródłowy magazyn danych i format nie są pierwotnie obsługiwane przez bazę kodu, Użyj zamiast niej funkcji **[Base](#staged-copy-by-using-polybase)** . Funkcja kopiowania etapowego zapewnia lepszą przepływność. Automatycznie konwertuje dane do formatu zgodnego z podstawą. Dane są przechowywane w usłudze Azure Blob Storage. Następnie ładuje dane do SQL Data Warehouse.

>[!TIP]
>Dowiedz się więcej na temat [najlepszych rozwiązań dotyczących korzystania z bazy Base](#best-practices-for-using-polybase).

### <a name="direct-copy-by-using-polybase"></a>Bezpośrednie kopiowanie przy użyciu bazy

SQL Data Warehouse baza jest bezpośrednio obsługiwana przez usługę Azure Blob, Azure Data Lake Storage Gen1 i Azure Data Lake Storage Gen2. Jeśli dane źródłowe są zgodne z kryteriami opisanymi w tej sekcji, należy użyć bazy danych Base do skopiowania bezpośrednio z magazynu z danymi źródłowymi do Azure SQL Data Warehouse. W przeciwnym razie użyj opcji [kopia przygotowana z użyciem bazy](#staged-copy-by-using-polybase).

> [!TIP]
> Aby efektywnie kopiować dane do SQL Data Warehouse, Dowiedz się więcej od [Azure Data Factory ułatwia to odkrywanie danych przy użyciu Data Lake Store z SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Jeśli wymagania nie są spełnione, Azure Data Factory sprawdza ustawienia i automatycznie powraca do mechanizmu BULKINSERT na potrzeby przenoszenia danych.

1. **Źródłowa usługa połączona** z następującymi typami i metodami uwierzytelniania:

    | Obsługiwany typ magazynu danych źródłowych                             | Obsługiwany typ uwierzytelniania źródłowego                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Obiekt blob platformy Azure](connector-azure-blob-storage.md)                | Uwierzytelnianie klucza konta, uwierzytelnianie tożsamości zarządzanej |
    | [Usługa Azure Data Lake Storage 1. generacji](connector-azure-data-lake-store.md) | Uwierzytelnianie jednostki usługi                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Uwierzytelnianie klucza konta, uwierzytelnianie tożsamości zarządzanej |

    >[!IMPORTANT]
    >Jeśli usługa Azure Storage jest skonfigurowana za pomocą punktu końcowego usługi sieci wirtualnej, należy użyć uwierzytelniania tożsamości zarządzanej — Zobacz, aby korzystać z [punktów końcowych usługi sieci wirtualnej w usłudze Azure Storage](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Zapoznaj się z wymaganymi konfiguracjami w Data Factory z poziomu [uwierzytelniania tożsamości zarządzanego przez obiekt blob platformy Azure](connector-azure-blob-storage.md#managed-identity) i sekcji [uwierzytelniania tożsamości zarządzanego](connector-azure-data-lake-storage.md#managed-identity) przez usługę Azure Data Lake Storage Gen2.

2. **Format danych źródłowych** jest **Parquet**, **Orc**lub **rozdzielany tekstem**z następującymi konfiguracjami:

   1. Ścieżka folderu nie zawiera filtru symboli wieloznacznych.
   2. Nazwa pliku jest pusta lub wskazuje na pojedynczy plik. W przypadku określenia wieloznacznej nazwy pliku w działaniu kopiowania można ją `*` lub `*.*`.
   3. `rowDelimiter` to **default**, **\n**, **\r\n**lub **\r**.
   4. `nullValue` jest pozostawiony jako domyślny lub ustawiony jako **ciąg pusty** (""), a `treatEmptyAsNull` jest pozostawiony jako domyślny lub ma ustawioną wartość true.
   5. `encodingName` jest pozostawiony jako domyślny lub ustawiony na **UTF-8**.
   6. nie określono `quoteChar`, `escapeChar`i `skipLineCount`. Obsługa linii bazowej pomija wiersz nagłówka, który można skonfigurować jako `firstRowAsHeader` w podajniku ADF.
   7. `compression` nie może być **kompresja**, **gzip**ani **Wklęśnięcie**.

3. Jeśli źródło jest folderem, `recursive` w działaniu kopiowania muszą mieć ustawioną wartość true.

>[!NOTE]
>Jeśli źródło jest folderem, należy zwrócić uwagę, że plik jest pobierany z folderu i wszystkich jego podfolderów, a nie pobiera danych z plików, dla których nazwa pliku zaczyna się od podkreślenia (_) lub kropki (.), zgodnie z opisem w [miejscu ARGUMENTU lokalizacji](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2).

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
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
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSetting",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>Przygotowana kopia przy użyciu bazy

Jeśli dane źródłowe nie spełniają kryteriów w poprzedniej sekcji, Włącz kopiowanie danych za pośrednictwem tymczasowego tymczasowego wystąpienia magazynu obiektów blob platformy Azure. Nie może to być Premium Storage platformy Azure. W takim przypadku Azure Data Factory automatycznie uruchamia przekształcenia danych w celu spełnienia wymagań dotyczących formatu danych Base. Następnie używa podstawy, aby załadować dane do SQL Data Warehouse. Na koniec czyści dane tymczasowe z magazynu obiektów BLOB. Aby uzyskać szczegółowe informacje o kopiowaniu danych za pośrednictwem wystąpienia tymczasowego magazynu obiektów blob platformy Azure, zobacz [przygotowane kopie](copy-activity-performance.md#staged-copy) .

Aby użyć tej funkcji, Utwórz [połączoną usługę azure BLOB Storage](connector-azure-blob-storage.md#linked-service-properties) , która odwołuje się do konta usługi Azure Storage z tymczasowym magazynem obiektów BLOB. Następnie określ `enableStaging` i `stagingSettings` właściwości działania kopiowania, jak pokazano w poniższym kodzie.

>[!IMPORTANT]
>Jeśli przejściowy Magazyn Azure jest skonfigurowany z punktem końcowym usługi sieci wirtualnej, należy użyć uwierzytelniania tożsamości zarządzanej — Zobacz, aby korzystać z [punktów końcowych usługi sieci wirtualnej w usłudze Azure Storage](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Informacje na temat konfiguracji wymaganych w programie Data Factory z poziomu [uwierzytelniania tożsamości zarządzanego przez usługę Azure Blob](connector-azure-blob-storage.md#managed-identity).

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

## <a name="best-practices-for-using-polybase"></a>Najlepsze rozwiązania dotyczące korzystania z bazy

W poniższych sekcjach znajdują się najlepsze rozwiązania, które są opisane w [najlepszych rozwiązaniach dotyczących Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Wymagane uprawnienie do bazy danych

Aby można było korzystać z bazy danych, użytkownik ładujący dane do SQL Data Warehouse musi mieć [uprawnienie "kontrola"](https://msdn.microsoft.com/library/ms191291.aspx) w docelowej. Jednym ze sposobów na osiągnięcie tego jest dodanie użytkownika jako członka roli **db_owner** . Dowiedz się, jak to zrobić w [SQL Data Warehouse przegląd](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limits"></a>Limity rozmiaru wierszy i typów danych

Obciążenia wielopodstawowe są ograniczone do wierszy mniejszych niż 1 MB. Nie można jej użyć do załadowania do VARCHR (MAX), NVARCHAR (MAX) lub VARBINARY (MAX). Aby uzyskać więcej informacji, zobacz [SQL Data Warehouse limitów pojemności usługi](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Gdy dane źródłowe mają wiersze większe niż 1 MB, możesz chcieć podzielić tabele źródłowe na kilka małych. Upewnij się, że największy rozmiar każdego wiersza nie przekracza limitu. Mniejsze tabele mogą następnie zostać załadowane przy użyciu bazy Base i scalone w Azure SQL Data Warehouse.

Alternatywnie, w przypadku danych z takimi szerokimi kolumnami, możesz użyć niebazowej do załadowania danych za pomocą funkcji ADF, wyłączając ustawienie "Zezwalaj na podstawę".

### <a name="sql-data-warehouse-resource-class"></a>Klasa zasobów SQL Data Warehouse

Aby osiągnąć najlepszą możliwą przepływność, należy przypisać do użytkownika większą klasę zasobów, która ładuje dane do SQL Data Warehouse za pośrednictwem bazy.

### <a name="polybase-troubleshooting"></a>Podstawowe rozwiązywanie problemów

**Ładowanie do kolumny dziesiętnej**

Jeśli dane źródłowe znajdują się w formacie tekstowym lub w innych niebazowych magazynach zgodnych (przy użyciu kopii etapowej i wielobazowej) i zawiera wartość pustą do załadowania do kolumny SQL Data Warehouse dziesiętnych, może wystąpić następujący błąd:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

Rozwiązaniem jest wyznaczanie opcji "**Użyj typu domyślnego**" (jako FAŁSZ) w obszarze ujścia działania kopiowania — > ustawienia podstawowe. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" jest podstawową konfiguracją natywną, która określa, jak obsłużyć brakujące wartości w rozdzielanych plikach tekstowych, gdy baza danych pobiera dane z pliku tekstowego. 

**`tableName` w Azure SQL Data Warehouse**

W poniższej tabeli przedstawiono przykłady sposobu określania właściwości **TableName** w zestawie danych JSON. Pokazuje kilka kombinacji nazw schematu i tabeli.

| Schemat bazy danych | Nazwa tabeli | **tabela** Właściwość JSON               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable lub dbo. MyTable lub [dbo]. MyTable |
| dbo1      | MyTable    | dbo1. MyTable lub [dbo1]. MyTable          |
| dbo       | My. Table   | [My. Table] lub [dbo]. [My. Table]            |
| dbo1      | My. Table   | [dbo1]. [My. Table]                         |

Jeśli zostanie wyświetlony następujący błąd, problem może być wartością określoną dla właściwości **TableName** . Poprzednia tabela zawiera poprawną metodę określania wartości dla właściwości **TableName** JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Kolumny z wartościami domyślnymi**

Obecnie funkcja wielobase w Data Factory akceptuje tylko taką samą liczbę kolumn jak w tabeli docelowej. Przykładem jest tabela z czterema kolumnami, w których jedna z nich jest zdefiniowana z wartością domyślną. Dane wejściowe nadal muszą zawierać cztery kolumny. Zestaw danych wejściowych z trzema kolumnami zwraca błąd podobny do następującego komunikatu:

```
All columns of the table must be specified in the INSERT BULK statement.
```

Wartość NULL jest specjalną formą wartości domyślnej. Jeśli kolumna dopuszcza wartość null, dane wejściowe w obiekcie blob dla tej kolumny mogą być puste. Ale nie może być brak w wejściowym zestawie danych. Baza jest wstawiana do wartości NULL w przypadku brakujących wartości w Azure SQL Data Warehouse.

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Dowiedz się więcej o [przekształceniu źródłowym](data-flow-source.md) i [transformacji ujścia](data-flow-sink.md) w mapowaniu przepływu danych.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapowanie typu danych dla Azure SQL Data Warehouse

Podczas kopiowania danych z programu lub do Azure SQL Data Warehouse, następujące mapowania są używane z Azure SQL Data Warehouse typów danych do Azure Data Factory pośrednich typów danych. Zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md) , aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych do ujścia.

>[!TIP]
>Informacje o [typach danych tabeli w Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md) artykule na temat obsługiwanych typów danych w usłudze SQL DW oraz obejścia dla nieobsługiwanych elementów.

| Typ danych Azure SQL Data Warehouse    | Data Factory typ danych pośrednich |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| Binarny                                | Byte []                         |
| bit                                   | Wartość logiczna                        |
| delikatn                                  | String, Char []                 |
| date                                  | DateTime                       |
| Datę                              | DateTime                       |
| datetime2                             | DateTime                       |
| DateTimeOffset                        | DateTimeOffset                 |
| Dokładności                               | Dokładności                        |
| FILESTREAM — atrybut (varbinary (max)) | Byte []                         |
| Float                                 | Double                         |
| image                                 | Byte []                         |
| int                                   | Elementem                          |
| finansowego                                 | Dokładności                        |
| nchar                                 | String, Char []                 |
| przypada                               | Dokładności                        |
| nvarchar                              | String, Char []                 |
| czasie rzeczywistym                                  | Pojedyncze                         |
| rowversion                            | Byte []                         |
| smalldatetime                         | DateTime                       |
| smallint                              | Int16                          |
| smallmoney                            | Dokładności                        |
| time                                  | Czasu                       |
| tinyint                               | Bajc                           |
| uniqueidentifier                      | Identyfikator GUID                           |
| liczby                             | Byte []                         |
| varchar                               | String, Char []                 |

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="getmetadata-activity-properties"></a>Właściwości działania GetMetadata

Aby uzyskać szczegółowe informacje na temat właściwości, sprawdź [działanie GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny i formaty danych](copy-activity-overview.md##supported-data-stores-and-formats).
