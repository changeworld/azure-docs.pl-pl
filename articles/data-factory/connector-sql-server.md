---
title: Kopiowanie danych do/z serwerem SQL przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o sposobach przenoszenia danych z bazy danych programu SQL Server, która działa lokalnie lub w Maszynie wirtualnej platformy Azure przy użyciu usługi Azure Data Factory.
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
ms.openlocfilehash: d28f6ed1957f8f6ae7ff7eb49f8ce4cbdec62266
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147422"
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>Kopiowanie danych do i z programu SQL Server przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-sqlserver-connector.md)
> * [Bieżąca wersja](connector-sql-server.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do bazy danych programu SQL Server. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Kopiowanie danych z i do bazy danych programu SQL Server do dowolnego obsługiwanego magazynu danych ujścia lub skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do bazy danych programu SQL Server. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik programu SQL Server obsługuje:

- SQL Server w wersji 2016, 2014, 2012, 2008 R2, 2008 i 2005
- Kopiowanie danych przy użyciu **SQL** lub **Windows** uwierzytelniania.
- Jako źródła pobierania danych przy użyciu zapytania SQL lub procedury składowanej.
- Jako ujścia dołączanie danych do tabeli docelowej lub wywołanie procedury składowanej za pomocą logiki niestandardowej podczas kopiowania.

Program SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) nie jest obecnie obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć kopiowania danych z bazy danych programu SQL Server, który nie jest dostępny publicznie, musisz skonfigurować środowiskiem Integration Runtime. Zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md) artykuł, aby uzyskać szczegółowe informacje. Infrastruktura Integration Runtime zapewnia wbudowane sterownik bazy danych programu SQL Server, dlatego nie trzeba ręcznie zainstalować dowolnego sterownika podczas kopiowania danych z i do bazy danych programu SQL Server.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika bazy danych programu SQL Server.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku połączonej usługi SQL Server:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **SqlServer** | Yes |
| Parametry połączenia |Określ informacje o parametry połączenia potrzebne do łączenia z bazą danych programu SQL Server przy użyciu uwierzytelniania SQL lub uwierzytelniania Windows. Zapoznaj się z poniższych przykładów.<br/>Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory. Można również wprowadzić hasło w usłudze Azure Key Vault oraz czy jest ściągnięcia uwierzytelniania SQL `password` konfiguracji poza parametry połączenia. Zobacz przykład kodu JSON pod tabelą i [Store poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md) artykułu z bardziej szczegółowymi informacjami. |Yes |
| userName |Określ nazwę użytkownika, jeśli korzystasz z uwierzytelniania Windows. Przykład: **nazwa_domeny\\username**. |Nie |
| hasło |Określ hasło dla konta użytkownika, która została określona jako nazwy użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Nie |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Używając środowiskiem Integration Runtime lub Azure Integration Runtime (Jeśli magazyn danych jest publicznie dostępny). Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

>[!TIP]
>Jeśli osiągnięty błąd z kodem jako "UserErrorFailedToConnectToSqlServer", a wiadomości, takich jak "limit sesji dla bazy danych jest XXX i został osiągnięty.", Dodaj `Pooling=false` parametry połączenia i spróbuj ponownie.

**Przykład 1: przy użyciu uwierzytelniania SQL**

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

**Przykład 2: przy użyciu uwierzytelniania SQL za pomocą hasła w usłudze Azure Key Vault**

```json
{
    "name": "SqlServerLinkedService",
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

**Przykład 3: przy użyciu uwierzytelniania Windows**

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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych programu SQL Server.

Aby skopiować dane z i do bazy danych programu SQL Server, są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **SqlServerTable** | Yes |
| tableName |Nazwa tabeli lub widoku w wystąpieniu bazy danych programu SQL Server, które połączona usługa przywołuje. | Brak źródła tak dla ujścia |

**Przykład:**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
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

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez program SQL Server, źródła i ujścia.

### <a name="sql-server-as-source"></a>SQL Server jako źródło

Aby skopiować dane z programu SQL Server, należy ustawić typ źródłowego w działaniu kopiowania, aby **SqlSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **SqlSource** | Yes |
| sqlReaderQuery |Umożliwia odczytywanie danych niestandardowe zapytania SQL. Przykład: `select * from MyTable`. |Nie |
| sqlReaderStoredProcedureName |Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. Ostatnią instrukcję SQL musi być instrukcja SELECT w procedurze składowanej. |Nie |
| storedProcedureParameters |Parametry procedury składowanej.<br/>Dozwolone wartości to: par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. |Nie |

**Informacje, które należy zwrócić uwagę:**

- Jeśli **sqlReaderQuery** określono SqlSource uruchomieniu działania kopiowania to zapytanie względem źródła programu SQL Server, aby uzyskać dane. Alternatywnie, można określić procedury składowanej, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli jest to procedura składowana pobiera parametry).
- Jeśli nie określisz "sqlReaderQuery" lub "sqlReaderStoredProcedureName" kolumny zdefiniowane w sekcji "strukturę" zestaw danych JSON służą do utworzenia kwerendy (`select column1, column2 from mytable`) w celu uruchomienia programu SQL Server. Definicja zestawu danych nie ma "strukturę", wszystkie kolumny są wybierane z tabeli.

**Przykład: użycie zapytania SQL**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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

**Przykład: użycie procedury składowanej**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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

### <a name="sql-server-as-sink"></a>SQL Server jako ujście

Aby skopiować dane do programu SQL Server, należy ustawić typ ujścia w działaniu kopiowania, aby **SqlSink**. Następujące właściwości są obsługiwane w działaniu kopiowania **ujścia** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type ujścia działania kopiowania: **SqlSink** | Yes |
| writeBatchSize |Liczba wierszy do wstawienia do tabeli SQL **na partię**.<br/>Dozwolone wartości to: liczba całkowita (liczba wierszy). Domyślnie Data Factory dynamiczne określanie rozmiar partii odpowiednie, w zależności od rozmiaru wiersza. |Nie |
| writeBatchTimeout |Czas na ukończenie przed upływem limitu czasu operacji wstawiania wsadowego oczekiwania.<br/>Dozwolone wartości to: przedziału czasu. Przykład: "00: 30:00" (30 minut). |Nie |
| preCopyScript |Określ zapytanie SQL, działanie kopiowania do wykonania przed zapisaniem danych do programu SQL Server. Jej będzie można wywołać tylko raz na kopiowania Uruchom. Ta właściwość umożliwia czyszczenie wstępnie załadowanych danych. |Nie |
| sqlWriterStoredProcedureName |Nazwa procedury składowanej, który definiuje sposób dotyczą źródła danych do tabeli docelowej, np. czy wykonuje operację UPSERT lub przekształcenie za pomocą z własną logiką biznesową. <br/><br/>Należy pamiętać, tę procedurę składowaną będzie **wywoływane na partię**. Jeśli chcesz wykonać operację, która tylko jest uruchamiane jeden raz i nie ma nic wspólnego z źródła danych, np. Usuń/truncate, użyj `preCopyScript` właściwości. |Nie |
| storedProcedureParameters |Parametry procedury składowanej.<br/>Dozwolone wartości to: par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. |Nie |
| sqlWriterTableType |Określ nazwę typu tabeli ma być używany w procedurze składowanej. Działanie kopiowania udostępnia dane jest przenoszony w tabeli tymczasowej w przypadku tego typu tabeli. Kod procedury składowanej można następnie scalić dane, w której są kopiowane z istniejącymi danymi. |Nie |

> [!TIP]
> Podczas kopiowania danych do programu SQL Server, działanie kopiowania dołącza dane do tabeli ujścia domyślnie. Aby przeprowadzić UPSERT lub dodatkowe reguły biznesowe, użyj procedury składowanej w SqlSink. Dowiedz się więcej szczegółów z [wywoływania procedury składowanej dla SQL ujścia](#invoking-stored-procedure-for-sql-sink).

**Przykład 1: dołączanie danych**

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
                "referenceName": "<SQL Server output dataset name>",
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

**Przykład 2: wywoływanie procedury składowanej podczas kopiowania dla upsert**

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
                "referenceName": "<SQL Server output dataset name>",
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

**Tabela źródłowa:**

```sql
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```

**Tabela docelowa:**

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

Podczas kopiowania danych do bazy danych serwera SQL, procedury składowanej można skonfigurować i wywoływane z dodatkowymi parametrami określone przez użytkownika.

Procedura składowana służy podczas kopiowania wbudowane mechanizmy nie służą do celów. Jest ona zwykle używana, gdy upsert (insert i update) lub dodatkowego przetwarzania (Scalanie kolumn wyszukiwania dodatkowe wartości, wstawiania do wielu tabel, itp.) musi odbywać się przed ostatnim wstawiania danych źródłowych w tabeli docelowej.

Poniższy przykład pokazuje sposób użycia procedury składowanej w celu upsert do tabeli w bazie danych programu SQL Server. Przyjęto założenie, że dane wejściowe i obiekt sink **marketingu** każda tabela ma trzy kolumny: **ProfileID**, **stanu**, i **kategorii**. Czy upsert na podstawie **ProfileID** kolumny i zastosować je tylko dla określonej kategorii.

**Wyjściowy zestaw danych:** "tableName" powinien być tej samej nazwie parametru typu tabeli w swojej przechowywanej procedurze (patrz poniżej skrypt procedury składowanej).

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
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

W bazie danych należy zdefiniować typ tabeli o takiej samej nazwie jako sqlWriterTableType. Zauważ, że schemat tabeli powinna być taka sama jak schemat zwrócony przez dane wejściowe.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

Funkcja procedura składowana wykorzystuje [parametry Table-Valued](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-sql-server"></a>Mapowanie dla programu SQL server — typ danych

Podczas kopiowania danych z i do programu SQL Server, następujące mapowania są używane z typów danych programu SQL Server do typów danych tymczasowych usługi Azure Data Factory. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

| Typ danych programu SQL Server | Typ danych tymczasowych fabryki danych |
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
| Numeryczne |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| tekst |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| Varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> Mapowania typów danych do typu dziesiętnego przejściowym aktualnie ADF obsługuje dokładności maksymalnie 28. Jeśli masz dane z dokładnością jest większy niż 28, należy wziąć pod uwagę do przekonwertowania na ciąg w zapytaniu SQL.

## <a name="troubleshooting-connection-issues"></a>Rozwiązywanie problemów z połączeniem

1. Skonfiguruj program SQL Server do akceptowania połączeń zdalnych. Uruchom **SQL Server Management Studio**, kliknij prawym przyciskiem myszy **serwera**i kliknij przycisk **właściwości**. Wybierz **połączeń** na liście i sprawdź **Zezwalaj na połączenia zdalne z serwerem**.

    ![Włącz połączenia zdalne](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Zobacz [konfigurowania dostępu zdalnego opcji konfiguracji serwera](https://msdn.microsoft.com/library/ms191464.aspx) szczegółowy opis kroków.

2. Uruchom **Menedżera konfiguracji SQL Server**. Rozwiń **konfiguracja sieci programu SQL Server** dla tego wystąpienia, a wybierz **protokoły dla MSSQLSERVER**. Powinien zostać wyświetlony protokołów, w okienku po prawej stronie. Włącz protokół TCP/IP, klikając prawym przyciskiem myszy **TCP/IP** i klikając **Włącz**.

    ![Włącz protokół TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Zobacz [Włączanie lub wyłączanie protokołu sieciowego serwera](https://msdn.microsoft.com/library/ms191294.aspx) szczegółowe informacje i alternatywnych sposobów włączania protokołu TCP/IP.

3. W tym samym oknie, kliknij dwukrotnie **TCP/IP** można uruchomić **właściwości protokołu TCP/IP** okna.
4. Przełącz się do **adresów IP** kartę. Przewiń w dół, zobacz **IPWszystkie** sekcji. Zanotuj **TCP Port** (wartość domyślna to **1433**).
5. Tworzenie **reguły zapory Windows** na maszynie, aby zezwolić na przychodzący ruch za pośrednictwem tego portu.  
6. **Weryfikowanie połączenia**: Aby połączyć się z serwerem SQL przy użyciu w pełni kwalifikowanej nazwy, należy użyć programu SQL Server Management Studio na innej maszynie. Na przykład: `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
