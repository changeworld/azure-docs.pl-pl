---
title: Kopiowanie danych do i z programu SQL Server przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o sposobach przenoszenia danych do i z bazy danych programu SQL Server, który działa lokalnie lub w Maszynie wirtualnej platformy Azure przy użyciu usługi Azure Data Factory.
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
ms.openlocfilehash: a6767c7c8931898c44fd748dbe4299b8ed23eb9c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443290"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Kopiowanie danych do i z programu SQL Server przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Azure Data Factory, której używasz:"]
> * [Wersja 1](v1/data-factory-sqlserver-connector.md)
> * [Bieżąca wersja](connector-sql-server.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do bazy danych programu SQL Server. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z i do bazy danych programu SQL Server do dowolnego obsługiwanego magazynu danych ujścia. Ewentualnie możesz skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do bazy danych programu SQL Server. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła lub ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik programu SQL Server obsługuje:

- Wersje programu SQL Server 2016, 2014, 2012, 2008 R2, 2008 i 2005.
- Kopiowanie danych przy użyciu uwierzytelniania SQL lub Windows.
- Jako źródła pobierania danych przy użyciu zapytania SQL lub procedury składowanej.
- Jako obiekt sink dołączanie danych do tabeli docelowej lub wywołanie procedury składowanej za pomocą logiki niestandardowej podczas kopiowania.

>[!NOTE]
>Program SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) nie jest teraz obsługiwana przez ten łącznik. Aby obejść, można użyć [ogólnego łącznika ODBC](connector-odbc.md) i sterownik SQL Server ODBC. Postępuj zgodnie z [Niniejsze wskazówki](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) ODBC driver połączenia i pobierania ciągu konfiguracji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć kopiowania danych z bazy danych programu SQL Server, który nie jest dostępny publicznie, musisz skonfigurować własne środowisko integration runtime. Aby uzyskać więcej informacji, zobacz [może być samodzielnie hostowane środowisko IR](create-self-hosted-integration-runtime.md). Infrastruktura integration runtime zapewnia wbudowane sterownik bazy danych programu SQL Server. Nie ma potrzeby ręcznego zainstalowania sterownika podczas kopiowania danych z lub do bazy danych programu SQL Server.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika bazy danych programu SQL Server.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku połączonej usługi SQL Server:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **SqlServer**. | Tak |
| connectionString |Określ **connectionString** informacje potrzebne do łączenia z bazą danych programu SQL Server przy użyciu uwierzytelniania SQL lub uwierzytelniania Windows. Zapoznaj się z poniższych przykładów.<br/>Oznacz to pole jako **SecureString** można bezpiecznie przechowywać w usłudze Azure Data Factory. Możesz również umieścić hasła w usłudze Azure Key Vault. Jeśli uwierzytelnianie SQL, ściąganie `password` konfiguracji poza parametry połączenia. Aby uzyskać więcej informacji, zobacz przykład kodu JSON pod tabelą i [Store poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| userName |Określ nazwę użytkownika, jeśli używasz uwierzytelniania Windows. Na przykład **nazwa_domeny\\username**. |Nie |
| password |Określ hasło dla konta użytkownika, która została określona jako nazwy użytkownika. Oznacz to pole jako **SecureString** można bezpiecznie przechowywać w usłudze Azure Data Factory. Można też [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Nie |
| connectVia | To [środowiska integration runtime](concepts-integration-runtime.md) służy do łączenia się z magazynem danych. Jeśli magazyn danych jest publicznie dostępny, można użyć własnego środowiska integration runtime lub Azure integration runtime. Jeśli nie zostanie określony, używane jest domyślne środowisko uruchomieniowe integracji Azure. |Nie |

>[!TIP]
>Jeśli napotkasz błąd z kodem błędu "UserErrorFailedToConnectToSqlServer", a także wiadomość, takie jak "limit sesji dla bazy danych jest XXX i został osiągnięty" Dodaj `Pooling=false` parametry połączenia i spróbuj ponownie.

**Przykład 1: Użyj uwierzytelniania SQL**

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

**Przykład 2: Użyj uwierzytelniania SQL za pomocą hasła w usłudze Azure Key Vault**

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

**Przykład 3: Uwierzytelnianie Windows**

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
| type | Właściwość typu elementu dataset musi być równa **SqlServerTable**. | Tak |
| tableName |Ta właściwość jest nazwa tabeli lub widoku w wystąpieniu bazy danych programu SQL Server, które połączona usługa przywołuje. | Brak źródła tak dla ujścia |

**Przykład**

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

Aby uzyskać pełną listę właściwości, które można używać do definiowania działań i sekcje zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez program SQL Server, źródła i ujścia.

### <a name="sql-server-as-a-source"></a>SQL Server jako źródło

Aby skopiować dane z programu SQL Server, należy ustawić typ źródłowego w działaniu kopiowania, aby **SqlSource**. W sekcji Źródło działania kopiowania, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być równa **SqlSource**. | Tak |
| sqlReaderQuery |Umożliwia odczytywanie danych niestandardowe zapytania SQL. Może to być na przykład `select * from MyTable`. |Nie |
| sqlReaderStoredProcedureName |Ta właściwość jest nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. Ostatnią instrukcję SQL musi być instrukcja SELECT w procedurze składowanej. |Nie |
| storedProcedureParameters |Te parametry są przeznaczone dla procedury składowanej.<br/>Dozwolone wartości to pary nazw ani wartości. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. |Nie |

**Informacje, które należy zwrócić uwagę:**

- Jeśli **sqlReaderQuery** jest określona dla **SqlSource**, działanie kopiowania jest uruchamiane to zapytanie względem źródła programu SQL Server, aby uzyskać dane. Można również określić procedury przechowywanej, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** Jeśli procedura składowana pobiera parametry.
- Jeśli nie podasz **sqlReaderQuery** lub **sqlReaderStoredProcedureName**, kolumny zdefiniowane w sekcji "strukturę" zestaw danych JSON służą do utworzenia kwerendy. Zapytanie `select column1, column2 from mytable` jest uruchamiana dla programu SQL Server. Definicja zestawu danych nie ma "strukturę", wszystkie kolumny są wybierane z tabeli.

**Przykład: Użyj zapytania SQL**

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

**Przykład: Użyj procedury składowanej**

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

### <a name="sql-server-as-a-sink"></a>SQL Server jako ujście

> [!TIP]
> Dowiedz się więcej na temat obsługiwanych zapisu zachowań, konfiguracji i najlepsze rozwiązania z [najlepsze praktyki dotyczące ładowania danych do programu SQL Server](#best-practice-for-loading-data-into-sql-server).

Aby skopiować dane do programu SQL Server, należy ustawić typ ujścia w działaniu kopiowania, aby **SqlSink**. W sekcji ujścia działania kopiowania, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type ujścia działania kopiowania musi być równa **SqlSink**. | Yes |
| writeBatchSize |Liczba wierszy do wstawienia do tabeli SQL *na partię*.<br/>Dozwolone wartości to liczby całkowite, liczby wierszy. Domyślnie usługi Azure Data Factory dynamicznie Określa rozmiar partii odpowiednie, w zależności od rozmiaru wiersza. |Nie |
| writeBatchTimeout |Ta właściwość określa czas oczekiwania na zakończenie przed upływem limitu czasu operacji wstawiania wsadowego.<br/>Dozwolone wartości to dla przedziału czasu. Na przykład "00: 30:00" w ciągu 30 minut. |Nie |
| preCopyScript |Ta właściwość określa zapytanie SQL, działanie kopiowania do uruchomienia przed zapisaniem danych do programu SQL Server. Jest wywoływana tylko raz na kopiowania Uruchom. Ta właściwość służy do czyszczenia załadowanych danych. |Nie |
| sqlWriterStoredProcedureName |Ta nazwa jest procedury przechowywanej, która definiuje sposób stosowania źródła danych do tabeli docelowej.<br/>Procedura składowana jest *wywoływane na partię*. Do wykonania operacji, która jest uruchamiany tylko raz i nie ma nic wspólnego z danych źródłowych, na przykład, usunąć lub obciąć należy użyć `preCopyScript` właściwości. |Nie |
| storedProcedureParameters |Te parametry są używane dla procedury składowanej.<br/>Dozwolone wartości to pary nazw ani wartości. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. |Nie |
| sqlWriterTableType |Ta właściwość określa nazwę typu tabeli ma być używany w procedurze składowanej. Działanie kopiowania udostępnia dane jest przenoszony w tabeli tymczasowej w przypadku tego typu tabeli. Kod procedury składowanej można następnie scalić dane, które są kopiowane z istniejącymi danymi. |Nie |

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

**Przykład 2: Wywołaj procedurę składowaną podczas kopiowania**

Dowiedz się więcej szczegółów z [wywołaj procedurę składowaną z ujścia SQL](#invoke-a-stored-procedure-from-a-sql-sink).

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

## <a name="best-practice-for-loading-data-into-sql-server"></a>Najlepsze praktyki dotyczące ładowania danych do programu SQL Server

Po skopiowaniu danych do programu SQL Server może wymagać zachowanie różnych zapisu:

- [Dołącz](#append-data): Moje źródło danych zawiera tylko nowe rekordy.
- [UPSERT](#upsert-data): Moje źródło danych zawiera zarówno wstawienia i aktualizacje.
- [Zastąp](#overwrite-the-entire-table): Czy chcesz ponownie załadować tabeli wymiarów całego każdorazowo.
- [Zapis za pomocą logiki niestandardowej](#write-data-with-custom-logic): Potrzebne są dodatkowe przetwarzania przed ostatnim wstawiania do tabeli docelowej.

Znajduje się w odpowiednich sekcjach dotyczące sposobu konfigurowania w usłudze Azure Data Factory i najlepszych rozwiązań.

### <a name="append-data"></a>Dołączanie danych

Dołączanie danych to domyślne zachowanie tego łącznika ujścia programu SQL Server. Usługa Azure Data Factory wykonuje wstawiania zbiorczego, można zapisać do tabeli wydajnie. Można skonfigurować źródła i ujścia odpowiednio w działaniu kopiowania.

### <a name="upsert-data"></a>Wykonywanie operacji upsert dla danych

**Opcja 1:** Jeśli masz dużą ilość danych, aby skopiować, zastosuj następujące podejście w celu upsert: 

- Najpierw za pomocą [tabeli tymczasowej](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) do ładowania zbiorczego, wszystkie rekordy za pomocą działania kopiowania. Ponieważ operacje względem tabele tymczasowe nie są rejestrowane, należy załadować miliony rekordów, w ciągu kilku sekund.
- Uruchom działania procedur składowanych w usłudze Azure Data Factory, aby zastosować [scalania](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) lub instrukcji INSERT/UPDATE. Tabela tymczasowa należy używać jako źródła do wykonywania wszystkich aktualizuje lub wstawia jako jedna transakcja. W ten sposób zmniejszono liczbę rund i operacje tworzenia dzienników. Na koniec działania procedury składowanej Tabela tymczasowa może być obcinana jest gotowy do następnego cyklu upsert.

Na przykład w usłudze Azure Data Factory, można utworzyć potoku za pomocą **działanie kopiowania, które** łańcuchowa przy użyciu **działania procedura składowana**. Pierwszych kopiuje dane z magazynu źródła do tabeli tymczasowej bazy danych, na przykład **##UpsertTempTable**, jako nazwę tabeli w zestawie danych. Następnie ten ostatni wywołuje procedurę przechowywaną, aby scalić źródła danych z tabeli tymczasowej do tabeli docelowej i wyczyścić tabeli tymczasowej.

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

Podczas kopiowania danych do bazy danych programu SQL Server, również można konfigurować i wywoływać procedury składowanej określonych przez użytkownika z dodatkowych parametrów.

> [!TIP]
> Wywoływanie procedury składowanej przetwarza dane wiersz po wierszu, a nie przy użyciu operacji zbiorczej nie zaleca się związanym z kopiowaniem na dużą skalę. Dowiedz się więcej z [najlepsze praktyki dotyczące ładowania danych do programu SQL Server](#best-practice-for-loading-data-into-sql-server).

Podczas kopiowania wbudowane mechanizmy nie służą do celów, można użyć procedury składowanej. Przykładem jest, gdy chcesz zastosować dodatkowe przetwarzania przed ostatnim wstawiania danych źródłowych do tabeli docelowej. Niektóre przykłady dodatkowego przetwarzania są umożliwia Scal kolumny, wyszukać dodatkowe wartości i wstawić dane do więcej niż jedną tabelą.

Poniższy przykład pokazuje sposób użycia procedury składowanej w celu upsert do tabeli w bazie danych programu SQL Server. Przyjęto założenie, że dane wejściowe i obiekt sink **marketingu** każda tabela ma trzy kolumny: **ProfileID**, **stanu**, i **kategorii**. Czy upsert na podstawie **ProfileID** kolumny i zastosować je tylko dla określonej kategorii.

**Wyjściowy zestaw danych:** Nazwa "tableName" jest tej samej nazwie parametru typu tabeli w swojej przechowywanej procedurze, jak pokazano w poniższym skrypcie procedura składowana:

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

## <a name="data-type-mapping-for-sql-server"></a>Mapowanie typu danych dla programu SQL Server

Podczas kopiowania danych z i do programu SQL Server, następujące mapowania są używane z typów danych programu SQL Server do typów danych tymczasowych usługi Azure Data Factory. Aby dowiedzieć się, jak działania kopiowania mapuje typ schematu i danych źródła do ujścia, zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md).

| Typ danych programu SQL Server | Typ danych tymczasowych w usłudze Azure Data Factory |
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

## <a name="troubleshoot-connection-issues"></a>Rozwiązywanie problemów z połączeniem

1. Konfigurowanie wystąpienia programu SQL Server do akceptowania połączeń zdalnych. Rozpocznij **SQL Server Management Studio**, kliknij prawym przyciskiem myszy **serwera**i wybierz **właściwości**. Wybierz **połączeń** z listy, a następnie wybierz pozycję **Zezwalaj na połączenia zdalne z tym serwerem** pole wyboru.

    ![Włącz połączenia zdalne](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Aby uzyskać szczegółowe instrukcje, zobacz [Konfigurowanie opcji konfiguracji serwera dostępu zdalnego](https://msdn.microsoft.com/library/ms191464.aspx).

2. Rozpocznij **Menedżera konfiguracji SQL Server**. Rozwiń **konfiguracja sieci programu SQL Server** dla tego wystąpienia, a wybierz **protokoły dla MSSQLSERVER**. Protokoły są wyświetlane w okienku po prawej stronie. Włącz protokół TCP/IP, klikając prawym przyciskiem myszy **TCP/IP** i wybierając polecenie **Włącz**.

    ![Włącz protokół TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Aby uzyskać więcej informacji i alternatywnych sposobów włączania protokołu TCP/IP, zobacz [Włączanie lub wyłączanie protokołu sieciowego serwera](https://msdn.microsoft.com/library/ms191294.aspx).

3. W tym samym oknie, kliknij dwukrotnie **TCP/IP** można uruchomić **właściwości protokołu TCP/IP** okna.
4. Przełącz się do **adresów IP** kartę. Przewiń w dół, zobacz **IPWszystkie** sekcji. Zapisz **TCP Port**. Wartość domyślna to **1433**.
5. Tworzenie **reguły zapory Windows** na maszynie, aby zezwolić na przychodzący ruch za pośrednictwem tego portu. 
6. **Weryfikowanie połączenia**: Aby połączyć z programem SQL Server przy użyciu w pełni kwalifikowanej nazwy, należy użyć programu SQL Server Management Studio na innej maszynie. Może to być na przykład `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
