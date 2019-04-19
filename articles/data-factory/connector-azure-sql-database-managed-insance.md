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
ms.date: 04/08/2019
ms.author: jingwang
ms.openlocfilehash: 9cb3c028c14e6c47d47eafcf6279a918c0917442
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272210"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Kopiowanie danych do i z wystąpienia zarządzanego Azure SQL Database przy użyciu usługi Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych do i z wystąpienia zarządzanego Azure SQL Database. Opiera się na [omówieniu działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z wystąpienia zarządzanego Azure SQL Database, do dowolnego obsługiwanego magazynu danych ujścia. Możesz także skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do wystąpienia zarządzanego. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła i ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik wystąpienia zarządzanego Azure SQL Database obsługuje:

- Kopiowanie danych przy użyciu uwierzytelniania SQL lub Windows.
- Jako źródła pobierania danych przy użyciu zapytania SQL lub procedury składowanej.
- Jako obiekt sink dołączanie danych do tabeli docelowej lub wywołanie procedury składowanej za pomocą logiki niestandardowej podczas kopiowania.

Program SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) nie jest obecnie obsługiwane. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć kopiowania danych z bazy danych wystąpienia zarządzanego Azure SQL, który znajduje się w sieci wirtualnej, skonfiguruj Self-Hosted integration runtime, które mogą dostęp do bazy danych. Aby uzyskać więcej informacji, zobacz [może być samodzielnie hostowane środowisko IR](create-self-hosted-integration-runtime.md).

Alokowania swoje własne środowisko integration runtime w tej samej sieci wirtualnej jako wystąpienia zarządzanego upewnij się, że komputerze integration runtime jest w innej podsieci niż wystąpienie zarządzane. Aprowizujesz swoje własne środowisko integration runtime w innej sieci wirtualnej niż wystąpienie zarządzane umożliwia komunikację równorzędną sieci wirtualnej lub sieci wirtualnej w celu połączenia sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [połączyć aplikację z wystąpienia zarządzanego Azure SQL Database](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika usługi Azure SQL Database Managed Instance.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla bazy danych wystąpienia zarządzanego Azure SQL połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **SqlServer**. | Tak. |
| connectionString |Ta właściwość określa informacje parametry połączenia, które jest wymagane do połączenia wystąpienia zarządzanego przy użyciu uwierzytelniania SQL lub uwierzytelniania Windows. Aby uzyskać więcej informacji zobacz następujące przykłady. <br/>Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory. Można również wprowadzić hasło w usłudze Azure Key Vault oraz czy jest ściągnięcia uwierzytelniania SQL `password` konfiguracji poza parametry połączenia. Zobacz przykład kodu JSON pod tabelą i [Store poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md) artykułu z bardziej szczegółowymi informacjami. |Tak. |
| userName |Ta właściwość określa nazwę użytkownika, jeśli używasz uwierzytelniania Windows. Na przykład **nazwa_domeny\\username**. |Nie. |
| password |Ta właściwość określa hasło dla konta użytkownika, która została określona jako nazwy użytkownika. Wybierz **SecureString** można bezpiecznie przechowywać informacji parametry połączenia w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Nie. |
| connectVia | To [środowiska integration runtime](concepts-integration-runtime.md) służy do łączenia się z magazynem danych. Dostarczanie własnego środowiska integration runtime w tej samej sieci wirtualnej jako wystąpienia zarządzanego. |Tak. |

>[!TIP]
>Można napotkać "UserErrorFailedToConnectToSqlServer" z komunikatem, takich jak "limit sesji dla bazy danych jest XXX i osiągnięto". kod błędu: Jeśli ten błąd występuje, Dodaj `Pooling=false` parametry połączenia i spróbuj ponownie.

**Przykład 1: Użyj uwierzytelniania SQL**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
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

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
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

**Przykład 3: Uwierzytelnianie Windows**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
            "userName": "<domain\\username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Aby skopiować dane do i z wystąpienia zarządzanego Azure SQL Database, należy ustawić właściwość typu zestawu danych na **SqlServerTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa **SqlServerTable**. | Tak. |
| tableName |Ta właściwość jest nazwa tabeli lub widoku w wystąpieniu bazy danych, które połączona usługa przywołuje. | Brak źródła. Tak w przypadku ujścia. |

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
| type | Właściwość typu źródła działania kopiowania musi być równa **SqlSource**. | Tak. |
| sqlReaderQuery |Ta właściwość używa niestandardowe zapytania SQL do odczytywania danych. Może to być na przykład `select * from MyTable`. |Nie. |
| sqlReaderStoredProcedureName |Ta właściwość jest nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. Ostatnią instrukcję SQL musi być instrukcja SELECT w procedurze składowanej. |Nie. |
| storedProcedureParameters |Te parametry są przeznaczone dla procedury składowanej.<br/>Dozwolone wartości to pary nazw ani wartości. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. |Nie. |

Pamiętaj o następujących kwestiach:

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

Aby skopiować dane do wystąpienia zarządzanego Azure SQL Database, należy ustawić typ ujścia w działaniu kopiowania, aby **SqlSink**. W sekcji ujścia działania kopiowania, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type ujścia działania kopiowania musi być równa **SqlSink**. | Tak. |
| writeBatchSize |Liczba wierszy do wstawienia do tabeli SQL **na partię**.<br/>Dozwolone wartości to liczby całkowite, liczby wierszy. |Nie (domyślne: 10,000). |
| writeBatchTimeout |Ta właściwość określa czas oczekiwania na zakończenie przed upływem limitu czasu operacji wstawiania wsadowego.<br/>Dozwolone wartości to zakresu czasu. Na przykład "00: 30:00," który jest 30 minut. |Nie. |
| preCopyScript |Ta właściwość określa zapytanie SQL, działanie kopiowania do wykonania przed zapisaniem danych do wystąpienia zarządzanego. Jest wywoływana tylko raz na kopiowania Uruchom. Ta właściwość umożliwia oczyszczania załadowanych danych. |Nie. |
| sqlWriterStoredProcedureName |Ta nazwa jest procedury przechowywanej, która definiuje sposób stosowania źródła danych do tabeli docelowej. Przykłady procedury to zrobić za pomocą z własną logiką biznesową wykonuje operację UPSERT lub przekształcenia. <br/><br/>Procedura składowana jest *wywoływane na partię*. Do wykonania operacji, która jest uruchamiany tylko raz i nie ma nic wspólnego z danych źródłowych, na przykład, usunąć lub obciąć należy użyć `preCopyScript` właściwości. |Nie. |
| storedProcedureParameters |Te parametry są używane dla procedury składowanej.<br/>Dozwolone wartości to pary nazw ani wartości. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. |Nie. |
| sqlWriterTableType |Ta właściwość określa nazwę typu tabeli ma być używany w procedurze składowanej. Działanie kopiowania udostępnia dane jest przenoszony w tabeli tymczasowej w przypadku tego typu tabeli. Kod procedury składowanej można następnie scalić dane, które są kopiowane z istniejącymi danymi. |Nie. |

> [!TIP]
> Gdy dane są kopiowane do wystąpienia zarządzanego Azure SQL Database, działanie kopiowania dołącza dane do tabeli ujścia domyślnie. Aby przeprowadzić upsert lub dodatkowe reguły biznesowe, użyj procedury składowanej w SqlSink. Aby uzyskać więcej informacji, zobacz [wywołaj procedurę składowaną z ujścia SQL](#invoke-a-stored-procedure-from-a-sql-sink).

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

**Przykład 2: Wywołaj procedurę składowaną podczas kopiowania dla upsert**

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

## <a name="identity-columns-in-the-target-database"></a>Kolumny tożsamości w docelowej bazie danych

Poniższy przykład kopiuje dane z tabeli źródłowej, za pomocą żadnej kolumny tożsamości do docelowej tabeli z kolumną tożsamości.

**Tabela źródłowa**

```sql
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```

**Tabela docelowa**

```sql
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```

Należy zauważyć, że tabela docelowa ma kolumny tożsamości.

**Definicja JSON zestawu danych źródłowych**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
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

**Definicja JSON zestawu danych docelowego**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "SqlServerTable",
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

Należy zauważyć, że tabela źródłowa i docelowa mają różne schemat. Tabela docelowa ma kolumny tożsamości. W tym scenariuszu należy określić właściwość "strukturę" w definicji zestawu danych docelowych, które nie zawiera kolumny tożsamości.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Wywołaj procedurę składowaną z SQL ujścia

Gdy dane są kopiowane do wystąpienia zarządzanego Azure SQL Database, procedurę składowaną można konfigurować i wywoływane z dodatkowymi parametrami, które określisz.

Podczas kopiowania wbudowane mechanizmy nie służą do celów, można użyć procedury składowanej. Jest ona zwykle używana, gdy upsert (aktualizacja + insert) lub dodatkowego przetwarzania musi odbywać się przed ostatnim wstawiania danych źródłowych w tabeli docelowej. Dodatkowe operacje mogą obejmować zadania, takie jak scalanie kolumn wyszukiwania dodatkowe wartości, a jego wstawieniem do wielu tabel.

Poniższy przykład pokazuje sposób użycia procedury składowanej w celu upsert do tabeli w bazie danych programu SQL Server. Przyjęto założenie, że dane wejściowe i obiekt sink **marketingu** każda tabela ma trzy kolumny: **ProfileID**, **stanu**, i **kategorii**. Czy upsert na podstawie **ProfileID** kolumny i zastosować je tylko dla określonej kategorii.

**Wyjściowy zestaw danych:** "tableName" powinien być tej samej nazwie parametru typu tabeli w swojej przechowywanej procedurze (patrz poniżej skrypt procedury składowanej).

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

Zdefiniuj **SQL ujścia** sekcji w działaniu kopiowania w następujący sposób.

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

W bazie danych, zdefiniuj procedurę składowaną z taką samą nazwę jak **SqlWriterStoredProcedureName**. Go obsługuje danych wejściowych z określonego źródła i scala w tabeli wyników. Nazwa parametru typu tabeli w procedurze składowanej powinna być taka sama jak **tableName** zdefiniowane w zestawie danych.

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

W bazie danych należy zdefiniować typ tabeli o takiej samej nazwie jako sqlWriterTableType. Schemat tabeli jest taka sama jak schemat zwrócony przez dane wejściowe.

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
