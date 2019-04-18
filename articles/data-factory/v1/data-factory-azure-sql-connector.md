---
title: Kopiowanie danych do i z usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z usługi Azure SQL Database przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a7789f9a3f3da46305a9d8cd7cda24019658f2ad
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811491"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Kopiowanie danych do i z usługi Azure SQL Database przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](data-factory-azure-sql-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-azure-sql-database.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [łącznika usługi Azure SQL Database w wersji 2](../connector-azure-sql-database.md).

W tym artykule wyjaśniono, jak użyć działania kopiowania w usłudze Azure Data Factory do przenoszenia danych do i z usługi Azure SQL Database. Opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przenoszenie danych za pomocą działania kopiowania.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane można kopiować **z usługi Azure SQL Database** się następujące dane są przechowywane:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Możesz skopiować dane z następujących magazynów danych **do usługi Azure SQL Database**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Obsługiwany typ uwierzytelniania
Łącznik usługi Azure SQL Database obsługuje uwierzytelnianie podstawowe.

## <a name="getting-started"></a>Wprowadzenie
Utworzysz potok z działaniem kopiowania, które przenosi dane z usługi Azure SQL Database przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **kreatora kopiowania**. Zobacz [samouczka: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Aby utworzyć potok umożliwia także następujących narzędzi: **Witryna Azure portal**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejsu API platformy .NET**i  **Interfejs API REST**. Zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy używasz narzędzi lub interfejsów API, należy wykonać poniższe kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Tworzenie **usługi data factory**. Fabryka danych może zawierać jeden lub wiele potoków.
2. Tworzenie **połączonych usług** połączyć dane wejściowe i wyjściowe przechowywane z fabryką danych. Na przykład jeśli kopiujesz dane z usługi Azure blob storage do usługi Azure SQL database, utworzysz dwie połączone usługi, aby połączyć Twoje konto usługi Azure storage i Azure SQL database z fabryką danych. Dla właściwości połączonej usługi, które są specyficzne dla usługi Azure SQL Database, zobacz [właściwości usługi połączonej](#linked-service-properties) sekcji.
3. Tworzenie **zestawów danych** do reprezentowania dane wejściowe i wyjściowe operacji kopiowania. W tym przykładzie wymienione w ostatnim kroku utworzysz zestaw danych, aby określić kontener obiektów blob oraz folder, który zawiera dane wejściowe. I utwórz inny zestaw danych, aby określić tabelę SQL w bazie danych Azure SQL, która przechowuje dane skopiowane z magazynu obiektów blob. Aby uzyskać właściwości zestawu danych, które są specyficzne dla usługi Azure Data Lake Store, zobacz [właściwości zestawu danych](#dataset-properties) sekcji.
4. Tworzenie **potoku** za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe. W przykładzie, o których wspomniano wcześniej możesz użyć BlobSource jako źródła i SqlSink jako obiekt sink dla działania kopiowania. Podobnie jeśli kopiujesz z usługi Azure SQL Database do magazynu obiektów Blob platformy Azure, użyjesz SqlSource i BlobSink w działaniu kopiowania. Właściwości działania kopiowania, które są specyficzne dla usługi Azure SQL Database, zobacz [właściwości działania kopiowania](#copy-activity-properties) sekcji. Aby uzyskać szczegółowe informacje na temat korzystania z magazynu danych jako źródła lub ujścia kliknij link w poprzedniej sekcji dla magazynu danych.

Korzystając z kreatora, definicje JSON dotyczące tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować te jednostki usługi Data Factory przy użyciu formatu JSON. Aby uzyskać przykłady przy użyciu definicji JSON dla jednostek fabryki danych, które są używane do kopiowania danych z usługi Azure SQL Database, zobacz [JSON przykłady](#json-examples-for-copying-data-to-and-from-sql-database) dalszej części tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi fabryka danych określonej do usługi Azure SQL Database:

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
Azure SQL połączone łącza do usług Azure SQL database z fabryką danych. Poniższa tabela zawiera opis dla elementów JSON, które są specyficzne dla połączonej usługi Azure SQL.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi być równa: **AzureSqlDatabase** |Yes |
| connectionString |Podaj informacje wymagane do połączenia z wystąpieniem usługi Azure SQL Database dla właściwości connectionString. Obsługiwane jest tylko uwierzytelnianie podstawowe. |Yes |

> [!IMPORTANT]
> Konfigurowanie [zapory bazy danych SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) serwera bazy danych do [Zezwalaj usługom Azure na dostęp do serwera](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Ponadto jeśli dane są kopiowane do usługi Azure SQL Database z zewnętrznej platformy Azure w tym z lokalnymi źródłami danych za pomocą bramy fabryki danych, należy skonfigurować odpowiedni zakres adresów IP dla maszyny, która wysyła dane do usługi Azure SQL Database.

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby określić zestaw danych do reprezentowania danych wejściowych lub wyjściowych w bazie danych Azure SQL, należy ustawić właściwość typu zestawu danych na: **AzureSqlTable**. Ustaw **linkedServiceName** właściwości zestawu danych do nazwy usługi Azure SQL połączoną usługę.

Aby uzyskać pełną listę sekcje & właściwości dostępne Definiowanie zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. Sekcje, takie jak struktury, dostępność i zasady zestawem danych JSON są podobne dla wszystkich typów na zestaw danych (Azure SQL, obiektów blob platformy Azure, usługa Azure table itp.).

W sekcji typeProperties różni się dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. **TypeProperties** sekcji dla zestawu danych typu **AzureSqlTable** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w wystąpieniu usługi Azure SQL Database, która połączona usługa przywołuje. |Yes |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

> [!NOTE]
> Działanie kopiowania przyjmuje tylko jeden zestaw danych wejściowych i tworzy tylko jedno wyjście.

Natomiast właściwości dostępnych w **typeProperties** różnią się w sekcji działania za pomocą poszczególnych typów działań. Działanie kopiowania ich różnią się w zależności od typów źródła i ujścia.

Jeśli dane są przenoszone z usługi Azure SQL database, należy ustawić typ źródła w działaniu kopiowania, aby **SqlSource**. Podobnie, jeśli dane są przenoszone do usługi Azure SQL database, należy ustawić typ ujścia w działaniu kopiowania, aby **SqlSink**. Ta sekcja zawiera listę właściwości obsługiwanych przez SqlSource i SqlSink.

### <a name="sqlsource"></a>SqlSource
W działaniu kopiowania, gdy źródłem jest typu **SqlSource**, następujące właściwości są dostępne w **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania SQL. Przykład: `select * from MyTable`. |Nie |
| sqlReaderStoredProcedureName |Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. Ostatnią instrukcję SQL musi być instrukcja SELECT w procedurze składowanej. |Nie |
| storedProcedureParameters |Parametry procedury składowanej. |Par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. |Nie |

Jeśli **sqlReaderQuery** określono SqlSource uruchomieniu działania kopiowania to zapytanie względem źródłowej bazy danych Azure SQL Database można pobrać danych. Alternatywnie, można określić procedury składowanej, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli jest to procedura składowana pobiera parametry).

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName kolumny zdefiniowane w sekcji strukturę danych JSON służą do tworzenia zapytania (`select column1, column2 from mytable`) w celu uruchomienia usługi Azure SQL Database. Definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.

> [!NOTE]
> Kiedy używasz **sqlReaderStoredProcedureName**, nadal należy określić wartość dla **tableName** właściwość w zestawie danych JSON. Nie ma żadnych operacji sprawdzania poprawności, mimo że wykonano zadania względem tej tabeli.
>
>

### <a name="sqlsource-example"></a>Przykład SqlSource

```JSON
"source": {
    "type": "SqlSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```

**Definicja procedury składowanej:**

```SQL
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

### <a name="sqlsink"></a>SqlSink
**SqlSink** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas na ukończenie przed upływem limitu czasu operacji wstawiania wsadowego oczekiwania. |Przedział czasu<br/><br/> Przykład: "00: 30:00" (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu. |Liczba całkowita (liczba wierszy) |Nie (domyślne: 10000) |
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania do wykonania w taki sposób, że po oczyszczeniu danych określonego wycinka. Aby uzyskać więcej informacji, zobacz [powtarzalne kopiowania](#repeatable-copy). |Instrukcja zapytania. |Nie |
| sliceIdentifierColumnName |Określ nazwę kolumny dla działania kopiowania w celu wypełnienia identyfikatorem automatycznie generowane wycinek, który jest używany do czyszczenia danych określonego wycinka czas ponownego. Aby uzyskać więcej informacji, zobacz [powtarzalne kopiowania](#repeatable-copy). |Nazwa kolumny kolumny z typem danych binary(32). |Nie |
| sqlWriterStoredProcedureName |Nazwa procedury składowanej, który definiuje sposób dotyczą źródła danych do tabeli docelowej, np. czy wykonuje operację UPSERT lub przekształcenie za pomocą z własną logiką biznesową. <br/><br/>Należy pamiętać, tę procedurę składowaną będzie **wywoływane na partię**. Jeśli chcesz wykonać operację, która tylko jest uruchamiane jeden raz i nie ma nic wspólnego z źródła danych, np. Usuń/truncate, użyj `sqlWriterCleanupScript` właściwości. |Nazwa procedury składowanej. |Nie |
| storedProcedureParameters |Parametry procedury składowanej. |Par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. |Nie |
| sqlWriterTableType |Określ nazwę typu tabeli ma być używany w procedurze składowanej. Działanie kopiowania udostępnia dane jest przenoszony w tabeli tymczasowej w przypadku tego typu tabeli. Kod procedury składowanej można następnie scalić dane, w której są kopiowane z istniejącymi danymi. |Nazwa typu tabeli. |Nie |

#### <a name="sqlsink-example"></a>Przykład SqlSink

```JSON
"sink": {
    "type": "SqlSink",
    "writeBatchSize": 1000000,
    "writeBatchTimeout": "00:05:00",
    "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
    "sqlWriterTableType": "CopyTestTableType",
    "storedProcedureParameters": {
        "identifier": { "value": "1", "type": "Int" },
        "stringData": { "value": "str1" },
        "decimalData": { "value": "1", "type": "Decimal" }
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>Przykłady JSON do kopiowania danych z bazy danych SQL
W poniższych przykładach udostępniono przykładowe definicji JSON, które umożliwiają tworzenie potoku za pomocą [witryny Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazują, jak kopiować dane do i z usługi Azure SQL Database i Azure Blob Storage. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła do dowolnego ujścia, o których wspomniano [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w usłudze Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Przykład: Kopiowanie danych z usługi Azure SQL Database do obiektów Blob platformy Azure
Taki sam definiuje następujących jednostek usługi Data Factory:

1. Połączonej usługi typu [AzureSqlDatabase](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureSqlTable](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [obiektów Blob platformy Azure](data-factory-azure-blob-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [SqlSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowy skrypt kopiuje dane szeregów czasowych (co godzinę, codziennie itp.) z tabeli w bazie danych Azure SQL do obiektu blob na godzinę. Właściwości JSON używanych w tych przykładach są opisane w sekcjach poniżej przykładów.

**Usługa Azure SQL Database połączoną usługę:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Zobacz sekcję połączona usługa SQL Azure na liście właściwości obsługiwanych przez tej połączonej usługi.

**Połączona usługa Azure Blob storage:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Zobacz [obiektów Blob platformy Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) artykułu na liście właściwości obsługiwanych przez tej połączonej usługi.


**Usługa Azure SQL wejściowy zestaw danych:**

Przykład przyjęto założenie, utworzono tabelę "MyTable" w tabeli SQL Azure i zawiera kolumnę o nazwie "timestampcolumn" w danych szeregów czasowych.

Ustawienie "external": "true" informuje o usłudze Azure Data Factory, zestaw danych jest zewnętrzne w usłudze data factory i nie jest generowany przez działanie w usłudze data factory.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

Sekcji usługi Azure SQL dataset typu właściwości listy właściwości obsługiwanych przez ten typ zestawu danych.

**Usługa Azure Blob wyjściowy zestaw danych:**

Dane są zapisywane do nowego obiektu blob, co godzinę (frequency: godzina, interwał: 1). Ścieżka folderu dla obiektu blob jest dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc, dzień i części godzin od zaplanowanej godziny rozpoczęcia.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
Zobacz [właściwości typu zestawu danych obiektów Blob platformy Azure](data-factory-azure-blob-connector.md#dataset-properties) część listy właściwości obsługiwanych przez ten typ zestawu danych.

**Działanie kopiowania w potoku za pomocą programu SQL źródła i ujścia obiektu Blob:**

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **SqlSource** i **ujścia** ustawiono typ **BlobSink**. Zapytanie SQL, określony dla **SqlReaderQuery** właściwość wybiera dane w ciągu ostatniej godziny do skopiowania.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
W tym przykładzie **sqlReaderQuery** dla SqlSource został określony. Działanie kopiowania działa to zapytanie względem źródłowej bazy danych Azure SQL Database można pobrać danych. Alternatywnie, można określić procedury składowanej, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli jest to procedura składowana pobiera parametry).

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName kolumny zdefiniowane w sekcji strukturę danych JSON są używane w celu skonstruowania zapytania w celu uruchomienia usługi Azure SQL Database. Na przykład: `select column1, column2 from mytable`. Definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.

Zobacz [źródła Sql](#sqlsource) sekcji i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) listy właściwości obsługiwanych przez SqlSource i BlobSink.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Przykład: Kopiowanie danych z obiektów Blob platformy Azure do usługi Azure SQL Database
Przykładowa aplikacja definiuje następujących jednostek usługi Data Factory:

1. Połączonej usługi typu [AzureSqlDatabase](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureSqlTable](#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i [SqlSink](#copy-activity-properties).

Kopiuje przykładowe szeregów czasowych danych (co godzinę, codziennie itp.) z blob Azure do tabeli w usłudze Azure SQL bazy danych, co godzinę. Właściwości JSON używanych w tych przykładach są opisane w sekcjach poniżej przykładów.

**Połączona usługa Azure SQL:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Zobacz sekcję połączona usługa SQL Azure na liście właściwości obsługiwanych przez tej połączonej usługi.

**Połączona usługa Azure Blob storage:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Zobacz [obiektów Blob platformy Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) artykułu na liście właściwości obsługiwanych przez tej połączonej usługi.


**Usługa Azure wejściowy zestaw danych obiektów Blob:**

Dane są pobierane z nowy obiekt blob co godzinę (frequency: godzina, interwał: 1). Folder ścieżkę i nazwę dla obiektu blob są dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc i dzień godziny rozpoczęcia, a nazwa pliku używa godzinę część czas rozpoczęcia. "external": ustawienia "true" w usłudze Data Factory informuje, że ta tabela jest zewnętrzne w usłudze data factory i nie jest generowany przez działanie w usłudze data factory.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
Zobacz [właściwości typu zestawu danych obiektów Blob platformy Azure](data-factory-azure-blob-connector.md#dataset-properties) część listy właściwości obsługiwanych przez ten typ zestawu danych.

**Usługa Azure SQL Database wyjściowy zestaw danych:**

Przykład kopiuje dane do tabeli o nazwie "MyTable" w tabeli SQL Azure. Tworzenie tabeli w usłudze Azure SQL z taką samą liczbę kolumn, zgodnie z oczekiwaniami plik obiektów Blob CSV będzie zawierał. Nowe wiersze są dodawane do tabeli, co godzinę.

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
Sekcji usługi Azure SQL dataset typu właściwości listy właściwości obsługiwanych przez ten typ zestawu danych.

**Działanie kopiowania w potoku za pomocą obiektu Blob źródła i ujścia SQL:**

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **BlobSource** i **ujścia** ustawiono typ **SqlSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
Zobacz [Sql ujścia](#sqlsink) sekcji i [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) listy właściwości obsługiwanych przez SqlSink i BlobSource.

## <a name="identity-columns-in-the-target-database"></a>Kolumny tożsamości w docelowej bazie danych
Ta sekcja zawiera przykład kopiowanie danych z tabeli źródłowej bez kolumny tożsamości do tabeli docelowej z kolumną tożsamości.

**Tabela źródłowa:**

```SQL
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Tabela docelowa:**

```SQL
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```
Należy zauważyć, że tabela docelowa ma kolumny tożsamości.

**Definicja JSON zestawu danych źródłowych**

```JSON
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**Definicja JSON zestawu danych docelowego**

```JSON
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

Należy zauważyć, że jako tabela źródłowa i docelowa mają różne schemat (element docelowy ma dodatkową kolumnę z tożsamością). W tym scenariuszu należy określić **struktury** właściwości w definicji zestawu danych docelowego nie zawiera kolumny tożsamości.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Wywołaj procedurę składowaną z SQL ujścia
Aby uzyskać przykład wywoływanie procedury składowanej z SQL ujścia w działaniu kopiowania w potoku, zobacz [wywoływanie procedury składowanej SQL ujścia w działaniu kopiowania](data-factory-invoke-stored-procedure-from-copy-activity.md) artykułu.

## <a name="type-mapping-for-azure-sql-database"></a>Mapowanie typu usługi Azure SQL Database
Jak wspomniano w [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu działanie kopiowania wykonuje automatyczne konwersje z typów źródła do ujścia typów przy użyciu następujących podejść krok 2:

1. Konwersji z typów natywnych źródła na typ architektury .NET
2. Przekonwertowanie z platformy .NET na typ ujścia natywne

Podczas przenoszenia danych do i z usługi Azure SQL Database, następujące mapowania są używane z typu SQL na typ architektury .NET i na odwrót. Mapowanie jest taka sama jak mapowanie typu danych serwera SQL dla ADO.NET.

| Typ aparatu bazy danych programu SQL Server | Typ .NET framework |
| --- | --- |
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
| sql_variant |Object * |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

## <a name="map-source-to-sink-columns"></a>Mapy źródła do ujścia kolumn
Aby uzyskać informacje dotyczące mapowania kolumn w zestaw danych źródłowych do kolumn w zestawie danych ujścia, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Powtarzalne kopiowania
Podczas kopiowania danych do bazy danych serwera SQL, działanie kopiowania dołącza dane do tabeli ujścia domyślnie. Aby zamiast tego przeprowadzić UPSERT, zobacz [Repeatable zapisu SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) artykułu.

Podczas kopiowania danych z relacyjnej bazie danych są przechowywane, Zachowaj powtarzalności należy pamiętać, aby uniknąć niezamierzonego wyników. W usłudze Azure Data Factory możesz ponownie uruchomić wycinek ręcznie. Można również skonfigurować zasady ponawiania dla zestawu danych, dzięki czemu wycinek będzie uruchamiana ponownie, gdy wystąpi błąd. Gdy wycinek będzie uruchamiana ponownie w obu przypadkach, należy się upewnić, że te same dane jest do odczytu niezależnie od tego, ile razy wycinek jest uruchamiany. Zobacz [Repeatable odczytywać źródła relacyjnego](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [wydajności działania kopiowania & przewodnika dostrajania](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego obniżenie wydajności przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory i różne sposoby, aby zoptymalizować ją.
