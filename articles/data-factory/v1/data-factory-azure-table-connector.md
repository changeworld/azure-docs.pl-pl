---
title: Przenoszenie danych do/z tabel Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przenieść dane z magazynem tabel Azure przy użyciu fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 07b046b1-7884-4e57-a613-337292416319
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3a24e919f1bbde6188e3655399f1ef843fbec23b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052994"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Przenoszenie danych do i z tabel Azure przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [W wersji 1](data-factory-azure-table-connector.md)
> * [W wersji 2 (bieżąca wersja)](../connector-azure-table-storage.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 fabryki danych. Jeśli używasz bieżącą wersję usługi fabryka danych, zobacz [łącznika magazynu tabel platformy Azure w wersji 2](../connector-azure-table-storage.md).

W tym artykule opisano sposób używania działania kopiowania w fabryce danych Azure do przeniesienia danych z magazynu tabel platformy Azure. Opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólny przegląd przenoszenia danych z działania kopiowania. 

Można skopiować danych z dowolnej obsługiwanej źródłowej magazynu danych do magazynu tabel platformy Azure lub z magazynu tabel Azure żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych obsługiwane jako źródła lub wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. 

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potoku o działanie kopiowania, który przenosi dane z magazynu tabel Azure przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem, aby utworzyć potok jest użycie **kreatora kopiowania**. Zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybkie przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager** , **Interfejs API .NET**, i **interfejsu API REST**. Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania. 

Czy można użyć narzędzia i interfejsy API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych: 

1. Utwórz **połączone usługi** Aby połączyć dane wejściowe i wyjściowe są przechowywane w fabryce danych.
2. Utwórz **zestawów danych** do reprezentowania danych wejściowych i wyjściowych operacji kopiowania. 
3. Utwórz **potoku** aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe. 

Korzystając z kreatora, definicje JSON do tych jednostek fabryki danych (połączone usługi, zestawy danych i potoki) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/API (z wyjątkiem interfejs API .NET), należy zdefiniować tych jednostek fabryki danych w formacie JSON.  Aby uzyskać przykłady z definicji JSON dla jednostek fabryki danych, które są używane do kopiowania danych do/z magazynu tabel Azure, zobacz [przykłady JSON](#json-examples) sekcji tego artykułu. 

Poniższe sekcje zawierają szczegółowe informacje o właściwości JSON, które są używane do definiowania jednostek fabryki danych określonej do magazynu tabel platformy Azure: 

## <a name="linked-service-properties"></a>Połączona usługa właściwości
Istnieją dwa typy połączonych usług używanego do łączenia z magazynu obiektów blob platformy Azure do fabryki danych Azure. Są one: **AzureStorage** połączonej usługi i **element AzureStorageSas** połączonej usługi. Połączoną usługą magazynu Azure zapewnia usłudze fabryka danych z globalnego dostępu do magazynu Azure. Związana SAS magazynu Azure (Shared Access Signature) usługa udostępnia fabryka danych z ograniczonej/czas-powiązane z dostępem do magazynu Azure. Nie istnieją inne różnice między tych dwóch połączonych usług. Wybierz połączonej usługi, która odpowiada Twoim potrzebom. Poniższe sekcje zawierają więcej szczegółowych informacji na temat tych dwóch usług połączonych.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. Sekcje zawierają informacje, takie jak struktury, dostępności i zasad zestawu danych JSON są podobne dla wszystkich typów obiektów dataset (Azure SQL, obiektów blob platformy Azure, Azure tabeli itp.).

Sekcja typeProperties jest różne dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. **TypeProperties** sekcja dla zestawu danych typu **AzureTable** ma następujące właściwości.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych w tabeli platformy Azure, odnoszący się do połączonej usługi. |Tak. W przypadku tableName bez azureTableSourceQuery wszystkie rekordy z tabeli są kopiowane do lokalizacji docelowej. Jeśli określono również azureTableSourceQuery, rekordy z tabeli, która spełnia zapytania są kopiowane do lokalizacji docelowej. |

### <a name="schema-by-data-factory"></a>Schemat fabryka danych
Dla magazynów danych bez schematu, takie jak tabel Azure usługi fabryka danych z wnioskuje schemat w jednym z następujących sposobów:

1. Jeśli określisz struktury danych za pomocą **struktury** tej struktury Schema honoruje właściwości w definicji zestawu danych, usługi fabryka danych. W tym przypadku jeśli wiersza nie zawiera wartości dla kolumny, wartość null podano dla niego.
2. Jeśli nie określisz struktury danych za pomocą **struktury** właściwości w definicji zestawu danych, fabryki danych wnioskuje schemat za pomocą pierwszego wiersza w danych. W takim przypadku jeśli pierwszy wiersz zawiera pełną schematu, niektóre kolumny zostaną pominięte w wyniku operacji kopiowania.

W związku z tym dla źródeł danych bez schematu, najlepszym rozwiązaniem jest zdefiniowanie struktury danych przy użyciu **struktury** właściwości.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe zestawy danych i zasady są dostępne dla wszystkich typów działań.

Właściwości, które są dostępne w sekcji typeProperties działania z drugiej strony zależą od każdego typu działania. Dla działania kopiowania różnią się w zależności od typów źródeł i sink.

**AzureTableSource** obsługuje następujące właściwości w sekcji typeProperties:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| azureTableSourceQuery |Użyj niestandardowych zapytania można odczytać danych. |Ciąg zapytania tabeli platformy Azure. Przykłady w następnej sekcji. |Nie. W przypadku tableName bez azureTableSourceQuery wszystkie rekordy z tabeli są kopiowane do lokalizacji docelowej. Jeśli określono również azureTableSourceQuery, rekordy z tabeli, która spełnia zapytania są kopiowane do lokalizacji docelowej. |
| azureTableSourceIgnoreTableNotFound |Wskazuje, czy swallow wyjątek tabela nie istnieje. |WARTOŚĆ TRUE<br/>WARTOŚĆ FALSE |Nie |

### <a name="azuretablesourcequery-examples"></a>Przykłady azureTableSourceQuery
W przypadku tabel Azure kolumny typu string:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

W przypadku tabel Azure kolumny typu Data/Godzina:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** obsługuje następujące właściwości w sekcji typeProperties:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Domyślna wartość klucza partycji, które mogą być używane przez obiekt sink. |Wartość ciągu. |Nie |
| azureTablePartitionKeyName |Określ nazwę kolumny, których wartości są używane jako klucze partycji. Jeśli nie zostanie określony, AzureTableDefaultPartitionKeyValue jest używana jako klucza partycji. |Nazwa kolumny. |Nie |
| azureTableRowKeyName |Określ nazwę kolumny, których wartości kolumn używanych jako klucz wiersza. Jeśli nie zostanie określony, użyj identyfikatora GUID dla każdego wiersza. |Nazwa kolumny. |Nie |
| azureTableInsertType |Tryb do wstawiania danych do tabeli platformy Azure.<br/><br/>Ta właściwość określa, czy wartości zastąpienia lub scalić zostać istniejących wierszy w tabeli wyników ze zgodnymi kluczami partycji i wiersza. <br/><br/>Aby dowiedzieć się więcej na temat działania tych ustawień (scalania i Zastąp), zobacz [wstawienia lub scalania jednostki](https://msdn.microsoft.com/library/azure/hh452241.aspx) i [wstawienia lub Zastąp jednostki](https://msdn.microsoft.com/library/azure/hh452242.aspx) tematów. <br/><br> To ustawienie jest stosowane na poziomie wiersza, a nie na poziomie tabeli, a żadna z tych opcji usuwa wiersze w tabeli danych wyjściowych, które nie istnieją w danych wejściowych. |merge (ustawienie domyślne)<br/>Zamień |Nie |
| writeBatchSize |Wstawia dane do tabeli platformy Azure, gdy zostaje trafiony writeBatchSize lub writeBatchTimeout. |Liczba całkowita (liczba wierszy) |Nie (domyślne: 10000) |
| writeBatchTimeout |Wstawia dane do tabeli platformy Azure, gdy zostaje trafiony writeBatchSize lub writeBatchTimeout |zakres czasu<br/><br/>Przykład: "00:20:00" (20 minut) |Nie (domyślnie magazynu klienta domyślny limit czasu wartość 90 s) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Mapowanie kolumny źródłowej do przy użyciu translatora właściwości JSON, zanim użyjesz kolumna docelowa jako azureTablePartitionKeyName kolumna docelowa.

W poniższym przykładzie kolumna źródłowa DivisionID jest zamapowany na kolumny docelowej: DivisionID.  

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
Poniższe przykłady zapewniają definicje JSON, których można utworzyć potok przy użyciu [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Przedstawiają sposób kopiowania danych do i z magazynu tabel platformy Azure i bazy danych obiektów Blob Azure. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła do żadnego z obsługiwanych sink. Aby uzyskać więcej informacji, zobacz sekcję "obsługiwane magazyny danych i formaty" w [przenoszenia danych za pomocą działania kopiowania](data-factory-data-movement-activities.md).

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Przykład: Kopiowanie danych z tabel Azure do obiektów Blob platformy Azure
Poniższy przykład przedstawia:

1. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (używane dla obiekt blob & tabeli).
2. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureTable](#dataset-properties).
3. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. [Potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [AzureTableSource](#activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane należące do domyślnej partycji w tabeli obiektu blob Azure co godzinę. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

**Połączonej usługi magazynu Azure:**

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
Fabryka danych Azure obsługuje dwa typy usług magazynu Azure połączony: **AzureStorage** i **element AzureStorageSas**. Dla pierwszego z nich Określ ciąg połączenia, który zawiera klucz konta i dla późniejszą, określ identyfikator Uri dostępu sygnatury dostępu Współdzielonego. Zobacz [połączonych usług](#linked-service-properties) sekcji, aby uzyskać szczegółowe informacje.  

**Azure tabeli wejściowy zestaw danych:**

Przykładzie przyjęto założenie, że utworzono tabelę "MyTable" w tabeli platformy Azure.

Ustawienie "external": "prawda" informuje usługi fabryka danych czy zestaw danych jest zewnętrzne do fabryki danych i nie jest generowany przez działanie w fabryce danych.

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

**Azure Blob wyjściowy zestaw danych:**

Dane są zapisywane do nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Ścieżka folderu dla obiekt blob jest dynamicznie obliczane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu używa rok, miesiąc, dzień i godziny części czas rozpoczęcia.

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

**Działanie kopiowania w potoku z AzureTableSource i BlobSink:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **AzureTableSource** i **zbiornika** ustawiono typ **BlobSink**. Zapytanie SQL określony za pomocą **AzureTableSourceQuery** właściwości wybiera danych z partycji domyślnej co godzinę do skopiowania.

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

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Przykład: Kopiowanie danych z obiektu Blob Azure do tabeli platformy Azure
Poniższy przykład przedstawia:

1. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (używane dla obiekt blob & tabeli)
2. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
3. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureTable](#dataset-properties).
4. [Potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i [AzureTableSink](#copy-activity-properties).

Kopie próbki szeregów czasowych dane z usługi Azure blob Azure tabeli co godzinę. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

**Usługa Azure storage (dla tabeli platformy Azure i obiektów Blob) połączonej usługi:**

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

Fabryka danych Azure obsługuje dwa typy usług magazynu Azure połączony: **AzureStorage** i **element AzureStorageSas**. Dla pierwszego z nich Określ ciąg połączenia, który zawiera klucz konta i dla późniejszą, określ identyfikator Uri dostępu sygnatury dostępu Współdzielonego. Zobacz [połączonych usług](#linked-service-properties) sekcji, aby uzyskać szczegółowe informacje.

**Azure wejściowego zestawu danych obiektów Blob:**

Dane są pobierane z nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Nazwa i ścieżka pliku folder dla obiektu blob dynamicznie są oceniane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu korzysta rok, miesiąc i dzień część czas rozpoczęcia, a nazwa pliku godzina część czas rozpoczęcia. "external": ustawienie "prawda" usługi fabryka danych informuje, czy zestaw danych jest zewnętrzne do fabryki danych i nie jest generowany przez działanie w fabryce danych.

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

**Tabeli platformy Azure wyjściowy zestaw danych:**

Przykład kopiuje dane do tabeli o nazwie "MyTable" w tabeli platformy Azure. Tworzenie tabeli platformy Azure z taką samą liczbę kolumn, zgodnie z oczekiwaniami pliku Blob CSV zawiera. Nowe wiersze są dodawane do tabeli co godzinę.

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

**Działanie kopiowania w potoku z BlobSource i AzureTableSink:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **BlobSource** i **zbiornika** ustawiono typ **AzureTableSink**.

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
## <a name="type-mapping-for-azure-table"></a>Mapowanie typu dla tabeli platformy Azure
Jak wspomniano w [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, automatyczne konwersje z typów źródła do zbiornika typy z następujących rozwinięcie wykonuje działanie kopiowania.

1. Konwertowanie typów natywnych źródła na typ architektury .NET
2. Konwertowanie na typ macierzysty ujścia typ architektury .NET

Podczas przenoszenia danych do i z tabel Azure, następujące [mapowania zdefiniowane przez usługę Azure tabeli](https://msdn.microsoft.com/library/azure/dd179338.aspx) są używane z typów OData tabeli platformy Azure na typ architektury .NET i na odwrót.

| Typ danych OData | Typ architektury .NET | Szczegóły |
| --- | --- | --- |
| Edm.Binary |byte[] |Tablica bajtów do 64 KB. |
| Edm.Boolean |wartość logiczna |Wartość logiczna. |
| Edm.DateTime |DateTime |Wartość 64-bitowa, wyrażone jako uniwersalny czas koordynowany (UTC). Obsługiwanym zakresem DateTime zaczyna się od 12:00, a 1 stycznia, 1601 r. N.E. (C.E.), UTC. Zakres kończy się po 31 grudnia 9999 r. |
| Edm.Double |double |64-bitowej zmiennej punktu wartości. |
| Edm.Guid |Identyfikator GUID |Globalnie unikatowy identyfikator 128-bitowego. |
| Edm.Int32 |Int32 |32-bitową liczbę całkowitą. |
| Edm.Int64 |Int64 |64-bitową liczbę całkowitą. |
| Edm.String |Ciąg |Wartość algorytmem UTF-16. Ciągi może być maksymalnie 64 KB. |

### <a name="type-conversion-sample"></a>Przykładowe konwersji typu
Poniższy przykład jest kopiowania danych z obiektu Blob Azure do tabeli platformy Azure z konwersji typu.

Załóżmy, że zestawu danych obiektów Blob jest w formacie CSV zawiera trzy kolumny. Jeden z nich jest kolumną daty/godziny w formacie datetime niestandardowych za pomocą nazwy skróconej francuskim dzień tygodnia.

Definiowanie zestawu danych obiektów Blob, źródłowy następujący wraz z definicji typu dla kolumny.

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
Podana mapowania typu z typu Azure tabeli OData do typów .NET, czy zdefiniować tabeli w tabeli platformy Azure z następującego schematu.

**Schemat tabeli platformy Azure:**

| Nazwa kolumny | Typ |
| --- | --- |
| Nazwa użytkownika |Edm.Int64 |
| name |Edm.String |
| lastlogindate |Edm.DateTime |

Następnie określ następujący zestaw danych tabel Azure. Nie trzeba określić "structure" sekcji informacji o typach, ponieważ informacje o typie został już określony w odpowiedni magazyn danych.

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

W takim przypadku fabryka danych automatycznie konwersje typów w tym polu daty/godziny w formacie datetime niestandardowych przy użyciu kultury "fr-fr" podczas przenoszenia danych z obiektu Blob do tabel Azure.

> [!NOTE]
> Aby mapować kolumn z zestawu źródła danych do kolumn z obiektu sink zestawu danych, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajność i dostrajania
Informacje na temat kluczowych czynników tego wydajność wpływ przenoszenia danych (działanie kopiowania) w usłudze fabryka danych Azure i zoptymalizować ją na różne sposoby, zobacz [wydajności działania kopiowania & dostrajanie przewodnik](data-factory-copy-activity-performance.md).
