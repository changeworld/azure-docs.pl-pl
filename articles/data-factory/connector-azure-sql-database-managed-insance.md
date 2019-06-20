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
ms.openlocfilehash: 9208ceeb760bba97c12b23a1b6e5bdff7efc9020
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274816"
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
>Wystąpienie usługi Azure SQL Database Managed **[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current)** nie jest obsługiwany przez ten łącznik na teraz. Aby obejść, można użyć [ogólnego łącznika ODBC](connector-odbc.md) i sterownik SQL Server ODBC za pośrednictwem środowiskiem Integration Runtime. Postępuj zgodnie z [Niniejsze wskazówki](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) ODBC driver połączenia i pobierania ciągu konfiguracji.

>[!NOTE]
>Uwierzytelnienia tożsamości podmiotu zabezpieczeń i zarządzana usługa nie są obecnie obsługiwane przez ten łącznik, a także z planem, aby włączyć wkrótce po. Teraz, aby uniknąć tego problemu możesz wybrać, czy łącznik usługi Azure SQL Database i ręcznie Określ serwer wystąpienia zarządzanego.

## <a name="prerequisites"></a>Wymagania wstępne

Aby dostęp do wystąpienia zarządzanego Azure SQL Database  **[publiczny punkt końcowy](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)** , możesz użyć usługi ADF zarządzane Azure IR Upewnij się, nie tylko włączyć publiczny punkt końcowy, ale także na ruch publiczny punkt końcowy w sieciowej grupie zabezpieczeń umożliwiają ADF może się połączyć z bazą danych, postępując zgodnie z [Niniejsze wskazówki](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Aby dostęp do wystąpienia zarządzanego Azure SQL Database **prywatnych punktów końcowych**, skonfiguruj [może być samodzielnie hostowane środowisko IR](create-self-hosted-integration-runtime.md) z dostępem do bazy danych. Alokowania własnego środowiska integration runtime w tej samej sieci wirtualnej jako wystąpienia zarządzanego upewnij się, że komputerze integration runtime jest w innej podsieci niż wystąpienie zarządzane. Aprowizujesz swoje własne środowisko integration runtime w innej sieci wirtualnej niż wystąpienie zarządzane umożliwia komunikację równorzędną sieci wirtualnej lub sieci wirtualnej w celu połączenia sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [połączyć aplikację z wystąpienia zarządzanego Azure SQL Database](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika usługi Azure SQL Database Managed Instance.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla bazy danych wystąpienia zarządzanego Azure SQL połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **SqlServer**. | Tak. |
| connectionString |Ta właściwość określa informacje parametry połączenia, które jest wymagany do połączenia z wystąpienia zarządzanego przy użyciu uwierzytelniania SQL. Aby uzyskać więcej informacji zobacz następujące przykłady. <br/>Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory. Można również wprowadzić hasło w usłudze Azure Key Vault oraz czy jest ściągnięcia uwierzytelniania SQL `password` konfiguracji poza parametry połączenia. Zobacz przykład kodu JSON pod tabelą i [Store poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md) artykułu z bardziej szczegółowymi informacjami. |Tak. |
| connectVia | To [środowiska integration runtime](concepts-integration-runtime.md) służy do łączenia się z magazynem danych. Używając środowiskiem Integration Runtime lub Azure Integration Runtime (Jeśli wystąpienie zarządzane ma publiczny punkt końcowy i umożliwić usługi ADF, aby uzyskać dostęp). Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Tak. |

**Przykład 1: Użyj uwierzytelniania SQL** domyślny port to 1433. Jeśli używasz wystąpienia zarządzanego usługi SQL z publicznym punktem końcowym, należy jawnie określić port 3342.

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

**Przykład 2: Użyj uwierzytelniania SQL za pomocą hasła w usłudze Azure Key Vault** domyślny port to 1433. Jeśli używasz wystąpienia zarządzanego usługi SQL z publicznym punktem końcowym, należy jawnie określić port 3342.

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

> [!TIP]
> Dowiedz się więcej na temat obsługiwanych zapisu zachowań, konfiguracji i najlepszym rozwiązaniem z [najlepsze praktyki dotyczące ładowania danych do wystąpienia zarządzanego Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Aby skopiować dane do wystąpienia zarządzanego Azure SQL Database, należy ustawić typ ujścia w działaniu kopiowania, aby **SqlSink**. W sekcji ujścia działania kopiowania, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type ujścia działania kopiowania musi być równa **SqlSink**. | Tak. |
| writeBatchSize |Liczba wierszy do wstawienia do tabeli SQL **na partię**.<br/>Dozwolone wartości to liczby całkowite, liczby wierszy. Domyślnie Data Factory dynamiczne określanie rozmiar partii odpowiednie, w zależności od rozmiaru wiersza.  |Nie |
| writeBatchTimeout |Ta właściwość określa czas oczekiwania na zakończenie przed upływem limitu czasu operacji wstawiania wsadowego.<br/>Dozwolone wartości to zakresu czasu. Na przykład "00: 30:00," który jest 30 minut. |Nie. |
| preCopyScript |Ta właściwość określa zapytanie SQL, działanie kopiowania do wykonania przed zapisaniem danych do wystąpienia zarządzanego. Jest wywoływana tylko raz na kopiowania Uruchom. Ta właściwość umożliwia oczyszczania załadowanych danych. |Nie. |
| sqlWriterStoredProcedureName |Ta nazwa jest procedury przechowywanej, która definiuje sposób stosowania źródła danych do tabeli docelowej. <br/>Procedura składowana jest *wywoływane na partię*. Do wykonania operacji, która jest uruchamiany tylko raz i nie ma nic wspólnego z danych źródłowych, na przykład, usunąć lub obciąć należy użyć `preCopyScript` właściwości. |Nie. |
| storedProcedureParameters |Te parametry są używane dla procedury składowanej.<br/>Dozwolone wartości to pary nazw ani wartości. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. |Nie. |
| sqlWriterTableType |Ta właściwość określa nazwę typu tabeli ma być używany w procedurze składowanej. Działanie kopiowania udostępnia dane jest przenoszony w tabeli tymczasowej w przypadku tego typu tabeli. Kod procedury składowanej można następnie scalić dane, które są kopiowane z istniejącymi danymi. |Nie. |

**Przykład 1: dołączanie danych**

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

**Przykład 2: wywoływanie procedury składowanej podczas kopiowania**

Dowiedz się więcej szczegółów z [wywołaj procedurę składowaną z ujścia SQL](#invoking-stored-procedure-for-sql-sink).

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

- **[Dołącz](#append-data)** : Moje źródło danych zawiera tylko nowe rekordy;
- **[UPSERT](#upsert-data)** : Moje źródło danych zawiera zarówno wstawienia i aktualizacje;
- **[Zastąp](#overwrite-entire-table)** : Czy chcesz ponownie załadować tabelę wymiarów całego każdorazowo;
- **[Zapis za pomocą logiki niestandardowej](#write-data-with-custom-logic)** : Potrzebne są dodatkowe przetwarzania przed ostatnim wstawiania do tabeli docelowej.

Zapoznaj się odpowiednio sekcje dotyczące sposobu konfigurowania w usłudze ADF i najlepszych rozwiązań.

### <a name="append-data"></a>Dołączanie danych

Jest to domyślne zachowanie tego łącznika ujścia wystąpienia zarządzanego Azure SQL Database, a następnie wykonaj ADF **wstawiania zbiorczego** można zapisać do tabeli wydajnie. Możesz po prostu skonfigurować źródła i ujścia odpowiednio w działaniu kopiowania.

### <a name="upsert-data"></a>Wykonywanie operacji upsert dla danych

**Opcja I** (sugerowany, szczególnie gdy korzystasz dużych ilości danych do skopiowania): **większość wydajne podejście** celu upsert jest następująca: 

- Po pierwsze, wykorzystaj [tabeli tymczasowej](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) można wykonać ładowania zbiorczego, wszystkie rekordy, za pomocą działania kopiowania. Jak operacje wobec tabel tymczasowych nie są rejestrowane, należy załadować miliony rekordów, w ciągu kilku sekund.
- Wykonaj działania procedury składowanej w usłudze ADF, aby zastosować [scalania](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) (lub INSERT/UPDATE) instrukcji i używaj tymczasowej tabeli jako źródła do wykonywania wszystkich aktualizuje lub wstawia jako jedna transakcja, skracając natężenie ruchu i rejestrowanie operacji. Po zakończeniu działania procedury składowanej Tabela tymczasowa może być obcinana jest gotowy do następnego cyklu upsert. 

Na przykład w usłudze Azure Data Factory, można utworzyć potoku za pomocą **działanie kopiowania, które** łańcuchowa przy użyciu **działania procedura składowana** w przypadku powodzenia. Pierwsza kopiuje dane z magazynu źródła do tabeli tymczasowej Załóżmy, że " **##UpsertTempTable**" jako nazwę tabeli w zestawie danych, następnie one wywołuje procedury składowanej, aby scalić źródła danych z tabeli tymczasowej do tabeli docelowej i oczyścić tabeli tymczasowej.

![UPSERT](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

W bazie danych zdefiniuj procedury składowanej, wraz z logiką scalania, podobnie do poniższego, który jest wskazywany powyższe działania procedury składowanej. Zakładając, że docelowy **marketingu** tabelę zawierającą trzy kolumny: **ProfileID**, **stanu**, i **kategorii**, i wykonaj upsert na podstawie **ProfileID** kolumny.

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

**Opcja II:** Alternatywnie możesz [wywołaj procedurę składowaną w ramach działania kopiowania](#invoking-stored-procedure-for-sql-sink), podczas Uwaga to podejście jest wykonywane dla każdego wiersza w tabeli źródłowej zamiast wykorzystaniu zbiorcze Wstaw jako podejście domyślne w działaniu kopiowania dlatego nie mieści się na dużą skalę upsert.

### <a name="overwrite-entire-table"></a>Zastąp całą tabelę

Można skonfigurować **preCopyScript** ujścia właściwości w działaniu kopiowania, w którym to przypadku dla każdego uruchomienia działania kopiowania usługi ADF wykonuje skrypt najpierw uruchom kopiowania, aby wstawić dane. Na przykład aby zastąpić całą tabelę przy użyciu najnowszych danych, można określić skrypt, aby usunąć wszystkie rekordy przed ładowania zbiorczego nowe dane ze źródła.

### <a name="write-data-with-custom-logic"></a>Zapisywanie danych za pomocą logiki niestandardowej

Podobnie, zgodnie z opisem w [danych Upsert](#upsert-data) sekcji, kiedy należy zastosować dodatkowe przetwarzania przed ostatnim wstawiania danych źródłowych do tabeli docelowej możesz) na dużą skalę, ładowanie do tabeli tymczasowej, a następnie wywołaj przechowywanego Procedura lub b) wywoływanie procedury składowanej podczas kopiowania.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Wywołaj procedurę składowaną z SQL ujścia

Po skopiowaniu danych do wystąpienia zarządzanego Azure SQL Database można również skonfigurować i wywoływać procedury składowanej określonych przez użytkownika z dodatkowych parametrów.

> [!TIP]
> Wywoływanie procedury składowanej przetwarza dane wiersz po wierszu zamiast operacji zbiorczej, która nie jest zalecane dla kopiowania na dużą skalę. Dowiedz się więcej z [najlepsze praktyki dotyczące ładowania danych do wystąpienia zarządzanego Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Można użyć procedury składowanej, podczas kopiowania wbudowane mechanizmy nie służą do celów, np. Zastosuj dodatkowe przetwarzania przed ostatnim wstawiania danych źródłowych do tabeli docelowej. Niektóre przykłady dodatkowego przetwarzania są łączenie kolumn wyszukiwania dodatkowe wartości, a jego wstawieniem do więcej niż jedną tabelą.

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
