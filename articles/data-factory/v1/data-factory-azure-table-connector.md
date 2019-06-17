---
title: Przenoszenie danych do i z usługi Azure Table | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przenieść dane z usługi Azure Table Storage przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 07b046b1-7884-4e57-a613-337292416319
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: aed341c50332b424a1149c129629cd451a4e5133
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66146926"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Przenoszenie danych do i z usługi Azure Table przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](data-factory-azure-table-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-azure-table-storage.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [łącznika usługi Azure Table Storage w wersji 2](../connector-azure-table-storage.md).

W tym artykule wyjaśniono, jak użyć działania kopiowania w usłudze Azure Data Factory do przenoszenia danych z usługi Azure Table Storage. Opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przenoszenie danych za pomocą działania kopiowania. 

Możesz skopiować dane z obsługiwanego źródłowego magazynu danych do usługi Azure Table Storage lub Azure Table Storage do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako źródła lub ujścia działania kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Wprowadzenie
Utworzysz potok z działaniem kopiowania, które przenosi dane z usługi Azure Table Storage przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **kreatora kopiowania**. Zobacz [samouczka: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Aby utworzyć potok umożliwia także następujących narzędzi: **Witryna Azure portal**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejsu API platformy .NET**i  **Interfejs API REST**. Zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania. 

Czy używasz narzędzi lub interfejsów API, należy wykonać poniższe kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia: 

1. Tworzenie **połączonych usług** połączyć dane wejściowe i wyjściowe przechowywane z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania dane wejściowe i wyjściowe operacji kopiowania. 
3. Tworzenie **potoku** za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe. 

Korzystając z kreatora, definicje JSON dotyczące tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować te jednostki usługi Data Factory przy użyciu formatu JSON. Aby uzyskać przykłady przy użyciu definicji JSON dla jednostek fabryki danych, które są używane do kopiowania danych z usługi Azure Table Storage, zobacz [JSON przykłady](#json-examples) dalszej części tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi fabryka danych określonej do usługi Azure Table Storage: 

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
Istnieją dwa typy połączonych usług, które służy do łączenia usługi Azure blob storage do usługi Azure data factory. Oto one: **AzureStorage** połączoną usługę i **AzureStorageSas** połączoną usługę. Połączona usługa Azure Storage udostępnia usługi data factory za pomocą globalnego dostępu do usługi Azure Storage. Natomiast połączonej usługi Azure Storage SAS (Shared Access Signature) usługa udostępnia usługi data factory z dostępem ograniczonym/czasowo do usługi Azure Storage. Nie istnieją żadne inne różnice między tymi dwoma usługami połączone. Wybierz połączonej usługi, która odpowiada Twoim potrzebom. Poniższe sekcje zawierają więcej szczegółów na tych dwóch połączonych usług.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje & właściwości dostępne Definiowanie zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. Sekcje, takie jak struktury, dostępność i zasady zestawem danych JSON są podobne dla wszystkich typów na zestaw danych (Azure SQL, obiektów blob platformy Azure, usługa Azure table itp.).

W sekcji typeProperties różni się dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. **TypeProperties** sekcji dla zestawu danych typu **AzureTable** ma następujące właściwości.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych tabel platformy Azure, która połączona usługa przywołuje. |Tak. Gdy właściwość tableName jest określona bez azureTableSourceQuery, wszystkie rekordy z tabeli są kopiowane do miejsca docelowego. Jeśli podano także azureTableSourceQuery, rekordy z tabeli, która spełnia warunki zapytania są kopiowane do lokalizacji docelowej. |

### <a name="schema-by-data-factory"></a>Schemat przez usługę Data Factory
Dla magazynów danych bez schematu, takich jak Azure Table usługa Data Factory wnioskuje schemat w jednym z następujących sposobów:

1. Jeśli określisz struktury danych przy użyciu **struktury** właściwości w definicji zestawu danych usługa Data Factory honoruje tej struktury Schema. W tym przypadku jeśli wiersz zawiera wartość dla kolumny, wartość null podano dla niego.
2. Jeśli nie określisz struktury danych przy użyciu **struktury** właściwości w definicji zestawu danych usługi Data Factory wnioskuje schemat przy użyciu pierwszego wiersza danych. W tym przypadku jeśli pierwszy wiersz zawiera pełny schemat, niektóre kolumny zostaną pominięte w wyniku operacji kopiowania.

W związku z tym, w przypadku źródeł danych bez schematu, najlepszym rozwiązaniem jest i określasz strukturę danych przy użyciu **struktury** właściwości.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe zestawy danych i zasady są dostępne dla wszystkich typów działań.

Właściwości, które są dostępne w sekcji typeProperties działania z drugiej strony zależą od każdego typu działania. Działanie kopiowania ich różnią się w zależności od typów źródła i ujścia.

**AzureTableSource** obsługuje następujące właściwości w sekcji typeProperties:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| azureTableSourceQuery |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania tabeli platformy Azure. Zobacz przykłady w następnej sekcji. |Nie. Gdy właściwość tableName jest określona bez azureTableSourceQuery, wszystkie rekordy z tabeli są kopiowane do miejsca docelowego. Jeśli podano także azureTableSourceQuery, rekordy z tabeli, która spełnia warunki zapytania są kopiowane do lokalizacji docelowej. |
| azureTableSourceIgnoreTableNotFound |Wskazuje, czy wchłonąć wyjątek tabela nie istnieje. |WARTOŚĆ TRUE<br/>WARTOŚĆ FALSE |Nie |

### <a name="azuretablesourcequery-examples"></a>Przykłady azureTableSourceQuery
Jeśli kolumna tabeli platformy Azure jest typu string:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

W przypadku kolumny tabeli platformy Azure typu Data/Godzina:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** obsługuje następujące właściwości w sekcji typeProperties:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Domyślna wartość klucza partycji, które mogą być używane przez obiekt sink. |Wartość ciągu. |Nie |
| azureTablePartitionKeyName |Określ nazwę kolumny, których wartości są używane jako klucze partycji. Jeśli nie zostanie określony, AzureTableDefaultPartitionKeyValue jest używany jako klucz partycji. |Nazwa kolumny. |Nie |
| azureTableRowKeyName |Określ nazwę kolumny, których wartości kolumny są używane jako klucz wiersza. Jeśli nie zostanie określony, użyj identyfikatora GUID dla każdego wiersza. |Nazwa kolumny. |Nie |
| azureTableInsertType |Tryb Aby wstawić dane do tabeli platformy Azure.<br/><br/>Ta właściwość określa, czy istniejące wiersze w tabeli danych wyjściowych ze zgodnymi kluczami partycji i wiersza mają wartości zastąpienia lub scalony. <br/><br/>Aby dowiedzieć się, jak działają te ustawienia (scalania i Zastąp), zobacz [Insert lub Merge Entity](https://msdn.microsoft.com/library/azure/hh452241.aspx) i [Insert lub Zastąp jednostki](https://msdn.microsoft.com/library/azure/hh452242.aspx) tematów. <br/><br> To ustawienie jest stosowane na poziomie wiersza, a nie na poziomie tabeli, a żadna opcja usuwa wiersze w tabeli danych wyjściowych, które nie istnieją w danych wejściowych. |Scal (ustawienie domyślne)<br/>Zastąp |Nie |
| writeBatchSize |Wstawia dane do tabeli platformy Azure, gdy writeBatchSize lub writeBatchTimeout tych limitów zostanie osiągnięty. |Liczba całkowita (liczba wierszy) |Nie (domyślne: 10000) |
| writeBatchTimeout |Wstawia dane do tabeli platformy Azure, po osiągnięciu writeBatchSize lub writeBatchTimeout |TimeSpan<br/><br/>Przykład: "00:20:00" (20 minut) |Nie (domyślnie magazynu klienta domyślna wartość limitu czasu wartości 90 s) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Kolumny źródłowej są mapowane na kolumny docelowej, za pomocą właściwości JSON w usłudze translator, zanim będzie można użyć kolumny docelowej jako azureTablePartitionKeyName.

W poniższym przykładzie kolumna źródłowa DivisionID jest mapowany na kolumny docelowej: DivisionID.  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
DivisionID jest określony jako klucza partycji.

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>Przykłady JSON
W poniższych przykładach udostępniono przykładowe definicji JSON, które umożliwiają tworzenie potoku za pomocą [witryny Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazują, jak kopiować dane do i z usługi Azure Table Storage i bazy danych obiektów Blob platformy Azure. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła do dowolnej obsługiwanej wychwytywanie. Aby uzyskać więcej informacji, zobacz sekcję "obsługiwane magazyny danych i formatów" w [przenoszenie danych za pomocą działania kopiowania](data-factory-data-movement-activities.md).

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Przykład: Kopiowanie danych z tabeli platformy Azure do obiektów Blob platformy Azure
Poniższy przykład pokazuje:

1. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (używane dla obiektu blob & tabeli).
2. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureTable](#dataset-properties).
3. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. [Potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa AzureTableSource i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowy skrypt kopiuje dane należące do domyślnej partycji w tabeli platformy Azure do obiektu blob, co godzinę. Właściwości JSON używanych w tych przykładach są opisane w sekcjach poniżej przykładów.

**Połączona usługa Azure storage:**

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
Usługa Azure Data Factory obsługuje dwa typy usługi Azure Storage, połączone usługi: **AzureStorage** i **AzureStorageSas**. Dla pierwszą z nich należy określić parametry połączenia, który zawiera klucz konta, a dla późniejszy go, należy określić identyfikator Uri sygnatury dostępu współdzielonego (SAS). Zobacz [połączonych usług](#linked-service-properties) sekcji, aby uzyskać szczegółowe informacje.  

**Usługa Azure Table wejściowy zestaw danych:**

Przykład założono, że utworzono tabelę "MyTable" w tabeli platformy Azure.

Ustawienie "external": "true" informuje usługa Data Factory, zestaw danych jest zewnętrzne w usłudze data factory i nie jest generowany przez działanie w usłudze data factory.

```JSON
{
  "name": "AzureTableInput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
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

**Usługa Azure Blob wyjściowy zestaw danych:**

Dane są zapisywane do nowego obiektu blob, co godzinę (frequency: godzina, interwał: 1). Ścieżka folderu dla obiektu blob jest dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc, dzień i części godzin od zaplanowanej godziny rozpoczęcia.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Działanie kopiowania w potoku za pomocą AzureTableSource i BlobSink:**

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **AzureTableSource** i **ujścia** ustawiono typ **BlobSink**. Zapytanie SQL określony za pomocą **AzureTableSourceQuery** właściwość wybiera dane z domyślną partycję co godzinę do skopiowania.

```JSON
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureTabletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureTableInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureTableSource",
                        "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
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

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Przykład: Kopiowanie danych z obiektów Blob platformy Azure do usługi Azure Table
Poniższy przykład pokazuje:

1. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (używane dla obiektu blob & tabeli)
2. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
3. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureTable](#dataset-properties).
4. [Potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i [AzureTableSink](#copy-activity-properties).

Kopiuje przykładowe szeregów czasowych danych z usługi Azure blob na platformie Azure tabeli co godzinę. Właściwości JSON używanych w tych przykładach są opisane w sekcjach poniżej przykładów.

**Usługa Azure storage (dla usługi Azure Table i Blob) połączoną usługę:**

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

Usługa Azure Data Factory obsługuje dwa typy usługi Azure Storage, połączone usługi: **AzureStorage** i **AzureStorageSas**. Dla pierwszą z nich należy określić parametry połączenia, który zawiera klucz konta, a dla późniejszy go, należy określić identyfikator Uri sygnatury dostępu współdzielonego (SAS). Zobacz [połączonych usług](#linked-service-properties) sekcji, aby uzyskać szczegółowe informacje.

**Usługa Azure wejściowy zestaw danych obiektów Blob:**

Dane są pobierane z nowy obiekt blob co godzinę (frequency: godzina, interwał: 1). Folder ścieżkę i nazwę dla obiektu blob są dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc i dzień godziny rozpoczęcia, a nazwa pliku używa godzinę część czas rozpoczęcia. "external": ustawienia "true" w usłudze Data Factory informuje, że zestaw danych jest zewnętrzne w usłudze data factory i nie jest generowany przez działanie w usłudze data factory.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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

**Usługa Azure Table wyjściowy zestaw danych:**

Przykład kopiuje dane do tabeli o nazwie "MyTable" w tabeli platformy Azure. Utwórz tabelę platformy Azure z taką samą liczbę kolumn, zgodnie z oczekiwaniami plik obiektów Blob CSV będzie zawierał. Nowe wiersze są dodawane do tabeli, co godzinę.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
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

**Działanie kopiowania w potoku za pomocą BlobSource i AzureTableSink:**

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **BlobSource** i **ujścia** ustawiono typ **AzureTableSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoTable",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureTableOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "AzureTableSink",
            "writeBatchSize": 100,
            "writeBatchTimeout": "01:00:00"
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
## <a name="type-mapping-for-azure-table"></a>Typ mapowania dla tabeli platformy Azure
Jak wspomniano w [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, działanie kopiowania wykonuje operację automatyczne konwersje z typów źródła do ujścia typy za pomocą następujących podejście dwuetapowe.

1. Konwersji z typów natywnych źródła na typ architektury .NET
2. Przekonwertowanie z platformy .NET na typ ujścia natywne

Podczas przenoszenia danych do i z usługi Azure Table następujące [mapowania zdefiniowane przez usługę Azure Table](https://msdn.microsoft.com/library/azure/dd179338.aspx) są używane z typów OData tabeli platformy Azure, typ architektury .NET i na odwrót.

| Typ danych OData | Typ architektury .NET | Szczegóły |
| --- | --- | --- |
| Edm.Binary |byte[] |Tablica bajtów do 64 KB. |
| Edm.Boolean |bool |Wartość logiczna. |
| Edm.DateTime |DateTime |Wartość 64-bitową, wyrażone jako uniwersalny czas koordynowany (UTC). Obsługiwanym zakresem daty/godziny, który zaczyna się od 12:00, a 1 stycznia, 1601 r. N.E. (C.E.), UTC. Zakres kończy się w dniu 31 grudnia 9999 r. |
| Edm.Double |double |64-bitowy zmiennoprzecinkowy wartość punktu. |
| Edm.Guid |Guid |Globalnie unikatowy identyfikator 128-bitowego. |
| Edm.Int32 |Int32 |32-bitową liczbę całkowitą. |
| Edm.Int64 |Int64 |64-bitową liczbę całkowitą. |
| Edm.String |String |Wartość algorytmem UTF-16. Wartości typu ciąg może być maksymalnie 64 KB. |

### <a name="type-conversion-sample"></a>Przykładowe konwersji typu
Poniższy przykład jest kopiowanie danych z obiektu Blob platformy Azure do tabeli platformy Azure za pomocą konwersji typu.

Załóżmy, że zestaw danych obiektów Blob jest w formacie CSV zawiera trzy kolumny. Jeden z nich jest kolumną daty/godziny w formacie niestandardowe daty/godziny przy użyciu skrócone nazwy francuska dzień tygodnia.

Definiowanie zestawu danych źródłowego obiektu Blob w następujący sposób wraz z definicji typu dla kolumny.

```JSON
{
    "name": " AzureBlobInput",
    "properties":
    {
        "structure":
        [
            { "name": "userid", "type": "Int64"},
            { "name": "name", "type": "String"},
            { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
        ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "external": true,
        "availability":
        {
            "frequency": "Hour",
            "interval": 1,
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
Biorąc pod uwagę mapowania typów z typu OData tabeli platformy Azure na typ architektury .NET, zdefiniujesz tabeli w usłudze Azure Table przy użyciu następującego schematu.

**Schemat tabeli platformy Azure:**

| Nazwa kolumny | Typ |
| --- | --- |
| userid |Edm.Int64 |
| name |Edm.String |
| lastlogindate |Edm.DateTime |

Następnie zdefiniuj zestaw danych tabeli platformy Azure w następujący sposób. Nie trzeba określić sekcji "strukturę" przy użyciu informacji o typie, ponieważ informacje o typie jest już określony w podstawowym magazynie danych.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
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

W tym przypadku Data Factory automatycznie konwersje typów w tym pola daty/godziny w formacie niestandardowe daty/godziny przy użyciu kultury "fr-fr", podczas przenoszenia danych z magazynu obiektów Blob do usługi Azure Table.

> [!NOTE]
> Aby zamapować kolumny z zestawu danych źródłowych do kolumn z zestawu danych ujścia, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Informacje na temat kluczowych czynników tego obniżenie wydajności przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory i zoptymalizować ją na różne sposoby, zobacz [wydajności działania kopiowania & przewodnika dostrajania](data-factory-copy-activity-performance.md).
