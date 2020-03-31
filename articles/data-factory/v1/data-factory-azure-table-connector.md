---
title: Przenoszenie danych do/z tabeli platformy Azure
description: Dowiedz się, jak przenieść dane do/z usługi Azure Table Storage przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 07b046b1-7884-4e57-a613-337292416319
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 462d54a9d89d6f03aed5e221fa02609da786c8c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260451"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Przenoszenie danych do i z tabeli platformy Azure przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-azure-table-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-azure-table-storage.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [łącznik usługi Azure Table Storage w wersji 2](../connector-azure-table-storage.md).

W tym artykule wyjaśniono, jak używać działania kopiowania w usłudze Azure Data Factory do przenoszenia danych do/z usługi Azure Table Storage. Opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) który przedstawia ogólny przegląd przenoszenia danych z działaniem kopiowania. 

Dane można kopiować z dowolnego obsługiwanego magazynu danych źródłowych do usługi Azure Table Storage lub z usługi Azure Table Storage do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako źródła lub pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, który przenosi dane do/z usługi Azure Table Storage przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) w celu szybkiego instruktażu w sprawie tworzenia potoku przy użyciu kreatora kopiowania danych.

Do utworzenia potoku można również użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon usługi Azure Resource Manager**, **.NET API**i REST **API**. Zobacz [Kopiowanie samouczka aktywności,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania. 

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia: 

1. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania. 
3. Utwórz **potok** z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. 

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), można zdefiniować te jednostki fabryki danych przy użyciu formatu JSON. W przypadku przykładów z definicjami JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych do/z usługi Azure Table Storage, zobacz [sekcję przykładów JSON](#json-examples) w tym artykule.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi Data Factory specyficznych dla usługi Azure Table Storage: 

## <a name="linked-service-properties"></a>Połączone właściwości usługi
Istnieją dwa typy połączonych usług, których można użyć do połączenia magazynu obiektów blob platformy Azure z fabryką danych platformy Azure. Są to: Usługa połączona **AzureStorage** i usługa połączona **AzureStorageSas.** Usługa połączona usługi Azure Storage zapewnia fabryce danych globalny dostęp do usługi Azure Storage. Natomiast usługa połączona z usługą Azure Storage SAS (Shared Access Signature) zapewnia fabryce danych ograniczony/czasowy dostęp do usługi Azure Storage. Nie ma innych różnic między tymi dwiema połączonymi usługami. Wybierz usługę połączony, która odpowiada Twoim potrzebom. Poniższe sekcje zawierają więcej szczegółów na temat tych dwóch połączonych usług.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych.](data-factory-create-datasets.md) Sekcje, takie jak struktura, dostępność i zasady zestawu danych JSON są podobne dla wszystkich typów zestawów danych (Azure SQL, Azure blob, tabela platformy Azure itp.).

Sekcja typeProperties jest inna dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Sekcja **typeProperties** dla zestawu danych typu **AzureTable** ma następujące właściwości.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych tabel platformy Azure, do których odwołuje się usługa. |Tak. Gdy nazwa tabeli jest określona bez azureTableSourceQuery, wszystkie rekordy z tabeli są kopiowane do miejsca docelowego. Jeśli azureTableSourceQuery jest również określony, rekordy z tabeli, która spełnia kwerendy są kopiowane do miejsca docelowego. |

### <a name="schema-by-data-factory"></a>Schemat według fabryki danych
W przypadku magazynów danych wolnych od schematu, takich jak Tabela platformy Azure, usługa Data Factory wywnioskuje schemat w jeden z następujących sposobów:

1. Jeśli określisz strukturę danych przy użyciu właściwości **structure** w definicji zestawu danych, usługa Fabryka danych honoruje tę strukturę jako schemat. W takim przypadku jeśli wiersz nie zawiera wartości dla kolumny, wartość null jest dla niego podana.
2. Jeśli struktura danych nie zostanie określona przy użyciu właściwości **structure** w definicji zestawu danych, usługa Data Factory wyjmuje schemat przy użyciu pierwszego wiersza w danych. W takim przypadku jeśli pierwszy wiersz nie zawiera pełnego schematu, niektóre kolumny są pomijane w wyniku operacji kopiowania.

W związku z tym dla źródeł danych bez schematu najlepszym rozwiązaniem jest określenie struktury danych przy użyciu właściwości **structure.**

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe zestawy danych i zasady są dostępne dla wszystkich typów działań.

Właściwości dostępne w sekcji typeProperties działania z drugiej strony różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się one w zależności od typów źródeł i pochłaniacze.

**Usługa AzureTableSource** obsługuje następujące właściwości w sekcji typeProperties:

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| azureTableSourceQuery |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania tabeli platformy Azure. Zobacz przykłady w następnej sekcji. |Nie. Gdy nazwa tabeli jest określona bez azureTableSourceQuery, wszystkie rekordy z tabeli są kopiowane do miejsca docelowego. Jeśli azureTableSourceQuery jest również określony, rekordy z tabeli, która spełnia kwerendy są kopiowane do miejsca docelowego. |
| azureTableSourceIgnoreTableNotFound |Wskazać, czy nie istnieje wyjątek tabeli. |Prawda<br/>FAŁSZ |Nie |

### <a name="azuretablesourcequery-examples"></a>przykłady azureTableSourceQuery
Jeśli kolumna tabela platformy Azure ma typ ciągu:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Jeśli kolumna Tabela platformy Azure ma typ datetime:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**Usługa AzureTableSink** obsługuje następujące właściwości w sekcji typeProperties:

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Domyślna wartość klucza partycji, która może być używana przez obiekt sink. |Wartość ciągu. |Nie |
| azureTablePartitionKeyName |Określ nazwę kolumny, której wartości są używane jako klucze partycji. Jeśli nie określono, AzureTableDefaultPartitionKeyValue jest używany jako klucz partycji. |Nazwa kolumny. |Nie |
| azureTableRowKeyName |Określ nazwę kolumny, której wartości kolumn są używane jako klucz wiersza. Jeśli nie zostanie określony, użyj identyfikatora GUID dla każdego wiersza. |Nazwa kolumny. |Nie |
| azureTableInsertType |Tryb wstawiania danych do tabeli platformy Azure.<br/><br/>Ta właściwość określa, czy istniejące wiersze w tabeli danych wyjściowych z pasującymi kluczami partycji i wierszy mają swoje wartości zastąpione lub scalone. <br/><br/>Aby dowiedzieć się, jak działają te ustawienia (scalanie i zamienianie), zobacz [Wstawianie lub scalanie tematów encji](https://msdn.microsoft.com/library/azure/hh452241.aspx) i [wstawianie lub zamienianie](https://msdn.microsoft.com/library/azure/hh452242.aspx) tematów encji. <br/><br> To ustawienie ma zastosowanie na poziomie wiersza, a nie na poziomie tabeli, a żadna z opcji nie usuwa wierszy w tabeli danych wyjściowych, które nie istnieją w danych wejściowych. |scalanie (domyślnie)<br/>Zastąp |Nie |
| writeBatchSize |Wstawia dane do tabeli platformy Azure, gdy writeBatchSize lub writeBatchTimeout zostanie trafiony. |Liczba całkowita (liczba wierszy) |Nie (domyślnie: 10000) |
| writeBatchTimeout |Wstawia dane do tabeli platformy Azure, gdy writeBatchSize lub writeBatchTimeout zostanie trafiony |Timespan<br/><br/>Przykład: "00:20:00" (20 minut) |Nie (domyślna wartość limitu czasu klienta magazynu 90 s) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Zamapuj kolumnę źródłową do kolumny docelowej przy użyciu właściwości Translator JSON, zanim będzie można użyć kolumny docelowej jako azureTablePartitionKeyName.

W poniższym przykładzie kolumna źródłowska DivisionID jest mapowana na kolumnę docelową: DivisionID.  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
Identyfikator podziału jest określony jako klucz partycji.

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>Przykłady JSON
Poniższe przykłady zawierają przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub programu Azure [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Pokazują one, jak skopiować dane do i z usługi Azure Table Storage i bazy danych obiektów Blob platformy Azure. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła do dowolnego z obsługiwanych pochłaniacze. Aby uzyskać więcej informacji, zobacz sekcję "Obsługiwane magazyny i formaty danych" w [obszarze Przenoszenie danych przy użyciu funkcji Kopiowanie aktywności](data-factory-data-movement-activities.md).

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Przykład: Kopiowanie danych z tabeli platformy Azure do obiektu blob platformy Azure
W poniższej próbce przedstawiono:

1. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (używana zarówno dla tabeli, jak & obiekt blob).
2. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureTable](#dataset-properties).
3. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. [Potok](data-factory-create-pipelines.md) z działaniem Kopiowania, który używa usługi AzureTableSource i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowy kopie dane należące do partycji domyślnej w tabeli platformy Azure do obiektu blob co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

**Usługa połączona z magazynem platformy Azure:**

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
Usługa Azure Data Factory obsługuje dwa typy usług połączonych usługi Azure Storage: **AzureStorage** i **AzureStorageSas**. W przypadku pierwszego z nich należy określić parametry połączenia zawierające klucz konta, a dla późniejszego identyfikatora Uri (Sygnatura dostępu współdzielonego). Szczegółowe informacje można znaleźć w sekcji [Usługi połączone.](#linked-service-properties)  

**Zestaw danych wejściowych tabeli platformy Azure:**

W przykładzie przyjęto założenie, że utworzono tabelę "MyTable" w tabeli platformy Azure.

Ustawienie "zewnętrzne": "true" informuje usługę Data Factory, że zestaw danych jest zewnętrzny dla fabryki danych i nie jest produkowany przez działanie w fabryce danych.

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

**Zestaw danych wyjściowych obiektów blob platformy Azure:**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu obiektu blob jest dynamicznie oceniana na podstawie czasu rozpoczęcia przetwarzanego plasterka. Ścieżka folderu używa części czasu rozpoczęcia z roku, miesiąca, dnia i godziny.

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

**Kopiowanie działania w potoku za pomocą usługi AzureTableSource i BlobSink:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **AzureTableSource** i typ **ujścia** jest ustawiony na **BlobSink**. Kwerenda SQL określona za pomocą właściwości **AzureTableSourceQuery** wybiera dane z domyślnej partycji co godzinę do skopiowania.

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

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Przykład: Kopiowanie danych z obiektu blob platformy Azure do tabeli platformy Azure
W poniższej próbce przedstawiono:

1. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (używana zarówno dla tabeli, jak & obiekt blob)
2. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
3. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureTable](#dataset-properties).
4. [Potok](data-factory-create-pipelines.md) z copy działania, który używa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i [AzureTableSink](#copy-activity-properties).

Przykładowy kopiuje dane szeregów czasowych z obiektu blob platformy Azure do tabeli platformy Azure co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

**Usługa połączona z usługą Azure Storage (zarówno dla usługi Azure Table, jak & blob):**

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

Usługa Azure Data Factory obsługuje dwa typy usług połączonych usługi Azure Storage: **AzureStorage** i **AzureStorageSas**. W przypadku pierwszego z nich należy określić parametry połączenia zawierające klucz konta, a dla późniejszego identyfikatora Uri (Sygnatura dostępu współdzielonego). Szczegółowe informacje można znaleźć w sekcji [Usługi połączone.](#linked-service-properties)

**Zestaw danych wejściowych obiektów Blob platformy Azure:**

Dane są pobierane z nowego obiektu blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu i nazwa pliku obiektu blob są dynamicznie oceniane na podstawie czasu rozpoczęcia wycinka, który jest przetwarzany. Ścieżka folderu używa części roku, miesiąca i dnia czasu rozpoczęcia i nazwy pliku, która używa części godzinowej godziny rozpoczęcia. "external": ustawienie "true" informuje usługę Data Factory, że zestaw danych jest zewnętrzny dla fabryki danych i nie jest wytwarzany przez działanie w fabryce danych.

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

**Zestaw danych wyjściowych tabeli platformy Azure:**

Przykładowy kopie dane do tabeli o nazwie "MyTable" w tabeli platformy Azure. Utwórz tabelę platformy Azure z taką samą liczbą kolumn, jak oczekujesz, że plik CSV obiektów blob będzie zawierać. Nowe wiersze są dodawane do tabeli co godzinę.

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

**Skopiuj działanie w potoku za pomocą usługi BlobSource i AzureTableSink:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **BlobSource** i typ **ujścia** jest ustawiony na **AzureTableSink**.

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
## <a name="type-mapping-for-azure-table"></a>Mapowanie typów dla tabeli platformy Azure
Jak wspomniano w [artykule działania przenoszenia danych,](data-factory-data-movement-activities.md) Copy activity wykonuje automatyczne konwersje typów z typów źródłowych do typów ujścia z następującym podejściem dwuetapowym.

1. Konwertowanie z natywnych typów źródeł na typ .NET
2. Konwertowanie z typu .NET na natywny typ ujścia

Podczas przenoszenia danych do & z tabeli platformy Azure następujące [mapowania zdefiniowane przez usługę Azure Table service](https://msdn.microsoft.com/library/azure/dd179338.aspx) są używane z typów OData tabeli platformy Azure do typu .NET i odwrotnie.

| Typ danych protokołu OData | Typ .NET | Szczegóły |
| --- | --- | --- |
| Edm.Binary (Edm.Binary) |bajt[] |Tablica bajtów o masie do 64 KB. |
| Edm.Boolean |bool |Wartość logiczna. |
| Edm.DateTime |DateTime |Wartość 64-bitowa wyrażona jako skoordynowany czas uniwersalny (UTC). Obsługiwany zakres DateTime rozpoczyna się od północy 12:00, 1 stycznia 1601 r. (C.E.), UTC. Zakres kończy się 31 grudnia 9999. |
| Edm.Double |double |64-bitowa wartość zmiennoprzecinkowa. |
| Edm.Guid (Edm.Guid) |Guid (identyfikator GUID) |128-bitowy unikatowy identyfikator globalny. |
| Edm.Int32 |Int32 |32-bitowa ć całkowita. |
| Edm.Int64 |Int64 |64-bitowa ć całkowita. |
| Edm.String |Ciąg |Wartość zakodowana w stanie UTF-16. Wartości ciągu mogą mieć maksymalnie 64 KB. |

### <a name="type-conversion-sample"></a>Przykład konwersji typu
Poniższy przykład służy do kopiowania danych z obiektu blob platformy Azure do tabeli platformy Azure z konwersjami typu.

Załóżmy, że zestaw danych obiektów blob jest w formacie CSV i zawiera trzy kolumny. Jednym z nich jest kolumna datetime z niestandardowym formatem datetime przy użyciu skróconych nazw francuskich dla dnia tygodnia.

Zdefiniuj zestaw danych Źródło obiektów blob w następujący sposób wraz z definicjami typów dla kolumn.

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
Biorąc pod uwagę mapowanie typów z typu OData tabeli platformy Azure do typu .NET, należy zdefiniować tabelę w tabeli platformy Azure za pomocą następującego schematu.

**Schemat tabeli platformy Azure:**

| Nazwa kolumny | Typ |
| --- | --- |
| userid |Edm.Int64 |
| name |Edm.String |
| Lastlogindate |Edm.DateTime |

Następnie zdefiniuj zestaw danych tabeli azure w następujący sposób. Nie trzeba określać sekcji "struktura" z informacjami o typie, ponieważ informacje o typie są już określone w podstawowym magazynie danych.

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

W takim przypadku usługa Data Factory automatycznie wpisuje konwersje, w tym pole Datetime z niestandardowym formatem datetime przy użyciu kultury "fr-fr" podczas przenoszenia danych z obiektu blob do tabeli azure.

> [!NOTE]
> Aby mapować kolumny ze źródłowego zestawu danych na kolumny z zestawu danych ujścia, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajność i strojenie
Aby dowiedzieć się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych (aktywność kopiowania) w usłudze Azure Data Factory i na różne sposoby jej optymalizacji, zobacz [Przewodnik wydajności działania kopiowania & dostrajania](data-factory-copy-activity-performance.md).
