---
title: Kopiowanie danych do i z wystąpienia zarządzanego Azure SQL Database przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przenieść dane do i z wystąpienia zarządzanego Azure SQL Database przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: jingwang
ms.openlocfilehash: 3e1978c761c365125ac94a1ecbef5f9ac7375eba
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67338614"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Kopiowanie danych do i z wystąpienia zarządzanego Azure SQL Database przy użyciu usługi Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych do i z wystąpienia zarządzanego Azure SQL Database. Opiera się na [omówieniu działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z wystąpienia zarządzanego Azure SQL Database, do dowolnego obsługiwanego magazynu danych ujścia. Możesz także skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do wystąpienia zarządzanego. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła i ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik wystąpienia zarządzanego Azure SQL Database obsługuje:

- Kopiowanie danych przy użyciu uwierzytelniania SQL.
- Jako źródła pobierania danych przy użyciu zapytania SQL lub procedury składowanej.
- Jako obiekt sink dołączanie danych do tabeli docelowej lub wywołanie procedury składowanej za pomocą logiki niestandardowej podczas kopiowania.

>[!NOTE]
>Wystąpienie usługi Azure SQL Database Managed [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) nie jest teraz obsługiwana przez ten łącznik. Aby obejść, można użyć [ogólnego łącznika ODBC](connector-odbc.md) i sterownik SQL Server ODBC przy użyciu własnego środowiska integration runtime. Postępuj zgodnie z [Niniejsze wskazówki](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) ODBC driver połączenia i pobierania ciągu konfiguracji.

>[!NOTE]
>Uwierzytelnienia tożsamości podmiotu zabezpieczeń i zarządzana usługa nie są obecnie obsługiwane przez ten łącznik. Aby obejść, wybierz łącznik usługi Azure SQL Database i ręcznie Określ serwer wystąpienia zarządzanego.

## <a name="prerequisites"></a>Wymagania wstępne

Azure SQL Database Managed Instance dostęp do [publicznym punktem końcowym](../sql-database/sql-database-managed-instance-public-endpoint-securely.md), możesz użyć usługi Azure Data Factory zarządzanych platformy Azure środowiska integration runtime. Upewnij się, że włączyć publiczny punkt końcowy, a także na ruch publiczny punkt końcowy w sieciowej grupie zabezpieczeń, tak aby usługi Azure Data Factory można połączyć z bazą danych. Aby uzyskać więcej informacji, zobacz [Niniejsze wskazówki](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Aby uzyskać dostęp do prywatnych punktów końcowych wystąpienia zarządzanego Azure SQL Database, skonfiguruj [może być samodzielnie hostowane środowisko IR](create-self-hosted-integration-runtime.md) z dostępem do bazy danych. Alokowania własnego środowiska integration runtime w tej samej sieci wirtualnej jako wystąpienia zarządzanego upewnij się, że komputerze integration runtime jest w innej podsieci niż wystąpienie zarządzane. Aprowizujesz swoje własne środowisko integration runtime w innej sieci wirtualnej niż wystąpienie zarządzane umożliwia komunikację równorzędną sieci wirtualnej lub sieci wirtualnej do połączenia sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [połączyć aplikację z wystąpienia zarządzanego Azure SQL Database](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi Azure Data Factory, określone do łącznika usługi Azure SQL Database Managed Instance.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla bazy danych wystąpienia zarządzanego Azure SQL połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **SqlServer**. | Tak |
| connectionString |Ta właściwość określa **connectionString** informacji wymaganych do łączenia z do wystąpienia zarządzanego przy użyciu uwierzytelniania programu SQL. Aby uzyskać więcej informacji zobacz następujące przykłady. <br/>Oznacz to pole jako **SecureString** można bezpiecznie przechowywać w usłudze Azure Data Factory. Możesz również umieścić hasła w usłudze Azure Key Vault. Jeśli uwierzytelnianie SQL, ściąganie `password` konfiguracji poza parametry połączenia. Aby uzyskać więcej informacji, zobacz przykład kodu JSON pod tabelą i [Store poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Tak |
| connectVia | To [środowiska integration runtime](concepts-integration-runtime.md) służy do łączenia się z magazynem danych. Jeśli Twoje wystąpienie zarządzane ma publiczny punkt końcowy, a także umożliwia usłudze Azure Data Factory uzyskać do niego dostęp, można użyć własnego środowiska integration runtime lub środowiska Azure integration runtime. Jeśli nie zostanie określony, używane jest domyślne środowisko uruchomieniowe integracji Azure. |Yes |

**Przykład 1: Użyj uwierzytelniania SQL**

Domyślny port to 1433. Jeśli używasz wystąpienia zarządzanego Azure SQL Database z publicznym punktem końcowym, należy jawnie określić port 3342.

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład 2: Użyj uwierzytelniania SQL za pomocą hasła w usłudze Azure Key Vault**

Domyślny port to 1433. Jeśli używasz wystąpienia zarządzanego Azure SQL Database z publicznym punktem końcowym, należy jawnie określić port 3342.

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
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

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości można używać do definiowania zestawów danych zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych wystąpienia zarządzanego Azure SQL Database.

Aby skopiować dane do i z wystąpienia zarządzanego Azure SQL Database, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa **SqlServerTable**. | Tak |
| tableName |Ta właściwość jest nazwa tabeli lub widoku w wystąpieniu bazy danych, które połączona usługa przywołuje. | Brak źródła tak dla ujścia |

**Przykład**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
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

Aby uzyskać pełną listę właściwości, które można używać do definiowania działań i sekcje zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez wystąpienia zarządzanego Azure SQL Database źródła i ujścia.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Wystąpienie usługi Azure SQL Database Managed jako źródło

Aby skopiować dane z wystąpienia zarządzanego Azure SQL Database, należy ustawić typ źródła w działaniu kopiowania, aby **SqlSource**. W sekcji Źródło działania kopiowania, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być równa **SqlSource**. | Tak |
| sqlReaderQuery |Ta właściwość używa niestandardowe zapytania SQL do odczytywania danych. Może to być na przykład `select * from MyTable`. |Nie |
| sqlReaderStoredProcedureName |Ta właściwość jest nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. Ostatnią instrukcję SQL musi być instrukcja SELECT w procedurze składowanej. |Nie |
| storedProcedureParameters |Te parametry są przeznaczone dla procedury składowanej.<br/>Dozwolone wartości to pary nazw ani wartości. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. |Nie |

**Pamiętaj o następujących kwestiach:**

- Jeśli **sqlReaderQuery** jest określona dla **SqlSource**, działanie kopiowania jest uruchamiane to zapytanie względem źródła wystąpienia zarządzanego w celu pobrania danych. Można również określić procedury przechowywanej, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** Jeśli procedura składowana pobiera parametry.
- Jeśli nie podasz **sqlReaderQuery** lub **sqlReaderStoredProcedureName** właściwość, kolumny, zdefiniowane w sekcji "strukturę" zestawu danych JSON służą do utworzenia zapytania. Zapytanie `select column1, column2 from mytable` jest uruchamiana dla wystąpienia zarządzanego. Definicja zestawu danych nie ma "strukturę", wszystkie kolumny są wybierane z tabeli.

**Przykład: Korzystać z zapytania SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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

**Przykład: Użyj procedury składowanej**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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

**Definicja procedury składowanej**

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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Wystąpienie usługi Azure SQL Database Managed jako obiekt sink

> [!TIP]
> Dowiedz się więcej na temat obsługiwanych zapisu zachowań, konfiguracji i najlepsze rozwiązania z [najlepsze praktyki dotyczące ładowania danych do wystąpienia zarządzanego Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Aby skopiować dane do wystąpienia zarządzanego Azure SQL Database, należy ustawić typ ujścia w działaniu kopiowania, aby **SqlSink**. W sekcji ujścia działania kopiowania, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type ujścia działania kopiowania musi być równa **SqlSink**. | Tak |
| writeBatchSize |Liczba wierszy do wstawienia do tabeli SQL *na partię*.<br/>Dozwolone wartości to liczby całkowite, liczby wierszy. Domyślnie usługi Azure Data Factory dynamicznie Określa rozmiar partii odpowiednie, w zależności od rozmiaru wiersza.  |Nie |
| writeBatchTimeout |Ta właściwość określa czas oczekiwania na zakończenie przed upływem limitu czasu operacji wstawiania wsadowego.<br/>Dozwolone wartości to dla przedziału czasu. Na przykład "00: 30:00," który jest 30 minut. |Nie |
| preCopyScript |Ta właściwość określa zapytanie SQL, działanie kopiowania do uruchomienia przed zapisaniem danych do wystąpienia zarządzanego. Jest wywoływana tylko raz na kopiowania Uruchom. Ta właściwość umożliwia oczyszczania załadowanych danych. |Nie |
| sqlWriterStoredProcedureName |Ta nazwa jest procedury przechowywanej, która definiuje sposób stosowania źródła danych do tabeli docelowej. <br/>Procedura składowana jest *wywoływane na partię*. Do wykonania operacji, która jest uruchamiany tylko raz i nie ma nic wspólnego z danych źródłowych, na przykład, usunąć lub obciąć należy użyć `preCopyScript` właściwości. |Nie |
| storedProcedureParameters |Te parametry są używane dla procedury składowanej.<br/>Dozwolone wartości to pary nazw ani wartości. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. |Nie |
| sqlWriterTableType |Ta właściwość określa nazwę typu tabeli ma być używany w procedurze składowanej. Działanie kopiowania udostępnia dane jest przenoszony w tabeli tymczasowej w przypadku tego typu tabeli. Kod procedury składowanej można następnie scalić dane, które są kopiowane z istniejącymi danymi. |Nie |

**Przykład 1: Dołączanie danych**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
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
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Najlepsze praktyki dotyczące ładowania danych do wystąpienia zarządzanego Azure SQL Database

Podczas kopiowania danych do wystąpienia zarządzanego Azure SQL Database, mogą wymagać zachowanie różnych zapisu:

- [Dołącz](#append-data): Moje źródło danych zawiera tylko nowe rekordy.
- [UPSERT](#upsert-data): Moje źródło danych zawiera zarówno wstawienia i aktualizacje.
- [Zastąp](#overwrite-the-entire-table): Czy chcesz ponownie załadować tabeli wymiarów całego każdorazowo.
- [Zapis za pomocą logiki niestandardowej](#write-data-with-custom-logic): Potrzebne są dodatkowe przetwarzania przed ostatnim wstawiania do tabeli docelowej. 

Znajduje się w odpowiednich sekcjach dotyczące sposobu konfigurowania w usłudze Azure Data Factory i najlepszych rozwiązań.

### <a name="append-data"></a>Dołączanie danych

Dołączanie danych to domyślne zachowanie tego łącznika ujścia Azure SQL Database Managed Instance. Usługa Azure Data Factory wykonuje wstawiania zbiorczego, można zapisać do tabeli wydajnie. Można skonfigurować źródła i ujścia odpowiednio w działaniu kopiowania.

### <a name="upsert-data"></a>Wykonywanie operacji upsert dla danych

**Opcja 1:** Jeśli masz dużą ilość danych, aby skopiować, zastosuj następujące podejście w celu upsert: 

- Najpierw za pomocą [tabeli tymczasowej](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) do ładowania zbiorczego, wszystkie rekordy za pomocą działania kopiowania. Ponieważ operacje względem tabele tymczasowe nie są rejestrowane, należy załadować miliony rekordów, w ciągu kilku sekund.
- Uruchom działania procedur składowanych w usłudze Azure Data Factory, aby zastosować [scalania](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) lub instrukcji INSERT/UPDATE. Tabela tymczasowa należy używać jako źródła do wykonywania wszystkich aktualizuje lub wstawia jako jedna transakcja. W ten sposób zmniejszono liczbę rund i operacje tworzenia dzienników. Na koniec działania procedury składowanej Tabela tymczasowa może być obcinana jest gotowy do następnego cyklu upsert.

Na przykład w usłudze Azure Data Factory, można utworzyć potoku za pomocą **działanie kopiowania, które** łańcuchowa przy użyciu **działania procedura składowana**. Pierwszych kopiuje dane z magazynu źródła do tabeli tymczasowej, na przykład **##UpsertTempTable**, jako nazwę tabeli w zestawie danych. Następnie ten ostatni wywołuje procedurę przechowywaną, aby scalić źródła danych z tabeli tymczasowej do tabeli docelowej i wyczyścić tabeli tymczasowej.

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

- Ładowanie do tabeli tymczasowej, a następnie wywołaj procedurę składowaną.
- Wywołaj procedurę składowaną podczas kopiowania.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Wywołaj procedurę składowaną z SQL ujścia

Podczas kopiowania danych do wystąpienia zarządzanego Azure SQL Database, również można konfigurować i wywoływać procedury składowanej określonych przez użytkownika z dodatkowych parametrów.

> [!TIP]
> Wywoływanie procedury składowanej przetwarza dane wiersz po wierszu, a nie przy użyciu operacji zbiorczej nie zaleca się związanym z kopiowaniem na dużą skalę. Dowiedz się więcej z [najlepsze praktyki dotyczące ładowania danych do wystąpienia zarządzanego Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Podczas kopiowania wbudowane mechanizmy nie służą do celów, można użyć procedury składowanej. Przykładem jest, gdy chcesz zastosować dodatkowe przetwarzania przed ostatnim wstawiania danych źródłowych do tabeli docelowej. Niektóre przykłady dodatkowego przetwarzania są umożliwia Scal kolumny, wyszukać dodatkowe wartości i wstawić dane do więcej niż jedną tabelą.

Poniższy przykład pokazuje sposób użycia procedury składowanej w celu upsert do tabeli w bazie danych programu SQL Server. Przyjęto założenie, że dane wejściowe i obiekt sink **marketingu** każda tabela ma trzy kolumny: **ProfileID**, **stanu**, i **kategorii**. Czy upsert na podstawie **ProfileID** kolumny i zastosować je tylko dla określonej kategorii.

**Wyjściowy zestaw danych:** Nazwa "tableName" jest tej samej nazwie parametru typu tabeli w swojej przechowywanej procedurze, jak pokazano w poniższym skrypcie procedura składowana:

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
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
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

Funkcja procedura składowana wykorzystuje [parametry z wartościami przechowywanymi w tabeli](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Mapowanie typu danych dla wystąpienia zarządzanego Azure SQL Database

Gdy dane są kopiowane do i z wystąpienia zarządzanego Azure SQL Database, następujące mapowania są używane z typów danych w wystąpieniu zarządzanym usługi Azure SQL Database do typów danych tymczasowych usługi Azure Data Factory. Aby dowiedzieć się, jak działanie kopiowania mapuje źródłowy typ schematu i danych ujścia, zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md).

| Typ danych w wystąpieniu zarządzanym bazy danych SQL platformy Azure | Typ danych tymczasowych w usłudze Azure Data Factory |
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
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> Dla typów danych, które mapowania typu dziesiętnego przejściowym usługi Azure Data Factory obsługuje obecnie dokładności maksymalnie 28. W przypadku danych, które wymagają dokładności jest większy niż 28 należy wziąć pod uwagę konwertowanie na ciąg w zapytaniu SQL.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
