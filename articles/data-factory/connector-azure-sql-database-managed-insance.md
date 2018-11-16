---
title: Kopiowanie danych do i z bazy danych wystąpienia zarządzanego Azure SQL przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o sposobach przenoszenia danych z bazy danych wystąpienia zarządzanego Azure SQL przy użyciu usługi Azure Data Factory.
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
ms.date: 11/15/2018
ms.author: jingwang
ms.openlocfilehash: 561e672436c38cd0b3e637b794662483fc630676
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706725"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-using-azure-data-factory"></a>Kopiowanie danych do i z bazy danych wystąpienia zarządzanego Azure SQL przy użyciu usługi Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do wystąpienia usługi Azure SQL Database Managed. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Kopiowanie danych z wystąpienia zarządzanego Azure SQL Database do dowolnego obsługiwanego magazynu danych ujścia lub skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do wystąpienia zarządzanego. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik wystąpienia zarządzanego Azure SQL Database obsługuje:

- Kopiowanie danych przy użyciu **SQL** lub **Windows** uwierzytelniania.
- Jako źródła pobierania danych przy użyciu zapytania SQL lub procedury składowanej.
- Jako ujścia dołączanie danych do tabeli docelowej lub wywołanie procedury składowanej za pomocą logiki niestandardowej podczas kopiowania.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć kopiowania danych z bazy danych wystąpienia zarządzanego Azure SQL, który znajduje się w sieci Wirtualnej, musisz skonfigurować własne środowisko IR w tej samej sieci Wirtualnej, do którego uzyskuje dostęp do bazy danych. Zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md) artykuł, aby uzyskać szczegółowe informacje.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do wystąpienia zarządzanego Azure SQL Database łącznika.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla bazy danych wystąpienia zarządzanego Azure SQL połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **SqlServer** | Yes |
| Parametry połączenia |Określ informacje connectionString wymagany do połączenia z wystąpieniem zarządzanym przy użyciu uwierzytelniania SQL lub uwierzytelniania Windows. Zapoznaj się z poniższego przykładu. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| userName |Określ nazwę użytkownika, jeśli korzystasz z uwierzytelniania Windows. Przykład: **nazwa_domeny\\username**. |Nie |
| hasło |Określ hasło dla konta użytkownika, która została określona jako nazwy użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Nie |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Aprowizuj środowiskiem Integration Runtime w tej samej sieci Wirtualnej jako wystąpienia zarządzanego. |Yes |

>[!TIP]
>Jeśli osiągnięty błąd z kodem jako "UserErrorFailedToConnectToSqlServer", a wiadomości, takich jak "limit sesji dla bazy danych jest XXX i został osiągnięty.", Dodaj `Pooling=false` parametry połączenia i spróbuj ponownie.

**Przykład 1: Przy użyciu uwierzytelniania SQL**

```json
{
    "name": "SqlServerLinkedService",
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

**Przykład 2: Uwierzytelnianie Windows**

```json
{
    "name": "SqlServerLinkedService",
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych w wystąpieniu zarządzanym usługi Azure SQL Database.

Aby skopiować dane z/do wystąpienia zarządzanego Azure SQL Database, należy ustawić właściwość typu zestawu danych na **SqlServerTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **SqlServerTable** | Yes |
| tableName |Nazwa tabeli lub widoku w wystąpieniu bazy danych, które połączona usługa przywołuje. | Brak źródła tak dla ujścia |

**Przykład**

```json
{
    "name": "SQLServerDataset",
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

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez wystąpienia zarządzanego Azure SQL Database źródła i ujścia.

### <a name="azure-sql-database-managed-instance-as-source"></a>Wystąpienie usługi Azure SQL Database Managed jako źródło

Aby skopiować dane z wystąpienia zarządzanego Azure SQL Database, należy ustawić typ źródła w działaniu kopiowania, aby **SqlSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **SqlSource** | Yes |
| sqlReaderQuery |Umożliwia odczytywanie danych niestandardowe zapytania SQL. Przykład: `select * from MyTable`. |Nie |
| sqlReaderStoredProcedureName |Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. Ostatnią instrukcję SQL musi być instrukcja SELECT w procedurze składowanej. |Nie |
| storedProcedureParameters |Parametry procedury składowanej.<br/>Dozwolone wartości to: par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. |Nie |

**Uwagi na**

- Jeśli **sqlReaderQuery** określono SqlSource uruchomieniu działania kopiowania to zapytanie względem źródła wystąpienia zarządzanego, aby uzyskać dane. Alternatywnie, można określić procedury składowanej, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli jest to procedura składowana pobiera parametry).
- Jeśli właściwość "sqlReaderQuery" lub "sqlReaderStoredProcedureName" nie jest określona, kolumny zdefiniowane w sekcji "strukturę" zestaw danych JSON służą do utworzenia kwerendy (`select column1, column2 from mytable`) w celu uruchomienia wystąpienia zarządzanego. Definicja zestawu danych nie ma "strukturę", wszystkie kolumny są wybierane z tabeli.

**Przykład: Użycie zapytania SQL**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
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

**Przykład: Użycie procedury składowanej**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
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

### <a name="azure-sql-database-managed-instance-as-sink"></a>Wystąpienie usługi Azure SQL Database zarządzane jako ujścia

Aby skopiować dane do wystąpienia zarządzanego Azure SQL Database, należy ustawić typ ujścia w działaniu kopiowania, aby **SqlSink**. Następujące właściwości są obsługiwane w działaniu kopiowania **ujścia** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type ujścia działania kopiowania: **SqlSink** | Yes |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu.<br/>Dozwolone wartości to: liczba całkowita (liczba wierszy). |Nie (domyślne: 10000) |
| writeBatchTimeout |Czas na ukończenie przed upływem limitu czasu operacji wstawiania wsadowego oczekiwania.<br/>Dozwolone wartości to: przedziału czasu. Przykład: "00: 30:00" (30 minut). |Nie |
| preCopyScript |Określ zapytanie SQL, działanie kopiowania w celu wykonania przed zapisanie danych w wystąpieniu zarządzanym. Jej będzie można wywołać tylko raz na kopiowania Uruchom. Ta właściwość umożliwia czyszczenie wstępnie załadowanych danych. |Nie |
| sqlWriterStoredProcedureName |Nazwa procedury składowanej, który definiuje sposób dotyczą źródła danych do tabeli docelowej, np. czy wykonuje operację UPSERT lub przekształcenie za pomocą z własną logiką biznesową. <br/><br/>Należy pamiętać, tę procedurę składowaną będzie **wywoływane na partię**. Jeśli chcesz wykonać operację, która tylko jest uruchamiane jeden raz i nie ma nic wspólnego z źródła danych, np. Usuń/truncate, użyj `preCopyScript` właściwości. |Nie |
| storedProcedureParameters |Parametry procedury składowanej.<br/>Dozwolone wartości to: par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. |Nie |
| sqlWriterTableType |Określ nazwę typu tabeli ma być używany w procedurze składowanej. Działanie kopiowania udostępnia dane jest przenoszony w tabeli tymczasowej w przypadku tego typu tabeli. Kod procedury składowanej można następnie scalić dane, w której są kopiowane z istniejącymi danymi. |Nie |

> [!TIP]
> Podczas kopiowania danych do wystąpienia zarządzanego Azure SQL Database, działanie kopiowania dołącza dane do tabeli ujścia domyślnie. Aby przeprowadzić UPSERT lub dodatkowe reguły biznesowe, użyj procedury składowanej w SqlSink. Dowiedz się więcej szczegółów z [wywoływania procedury składowanej dla SQL ujścia](#invoking-stored-procedure-for-sql-sink).

**Przykład 1: Dołączanie danych**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
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

**Przykład 2: Wywoływanie procedury składowanej podczas kopiowania dla upsert**

Dowiedz się więcej szczegółów z [wywoływania procedury składowanej dla SQL ujścia](#invoking-stored-procedure-for-sql-sink).

```json
"activities":[
    {
        "name": "CopyToSQLServer",
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

Ta sekcja zawiera przykład, który kopiuje dane z tabeli źródłowej, za pomocą żadnej kolumny tożsamości do tabeli docelowej z kolumną tożsamości.

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

Należy zauważyć, że jako tabela źródłowa i docelowa mają różne schemat (element docelowy ma dodatkową kolumnę z tożsamością). W tym scenariuszu należy określić **struktury** właściwości w definicji zestawu danych docelowego nie zawiera kolumny tożsamości.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Wywołaj procedurę składowaną z SQL ujścia

Podczas kopiowania danych do bazy danych wystąpienia zarządzanego Azure SQL, określonego przez użytkownika, które zostały zapisane procedury może skonfigurować i wywołana z dodatkowych parametrów.

Procedura składowana służy podczas kopiowania wbudowane mechanizmy nie służą do celów. Jest ona zwykle używana, gdy upsert (insert i update) lub dodatkowego przetwarzania (Scalanie kolumn wyszukiwania dodatkowe wartości, wstawiania do wielu tabel, itp.) musi odbywać się przed ostatnim wstawiania danych źródłowych w tabeli docelowej.

Poniższy przykład pokazuje, jak zrobić upsert do tabeli w wystąpieniu zarządzanym za pomocą procedury składowanej. Zakładając, że dane wejściowe, jak i w tabeli "Marketing" sink zawiera trzy kolumny: identyfikator profilu, stan i kategorii. Przeprowadź upsert, w oparciu o kolumnę "ProfileID" i mają zastosowanie tylko do określonej kategorii.

**Wyjściowy zestaw danych**

```json
{
    "name": "SQLServerDataset",
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

Zdefiniuj sekcji SqlSink w działaniu kopiowania w następujący sposób.

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

W bazie danych zdefiniuj procedurę składowaną o takiej samej nazwie jako SqlWriterStoredProcedureName. Obsługuje on danych wejściowych z określonego źródła, a merge w tabeli danych wyjściowych. Nazwa parametru typu tabeli w procedurze składowanej powinna być taka sama jak nazwa "tableName" zdefiniowany w zestawie danych.

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

W bazie danych należy zdefiniować typ tabeli o takiej samej nazwie jako sqlWriterTableType. Zauważ, że schemat tabeli powinna być taka sama jak schemat zwrócony przez dane wejściowe.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

Funkcja procedura składowana wykorzystuje [parametry Table-Valued](https://msdn.microsoft.com/library/bb675163.aspx).

>[!NOTE]
>Jeśli piszesz na typ danych pieniądze/Smallmoney przez wywoływanie procedury składowanej, mogą być zaokrąglane wartości. Określ odpowiedni typ danych w TVP jako dziesiętne zamiast pieniądze/Smallmoney, aby uniknąć. 

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Mapowanie typu danych dla wystąpienia zarządzanego Azure SQL Database

Podczas kopiowania danych z i do wystąpienia zarządzanego Azure SQL Database, następujące mapowania są używane do typów danych tymczasowych usługi Azure Data Factory z typów danych w wystąpieniu zarządzanym. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

| Typ danych w wystąpieniu zarządzanym bazy danych SQL platformy Azure | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| bigint |Int64 |
| dane binarne |Byte[] |
| Bitowe |Wartość logiczna |
| Char |Ciąg, Char] |
| date |DateTime |
| Data/godzina |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Dziesiętna |Dziesiętna |
| Atrybut FILESTREAM (varbinary(max)) |Byte[] |
| Liczba zmiennoprzecinkowa |Podwójne |
| image |Byte[] |
| int |Int32 |
| pieniędzy |Dziesiętna |
| nchar |Ciąg, Char] |
| ntext |Ciąg, Char] |
| Numeryczne |Dziesiętna |
| nvarchar |Ciąg, Char] |
| rzeczywiste |Pojedyncze |
| ROWVERSION |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Dziesiętna |
| sql_variant |Obiekt * |
| tekst |Ciąg, Char] |
| time |Przedział czasu |
| sygnatura czasowa |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Identyfikator GUID |
| varbinary |Byte[] |
| varchar |Ciąg, Char] |
| xml |Xml |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
