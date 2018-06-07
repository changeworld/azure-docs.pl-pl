---
title: Kopiowanie danych do/z bazy danych SQL Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z bazy danych SQL Azure przy użyciu fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 683e8b1407042624d08aee1f0a2120990e2f702e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621714"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Kopiowanie danych do i z bazy danych SQL Azure przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-azure-sql-connector.md)
> * [Wersja 2 — wersja zapoznawcza](../connector-azure-sql-database.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [łącznika usługi Azure SQL Database w wersji 2](../connector-azure-sql-database.md).

W tym artykule opisano sposób używania działania kopiowania w fabryce danych Azure do przenoszenia danych do i z bazy danych SQL Azure. Opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólny przegląd przenoszenia danych z działania kopiowania.  

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane należy skopiować **z bazy danych SQL Azure** do następujących danych przechowuje:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Możesz skopiować dane z następujących baz danych **bazą danych SQL Azure**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Obsługiwany typ uwierzytelniania
Łącznik bazy danych SQL Azure obsługuje uwierzytelnianie podstawowe.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potoku o działanie kopiowania, który przenosi dane z bazy danych SQL Azure za pomocą różnych narzędzi/interfejsów API.

Najprostszym sposobem, aby utworzyć potok jest użycie **kreatora kopiowania**. Zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybkie przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejs API .NET**, i **interfejsu API REST**. Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania. 

Czy można użyć narzędzia i interfejsy API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych: 

1. Utwórz **fabryki danych**. Fabryka danych może zawierać co najmniej jeden potoków. 
2. Utwórz **połączone usługi** Aby połączyć dane wejściowe i wyjściowe są przechowywane w fabryce danych. Na przykład jeśli kopiujesz danych z magazynu obiektów blob platformy Azure do bazy danych Azure SQL, Utwórz dwa połączone usługi, aby połączyć z kontem magazynu platformy Azure i bazą danych Azure SQL z fabryką danych. Dla właściwości połączonej usługi, które są specyficzne dla bazy danych SQL Azure, zobacz [połączona usługa właściwości](#linked-service-properties) sekcji. 
3. Utwórz **zestawów danych** do reprezentowania danych wejściowych i wyjściowych operacji kopiowania. W tym przykładzie wymienionych w ostatnim kroku tworzenia zestawu danych, aby określić folder, który zawiera dane wejściowe i kontener obiektów blob. I Utwórz innego elementu dataset, aby określić tabeli SQL w bazie danych Azure SQL, która przechowuje dane skopiowane z magazynu obiektów blob. Dla właściwości zestawu danych, które są specyficzne dla usługi Azure Data Lake Store, zobacz [właściwości zestawu danych](#dataset-properties) sekcji.
4. Utwórz **potoku** aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe. W przykładzie wspomniano wcześniej używasz BlobSource jako źródło i SqlSink jako zbiorniku dla działania kopiowania. Podobnie baza danych SQL Azure są kopiowane do magazynu obiektów Blob Azure, należy użyć SqlSource i BlobSink w przypadku działania kopiowania. Dla właściwości działania kopiowania, które są specyficzne dla bazy danych SQL Azure, zobacz [skopiować właściwości działania](#copy-activity-properties) sekcji. Aby uzyskać szczegółowe informacje dotyczące sposobu używania magazynu danych jako źródło lub zbiorniku kliknij łącze w poprzedniej sekcji dla magazynu danych.

Korzystając z kreatora, definicje JSON do tych jednostek fabryki danych (połączone usługi, zestawy danych i potoki) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/API (z wyjątkiem interfejs API .NET), należy zdefiniować tych jednostek fabryki danych w formacie JSON.  Aby uzyskać przykłady z definicji JSON dla jednostek fabryki danych, które są używane do kopiowania danych do/z bazy danych SQL Azure, zobacz [przykłady JSON](#json-examples-for-copying-data-to-and-from-sql-database) sekcji tego artykułu. 

Poniższe sekcje zawierają szczegółowe informacje o właściwości JSON, które są używane do definiowania jednostek fabryki danych określonej do bazy danych SQL Azure: 

## <a name="linked-service-properties"></a>Połączona usługa właściwości
Azure SQL połączone usługi łączy bazy danych Azure SQL z fabryką danych. Poniższa tabela zawiera opis specyficzne dla usługi Azure SQL połączone elementy JSON.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi mieć ustawioną: **AzureSqlDatabase** |Yes |
| Parametry połączenia |Podaj informacje wymagane do połączenia z wystąpieniem bazy danych SQL Azure dla właściwości connectionString. Obsługiwane jest tylko uwierzytelnianie podstawowe. |Yes |

> [!IMPORTANT]
> Skonfiguruj [zapory bazy danych SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) serwera bazy danych do [Zezwalaj usługom Azure na dostęp do serwera](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Ponadto jeśli kopiujesz danych do bazy danych SQL Azure z poza tym Azure z lokalnych źródeł danych z bramą fabryki danych, należy skonfigurować odpowiedni zakres adresów IP dla komputera, który wysyła dane do bazy danych SQL Azure.

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby określić zestaw danych do reprezentowania danych wejściowych lub wyjściowych w bazie danych Azure SQL, należy ustawić właściwość type zestawu danych do: **AzureSqlTable**. Ustaw **linkedServiceName** właściwości zestawu danych do nazwy Azure SQL połączonej usługi.  

Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. Sekcje zawierają informacje, takie jak struktury, dostępności i zasad zestawu danych JSON są podobne dla wszystkich typów obiektów dataset (Azure SQL, obiektów blob platformy Azure, Azure tabeli itp.).

Sekcja typeProperties jest różne dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. **TypeProperties** sekcja dla zestawu danych typu **AzureSqlTable** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w wystąpieniu bazy danych SQL Azure, odnoszący się do połączonej usługi. |Yes |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

> [!NOTE]
> Działanie kopiowania przyjmuje tylko jeden parametr wejściowy i tworzy tylko jedno wyjście.

Właściwości dostępne w **typeProperties** sekcji działania zależne od każdego typu działania. Dla działania kopiowania różnią się w zależności od typów źródeł i sink.

Jeśli przenosisz dane z bazy danych Azure SQL, w przypadku działania kopiowania, aby ustawić typ źródła **SqlSource**. Podobnie jeśli przenosisz dane do bazy danych Azure SQL, należy ustawić typ ujścia w działanie kopiowania do **SqlSink**. Ta sekcja zawiera listę obsługiwanych przez SqlSource i SqlSink właściwości.

### <a name="sqlsource"></a>SqlSource
W przypadku działania kopiowania, gdy źródłem jest typu **SqlSource**, są dostępne w następujących właściwości **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj niestandardowych zapytania można odczytać danych. |Ciąg zapytania SQL. Przykład: `select * from MyTable`. |Nie |
| sqlReaderStoredProcedureName |Nazwa procedury przechowywanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. Ostatniej instrukcji SQL musi być instrukcji SELECT w procedurze składowanej. |Nie |
| storedProcedureParameters |Parametry dla procedury składowanej. |Par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametry procedury składowanej. |Nie |

Jeśli **sqlReaderQuery** określono dla SqlSource, odbywa się działanie kopii tego zapytania względem źródła bazy danych SQL Azure umożliwiają pobieranie danych. Można również określić procedury składowanej, podając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli jest to procedura składowana pobiera parametry).

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName kolumny zdefiniowane w sekcji struktury zestawu danych JSON są używane w celu skonstruowania zapytania (`select column1, column2 from mytable`) w celu uruchomienia bazy danych SQL Azure. Jeśli definicji zestawu danych nie ma on struktury, wszystkie kolumny są wybierane w tabeli.

> [!NOTE]
> Jeśli używasz **sqlReaderStoredProcedureName**, należy określić wartość dla **tableName** właściwość w zestawie danych JSON. Nie ma żadnych operacji sprawdzania poprawności, jednak wykonywać na tej tabeli.
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
| writeBatchTimeout |Czas na ukończenie zanim upłynie limit czasu operacji wstawiania wsadowego oczekiwania. |Zakres czasu<br/><br/> Przykład: "00: 30:00" (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu. |Liczba całkowita (liczba wierszy) |Nie (domyślne: 10000) |
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania do wykonania w taki sposób, że dane określonych wycinek jest wyczyszczone. Aby uzyskać więcej informacji, zobacz [powtarzalne kopiowania](#repeatable-copy). |Instrukcja zapytania. |Nie |
| sliceIdentifierColumnName |Określ nazwę kolumny dla aktywności kopiowania wypełnić automatycznie generowane wycinek identyfikator, który służy do oczyszczania danych określonego wycinek czas ponownego uruchomienia. Aby uzyskać więcej informacji, zobacz [powtarzalne kopiowania](#repeatable-copy). |Nazwa kolumny kolumnę o typie danych binary(32). |Nie |
| sqlWriterStoredProcedureName |Nazwa procedury przechowywanej, która definiuje sposób dotyczą źródła danych do tabeli docelowej, np. czy upserts lub Przekształcanie przy użyciu logiki biznesowej. <br/><br/>Należy pamiętać, będzie tej procedury składowanej **wywoływane na partię**. Jeśli chcesz wykonać operację, która tylko jest uruchamiane jeden raz i nie ma nic do wykonania z źródło danych, np. Usuń/obcięcia należy użyć `sqlWriterCleanupScript` właściwości. |Nazwa procedury składowanej. |Nie |
| storedProcedureParameters |Parametry dla procedury składowanej. |Par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametry procedury składowanej. |Nie |
| sqlWriterTableType |Określ nazwę typu tabeli do użycia w procedurze składowanej. Działanie kopiowania udostępnia dane jest przenoszony w tabeli tymczasowej o tym typie tabeli. Kod procedury składowanej można następnie scalić dane są kopiowane z istniejącymi danymi. |Nazwa typu tabeli. |Nie |

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
Poniższe przykłady zapewniają definicje JSON, których można utworzyć potok przy użyciu [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Przedstawiają sposób kopiowania danych do i z usługi Azure SQL Database oraz magazynu obiektów Blob Azure. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła do dowolnego wychwytywanie podane [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w fabryce danych Azure.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Przykład: Kopiowanie danych z bazy danych SQL Azure do obiektów Blob platformy Azure
Taki sam definiuje następujące jednostek fabryki danych:

1. Połączonej usługi typu [AzureSqlDatabase](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureSqlTable](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [obiektów Blob platformy Azure](data-factory-azure-blob-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) z działania kopiowania, która używa [SqlSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane szeregów czasowych (co godzinę, codziennie, itp.) z tabeli w bazie danych Azure SQL do obiektu blob co godzinę. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.  

**Baza danych SQL Azure połączonej usługi:**

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
Zobacz [połączoną usługę SQL Azure](#linked-service) sekcji listy właściwości obsługiwanych przez tej połączonej usługi.

**Magazyn obiektów Blob Azure połączonej usługi:**

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
Zobacz [obiektów Blob platformy Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) artykułu dla listy właściwości obsługiwanych przez tej połączonej usługi.


**Azure SQL wejściowy zestaw danych:**

Przykład przyjęto założenie, utworzono tabelę "MyTable" w języku SQL Azure i zawiera kolumnę o nazwie "timestampcolumn" dla czasu serii danych.

Ustawienie "external": "prawda" informuje usługi fabryka danych Azure czy zestaw danych jest zewnętrzne do fabryki danych i nie jest generowany przez działanie w fabryce danych.

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

Zobacz [właściwości typu zestawu danych Azure SQL](#dataset) sekcji listy właściwości obsługiwany przez dany typ zestawu danych.  

**Azure Blob wyjściowy zestaw danych:**

Dane są zapisywane do nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Ścieżka folderu dla obiekt blob jest dynamicznie obliczane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu używa rok, miesiąc, dzień i godziny części czas rozpoczęcia.

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
Zobacz [właściwości typu zestawu danych obiektów Blob platformy Azure](data-factory-azure-blob-connector.md#dataset-properties) sekcji listy właściwości obsługiwany przez dany typ zestawu danych.  

**Działanie kopiowania w potoku z SQL źródłowy i odbiorczy obiektów Blob:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **SqlSource** i **zbiornika** ustawiono typ **BlobSink**. Określony dla zapytania SQL **SqlReaderQuery** właściwości wybiera dane w ostatniej godziny do skopiowania.

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
W tym przykładzie **sqlReaderQuery** dla SqlSource został określony. Działanie kopiowania jest uruchamiana ta kwerenda dla źródłowej bazy danych SQL Azure umożliwiają pobieranie danych. Można również określić procedury składowanej, podając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli jest to procedura składowana pobiera parametry).

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName kolumny zdefiniowane w sekcji struktury zestawu danych JSON służą do skonstruowania zapytania do bazy danych SQL Azure. Na przykład: `select column1, column2 from mytable`. Jeśli definicji zestawu danych nie ma on struktury, wszystkie kolumny są wybierane w tabeli.

Zobacz [źródła Sql](#sqlsource) sekcji i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) listę obsługiwanych przez SqlSource i BlobSink właściwości.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Przykład: Kopiowanie danych z obiektów Blob platformy Azure do bazy danych SQL Azure
Przykład definiuje następujące jednostek fabryki danych:  

1. Połączonej usługi typu [AzureSqlDatabase](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureSqlTable](#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i [SqlSink](#copy-activity-properties).

Kopie próbki szeregów czasowych danych (co godzinę, codziennie, itp.) z usługi Azure blob do tabeli w usłudze Azure SQL bazy danych co godzinę. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

**Azure połączoną usługą SQL:**

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
Zobacz [połączoną usługę SQL Azure](#linked-service) sekcji listy właściwości obsługiwanych przez tej połączonej usługi.

**Magazyn obiektów Blob Azure połączonej usługi:**

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
Zobacz [obiektów Blob platformy Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) artykułu dla listy właściwości obsługiwanych przez tej połączonej usługi.


**Azure wejściowego zestawu danych obiektów Blob:**

Dane są pobierane z nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Nazwa i ścieżka pliku folder dla obiektu blob dynamicznie są oceniane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu korzysta rok, miesiąc i dzień część czas rozpoczęcia, a nazwa pliku godzina część czas rozpoczęcia. "external": ustawienie "prawda" usługi fabryka danych informuje, że w tej tabeli zewnętrznej dla fabryki danych i nie jest generowany przez działanie w fabryce danych.

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
Zobacz [właściwości typu zestawu danych obiektów Blob platformy Azure](data-factory-azure-blob-connector.md#dataset-properties) sekcji listy właściwości obsługiwany przez dany typ zestawu danych.

**Baza danych SQL Azure wyjściowy zestaw danych:**

Przykład kopiuje dane do tabeli o nazwie "MyTable" w języku SQL Azure. Tworzenie tabeli SQL Azure z taką samą liczbę kolumn zgodnie z oczekiwaniami pliku Blob CSV zawiera. Nowe wiersze są dodawane do tabeli co godzinę.

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
Zobacz [właściwości typu zestawu danych Azure SQL](#dataset) sekcji listy właściwości obsługiwany przez dany typ zestawu danych.

**Działanie kopiowania w potoku z obiektu Blob, źródłowy i odbiorczy SQL:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **BlobSource** i **zbiornika** ustawiono typ **SqlSink**.

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
Zobacz [zbiornika Sql](#sqlsink) sekcji i [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) listę obsługiwanych przez SqlSink i BlobSource właściwości.

## <a name="identity-columns-in-the-target-database"></a>Kolumny tożsamości w docelowej bazie danych
Ta sekcja zawiera przykład kopiowanie danych z tabeli źródłowej bez kolumny tożsamości do tabeli docelowej z kolumny tożsamości.

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
Zwróć uwagę, że tabela docelowa ma kolumny tożsamości.

**Źródło zestawu danych JSON definicji**

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
**Docelowy zestaw danych JSON definicji**

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

Należy zauważyć, że jako tabela źródłowa i docelowa mają różne schemat (element docelowy ma dodatkową kolumnę z tożsamością). W tym scenariuszu, należy określić **struktury** właściwości w definicji zestawu danych docelowego, który nie zawiera kolumny tożsamości.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Wywołaj procedurę składowaną z zbiornika SQL
Na przykład wywoływania procedury składowanej z obiektu sink SQL w działaniu kopiowania potoku, zobacz [wywołaj procedurę składowaną dla obiekt sink SQL w przypadku działania kopiowania](data-factory-invoke-stored-procedure-from-copy-activity.md) artykułu. 

## <a name="type-mapping-for-azure-sql-database"></a>Mapowanie typu dla bazy danych SQL Azure
Jak wspomniano w [działań przepływu danych](data-factory-data-movement-activities.md) artykułu działanie kopiowania przeprowadza automatyczne konwersje z typów źródła do zbiornika typów o następujące podejście krok 2:

1. Konwertowanie typów natywnych źródła na typ architektury .NET
2. Konwertowanie na typ macierzysty ujścia typ architektury .NET

Podczas przenoszenia danych do i z bazy danych SQL Azure, następujące mapowania są używane z typu SQL typ architektury .NET i na odwrót. Mapowanie jest taka sama jak mapowanie typu danych serwera SQL dla ADO.NET.

| Typ aparatu bazy danych programu SQL Server | Typ programu .NET framework |
| --- | --- |
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
| tinyint |Bajtów |
| Unikatowy identyfikator |Identyfikator GUID |
| varbinary |Byte[] |
| varchar |Ciąg, Char] |
| xml |Xml |

## <a name="map-source-to-sink-columns"></a>Obiekt sink kolumn mapy źródła
Aby uzyskać informacje dotyczące mapowania kolumn w zestawie źródła danych do kolumn w zestawie danych zbiornika, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Kopiuj powtarzalne
Podczas kopiowania danych do bazy danych serwera SQL, działanie kopiowania dołącza dane do tabeli ujścia domyślnie. Aby zamiast tego przeprowadzić UPSERT, zobacz [Repeatable zapisu SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) artykułu. 

Podczas kopiowania danych z danych relacyjnych przechowuje, należy pamiętać, aby uniknąć niezamierzone wyniki powtarzalności. Fabryka danych Azure możesz ponownie ręcznie wycinek. Można również skonfigurować zasady ponawiania dla zestawu danych, aby wycinek jest uruchamiany ponownie, gdy wystąpi błąd. Podczas wycinek zostanie uruchomiona ponownie w obu przypadkach, należy się upewnić, że te same dane jest do odczytu niezależnie od tego, ile razy wycinek jest uruchamiany. Zobacz [Repeatable odczytywać źródłami relacyjnymi](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i dostrajania
Zobacz [wydajności działania kopiowania & dostrajanie przewodnik](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego wydajność wpływ przenoszenia danych (działanie kopiowania) w usłudze fabryka danych Azure i zoptymalizować ją na różne sposoby.
