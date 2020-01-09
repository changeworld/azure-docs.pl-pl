---
title: Kopiowanie i Przekształcanie danych w usłudze Azure Synapse Analytics
description: Dowiedz się, jak kopiować dane do i z usługi Azure Synapse Analytics oraz przekształcać dane w usłudze Azure Synapse Analytics przy użyciu Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 02fb46fe764c8e34b440a1a0388fc31f7615cfb3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440744"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Kopiowanie i Przekształcanie danych w usłudze Azure Synapse Analytics (dawniej Azure SQL Data Warehouse) za pomocą Azure Data Factory 

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Bieżąca wersja](connector-azure-sql-data-warehouse.md)

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z i do usługi Azure Synapse Analytics oraz do przekształcania danych w Azure Data Lake Storage Gen2. Aby dowiedzieć się więcej na temat usługi Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik usługi Azure Synapse Analytics jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną tabelą macierzy źródłowej/ujścia](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)

W przypadku działania kopiowania ten łącznik usługi Azure Synapse Analytics obsługuje te funkcje:

- Kopiowanie danych przy użyciu uwierzytelniania SQL i uwierzytelnianie tokenu aplikacji usługi Azure Active Directory (Azure AD) przy użyciu tożsamości podmiotu zabezpieczeń lub zarządzanej usługi dla zasobów platformy Azure.
- Jako źródło pobierać dane przy użyciu zapytania SQL lub procedury składowanej.
- Jako ujścia Załaduj dane przy użyciu instrukcji [Base](#use-polybase-to-load-data-into-azure-sql-data-warehouse) lub [copy](#use-copy-statement) (wersja zapoznawcza) lub Wstaw zbiorczo. Zalecamy użycie instrukcji Base lub COPY (wersja zapoznawcza) w celu uzyskania lepszej wydajności kopiowania.

> [!IMPORTANT]
> W przypadku kopiowania danych za pomocą usługi Azure Data Factory Integration Runtime, skonfiguruj [zapory serwera Azure SQL](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) tak, aby usług platformy Azure mają dostęp do serwera.
> W przypadku kopiowania danych przy użyciu własnego środowiska integration runtime, należy skonfigurować zaporę serwera Azure SQL, aby umożliwić odpowiedni zakres adresów IP. Ten zakres obejmuje adres IP maszyny, który jest używany do nawiązywania połączenia z usługą Azure Synapse Analytics.

## <a name="get-started"></a>Rozpocznij

> [!TIP]
> Aby uzyskać najlepszą wydajność, należy użyć bazy danych na platformie Azure Synapse Analytics. [Aby załadować dane do usługi Azure Synapse Analytics, należy wykonać](#use-polybase-to-load-data-into-azure-sql-data-warehouse) szczegóły. Aby zapoznać się z przewodnikiem dotyczącym przypadku użycia, zobacz [ładowanie 1 TB do usługi Azure Synapse Analytics na 15 minut z Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które definiują Data Factory jednostek specyficznych dla łącznika usługi Azure Synapse Analytics.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla połączonej usługi Azure Synapse Analytics:

| Właściwość            | Opis                                                  | Wymagane                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | Właściwość type musi być równa **AzureSqlDW**.             | Tak                                                          |
| connectionString    | Określ informacje, które są konieczne do nawiązania połączenia z wystąpieniem usługi Azure Synapse Analytics dla właściwości **ConnectionString** . <br/>Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory. Klucz nazwy głównej usługi można również umieścić w Azure Key Vault i jeśli jest to uwierzytelnianie SQL, należy ściągnąć konfigurację `password` z parametrów połączenia. Zobacz przykład JSON poniżej tabeli i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. | Tak                                                          |
| servicePrincipalId  | Określ identyfikator klienta aplikacji.                         | Tak, gdy używasz uwierzytelniania usługi Azure AD przy użyciu jednostki usługi. |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Tak, gdy używasz uwierzytelniania usługi Azure AD przy użyciu jednostki usługi. |
| tenant              | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Można je pobrać, ustawiając kursor myszy w prawym górnym rogu witryny Azure Portal. | Tak, gdy używasz uwierzytelniania usługi Azure AD przy użyciu jednostki usługi. |
| connectVia          | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. (Jeśli Twój magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub własnego środowiska integration runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. | Nie                                                           |

Różnymi typami uwierzytelniania można znaleźć w następnych sekcjach dotyczących wymagań wstępnych i przykłady kodu JSON odpowiednio:

- [Uwierzytelnianie SQL](#sql-authentication)
- Uwierzytelnianie usługi Azure AD aplikacji tokenu: [nazwy głównej usługi](#service-principal-authentication)
- Uwierzytelnianie usługi Azure AD aplikacji tokenu: [zarządzanych tożsamości dla zasobów platformy Azure](#managed-identity)

>[!TIP]
>Jeśli osiągnięty błąd z kodem jako "UserErrorFailedToConnectToSqlServer", a wiadomości, takich jak "limit sesji dla bazy danych jest XXX i został osiągnięty.", Dodaj `Pooling=false` parametry połączenia i spróbuj ponownie.

### <a name="sql-authentication"></a>Uwierzytelnianie SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Przykład połączonej usługi, który używa uwierzytelniania SQL

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

Aby użyć uwierzytelniania tokenu aplikacji usługi oparte na jednostce usługi Azure AD, wykonaj następujące kroki:

1. **[Tworzenie aplikacji usługi Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)**  w witrynie Azure portal. Zanotuj nazwę aplikacji i następujące wartości, które definiują połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. **[Aprowizowanie administrator usługi Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  dla serwera Azure SQL w witrynie Azure portal, jeśli jeszcze tego nie zrobiłeś. Administrator usługi Azure AD może być użytkownika usługi Azure AD lub grupy usługi Azure AD. Jeśli grupa ma zarządzaną tożsamość rolę administratora, Pomiń kroki 3 i 4. Administrator będą mieć pełny dostęp do bazy danych.

3. **[Tworzenie użytkowników zawartej bazy danych](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  dla jednostki usługi. Łączenie z magazynem danych, z lub do której należy skopiować dane za pomocą narzędzi, takich jak program SSMS, za pomocą tożsamości usługi Azure AD, który ma co najmniej uprawnienie ALTER ANY użytkownika. Uruchom polecenie języka T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Przyznaj nazwy głównej usługi potrzebnych uprawnień** , jak zwykle dla użytkowników SQL lub inne osoby. Uruchom Poniższy kod lub zapoznaj się z innymi opcjami [tutaj](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Jeśli chcesz użyć bazy danych, aby załadować dane, zapoznaj się z [wymaganym uprawnieniem](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Skonfiguruj połączoną usługę Azure Synapse Analytics** w Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Przykład połączonej usługi, który używa uwierzytelniania jednostki usługi

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30",
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

### <a name="managed-identity"></a> Zarządzanych tożsamości do uwierzytelniania zasobów platformy Azure

Fabryka danych może być skojarzony z [tożsamości zarządzanej dla zasobów platformy Azure](data-factory-service-identity.md) reprezentujący określonego fabryki. Tej tożsamości zarządzanej można użyć do uwierzytelniania usługi Azure Synapse Analytics. Fabryka wyznaczonym mogą uzyskiwać dostęp do i kopiowania danych z lub do danych magazynu przy użyciu tej tożsamości.

Aby korzystać z uwierzytelniania tożsamości zarządzanej, wykonaj następujące kroki:

1. **[Aprowizowanie administrator usługi Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  dla serwera Azure SQL w witrynie Azure portal, jeśli jeszcze tego nie zrobiłeś. Administrator usługi Azure AD może być użytkownika usługi Azure AD lub grupy usługi Azure AD. Jeśli grupa ma zarządzaną tożsamość rolę administratora, Pomiń kroki 3 i 4. Administrator będą mieć pełny dostęp do bazy danych.

2. **[Utwórz użytkowników zawartej bazy danych](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** dla Data Factory tożsamości zarządzanej. Łączenie z magazynem danych, z lub do której należy skopiować dane za pomocą narzędzi, takich jak program SSMS, za pomocą tożsamości usługi Azure AD, który ma co najmniej uprawnienie ALTER ANY użytkownika. Uruchom języka T-SQL. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Przyznaj Data Factoryj zarządzanej tożsamości** , które są potrzebne w zwykły sposób dla użytkowników SQL i innych. Uruchom Poniższy kod lub zapoznaj się z innymi opcjami [tutaj](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Jeśli chcesz użyć bazy danych, aby załadować dane, zapoznaj się z [wymaganym uprawnieniem](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Skonfiguruj połączoną usługę Azure Synapse Analytics** w Azure Data Factory.

**Przykład:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. 

Następujące właściwości są obsługiwane dla zestawu danych usługi Azure Synapse Analytics:

| Właściwość  | Opis                                                  | Wymagane                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | **Typu** właściwości zestawu danych musi być równa **AzureSqlDWTable**. | Tak                         |
| schema | Nazwa schematu. |Brak źródła tak dla ujścia  |
| table | Nazwa tabeli/widoku. |Brak źródła tak dla ujścia  |
| tableName | Nazwa tabeli/widoku ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z poprzednimi wersjami. W przypadku nowych obciążeń Użyj `schema` i `table`. | Brak źródła tak dla ujścia |

#### <a name="dataset-properties-example"></a>Przykład właściwości zestawu danych

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure Synapse Analytics linked service name>",
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

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujścia usługi Azure Synapse Analytics.

### <a name="azure-synapse-analytics-as-the-source"></a>Usługa Azure Synapse Analytics jako źródło

Aby skopiować dane z usługi Azure Synapse Analytics, ustaw właściwość **Type** w źródle działania Copy na **SqlDWSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość                     | Opis                                                  | Wymagane |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | **Typu** właściwość źródła działania kopiowania musi być równa **SqlDWSource**. | Tak      |
| sqlReaderQuery               | Umożliwia odczytywanie danych niestandardowe zapytania SQL. Przykład: `select * from MyTable`. | Nie       |
| sqlReaderStoredProcedureName | Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. Ostatnią instrukcję SQL musi być instrukcja SELECT w procedurze składowanej. | Nie       |
| storedProcedureParameters    | Parametry procedury składowanej.<br/>Dozwolone wartości to pary nazw ani wartości. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. | Nie       |

**Przykład: użycie zapytania SQL**

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

**Przykład: używanie procedury składowanej**

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

**Przykładowa procedura składowana:**

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

### <a name="azure-sql-data-warehouse-as-sink"></a>Azure Synapse Analytics jako ujścia

Azure Data Factory obsługuje trzy sposoby ładowania danych do SQL Data Warehouse.

![Opcje kopiowania ujścia magazynu danych SQL](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Użyj bazy](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 
- [Use COPY — instrukcja (wersja zapoznawcza)](#use-copy-statement)
- Użyj wstawiania zbiorczego

Najszybszym i najbardziej skalowalnym sposobem ładowania danych jest użycie instrukcji [Base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) lub [copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (wersja zapoznawcza).

Aby skopiować dane do usługi Azure SQL Data Warehouse, należy ustawić typ ujścia w działaniu kopiowania, aby **SqlDWSink**. Następujące właściwości są obsługiwane w działaniu kopiowania **ujścia** sekcji:

| Właściwość          | Opis                                                  | Wymagane                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | **Typu** właściwość ujścia działania kopiowania musi być równa **SqlDWSink**. | Tak                                           |
| allowPolyBase     | Wskazuje, czy baza danych ma zostać załadowana do SQL Data Warehouse. `allowCopyCommand` i `allowPolyBase` nie mogą jednocześnie mieć wartości true. <br/><br/>Aby uzyskać informacje o ograniczeniach i szczegółach, zobacz temat [Korzystanie z bazy Azure SQL Data Warehouse danych](#use-polybase-to-load-data-into-azure-sql-data-warehouse) .<br/><br/>Dozwolone wartości to **True** i **False** (ustawienie domyślne). | Nie.<br/>Zastosuj podczas korzystania z bazy.     |
| polyBaseSettings  | Grupa właściwości, które można określić, gdy właściwość `allowPolybase` ma **wartość true**. | Nie.<br/>Zastosuj podczas korzystania z bazy. |
| allowCopyCommand | Wskazuje, czy należy użyć [instrukcji Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (wersja zapoznawcza) w celu załadowania danych do SQL Data Warehouse. `allowCopyCommand` i `allowPolyBase` nie mogą jednocześnie mieć wartości true. <br/><br/>Zobacz [używanie instrukcji Copy, aby załadować dane do sekcji Azure SQL Data Warehouse](#use-copy-statement) w celu uzyskania ograniczeń i szczegółów.<br/><br/>Dozwolone wartości to **True** i **False** (ustawienie domyślne). | Nie.<br>Zastosuj, gdy jest używana kopia. |
| copyCommandSettings | Grupa właściwości, które można określić, gdy właściwość `allowCopyCommand` ma wartość TRUE. | Nie.<br/>Zastosuj, gdy jest używana kopia. |
| writeBatchSize    | Liczba wierszy do wstawienia do tabeli SQL **na partię**.<br/><br/>Dozwolone wartości to **całkowitą** (liczba wierszy). Domyślnie Data Factory dynamicznie określa odpowiedni rozmiar wsadu na podstawie rozmiaru wiersza. | Nie.<br/>Zastosuj przy użyciu wstawiania zbiorczego.     |
| writeBatchTimeout | Czas na zakończenie przed upływem limitu czasu operacji wstawiania wsadowego oczekiwania.<br/><br/>Dozwolone wartości to **timespan**. Przykład: "00: 30:00" (30 minut). | Nie.<br/>Zastosuj przy użyciu wstawiania zbiorczego.        |
| preCopyScript     | Określ zapytanie SQL, działanie kopiowania do uruchomienia przed zapisaniem danych do usługi Azure SQL Data Warehouse w każdym przebiegu. Ta właściwość służy do oczyszczania załadowanych danych. | Nie                                            |
| tableOption | Określa, czy tabela ujścia ma być automatycznie tworzona, jeśli nie istnieje na podstawie schematu źródłowego. Funkcja autotworzenia tabeli nie jest obsługiwana, gdy kopia etapowa jest skonfigurowana w działaniu kopiowania. Dozwolone wartości to: `none` (wartość domyślna), `autoCreate`. |Nie |
| disableMetricsCollection | Data Factory zbiera metryki, takie jak SQL Data Warehouse jednostek dwu na potrzeby optymalizacji wydajności kopiowania i zaleceń. Jeśli chodzi o to zachowanie, określ `true`, aby je wyłączyć. | Nie (wartość domyślna to `false`) |

#### <a name="sql-data-warehouse-sink-example"></a>Przykład obiektu sink SQL Data Warehouse

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

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Ładowanie danych do usługi Azure SQL Data Warehouse przy użyciu technologii PolyBase

Korzystanie z [bazy danych Base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) jest wydajnym sposobem na ładowanie dużej ilości dane do usługi Azure Synapse Analytics z wysoką przepływność. Zobaczysz duże korzyści przepływności przy użyciu programu PolyBase zamiast domyślnego mechanizmu BULKINSERT. Przewodnik z przypadkiem użycia można znaleźć w temacie [Load 1 TB do usługi Azure Synapse Analytics](v1/data-factory-load-sql-data-warehouse.md).

* Jeśli dane źródłowe są w **obiekcie blob platformy Azure, Azure Data Lake Storage Gen1 lub Azure Data Lake Storage Gen2**i **Format jest zgodny z bazą**danych, można użyć działania kopiowania, aby bezpośrednio wywołać Azure SQL Data Warehouse bazę danych z źródła. Aby uzyskać więcej informacji, zobacz  **[bezpośrednie kopiowania przy użyciu programu PolyBase](#direct-copy-by-using-polybase)** .
* Jeśli Twoje źródłowy magazyn danych i format pierwotnie nie jest obsługiwana przez program PolyBase, użyj **[kopiowania etapowego za pomocą programu PolyBase](#staged-copy-by-using-polybase)** są wyposażone w zamian. Funkcja kopiowania przejściowego zapewnia także większą przepływność. Dane są automatycznie konwertowane w formacie zgodnym z podstawą, są przechowywane w usłudze Azure Blob Storage SQL Data Warehouse.

>[!TIP]
>Dowiedz się więcej na temat [najlepszych rozwiązań dotyczących korzystania z bazy Base](#best-practices-for-using-polybase).

Następujące ustawienia podstawowe są obsługiwane w obszarze `polyBaseSettings` działania kopiowania:

| Właściwość          | Opis                                                  | Wymagane                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Określa liczbę lub wartość procentowa wierszy, które można odrzucić przed zapytanie nie powiedzie się.<br/><br/>Dowiedz się więcej na temat opcji odrzucania w technologii PolyBase w sekcji argumenty [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Dozwolone wartości to 0 (domyślnie), 1, 2, itp. | Nie                                            |
| rejectType        | Określa, czy **rejectValue** opcja jest wartością literałową lub wartości procentowej.<br/><br/>Dozwolone wartości to **wartość** (ustawienie domyślne) i **procent**. | Nie                                            |
| rejectSampleValue | Określa liczbę wierszy do pobrania, zanim program PolyBase ponownie oblicza odsetek odrzuconych wierszy.<br/><br/>Dozwolone wartości to 1, 2, itp. | Tak, jeśli **rejectType** jest **procent**. |
| useTypeDefault    | Określa sposób obsługi brakujących wartości w rozdzielanych plików tekstowych, jeśli funkcja PolyBase pobiera dane z pliku tekstowego.<br/><br/>Dowiedz się więcej na temat tej właściwości z sekcji argumentów w [tworzenie EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Dozwolone wartości to **True** i **False** (ustawienie domyślne).<br><br> | Nie                                            |

### <a name="direct-copy-by-using-polybase"></a>Bezpośrednie kopiowania przy użyciu programu PolyBase

SQL Data Warehouse baza jest bezpośrednio obsługiwana przez usługę Azure Blob, Azure Data Lake Storage Gen1 i Azure Data Lake Storage Gen2. Jeśli dane źródłowe spełniają kryteria opisane w tej sekcji, do kopiowania bezpośrednio z magazynu danych źródłowych do usługi Azure Synapse Analytics należy użyć bazy. W przeciwnym razie użyj [kopiowania etapowego za pomocą programu PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Aby efektywnie kopiować dane do SQL Data Warehouse, Dowiedz się więcej od [Azure Data Factory ułatwia to odkrywanie danych przy użyciu Data Lake Store z SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Jeśli nie są spełnione wymagania, usługi Azure Data Factory umożliwia sprawdzenie ustawień i automatycznie powraca do przenoszenia danych przy użyciu mechanizmu BULKINSERT.

1. **Źródłowa usługa połączona** z następującymi typami i metodami uwierzytelniania:

    | Obsługiwany typ magazynu danych źródłowych                             | Obsługiwany typ uwierzytelniania źródłowego                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | Uwierzytelnianie klucza konta, uwierzytelnianie tożsamości zarządzanej |
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
   7. `compression` może być **bez kompresji**, **GZip**, lub **Deflate**.

3. Jeśli źródło jest folderem, `recursive` w działaniu kopiowania muszą mieć ustawioną wartość true.

4. nie określono `wildcardFolderPath`, `wildcardFilename`, `modifiedDateTimeStart`i `modifiedDateTimeEnd`.

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

### <a name="staged-copy-by-using-polybase"></a>Kopiowania przejściowego przy użyciu programu PolyBase

Jeśli dane źródłowe nie są natywnie zgodne z bazą danych Base, włącz je w ramach tymczasowego przemieszczania wystąpienia magazynu obiektów blob platformy Azure (nie może to być Premium Storage platformy Azure). W takim przypadku Azure Data Factory automatycznie konwertuje dane, aby spełniały wymagania formatu danych Base. Następnie wywołuje bazę danych Base, aby załadować dane do SQL Data Warehouse. Na koniec go czyści dane tymczasowe z magazynu obiektów blob. Zobacz [kopiowania etapowego](copy-activity-performance.md#staged-copy) szczegółowe informacje na temat kopiowania danych za pośrednictwem przemieszczania wystąpienia magazynu obiektów Blob platformy Azure.

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

### <a name="best-practices-for-using-polybase"></a>Najlepsze rozwiązania dotyczące przy użyciu technologii PolyBase

W poniższych sekcjach znajdują się najlepsze rozwiązania, które są opisane w [najlepszych rozwiązaniach dotyczących usługi Azure Synapse Analytics](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

#### <a name="required-database-permission"></a>Uprawnienia wymagane bazy danych

Aby korzystać z technologii PolyBase, musi mieć użytkownik, który ładuje dane do usługi SQL Data Warehouse [uprawnienie "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) do docelowej bazy danych. Jest jednym ze sposobów, aby to osiągnąć, aby dodać użytkownika jako członka **db_owner** roli. Dowiedz się, jak to zrobić w [omówienie SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Rozmiar wiersza i danych typ ograniczenia

Obciążenia funkcji PolyBase są ograniczone do wierszy jest mniejszy niż 1 MB. Nie można jej użyć do załadowania do VARCHR (MAX), NVARCHAR (MAX) lub VARBINARY (MAX). Aby uzyskać więcej informacji, zobacz [limitów pojemności usługi SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Źródło danych zawiera wiersze przekracza 1 MB, można w pionie z kilku małych sieci podzielić tabel źródłowych. Upewnij się, że największy rozmiar dla każdego wiersza nie przekracza limit. Mniejsze tabele mogą następnie zostać załadowane przy użyciu bazy Base i scalone ze sobą w usłudze Azure Synapse Analytics.

Alternatywnie, w przypadku danych z takimi szerokimi kolumnami, możesz użyć niebazowej do załadowania danych za pomocą funkcji ADF, wyłączając ustawienie "Zezwalaj na podstawę".

#### <a name="sql-data-warehouse-resource-class"></a>Klasa zasobów SQL Data Warehouse

Aby osiągnąć najlepsze możliwe przepływność, należy przypisać do większej klasy zasobów do użytkownika, który ładuje dane do usługi SQL Data Warehouse za pomocą programu PolyBase.

#### <a name="polybase-troubleshooting"></a>Podstawowe rozwiązywanie problemów

**Ładowanie do kolumny dziesiętnej**

Jeśli dane źródłowe znajdują się w formacie tekstowym lub w innych niebazowych magazynach zgodnych (przy użyciu kopii etapowej i wielobazowej) i zawiera wartość pustą do załadowania do kolumny SQL Data Warehouse dziesiętnych, może wystąpić następujący błąd:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

Rozwiązaniem jest wyznaczanie opcji "**Użyj typu domyślnego**" (jako FAŁSZ) w obszarze ujścia działania kopiowania — > ustawienia podstawowe. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" jest podstawową konfiguracją natywną, która określa, jak obsłużyć brakujące wartości w rozdzielanych plikach tekstowych, gdy baza danych pobiera dane z pliku tekstowego. 

**`tableName` w usłudze Azure Synapse Analytics**

W poniższej tabeli przedstawiono przykłady sposobu określania **tableName** właściwość w zestawie danych JSON. Pokazuje kilka kombinacji nazwy schematu i tabeli.

| Schemat bazy danych | Nazwa tabeli | **Właściwość tableName** właściwość JSON               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable lub dbo. MyTable lub [dbo]. [MyTable] |
| dbo1      | MyTable    | dbo1. MyTable lub [dbo1]. [MyTable]          |
| dbo       | My.Table   | [My.Table] lub [dbo].[My.Table]            |
| dbo1      | My.Table   | [dbo1].[My.Table]                         |

Jeśli zostanie wyświetlony następujący błąd, problem może polegać na wartość określona dla **tableName** właściwości. Można znaleźć w poprzedniej tabeli prawidłowym sposobem, aby określić wartości dla **tableName** właściwość JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Kolumny z wartościami domyślnymi**

Obecnie funkcja PolyBase w usłudze Data Factory akceptuje tylko tę samą liczbę kolumn w tabeli docelowej. Przykładem jest tabela z czterech kolumn, w którym jeden z nich jest zdefiniowana z wartością domyślną. Dane wejściowe nadal musi mieć czterech kolumn. Wejściowy zestaw danych trzy kolumny daje błąd podobny do następującego:

```
All columns of the table must be specified in the INSERT BULK statement.
```

Wartość NULL jest specjalną forma wartości domyślnej. Jeśli kolumna ma wartość null, dane wejściowe w obiekcie blob dla tej kolumny może być pusta. Ale nie może być Brak wejściowego zestawu danych. Baza jest wstawiana do wartości NULL w przypadku brakujących wartości w usłudze Azure Synapse Analytics.

## <a name="use-copy-statement"></a>Użyj instrukcji COPY, aby załadować dane do Azure SQL Data Warehouse (wersja zapoznawcza)

[Instrukcja SQL Data Warehouse Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (wersja zapoznawcza) bezpośrednio obsługuje ładowanie danych z **obiektów blob platformy Azure i Azure Data Lake Storage Gen2**. Jeśli dane źródłowe spełniają kryteria opisane w tej sekcji, możesz użyć instrukcji COPY w ADF, aby załadować dane do Azure SQL Data Warehouse. Azure Data Factory sprawdza ustawienia i uruchamia działanie kopiowania w przypadku niespełnienia kryteriów.

>[!NOTE]
>Obecnie Data Factory obsługują tylko kopiowanie ze źródeł zgodnych z instrukcją COPY wymienione poniżej.

Użycie instrukcji COPY obsługuje następującą konfigurację:

1. **Źródłowa połączona usługa i format** są z następującymi typami i metodami uwierzytelniania:

    | Obsługiwany typ magazynu danych źródłowych                             | Obsługiwany format           | Obsługiwany typ uwierzytelniania źródłowego                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | [Tekst rozdzielany](format-delimited-text.md)             | Uwierzytelnianie klucza konta, uwierzytelnianie sygnatury dostępu współdzielonego, uwierzytelnianie nazwy głównej usługi, uwierzytelnianie tożsamości zarządzanej |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | Uwierzytelnianie klucza konta, uwierzytelnianie sygnatury dostępu współdzielonego |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | Uwierzytelnianie klucza konta, uwierzytelnianie sygnatury dostępu współdzielonego |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [Tekst rozdzielany](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | Uwierzytelnianie klucza konta, uwierzytelnianie jednostki usługi, uwierzytelnianie tożsamości zarządzanej |

    >[!IMPORTANT]
    >Jeśli usługa Azure Storage jest skonfigurowana za pomocą punktu końcowego usługi sieci wirtualnej, należy użyć uwierzytelniania tożsamości zarządzanej — Zobacz, aby korzystać z [punktów końcowych usługi sieci wirtualnej w usłudze Azure Storage](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Zapoznaj się z wymaganymi konfiguracjami w Data Factory z poziomu [uwierzytelniania tożsamości zarządzanego przez obiekt blob platformy Azure](connector-azure-blob-storage.md#managed-identity) i sekcji [uwierzytelniania tożsamości zarządzanego](connector-azure-data-lake-storage.md#managed-identity) przez usługę Azure Data Lake Storage Gen2.

2. Ustawienia formatu są następujące:

   1. Dla **Parquet**: `compression` nie może być **kompresją**, **przyciągiem**ani **gzip**.
   2. Dla **Orc**: `compression` **nie**może mieć kompresji, **zlib**ani **przyciągania**.
   3. Dla **tekstu rozdzielanego**:
      1. `rowDelimiter` jest jawnie ustawiona jako **pojedynczy znak** lub " **\r\n**", wartość domyślna nie jest obsługiwana.
      2. `nullValue` jest pozostawiony jako domyślny lub ustawiony jako **pusty ciąg** ("").
      3. `encodingName` jest pozostawiony jako domyślny lub ustawiony na **UTF-8 lub UTF-16**.
      4. `escapeChar` musi być taka sama jak `quoteChar`i nie jest pusta.
      5. `skipLineCount` jest pozostawiony jako domyślny lub ustawiony na 0.
      6. `compression` nie może być **kompresją** ani **gzip**.

3. Jeśli źródło jest folderem, `recursive` w działaniu kopiowania muszą mieć ustawioną wartość true.

4. nie określono `wildcardFolderPath`, `wildcardFilename`, `modifiedDateTimeStart`i `modifiedDateTimeEnd`.

Następujące ustawienia instrukcji COPY są obsługiwane w `allowCopyCommand` w działaniu kopiowania:

| Właściwość          | Opis                                                  | Wymagane                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| wartości Defaults | Określa wartości domyślne dla każdej kolumny docelowej w programie SQL DW.  Wartości domyślne we właściwości zastępują domyślne ograniczenie ustawione w magazynie danych, a kolumna tożsamości nie może mieć wartości domyślnej. | Nie |
| additionalOptions | Dodatkowe opcje, które zostaną przesłane do instrukcji COPY programu SQL DW bezpośrednio w klauzuli "with" w [instrukcji Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). W razie potrzeby podaj wartość, aby dostosować ją do wymagań instrukcji COPY. | Nie |

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaCOPY",
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
                "allowCopyCommand": true, 
                "copyCommandSettings": {
                    "defaultValues": [ 
                        { 
                            "columnName": "col_string", 
                            "defaultValue": "DefaultStringValue" 
                        }
                    ],
                    "additionalOptions": { 
                        "MAXERRORS": "10000", 
                        "DATEFORMAT": "'ymd'" 
                    }
                }
            },
            "enableSkipIncompatibleRow": true
        }
    }
]
```


## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="getmetadata-activity-properties"></a>Właściwości działania GetMetadata

Aby uzyskać szczegółowe informacje na temat właściwości, sprawdź [działanie GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapowanie typu danych dla usługi Azure SQL Data Warehouse

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Podczas przekształcania danych w mapowaniu przepływu danych można czytać i zapisywać w tabelach z usługi Azure Synapse Analytics. Aby uzyskać więcej informacji, zobacz [przekształcenie źródłowe](data-flow-source.md) i [przekształcanie ujścia](data-flow-sink.md) w mapowaniu przepływów danych.

### <a name="source-transformation"></a>Transformacja źródła

Ustawienia specyficzne dla usługi Azure Synapse Analytics są dostępne na karcie **Opcje źródła** transformacji źródłowej. 

**Dane wejściowe:** Wybierz, czy chcesz wskazać źródło w tabeli (równoważnej ```Select * from <table-name>```), czy wprowadzić niestandardowe zapytanie SQL.

**Zapytanie**: w przypadku wybrania zapytania w polu wejściowym wprowadź zapytanie SQL dla źródła. To ustawienie przesłania każdą tabelę, która została wybrana w zestawie danych. Klauzule **order by** nie są obsługiwane w tym miejscu, ale można ustawić pełną instrukcję SELECT FROM. Można również użyć funkcji tabeli zdefiniowanej przez użytkownika. **SELECT * FROM udfGetData ()** to format UDF w języku SQL, który zwraca tabelę. To zapytanie spowoduje utworzenie tabeli źródłowej, której można użyć w przepływie danych. Używanie zapytań jest również doskonałym sposobem zredukowania liczby wierszy do testowania lub wyszukiwania. 

* Przykład SQL: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Rozmiar wsadu**: wprowadź rozmiar partii, aby podzielić duże ilości danych na odczyt.

**Poziom izolacji**: wartość domyślna dla źródeł SQL w mapowaniu przepływu danych jest odczytana. Poziom izolacji można zmienić tutaj na jedną z następujących wartości:
* Odczytaj zatwierdzone
* Odczytaj niezatwierdzone
* Odczyt powtarzalny
* Serializable
* Brak (Ignoruj poziom izolacji)

![Poziom izolacji](media/data-flow/isolationlevel.png "Poziom izolacji")

### <a name="sink-transformation"></a>Przekształcanie ujścia

Ustawienia specyficzne dla usługi Azure Synapse Analytics są dostępne na karcie **Ustawienia** transformacji ujścia.

**Metoda aktualizacji:** Określa, jakie operacje są dozwolone w miejscu docelowym bazy danych. Domyślnie zezwala na operacje wstawiania. Aby zaktualizować, upsert lub usunąć wiersze, przekształcenie ALTER Row jest wymagane do tagowania wierszy dla tych działań. W przypadku aktualizacji, upserts i usunięć należy ustawić kolumnę klucza lub kolumny, aby określić, który wiersz ma być zmieniany.

**Akcja tabeli:** Określa, czy należy ponownie utworzyć lub usunąć wszystkie wiersze z tabeli docelowej przed zapisem.
* Brak: w tabeli nie zostanie wykonana żadna akcja.
* Utwórz ponownie: tabela zostanie porzucona i utworzona ponownie. Wymagane w przypadku dynamicznego tworzenia nowej tabeli.
* Obcinanie: wszystkie wiersze z tabeli docelowej zostaną usunięte.

**Włącz przemieszczanie:** Określa, czy podczas zapisywania do usługi Azure Synapse Analytics ma być używana [baza Base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) .

**Rozmiar wsadu**: określa, ile wierszy jest zapisywanych w każdym przedziale. Większe rozmiary partii zwiększają optymalizację kompresji i pamięci, ale grozi wyjątkami dotyczącymi pamięci podczas buforowania danych.

**Wstępne i gotowe skrypty SQL**: wprowadź wielowierszowe skrypty SQL, które zostaną wykonane przed (przetwarzanie wstępne) i po wykonaniu (po przetworzeniu) dane są zapisywane w bazie danych ujścia

![wstępne i końcowe skrypty przetwarzania SQL](media/data-flow/prepost1.png "Skrypty przetwarzania SQL")

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Mapowanie typu danych dla usługi Azure Synapse Analytics

W przypadku kopiowania danych z programu lub do usługi Azure Synapse Analytics następujące mapowania są używane z typów danych usługi Azure Synapse Analytics do Azure Data Factory danych pośrednich. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby dowiedzieć się, jak działania kopiowania mapuje typ schematu i danych źródła do ujścia.

>[!TIP]
>Zapoznaj się z tematem [typy danych tabeli w artykule Analiza usługi Azure Synapse](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md) na temat typów danych obsługiwanych przez usługę SQL DW i obejścia dla nieobsługiwanych.

| Typ danych usługi Azure Synapse Analytics    | Typ danych tymczasowych fabryki danych |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binary                                | Byte[]                         |
| bit                                   | Wartość logiczna                        |
| char                                  | String, Char[]                 |
| date                                  | Data i godzina                       |
| Datetime                              | Data i godzina                       |
| datetime2                             | Data i godzina                       |
| Datetimeoffset                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| FILESTREAM attribute (varbinary(max)) | Byte[]                         |
| float                                 | Double                         |
| image                                 | Byte[]                         |
| int                                   | Int32                          |
| money                                 | Decimal                        |
| nchar                                 | String, Char[]                 |
| numeric                               | Decimal                        |
| nvarchar                              | String, Char[]                 |
| real                                  | Pojedyncze                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | Data i godzina                       |
| smallint                              | Int16                          |
| smallmoney                            | Decimal                        |
| time                                  | TimeSpan                       |
| tinyint                               | Bajtów                           |
| uniqueidentifier                      | Identyfikator GUID                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych i formatów](copy-activity-overview.md##supported-data-stores-and-formats).
