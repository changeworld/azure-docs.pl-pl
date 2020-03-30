---
title: Kopiowanie danych do/z bazy danych SQL usługi Azure
description: Dowiedz się, jak kopiować dane do/z usługi Azure SQL Database przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7fc0b2822195d952c2a4f9c02bf3758c0e2b809a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260503"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Kopiowanie danych do i z usługi Azure SQL Database przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-azure-sql-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-azure-sql-database.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [łącznik bazy danych SQL azure w wersji 2](../connector-azure-sql-database.md).

W tym artykule wyjaśniono, jak używać działania kopiowania w usłudze Azure Data Factory do przenoszenia danych do i z bazy danych SQL azure. Opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) który przedstawia ogólny przegląd przenoszenia danych z działaniem kopiowania.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane można kopiować **z usługi Azure SQL Database** do następujących magazynów danych:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Dane z następujących magazynów danych można **skopiować do bazy danych SQL Azure:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Typ obsługiwanego uwierzytelniania
Łącznik bazy danych SQL usługi Azure obsługuje uwierzytelnianie podstawowe.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, który przenosi dane do/z bazy danych SQL Azure przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) w celu szybkiego instruktażu w sprawie tworzenia potoku przy użyciu kreatora kopiowania danych.

Do utworzenia potoku można również użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon usługi Azure Resource Manager**, **.NET API**i REST **API**. Zobacz [Kopiowanie samouczka aktywności,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania.

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie **fabryki danych**. Fabryka danych może zawierać jeden lub więcej potoków.
2. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych. Na przykład w przypadku kopiowania danych z magazynu obiektów blob platformy Azure do bazy danych SQL platformy Azure utworzysz dwie połączone usługi, aby połączyć konto usługi Azure storage i bazę danych SQL platformy Azure z fabryką danych. Właściwości usługi połączone, które są specyficzne dla usługi Azure SQL Database, zobacz [sekcję właściwości usługi połączone.](#linked-service-properties)
3. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania. W przykładzie wymienionym w ostatnim kroku utworzysz zestaw danych, aby określić kontener obiektu blob i folder zawierający dane wejściowe. I utworzyć inny zestaw danych, aby określić tabelę SQL w bazie danych SQL platformy Azure, która przechowuje dane skopiowane z magazynu obiektów blob. Właściwości zestawu danych, które są specyficzne dla usługi Azure Data Lake Store, zobacz [sekcję właściwości zestawu danych.](#dataset-properties)
4. Utwórz **potok** z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. W przykładzie wspomniano wcześniej, należy użyć BlobSource jako źródło i SqlSink jako ujście dla działania kopiowania. Podobnie, jeśli kopiujesz z usługi Azure SQL Database do usługi Azure Blob Storage, używasz SqlSource i BlobSink w działania kopiowania. Aby uzyskać właściwości działania kopiowania, które są specyficzne dla usługi Azure SQL Database, zobacz [sekcję właściwości działania kopiowania.](#copy-activity-properties) Aby uzyskać szczegółowe informacje na temat używania magazynu danych jako źródła lub ujścia, kliknij łącze w poprzedniej sekcji dla magazynu danych.

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), można zdefiniować te jednostki fabryki danych przy użyciu formatu JSON. W przypadku przykładów z definicjami JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych do/z bazy danych SQL azure, zobacz [sekcję przykładów JSON](#json-examples-for-copying-data-to-and-from-sql-database) w tym artykule.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi Data Factory specyficznych dla usługi Azure SQL Database:

## <a name="linked-service-properties"></a>Połączone właściwości usługi
Usługa połączona z usługą Azure SQL łączy bazę danych SQL platformy Azure z fabryką danych. Poniższa tabela zawiera opis elementów JSON specyficznych dla usługi połączonej sql platformy Azure.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type |Właściwość typu musi być ustawiona na: **AzureSqlDatabase** |Tak |
| Parametry połączenia |Określ informacje potrzebne do nawiązania połączenia z wystąpieniem bazy danych SQL platformy Azure dla właściwości connectionString. Obsługiwane jest tylko uwierzytelnianie podstawowe. |Tak |

> [!IMPORTANT]
> Skonfiguruj [zaporę bazy danych usługi Azure SQL,](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) aby [umożliwić usługom platformy Azure dostęp do serwera](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Ponadto w przypadku kopiowania danych do usługi Azure SQL Database spoza platformy Azure, w tym z lokalnych źródeł danych z bramą fabryki danych, skonfiguruj odpowiedni zakres adresów IP dla komputera, który wysyła dane do bazy danych SQL Azure.

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby określić zestaw danych do reprezentowania danych wejściowych lub wyjściowych w bazie danych SQL platformy Azure, należy ustawić właściwość typu zestawu danych na: **AzureSqlTable**. Ustaw właściwość **linkedServiceName** zestawu danych na nazwę usługi połączonej SQL platformy Azure.

Aby uzyskać pełną listę sekcji & właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych.](data-factory-create-datasets.md) Sekcje, takie jak struktura, dostępność i zasady zestawu danych JSON są podobne dla wszystkich typów zestawów danych (Azure SQL, Azure blob, tabela platformy Azure itp.).

Sekcja typeProperties jest inna dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Sekcja **typeProperties** dla zestawu danych typu **AzureSqlTable** ma następujące właściwości:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w wystąpieniu usługi Azure SQL Database, do których odwołuje się usługa połączona. |Tak |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, tabele danych wejściowych i wyjściowych oraz zasady są dostępne dla wszystkich typów działań.

> [!NOTE]
> Działanie kopiowania zajmuje tylko jedno dane wejściowe i generuje tylko jedno dane wyjściowe.

Mając na uwadze, właściwości dostępne w sekcji **typeProperties** działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się one w zależności od typów źródeł i pochłaniacze.

Jeśli przenosisz dane z bazy danych SQL platformy Azure, typ źródła w działaniu kopiowania jest ustawiany na **SqlSource.** Podobnie, jeśli przenosisz dane do bazy danych SQL platformy Azure, typ ujścia w działaniu kopiowania jest ustawiony na **SqlSink**. Ta sekcja zawiera listę właściwości obsługiwanych przez SqlSource i SqlSink.

### <a name="sqlsource"></a>Źródło sqlsource
W działaniu kopiowania, gdy źródłem jest typ **SqlSource,** następujące właściwości są dostępne w sekcji **typeProperties:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania SQL. Przykład: `select * from MyTable`. |Nie |
| nazwa sqlReaderStoredProcedureName |Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. Ostatnia instrukcja SQL musi być instrukcją SELECT w procedurze składowanej. |Nie |
| przechowywaneParametryprocedure |Parametry procedury składowanej. |Pary nazwy/wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |

Jeśli **sqlReaderQuery** jest określony dla SqlSource, copy activity uruchamia tę kwerendę względem źródła bazy danych SQL Azure, aby uzyskać dane. Alternatywnie można określić procedurę składowaną, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli procedura składowana przyjmuje parametry).

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName, kolumny zdefiniowane w sekcji struktury zestawu danych JSON`select column1, column2 from mytable`są używane do tworzenia kwerendy ( ) do uruchamiania w bazie danych SQL Azure. Jeśli definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.

> [!NOTE]
> Korzystając z **programu sqlReaderStoredProcedureName,** nadal należy określić wartość właściwości **tableName** w zestawie danych JSON. Nie ma jednak żadnych weryfikacji wykonywane dla tej tabeli.
>
>

### <a name="sqlsource-example"></a>Przykład sqlsource

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

### <a name="sqlsink"></a>SqlSink (w języku sql)
**SqlSink** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji wstawiania partii przed jej limitem czasu. |Timespan<br/><br/> Przykład: "00:30:00" (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy rozmiar buforu osiągnie writeBatchSize. |Liczba całkowita (liczba wierszy) |Nie (domyślnie: 10000) |
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania, aby wykonać takie, że dane określonego plasterka są czyszczone. Aby uzyskać więcej informacji, zobacz [powtarzalną kopię](#repeatable-copy). |Instrukcja kwerendy. |Nie |
| sliceIdentifierColumnName |Określ nazwę kolumny działania kopiowania, aby wypełnić automatycznie wygenerowanym identyfikatorem plasterka, który jest używany do czyszczenia danych określonego plasterka podczas ponownego uruchomienia. Aby uzyskać więcej informacji, zobacz [powtarzalną kopię](#repeatable-copy). |Nazwa kolumny z typem danych binarnym(32). |Nie |
| nazwa sqlWriterStoredProcedureName |Nazwa procedury składowanej, która określa sposób stosowania danych źródłowych do tabeli docelowej, na przykład do wykonywania upserts lub przekształcania przy użyciu własnej logiki biznesowej. <br/><br/>Uwaga ta procedura **składowana będzie wywoływana na partię**. Jeśli chcesz wykonać operację, która działa tylko raz i nie ma nic wspólnego z `sqlWriterCleanupScript` danymi źródłowymi, np. |Nazwa procedury składowanej. |Nie |
| przechowywaneParametryprocedure |Parametry procedury składowanej. |Pary nazwy/wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |
| sqlWriterTableType (Typ tabeli maszyn sqlWriterTableType) |Określ nazwę typu tabeli, która ma być używana w procedurze składowanej. Działanie kopiowania sprawia, że dane są dostępne w tabeli tymczasowej z tego typu tabeli. Kod procedury składowanej można następnie scalić dane kopiowane z istniejącymi danymi. |Nazwa typu tabeli. |Nie |

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

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>Przykłady JSON do kopiowania danych do i z bazy danych SQL
Poniższe przykłady zawierają przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub programu Azure [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Pokazują one, jak skopiować dane do i z usługi Azure SQL Database i usługi Azure Blob Storage. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła do dowolnego ujścia podane [w tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w usłudze Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Przykład: Kopiowanie danych z usługi Azure SQL Database do obiektu blob platformy Azure
To samo definiuje następujące jednostki fabryki danych:

1. Połączona usługa typu [AzureSqlDatabase](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureSqlTable](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu Azure [Blob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [SqlSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowy kopiuje dane szeregów czasowych (co godzinę, codziennie itp.) z tabeli w bazie danych SQL platformy Azure do obiektu blob co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

**Usługa połączona z usługą Azure SQL Database:**

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
Zobacz sekcję Azure SQL Linked Service, aby uzyskać listę właściwości obsługiwanych przez tę usługę połączone.

**Usługa połączona z magazynem obiektów Blob platformy Azure:**

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
Zobacz artykuł [obiektu Blob platformy Azure,](data-factory-azure-blob-connector.md#azure-storage-linked-service) aby uzyskać listę właściwości obsługiwanych przez tę połączone usługi.


**Zestaw danych wejściowych SQL platformy Azure:**

W przykładzie przyjęto założenie, że utworzono tabelę "MyTable" w usłudze Azure SQL i zawiera kolumnę o nazwie "timestampcolumn" dla danych szeregów czasowych.

Ustawienie "zewnętrzne": "true" informuje usługę Azure Data Factory, że zestaw danych jest zewnętrzny dla fabryki danych i nie jest produkowany przez działanie w fabryce danych.

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

Zobacz sekcję właściwości typu zestawu danych SQL platformy Azure, aby uzyskać listę właściwości obsługiwanych przez ten typ zestawu danych.

**Zestaw danych wyjściowych obiektów blob platformy Azure:**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu obiektu blob jest dynamicznie oceniana na podstawie czasu rozpoczęcia przetwarzanego plasterka. Ścieżka folderu używa części czasu rozpoczęcia z roku, miesiąca, dnia i godziny.

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
Zobacz sekcję [właściwości typu zestawu danych obiektów Blob platformy Azure,](data-factory-azure-blob-connector.md#dataset-properties) aby uzyskać listę właściwości obsługiwanych przez ten typ zestawu danych.

**Działanie kopiowania w potoku ze źródłem SQL i ujściem obiektu Blob:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **SqlSource** i typ **ujścia** jest ustawiony na **BlobSink**. Kwerenda SQL określona dla właściwości **SqlReaderQuery** wybiera dane w ciągu ostatniej godziny do skopiowania.

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
W przykładzie **sqlReaderQuery** jest określony dla SqlSource. Działanie kopiowania uruchamia tę kwerendę względem źródła bazy danych SQL azure, aby uzyskać dane. Alternatywnie można określić procedurę składowaną, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli procedura składowana przyjmuje parametry).

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName, kolumny zdefiniowane w sekcji struktury zestawu danych JSON są używane do tworzenia kwerendy do uruchomienia względem usługi Azure SQL Database. Na przykład: `select column1, column2 from mytable`. Jeśli definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.

Zobacz sekcji [Źródło sql](#sqlsource) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) listę właściwości obsługiwanych przez SqlSource i BlobSink.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Przykład: Kopiowanie danych z obiektu blob platformy Azure do bazy danych SQL usługi Azure
Przykład definiuje następujące jednostki fabryki danych:

1. Połączona usługa typu [AzureSqlDatabase](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureSqlTable](#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z copy działania, który używa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i [SqlSink](#copy-activity-properties).

Przykładowy kopiuje dane szeregów czasowych (co godzinę, codziennie itp.) z obiektu blob platformy Azure do tabeli w bazie danych SQL platformy Azure co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

**Usługa połączona z usługą Azure SQL:**

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
Zobacz sekcję Azure SQL Linked Service, aby uzyskać listę właściwości obsługiwanych przez tę usługę połączone.

**Usługa połączona z magazynem obiektów Blob platformy Azure:**

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
Zobacz artykuł [obiektu Blob platformy Azure,](data-factory-azure-blob-connector.md#azure-storage-linked-service) aby uzyskać listę właściwości obsługiwanych przez tę połączone usługi.


**Zestaw danych wejściowych obiektów Blob platformy Azure:**

Dane są pobierane z nowego obiektu blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu i nazwa pliku obiektu blob są dynamicznie oceniane na podstawie czasu rozpoczęcia wycinka, który jest przetwarzany. Ścieżka folderu używa części roku, miesiąca i dnia czasu rozpoczęcia i nazwy pliku, która używa części godzinowej godziny rozpoczęcia. "external": ustawienie "true" informuje usługę Data Factory, że ta tabela jest zewnętrzna dla fabryki danych i nie jest wytwarzana przez działanie w fabryce danych.

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
Zobacz sekcję [właściwości typu zestawu danych obiektów Blob platformy Azure,](data-factory-azure-blob-connector.md#dataset-properties) aby uzyskać listę właściwości obsługiwanych przez ten typ zestawu danych.

**Zestaw danych wyjściowych bazy danych SQL usługi Azure:**

Przykładowy kopie dane do tabeli o nazwie "MyTable" w usłudze Azure SQL. Utwórz tabelę w usłudze Azure SQL z taką samą liczbą kolumn, jak oczekujesz, że plik CSV obiektów blob będzie zawierać. Nowe wiersze są dodawane do tabeli co godzinę.

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
Zobacz sekcję właściwości typu zestawu danych SQL platformy Azure, aby uzyskać listę właściwości obsługiwanych przez ten typ zestawu danych.

**Działanie kopiowania w potoku ze źródłem obiektów Blob i ujściem SQL:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **BlobSource** i typ **ujścia** jest ustawiony na **SqlSink**.

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
Zobacz sekcji [Zlewnik sql](#sqlsink) i [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) listy właściwości obsługiwanych przez SqlSink i BlobSource.

## <a name="identity-columns-in-the-target-database"></a>Kolumny tożsamości w docelowej bazie danych
Ta sekcja zawiera przykład kopiowania danych z tabeli źródłowej bez kolumny tożsamości do tabeli docelowej z kolumną tożsamości.

**Tabela źródło:**

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
Należy zauważyć, że tabela docelowa ma kolumnę tożsamości.

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
**Definicja JSON docelowego zestawu danych**

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

Należy zauważyć, że jako źródło i tabela docelowa mają inny schemat (obiekt docelowy ma dodatkową kolumnę z tożsamością). W tym scenariuszu należy określić **właściwość struktury** w definicji docelowego zestawu danych, która nie zawiera kolumny tożsamości.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Wywoływanie procedury składowanej z ujścia SQL
Na przykład wywoływania procedury składowanej z ujścia SQL w działaniu kopiowania potoku, zobacz [Wywoływanie procedury składowanej dla ujścia SQL w artykule działania kopiowania.](data-factory-invoke-stored-procedure-from-copy-activity.md)

## <a name="type-mapping-for-azure-sql-database"></a>Mapowanie typów dla bazy danych SQL usługi Azure
Jak wspomniano w [działaniach przenoszenia danych](data-factory-data-movement-activities.md) artykuł Kopiowanie działania wykonuje automatyczne konwersje typów z typów źródłowych do typów ujścia z następującym podejściem 2-etapowym:

1. Konwertowanie z natywnych typów źródeł na typ .NET
2. Konwertowanie z typu .NET na natywny typ ujścia

Podczas przenoszenia danych do i z usługi Azure SQL Database następujące mapowania są używane z typu SQL do typu .NET i odwrotnie. Mapowanie jest takie samo jak mapowanie typów danych programu SQL Server dla ADO.NET.

| Typ aparatu bazy danych programu SQL Server | Typ programu .NET Framework |
| --- | --- |
| bigint |Int64 |
| binarny |Bajt[] |
| bit |Wartość logiczna |
| char |Ciąg, Char[] |
| date |DateTime |
| Datetime (data/godzina) |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |Datetimeoffset |
| Wartość dziesiętna |Wartość dziesiętna |
| Atrybut FILESTREAM (varbinary(max)) |Bajt[] |
| Liczba zmiennoprzecinkowa |Double |
| image |Bajt[] |
| int |Int32 |
| pieniędzy |Wartość dziesiętna |
| nchar |Ciąg, Char[] |
| Ntext |Ciąg, Char[] |
| numeryczne |Wartość dziesiętna |
| nvarchar |Ciąg, Char[] |
| rzeczywiste |Single |
| Rowversion |Bajt[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Wartość dziesiętna |
| Sql_variant |Obiekt * |
| tekst |Ciąg, Char[] |
| time |przedział_czasu |
| sygnatura czasowa |Bajt[] |
| tinyint |Byte |
| uniqueidentifier |Guid (identyfikator GUID) |
| varbinary |Bajt[] |
| varchar |Ciąg, Char[] |
| xml |Xml |

## <a name="map-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia
Aby dowiedzieć się więcej o mapowaniu kolumn w źródłowym zestawie danych na kolumny w zestawie danych ujścia, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Powtarzalna kopia
Podczas kopiowania danych do bazy danych programu SQL Server działanie kopiowania domyślnie dołącza dane do tabeli ujścia. Zamiast tego należy wykonać program UPSERT, zobacz powtarzalny zapis w artykule [SqlSink.](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink)

Podczas kopiowania danych z relacyjnych magazynów danych należy pamiętać o powtarzalności, aby uniknąć niezamierzonych wyników. W usłudze Azure Data Factory można ponownie uruchomić plasterek ręcznie. Można również skonfigurować zasady ponawiania dla zestawu danych, tak aby plasterek został ponownie uruchomny w przypadku wystąpienia błędu. Gdy plasterek jest uruchamiany ponownie w obu przypadkach, należy upewnić się, że te same dane są odczytywane bez względu na to, ile razy jest uruchamiany plasterek. Zobacz [Powtarzalny odczyt ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i strojenie
Zobacz [Kopiowanie wydajności działania & dostrajania przewodnika,](data-factory-copy-activity-performance.md) aby dowiedzieć się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych (Copy Activity) w usłudze Azure Data Factory i na różne sposoby optymalizacji.
