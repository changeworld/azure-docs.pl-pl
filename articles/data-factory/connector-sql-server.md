---
title: Kopiowanie danych do/z programu SQL Server przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu przenoszenia danych z bazy danych programu SQL Server działa lokalnie lub w maszynie Wirtualnej platformy Azure przy użyciu fabryki danych Azure.
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: cb68b00232edd79a7b9cf239c1576c88731812d6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34617685"
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>Kopiowanie danych do i z programu SQL Server przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-sqlserver-connector.md)
> * [Wersja 2 — wersja zapoznawcza](connector-sql-server.md)

W tym artykule omówiono sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z i do bazy danych programu SQL Server. Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [łącznika programu SQL Server w wersji 1](v1/data-factory-sqlserver-connector.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Kopiowanie danych z/do bazy danych programu SQL Server do żadnych obsługiwanych ujścia magazynu danych lub skopiować dane z magazynu danych w dowolnej obsługiwanej źródłowej bazy danych SQL Server. Lista magazynów danych, które są obsługiwane jako źródła/wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik programu SQL Server obsługuje:

- SQL Server w wersji 2016, 2014 2012, 2008 R2, 2008 i 2005
- Kopiowanie danych przy użyciu **SQL** lub **Windows** uwierzytelniania.
- Jako źródło pobierania danych przy użyciu zapytania SQL lub procedurę składowaną.
- Jako obiekt sink dodanie danych do tabeli docelowej lub wywoływania procedury składowanej z niestandardowej logiki podczas kopiowania.

## <a name="prerequisites"></a>Wymagania wstępne

Umożliwia kopiowanie danych z bazy danych programu SQL Server, który nie jest dostępny publicznie, należy skonfigurować środowisko uruchomieniowe Self-hosted integracji. Zobacz [środowiska uruchomieniowego integracji Self-hosted](create-self-hosted-integration-runtime.md) artykułu, aby uzyskać szczegółowe informacje. Środowiska uruchomieniowego integracji oferuje wbudowane sterownik bazy danych programu SQL Server, dlatego nie trzeba ręcznie zainstalowania sterownika podczas kopiowania danych z/do bazy danych programu SQL Server.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do łącznika bazy danych programu SQL Server.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Usługi SQL Server połączone obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **SqlServer** | Yes |
| Parametry połączenia |Określ connectionString informacje potrzebne do łączenia z bazą danych programu SQL Server przy użyciu uwierzytelniania SQL lub uwierzytelniania systemu Windows. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| userName |Określ nazwę użytkownika, jeśli używasz uwierzytelniania systemu Windows. Przykład: **domainname\\username**. |Nie |
| hasło |Określ hasło dla konta użytkownika, określone nazwy użytkownika. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Nie |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. (Jeśli w magazynie danych jest dostępny publicznie) można użyć środowiska uruchomieniowego integracji Self-hosted lub środowiska uruchomieniowego integracji Azure. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. |Nie |

**Przykład 1: uwierzytelnianie SQL**

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

**Przykład 2: przy użyciu uwierzytelniania systemu Windows**

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

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych programu SQL Server.

Aby skopiować dane z/do bazy danych programu SQL Server, ustaw właściwość Typ zestawu danych do **SqlServerTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type zestawu danych: **SqlServerTable** | Yes |
| tableName |Nazwa tabeli lub widoku w wystąpieniu bazy danych programu SQL Server, odnoszący się do połączonej usługi. | Yes |

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
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę obsługiwanych przez program SQL Server źródłowy i odbiorczy właściwości.

### <a name="sql-server-as-source"></a>SQL Server jako źródło

Aby skopiować dane z programu SQL Server, należy ustawić typ źródła w przypadku działania kopiowania do **SqlSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania: **SqlSource** | Yes |
| sqlReaderQuery |Użyj niestandardowych zapytania SQL można odczytać danych. Przykład: `select * from MyTable`. |Nie |
| sqlReaderStoredProcedureName |Nazwa procedury przechowywanej, która odczytuje dane z tabeli źródłowej. Ostatniej instrukcji SQL musi być instrukcji SELECT w procedurze składowanej. |Nie |
| storedProcedureParameters |Parametry dla procedury składowanej.<br/>Dozwolone wartości to: par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametry procedury składowanej. |Nie |

**Informacje, które należy zwrócić uwagę:**

- Jeśli **sqlReaderQuery** określono dla SqlSource, odbywa się działanie kopii tego zapytania względem źródła programu SQL Server można pobrać danych. Można również określić procedury składowanej, podając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli jest to procedura składowana pobiera parametry).
- Jeśli nie określisz "sqlReaderQuery" lub "sqlReaderStoredProcedureName" kolumn określona w sekcji "structure" zestawu danych JSON służą do utworzenia kwerendy (`select column1, column2 from mytable`) w celu uruchomienia programu SQL Server. Jeśli definicji zestawu danych nie ma "structure", wszystkie kolumny są wybierane w tabeli.
- Jeśli używasz **sqlReaderStoredProcedureName**, nadal należy określić manekina **tableName** właściwość w zestawie danych JSON.

**Przykład: przy użyciu zapytania SQL**

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

**Przykład: przy użyciu procedury składowanej**

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

### <a name="sql-server-as-sink"></a>SQL Server jako odbioru

Aby skopiować dane do programu SQL Server, należy ustawić typ ujścia w działaniu kopiowania do **SqlSink**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **zbiornika** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość typu sink działania kopiowania: **SqlSink** | Yes |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu.<br/>Dozwolone wartości to: liczba całkowita (liczba wierszy). |Nie (domyślne: 10000) |
| writeBatchTimeout |Czas na ukończenie zanim upłynie limit czasu operacji wstawiania wsadowego oczekiwania.<br/>Dozwolone wartości to: timespan. Przykład: "00: 30:00" (30 minut). |Nie |
| preCopyScript |Określ zapytanie SQL dla aktywności kopiowania do wykonania przed zapisaniem danych do programu SQL Server. Go będzie można wywołać tylko raz na kopii Uruchom. Ta właściwość służy do oczyszczania danych wstępnie załadowane. |Nie |
| sqlWriterStoredProcedureName |Nazwa procedury przechowywanej, która definiuje sposób dotyczą źródła danych do tabeli docelowej, np. czy upserts lub Przekształcanie przy użyciu logiki biznesowej. <br/><br/>Należy pamiętać, będzie tej procedury składowanej **wywoływane na partię**. Jeśli chcesz wykonać operację, która tylko jest uruchamiane jeden raz i nie ma nic do wykonania z źródło danych, np. Usuń/obcięcia należy użyć `preCopyScript` właściwości. |Nie |
| storedProcedureParameters |Parametry dla procedury składowanej.<br/>Dozwolone wartości to: par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametry procedury składowanej. |Nie |
| sqlWriterTableType |Określ nazwę typu tabeli do użycia w procedurze składowanej. Działanie kopiowania udostępnia dane jest przenoszony w tabeli tymczasowej o tym typie tabeli. Kod procedury składowanej można następnie scalić dane są kopiowane z istniejącymi danymi. |Nie |

> [!TIP]
> Podczas kopiowania danych do programu SQL Server, działanie kopiowania dołącza dane do tabeli ujścia domyślnie. Aby przeprowadzić UPSERT lub dodatkowe reguły biznesowe, użyj procedury składowanej w SqlSink. Dowiedz się więcej szczegółów z [wywoływania procedury składowanej dla obiekt Sink SQL](#invoking-stored-procedure-for-sql-sink).

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

Dowiedz się więcej szczegółów z [wywoływania procedury składowanej dla obiekt Sink SQL](#invoking-stored-procedure-for-sql-sink).

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

Ta sekcja zawiera przykład, w którym kopiuje dane z tabeli źródłowej z żadnej kolumny tożsamości do tabeli docelowej z kolumny tożsamości.

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

Zwróć uwagę, że tabela docelowa ma kolumny tożsamości.

**Źródło zestawu danych JSON definicji**

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

**Docelowy zestaw danych JSON definicji**

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

Należy zauważyć, że jako tabela źródłowa i docelowa mają różne schemat (element docelowy ma dodatkową kolumnę z tożsamością). W tym scenariuszu, należy określić **struktury** właściwości w definicji zestawu danych docelowego, który nie zawiera kolumny tożsamości.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Wywołaj procedurę składowaną z zbiornika SQL

Podczas kopiowania danych do bazy danych programu SQL Server, użytkownikiem określone procedury składowanej można skonfigurować i wywoływane z dodatkowymi parametrami.

Podczas kopiowania wbudowane mechanizmy nie służą można procedury składowanej. Zazwyczaj jest używany podczas upsert (insert i update) lub dodatkowego przetwarzania (Scalanie kolumn wyszukiwania dodatkowe wartości, wstawiania do wielu tabel, itp.) musi odbywać się przed ostatnim wstawiania źródła danych w tabeli docelowej.

Poniższy przykład przedstawia sposób użycia procedury składowanej celu upsert do tabeli w bazie danych programu SQL Server. Zakładając, że dane wejściowe, jak i w tabeli "Marketingowych" sink zawiera trzy kolumny: identyfikator profilu, stan i kategorii. Wykonaj upsert na podstawie kolumny "ProfileID" i stosowane tylko dla określonej kategorii.

**Wyjściowy zestaw danych**

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

Zdefiniuj sekcji SqlSink w przypadku działania kopiowania w następujący sposób.

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

W bazie danych należy zdefiniować procedury składowanej o takiej samej nazwie jako SqlWriterStoredProcedureName. Obsługuje on dane wejściowe z określonego źródła i merge w tabeli wyników. Zwróć uwagę, nazwa parametru procedury składowanej powinna być taka sama jak "Nazwa_tabeli" zdefiniowany w zestawie danych.

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

W bazie danych należy zdefiniować typu tabeli o takiej samej nazwie jako sqlWriterTableType. Należy zauważyć, że schemat tabeli powinna być taka sama jak schemat zwrócony przez dane wejściowe.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

Funkcja procedury składowanej korzysta z [zwracającej tabelę parametrów](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-sql-server"></a>Mapowanie dla programu SQL server typu danych

Podczas kopiowania danych z/do programu SQL Server, z typów danych programu SQL Server są używane następujące mapowania do typów danych tymczasowych fabryki danych Azure. Zobacz [schemat i dane typu mapowania](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typu źródłowego: schemat i dane sink.

| Typ danych programu SQL Server | Typ danych tymczasowych fabryki danych |
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
| tinyint |Int16 |
| Unikatowy identyfikator |Identyfikator GUID |
| varbinary |Byte[] |
| varchar |Ciąg, Char] |
| xml |Xml |

## <a name="troubleshooting-connection-issues"></a>Rozwiązywanie problemów z połączeniem

1. Skonfiguruj program SQL Server na akceptowanie połączeń zdalnych. Uruchom **programu SQL Server Management Studio**, kliknij prawym przyciskiem myszy **serwera**i kliknij przycisk **właściwości**. Wybierz **połączeń** z listy i wyboru **zezwolenie na zdalne połączenia z serwerem**.

    ![Włącz połączenia zdalne](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Zobacz [konfigurowania dostępu zdalnego opcji konfiguracji serwera](https://msdn.microsoft.com/library/ms191464.aspx) szczegółowy opis kroków.

2. Uruchom **programu SQL Server Configuration Manager**. Rozwiń węzeł **konfigurację sieci programu SQL Server** dla tego wystąpienia, a następnie wybierz **protokoły dla elementu MSSQLSERVER**. Powinny pojawić się protokołów w prawym okienku. Włącz protokół TCP/IP, klikając prawym przyciskiem myszy **TCP/IP** i klikając **włączyć**.

    ![Włącz protokół TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Zobacz [włączyć lub wyłączyć protokół sieciowy serwera](https://msdn.microsoft.com/library/ms191294.aspx) szczegółowe informacje i alternatywny sposób włączania protokołu TCP/IP.

3. W tym samym oknie, kliknij dwukrotnie **TCP/IP** można uruchomić **właściwości protokołu TCP/IP** okna.
4. Przełącz się do **adresów IP** kartę. Przewiń w dół, aby znaleźć **IPWszystkie** sekcji. Należy zanotować ** TCP Port ** (domyślnie jest **1433**).
5. Utwórz **reguły zapory systemu Windows** na maszynie, aby zezwolić na ruch przychodzący za pośrednictwem tego portu.  
6. **Sprawdź połączenie**: Aby połączyć się z serwerem SQL przy użyciu w pełni kwalifikowanej nazwy, użyj programu SQL Server Management Studio z innego komputera. Na przykład: `"<machine>.<domain>.corp.<company>.com,1433"`.


## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
