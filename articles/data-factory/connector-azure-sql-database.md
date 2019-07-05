---
title: Kopiowanie danych do i z usługi Azure SQL Database przy użyciu usługi fabryka danych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z obsługiwanego źródłowego magazynów danych do usługi Azure SQL Database lub z bazy danych SQL do magazynów danych ujścia obsługiwane za pomocą usługi Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: jingwang
ms.openlocfilehash: 5dbd739070b1f66fe5ff04f319a3818269d0bdaa
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449603"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Kopiuj dane do / z usługi Azure SQL Database przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Azure Data Factory, której używasz:"]
> * [Wersja 1](v1/data-factory-azure-sql-connector.md)
> * [Bieżąca wersja](connector-azure-sql-database.md)

W tym artykule opisano sposób kopiowania danych do i z usługi Azure SQL Database. Aby dowiedzieć się więcej na temat usługi Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik usługi Azure SQL Database jest obsługiwane w przypadku następujących działań:

- [Działanie kopiowania, które](copy-activity-overview.md) z [obsługiwane źródło/ujście macierzy](copy-activity-overview.md) tabeli
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)

W szczególności ten łącznik usługi Azure SQL Database obsługuje te funkcje:

- Kopiowanie danych przy użyciu uwierzytelniania SQL i uwierzytelnianie tokenu aplikacji usługi Azure Active Directory (Azure AD) przy użyciu tożsamości podmiotu zabezpieczeń lub zarządzanej usługi dla zasobów platformy Azure.
- Jako źródła pobierania danych przy użyciu zapytania SQL lub procedury składowanej.
- Jako obiekt sink dołączanie danych do tabeli docelowej lub wywołanie procedury składowanej za pomocą logiki niestandardowej podczas kopiowania.

>[!NOTE]
>Usługa Azure SQL Database [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) nie jest teraz obsługiwana przez ten łącznik. Aby obejść, można użyć [ogólnego łącznika ODBC](connector-odbc.md) i sterownik SQL Server ODBC przy użyciu własnego środowiska integration runtime. Postępuj zgodnie z [Niniejsze wskazówki](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) ODBC driver połączenia i pobierania ciągu konfiguracji.

> [!IMPORTANT]
> W przypadku kopiowania danych za pomocą środowiska integration runtime w usłudze Azure Data Factory, skonfiguruj [zapory serwera SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) tak, aby usług platformy Azure mają dostęp do serwera.
> W przypadku kopiowania danych przy użyciu własnego środowiska integration runtime, należy skonfigurować zaporę serwera SQL Azure, aby umożliwić odpowiedni zakres adresów IP. Ten zakres obejmuje adres IP komputera, który jest używany do łączenia z usługą Azure SQL Database.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi Azure Data Factory określonych do łącznika usługi Azure SQL Database.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Te właściwości są obsługiwane w przypadku platformy Azure połączonej usługi SQL Database:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość musi być równa **AzureSqlDatabase**. | Yes |
| connectionString | Podaj informacje wymagane do nawiązania wystąpienia usługi Azure SQL Database dla **connectionString** właściwości. <br/>Oznacz to pole jako **SecureString** można bezpiecznie przechowywać w usłudze Azure Data Factory. Możesz również umieścić hasła lub usługi klucz jednostki w usłudze Azure Key Vault. Jeśli uwierzytelnianie SQL, ściąganie `password` konfiguracji poza parametry połączenia. Aby uzyskać więcej informacji, zobacz przykład kodu JSON pod tabelą i [Store poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Tak, jeśli używasz uwierzytelniania usługi Azure AD przy użyciu jednostki usługi |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako **SecureString** można bezpiecznie przechowywać w usłudze Azure Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Tak, jeśli używasz uwierzytelniania usługi Azure AD przy użyciu jednostki usługi |
| tenant | Określ informacji o dzierżawie, takie jak nazwa domeny lub identyfikator, pod którą znajduje się aplikacja dzierżawy. Pobierz go przez umieszczenie nad nim kursora myszy w prawym górnym rogu witryny Azure portal. | Tak, jeśli używasz uwierzytelniania usługi Azure AD przy użyciu jednostki usługi |
| connectVia | To [środowiska integration runtime](concepts-integration-runtime.md) służy do łączenia się z magazynem danych. Środowisko uruchomieniowe integracji platformy Azure lub własnego środowiska integration runtime można użyć, jeśli magazyn danych znajduje się w sieci prywatnej. Jeśli nie zostanie określony, używane jest domyślne środowisko uruchomieniowe integracji Azure. | Nie |

Różnymi typami uwierzytelniania można znaleźć w następnych sekcjach dotyczących wymagań wstępnych i przykłady kodu JSON odpowiednio:

- [Uwierzytelnianie SQL](#sql-authentication)
- [Uwierzytelnianie usługi Azure AD aplikacji tokenu: Nazwa główna usługi](#service-principal-authentication)
- [Uwierzytelnianie usługi Azure AD aplikacji tokenu: zarządzanych tożsamości dla zasobów platformy Azure](#managed-identity)

>[!TIP]
>Jeśli napotkasz błąd z kodem błędu "UserErrorFailedToConnectToSqlServer", a także wiadomość, takie jak "limit sesji dla bazy danych jest XXX i został osiągnięty" Dodaj `Pooling=false` parametry połączenia i spróbuj ponownie.

### <a name="sql-authentication"></a>Uwierzytelnianie SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Przykład połączonej usługi, który używa uwierzytelniania SQL

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

**Hasło w usłudze Azure Key Vault** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

1. [Tworzenie aplikacji usługi Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) w witrynie Azure portal. Zanotuj nazwę aplikacji i następujące wartości, które definiują połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. [Aprowizowanie administrator usługi Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) dla serwera SQL Azure w witrynie Azure portal, jeśli jeszcze tego nie zrobiłeś. Administrator usługi Azure AD, musisz być użytkownika usługi Azure AD lub grupy usługi Azure AD, ale nie może być nazwy głównej usługi. W tym kroku odbywa się tak, aby w następnym kroku można użyć tożsamości usługi Azure AD, aby utworzyć użytkownika zawartej bazy danych dla usługi jednostkę.

3. [Tworzenie użytkowników zawartej bazy danych](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) dla jednostki usługi. Łączenie z bazą danych z lub do której należy skopiować dane za pomocą narzędzi, takich jak SQL Server Management Studio, za pomocą tożsamości usługi Azure AD, który ma co najmniej uprawnienie ALTER ANY użytkownika. Uruchom polecenie języka T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Przyznać, że nazwy głównej usługi wymagane uprawnienia, jak zwykle dla użytkowników SQL lub inne osoby. Uruchom poniższy kod. Aby uzyskać więcej opcji, zobacz [w tym dokumencie](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. Konfigurowanie platformy Azure połączonej usługi SQL Database w usłudze Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Przykład połączonej usługi, który używa uwierzytelniania jednostki usługi

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

### <a name="managed-identity"></a> Zarządzanych tożsamości do uwierzytelniania zasobów platformy Azure

Fabryka danych może być skojarzony z [tożsamości zarządzanej dla zasobów platformy Azure](data-factory-service-identity.md) reprezentujący fabryki określonych danych. Ta tożsamość zarządzaną służy do uwierzytelniania usługi Azure SQL Database. Fabryka wyznaczonym mogą uzyskiwać dostęp do i kopiowanie danych z lub z bazą danych przy użyciu tej tożsamości.

Aby użyć uwierzytelniania tożsamości zarządzanej, wykonaj następujące kroki.

1. [Aprowizowanie administrator usługi Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) dla serwera SQL Azure w witrynie Azure portal, jeśli jeszcze tego nie zrobiłeś. Administrator usługi Azure AD może być użytkownikiem usługi Azure AD lub grupy usługi Azure AD. Przyznanie grupie z tożsamości zarządzanej rolę administratora, pomiń kroki 3 i 4. Administrator ma pełny dostęp do bazy danych.

2. [Tworzenie użytkowników zawartej bazy danych](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) tożsamości zarządzanej przez usługę Azure Data Factory. Łączenie z bazą danych z lub do której należy skopiować dane za pomocą narzędzi, takich jak SQL Server Management Studio, za pomocą tożsamości usługi Azure AD, który ma co najmniej uprawnienie ALTER ANY użytkownika. Uruchom polecenie języka T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Przyznać fabryki danych tożsamości zarządzanej wymagane uprawnienia, jak zwykle wobec użytkowników SQL i innym osobom. Uruchom poniższy kod. Aby uzyskać więcej opcji, zobacz [w tym dokumencie](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. Konfigurowanie platformy Azure połączonej usługi SQL Database w usłudze Azure Data Factory.

**Przykład**

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

Aby uzyskać pełną listę właściwości, które można zdefiniować zestawy danych i sekcje zobacz [zestawów danych](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services). Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych usługi Azure SQL Database.

Aby skopiować dane z lub do usługi Azure SQL Database, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwości zestawu danych musi być równa **AzureSqlTable**. | Yes |
| tableName | Nazwa tabeli lub widoku w wystąpieniu usługi Azure SQL Database, która połączona usługa przywołuje. | Brak źródła tak dla ujścia |

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
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md). Ta sekcja zawiera listę właściwości obsługiwanych przez usługi Azure SQL Database źródła i ujścia.

### <a name="azure-sql-database-as-the-source"></a>Usługa Azure SQL Database jako źródło

Aby skopiować dane z usługi Azure SQL Database, należy ustawić **typu** właściwości w źródle działania kopiowania **SqlSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| — typ | **Typu** właściwość źródła działania kopiowania musi być równa **SqlSource**. | Tak |
| sqlReaderQuery | Ta właściwość używa niestandardowe zapytania SQL do odczytywania danych. Może to być na przykład `select * from MyTable`. | Nie |
| sqlReaderStoredProcedureName | Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. Ostatnią instrukcję SQL musi być instrukcja SELECT w procedurze składowanej. | Nie |
| storedProcedureParameters | Parametry procedury składowanej.<br/>Dozwolone wartości to pary nazw ani wartości. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. | Nie |

**Informacje, które należy zwrócić uwagę:**

- Jeśli **sqlReaderQuery** jest określona dla **SqlSource**, uruchomieniu działania kopiowania to zapytanie względem źródłowej bazy danych Azure SQL Database można pobrać danych. Można również określić procedury przechowywanej, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** Jeśli procedura składowana pobiera parametry.
- Jeśli nie podasz **sqlReaderQuery** lub **sqlReaderStoredProcedureName**, kolumny zdefiniowane w sekcji "strukturę" zestaw danych JSON służą do utworzenia kwerendy. Zapytanie `select column1, column2 from mytable` działa w odniesieniu do bazy danych Azure SQL Database. Definicja zestawu danych nie ma "strukturę", wszystkie kolumny są wybierane z tabeli.

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

### <a name="azure-sql-database-as-the-sink"></a>Usługa Azure SQL Database jako obiekt sink

> [!TIP]
> Dowiedz się więcej na temat obsługiwanych zapisu zachowań, konfiguracji i najlepsze rozwiązania z [najlepsze praktyki dotyczące ładowania danych do usługi Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database).

Aby skopiować dane do usługi Azure SQL Database, należy ustawić **typu** właściwość w działaniu kopiowania zlew do **SqlSink**. Następujące właściwości są obsługiwane w działaniu kopiowania **ujścia** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| — typ | **Typu** właściwość ujścia działania kopiowania musi być równa **SqlSink**. | Yes |
| writeBatchSize | Liczba wierszy do wstawienia do tabeli SQL *na partię*.<br/> Dozwolone wartości to **całkowitą** (liczba wierszy). Domyślnie usługi Azure Data Factory dynamicznie Określa rozmiar partii odpowiednie, w zależności od rozmiaru wiersza. | Nie |
| writeBatchTimeout | Czas oczekiwania dla partii wstawić na zakończenie przed upływem limitu czasu operacji.<br/> Dozwolone wartości to **timespan**. Na przykład "00: 30:00" (30 minut). | Nie |
| preCopyScript | Określ zapytanie SQL, działanie kopiowania do uruchomienia przed zapisanie danych w usłudze Azure SQL Database. Jest wywoływana tylko raz na kopiowania Uruchom. Ta właściwość służy do oczyszczania załadowanych danych. | Nie |
| sqlWriterStoredProcedureName | Nazwa procedury składowanej, który definiuje sposób stosowania źródła danych do tabeli docelowej. <br/>Procedura składowana jest *wywoływane na partię*. W przypadku operacji, które są wykonywane tylko raz i mają one nic wspólnego z danych źródłowych, na przykład, usunąć lub truncate, użyj `preCopyScript` właściwości. | Nie |
| storedProcedureParameters |Parametry procedury składowanej.<br/>Dozwolone wartości to pary nazw i wartości. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. | Nie |
| sqlWriterTableType | Określ nazwę typu tabeli ma być używany w procedurze składowanej. Działanie kopiowania udostępnia dane jest przenoszony w tabeli tymczasowej w przypadku tego typu tabeli. Kod procedury składowanej można następnie scalić dane, które są kopiowane z istniejącymi danymi. | Nie |

**Przykład 1: Dołączanie danych**

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

**Przykład 2: Wywołaj procedurę składowaną podczas kopiowania**

Dowiedz się więcej szczegółów z [wywołaj procedurę składowaną z ujścia SQL](#invoke-a-stored-procedure-from-a-sql-sink).

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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Najlepsze praktyki dotyczące ładowania danych do usługi Azure SQL Database

Podczas kopiowania danych do usługi Azure SQL Database, mogą wymagać zachowanie różnych zapisu:

- [Dołącz](#append-data): Moje źródło danych zawiera tylko nowe rekordy.
- [UPSERT](#upsert-data): Moje źródło danych zawiera zarówno wstawienia i aktualizacje.
- [Zastąp](#overwrite-the-entire-table): Czy chcesz ponownie załadować tabeli wymiarów całego każdorazowo.
- [Zapis za pomocą logiki niestandardowej](#write-data-with-custom-logic): Potrzebne są dodatkowe przetwarzania przed ostatnim wstawiania do tabeli docelowej.

Można znaleźć odpowiednich sekcjach o tym, jak skonfigurować w usłudze Azure Data Factory i najlepszych rozwiązań.

### <a name="append-data"></a>Dołączanie danych

Dołączanie danych to domyślne zachowanie tego łącznika ujścia Azure SQL Database. Usługa Azure Data Factory wykonuje wstawiania zbiorczego, można zapisać do tabeli wydajnie. Można skonfigurować źródła i ujścia odpowiednio w działaniu kopiowania.

### <a name="upsert-data"></a>Wykonywanie operacji upsert dla danych

**Opcja 1:** Jeśli masz dużą ilość danych, aby skopiować, zastosuj następujące podejście w celu upsert: 

- Najpierw za pomocą [tabeli tymczasowej w zakresie bazy danych](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) do ładowania zbiorczego, wszystkie rekordy za pomocą działania kopiowania. Ponieważ operacje względem tabele tymczasowe w zakresie bazy danych nie są rejestrowane, należy załadować miliony rekordów, w ciągu kilku sekund.
- Uruchom działania procedur składowanych w usłudze Azure Data Factory, aby zastosować [scalania](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) lub instrukcji INSERT/UPDATE. Tabela tymczasowa należy używać jako źródła do wykonywania wszystkich aktualizuje lub wstawia jako jedna transakcja. W ten sposób zmniejszono liczbę rund i operacje tworzenia dzienników. Na koniec działania procedury składowanej Tabela tymczasowa może być obcinana jest gotowy do następnego cyklu upsert.

Na przykład w usłudze Azure Data Factory, można utworzyć potoku za pomocą **działanie kopiowania, które** łańcuchowa przy użyciu **działania procedura składowana**. Poprzedniego kopiuje dane z magazynu źródła do tabeli tymczasowej bazy danych SQL Azure, na przykład **##UpsertTempTable**, jako nazwę tabeli w zestawie danych. Następnie ten ostatni wywołuje procedurę przechowywaną, aby scalić źródła danych z tabeli tymczasowej do tabeli docelowej i wyczyścić tabeli tymczasowej.

![UPSERT](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

W bazie danych zdefiniuj procedurę składowaną z logiką scalania, jak w poniższym przykładzie, który jest wskazywany z poprzedniego działania procedury składowanej. Przyjęto założenie, że miejsce docelowe jest **marketingu** tabelę zawierającą trzy kolumny: **ProfileID**, **stanu**, i **kategorii**. Czy upsert na podstawie **ProfileID** kolumny.

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**Opcja 2:** Istnieje również możliwość [wywołaj procedurę składowaną w ramach działania kopiowania](#invoke-a-stored-procedure-from-a-sql-sink). Ta metoda działa każdy wiersz w tabeli źródłowej zamiast wstawiania zbiorczego metoda domyślna w przypadku działania kopiowania nie jest odpowiednia dla upsert na dużą skalę.

### <a name="overwrite-the-entire-table"></a>Zastąp całą tabelę

Można skonfigurować **preCopyScript** właściwość ujścia działania kopiowania. W takim przypadku dla każdego działania kopiowania, które uruchamia usługi Azure Data Factory uruchamia skrypt najpierw. Następnie uruchamia kopiowania, aby wstawić dane. Na przykład aby zastąpić całą tabelę przy użyciu najnowszych danych, należy określić skrypt, aby najpierw usunąć wszystkie rekordy, zanim można zbiorczo załadować nowe dane ze źródła.

### <a name="write-data-with-custom-logic"></a>Zapisywanie danych za pomocą logiki niestandardowej

Kroki, aby zapisać dane za pomocą logiki niestandardowej są podobne do tych opisanych w [danych Upsert](#upsert-data) sekcji. Gdy konieczne jest zastosowanie dodatkowych przetwarzania przed końcowego Wstawianie danych źródłowych do tabeli docelowej na dużą skalę, możecie jedna z następujących czynności:

- Ładowanie do tabeli tymczasowej o zakresie bazy danych, a następnie wywołaj procedurę składowaną. 
- Wywołaj procedurę składowaną podczas kopiowania.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Wywołaj procedurę składowaną z SQL ujścia

Podczas kopiowania danych do usługi Azure SQL Database, również można konfigurować i wywoływać procedury składowanej określonych przez użytkownika z dodatkowych parametrów.

> [!TIP]
> Wywoływanie procedury składowanej przetwarza dane wiersz po wierszu, a nie przy użyciu operacji zbiorczej nie zaleca się związanym z kopiowaniem na dużą skalę. Dowiedz się więcej z [najlepsze praktyki dotyczące ładowania danych do usługi Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database).

Podczas kopiowania wbudowane mechanizmy nie służą do celów, można użyć procedury składowanej. Przykładem jest, gdy chcesz zastosować dodatkowe przetwarzania przed ostatnim wstawiania danych źródłowych do tabeli docelowej. Niektóre przykłady dodatkowego przetwarzania są umożliwia Scal kolumny, wyszukać dodatkowe wartości i wstawić do więcej niż jedną tabelą.

Poniższy przykład pokazuje, jak zrobić upsert do tabeli w usłudze Azure SQL Database za pomocą procedury składowanej. Przyjęto założenie, że dane wejściowe i obiekt sink **marketingu** każda tabela ma trzy kolumny: **ProfileID**, **stanu**, i **kategorii**. Czy upsert na podstawie **ProfileID** kolumny i zastosować je tylko dla określonej kategorii.

**Wyjściowy zestaw danych:** Nazwa "tableName" jest tej samej nazwie parametru typu tabeli w swojej przechowywanej procedurze, jak pokazano w poniższym skrypcie procedura składowana:

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

Zdefiniuj **SQL ujścia** sekcji w działaniu kopiowania w następujący sposób:

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

W bazie danych, zdefiniuj procedurę składowaną z taką samą nazwę jak **SqlWriterStoredProcedureName**. Go obsługuje danych wejściowych z określonego źródła i scala w tabeli wyników. Nazwa parametru typu tabeli w procedurze składowanej jest taka sama jak **tableName** zdefiniowane w zestawie danych.

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
      VALUES (source.ProfileID, source.State, source.Category);
END
```

W bazie danych, zdefiniuj typ tabeli z taką samą nazwę jak **sqlWriterTableType**. Schemat tabeli jest taka sama jak schemat zwrócony przez dane wejściowe.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL
)
```

Funkcja procedura składowana wykorzystuje [parametry z wartościami przechowywanymi w tabeli](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Dowiedz się, szczegółowe informacje z [źródła przekształcenia](data-flow-source.md) i [ujścia przekształcania](data-flow-sink.md) w mapowaniu przepływu danych.

## <a name="data-type-mapping-for-azure-sql-database"></a>Mapowanie typu danych dla usługi Azure SQL Database

Gdy dane są kopiowane z lub do usługi Azure SQL Database, następujące mapowania są używane z typów danych usługi Azure SQL Database do typów danych tymczasowych usługi Azure Data Factory. Aby dowiedzieć się, jak działania kopiowania mapuje typ schematu i danych źródła do ujścia, zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md).

| Typ danych w usłudze Azure SQL Database | Typ danych tymczasowych w usłudze Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> Dla typów danych, które mapowania typu dziesiętnego przejściowym usługi Azure Data Factory obsługuje obecnie dokładności maksymalnie 28. Jeśli masz dane z dokładnością jest większy niż 28, należy wziąć pod uwagę konwertowanie na ciąg w zapytaniu SQL.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych i formatów](copy-activity-overview.md##supported-data-stores-and-formats).
