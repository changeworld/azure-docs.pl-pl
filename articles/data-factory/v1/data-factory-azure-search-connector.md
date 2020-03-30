---
title: Wypychanie danych do indeksu wyszukiwania przy użyciu usługi Data Factory
description: Dowiedz się, jak wypychać dane do indeksu usługi Azure Cognitive Search przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5b1170f721cf8521cfe1762df0cc616c938ddf28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281563"
---
# <a name="push-data-to-an-azure-cognitive-search-index-by-using-azure-data-factory"></a>Wypychanie danych do indeksu usługi Azure Cognitive Search przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-azure-search-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-azure-search.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [łącznik usługi Azure Cognitive Search w wersji 2](../connector-azure-search.md).

W tym artykule opisano, jak używać działania kopiowania do wypychania danych z obsługiwanego magazynu danych źródłowych do indeksu usługi Azure Cognitive Search. Obsługiwane źródłowe magazyny danych są wymienione w kolumnie Źródło [obsługiwanych źródeł i pochłaniacze](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. W tym artykule opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) który przedstawia ogólny przegląd przenoszenia danych z copy activity i obsługiwanych kombinacji magazynu danych.

## <a name="enabling-connectivity"></a>Włączanie łączności
Aby umożliwić usługę Data Factory łączenie się z lokalnym magazynem danych, należy zainstalować bramę zarządzania danymi w środowisku lokalnym. Bramę można zainstalować na tym samym komputerze, na którym znajduje się źródłowy magazyn danych, lub na oddzielnym komputerze, aby uniknąć konkurowania o zasoby z magazynem danych.

Brama zarządzania danymi łączy lokalne źródła danych z usługami w chmurze w bezpieczny i zarządzany sposób. Aby uzyskać szczegółowe informacje na temat bramy zarządzania danymi, zobacz [Przenoszenie danych między środowiskiem lokalnym a chmurą.](data-factory-move-data-between-onprem-and-cloud.md)

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, które wypycha dane z magazynu danych źródłowych do indeksu wyszukiwania przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) w celu szybkiego instruktażu w sprawie tworzenia potoku przy użyciu kreatora kopiowania danych.

Do utworzenia potoku można również użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon usługi Azure Resource Manager**, **.NET API**i REST **API**. Zobacz [Kopiowanie samouczka aktywności,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania.

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania.
3. Utwórz **potok** z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), można zdefiniować te jednostki fabryki danych przy użyciu formatu JSON.  W przypadku przykładu z definicjami JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych do indeksu wyszukiwania, zobacz [przykład JSON: Kopiowanie danych z lokalnego programu SQL Server do](#json-example-copy-data-from-on-premises-sql-server-to-azure-cognitive-search-index) sekcji indeksu usługi Azure Cognitive Search w tym artykule.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek fabryki danych specyficznych dla indeksu wyszukiwania:

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Poniższa tabela zawiera opisy elementów JSON, które są specyficzne dla usługi połączonej usługi Azure Cognitive Search.

| Właściwość | Opis | Wymagany |
| -------- | ----------- | -------- |
| type | Właściwość typu musi być ustawiona na: **AzureSearch**. | Tak |
| url | adres URL usługi wyszukiwania. | Tak |
| key | Klucz administratora usługi wyszukiwania. | Tak |

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych.](data-factory-create-datasets.md) Sekcje, takie jak struktura, dostępność i zasady zestawu danych JSON są podobne dla wszystkich typów zestawów danych. Sekcja **typeProperties** jest inna dla każdego typu zestawu danych. Sekcja typeProperties dla zestawu danych typu **AzureSearchIndex** ma następujące właściwości:

| Właściwość | Opis | Wymagany |
| -------- | ----------- | -------- |
| type | Właściwość typu musi być ustawiona na **AzureSearchIndex**.| Tak |
| nazwa indeksu | Nazwa indeksu wyszukiwania. Fabryka danych nie tworzy indeksu. Indeks musi istnieć w usłudze Azure Cognitive Search. | Tak |


## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania działań, zobacz [Tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, tabele danych wejściowych i wyjściowych oraz różne zasady są dostępne dla wszystkich typów działań. Mając na uwadze, właściwości dostępne w sekcji typeProperties różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się one w zależności od typów źródeł i pochłaniacze.

W przypadku działania kopiowania, gdy ujście jest typu **AzureSearchIndexSink,** następujące właściwości są dostępne w sekcji typeProperties:

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior (Zachowanie zapisu) | Określa, czy dokument ma być scalany, czy zamieniany, gdy dokument już istnieje w indeksie. Zobacz [WriteBehavior właściwości](#writebehavior-property).| Scalanie (domyślnie)<br/>Upload| Nie |
| Rozmiar writebatchsize | Przekazuje dane do indeksu wyszukiwania, gdy rozmiar buforu osiągnie writeBatchSize. Zobacz [WriteBatchSize właściwość,](#writebatchsize-property) aby uzyskać szczegółowe informacje. | od 1 do 1000. Wartość domyślna to 1000. | Nie |

### <a name="writebehavior-property"></a>WriteBehavior właściwość
Usługa AzureSearchSink upserts podczas zapisywania danych. Innymi słowy podczas pisania dokumentu, jeśli klucz dokumentu już istnieje w indeksie wyszukiwania, usługa Azure Cognitive Search aktualizuje istniejący dokument, zamiast zgłaszać wyjątek konfliktu.

Usługa AzureSearchSink udostępnia następujące dwa zachowania upsert (przy użyciu narzędzia AzureSearch SDK):

- **Scalanie**: połącz wszystkie kolumny w nowym dokumencie z istniejącą. W przypadku kolumn o wartości null w nowym dokumencie wartość w istniejącym jest zachowywana.
- **Przekaż**: Nowy dokument zastępuje istniejący. W przypadku kolumn niewymienione w nowym dokumencie wartość jest ustawiona na wartość null, niezależnie od tego, czy w istniejącym dokumencie jest wartość inną niż null.

Domyślnym zachowaniem jest **Scalanie**.

### <a name="writebatchsize-property"></a>WriteBatchSize Właściwość
Usługa Azure Cognitive Search obsługuje zapisywanie dokumentów jako partii. Partia może zawierać od 1 do 1000 akcji. Akcja obsługuje jeden dokument do wykonania operacji przekazywania/scalania.

### <a name="data-type-support"></a>Obsługa typów danych
W poniższej tabeli określono, czy typ danych usługi Azure Cognitive Search jest obsługiwany, czy nie.

| Typ danych usługi Azure Cognitive Search | Obsługiwane w usłudze Azure Cognitive Search Sink |
| ---------------------- | ------------------------------ |
| Ciąg | Tak |
| Int32 | Tak |
| Int64 | Tak |
| Double | Tak |
| Wartość logiczna | Tak |
| DataTimeOffset (Zestaw danych) | Tak |
| Tablica ciągów | Nie |
| GeografiaPunkt | Nie |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-cognitive-search-index"></a>Przykład JSON: kopiowanie danych z lokalnego programu SQL Server do indeksu usługi Azure Cognitive Search

W poniższej próbce przedstawiono:

1. Połączona usługa typu [AzureSearch](#linked-service-properties).
2. Połączona usługa typu [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureSearchIndex](#dataset-properties).
4. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) i [AzureSearchIndexSink](#copy-activity-properties).

Przykładowy kopiuje dane szeregów czasowych z lokalnej bazy danych programu SQL Server do wyszukiwania indeksu co godzinę. Właściwości JSON używane w tym przykładzie są opisane w sekcjach następujących po próbkach.

W pierwszej kolejności skonfiguruj bramę zarządzania danymi na komputerze lokalnym. Instrukcje znajdują się w [ruchomych danych między lokalizacjami lokalnymi i](data-factory-move-data-between-onprem-and-cloud.md) w chmurze artykułu.

**Usługa połączona usługi Azure Cognitive Search:**

```JSON
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

**Usługa połączona z programem SQL Server**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**Zestaw danych wejściowych programu SQL Server**

W przykładzie przyjęto założenie, że utworzono tabelę "MyTable" w programie SQL Server i zawiera kolumnę o nazwie "timestampcolumn" dla danych szeregów czasowych. Można wysyłać kwerendy za pośrednictwem wielu tabel w tej samej bazie danych przy użyciu pojedynczego zestawu danych, ale dla zestawu danych musi być używana pojedyncza tabelaName typeProperty zestawu danych.

Ustawienie "zewnętrzne": "true" informuje usługę Data Factory, że zestaw danych jest zewnętrzny dla fabryki danych i nie jest produkowany przez działanie w fabryce danych.

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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

**Zestaw danych wyjściowych usługi Azure Cognitive Search:**

Przykładowy kopiuje dane do indeksu usługi Azure Cognitive Search o nazwie **produkty**. Fabryka danych nie tworzy indeksu. Aby przetestować przykład, należy utworzyć indeks o tej nazwie. Utwórz indeks wyszukiwania z taką samą liczbą kolumn, jak w wejściowym zestawie danych. Nowe wpisy są dodawane do indeksu wyszukiwania co godzinę.

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

**Kopiowanie działania w potoku ze źródłem SQL i ujściem indeksu usługi Azure Cognitive Search:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **SqlSource** i typ **ujścia** jest ustawiony na **AzureSearchIndexSink**. Kwerenda SQL określona dla właściwości **SqlReaderQuery** wybiera dane w ciągu ostatniej godziny do skopiowania.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
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

Jeśli kopiujesz dane z magazynu danych w `executionLocation` chmurze do usługi Azure Cognitive Search, właściwość jest wymagana. Poniższy fragment kodu JSON pokazuje zmiany potrzebne `typeProperties` w obszarze Działanie kopiowania jako przykład. Sprawdź [sekcję Kopiuj dane między magazynami danych w chmurze,](data-factory-data-movement-activities.md#global) aby uzyskać obsługiwane wartości i więcej szczegółów.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```


## <a name="copy-from-a-cloud-source"></a>Kopiowanie ze źródła w chmurze
Jeśli kopiujesz dane z magazynu danych w `executionLocation` chmurze do usługi Azure Cognitive Search, właściwość jest wymagana. Poniższy fragment kodu JSON pokazuje zmiany potrzebne `typeProperties` w obszarze Działanie kopiowania jako przykład. Sprawdź [sekcję Kopiuj dane między magazynami danych w chmurze,](data-factory-data-movement-activities.md#global) aby uzyskać obsługiwane wartości i więcej szczegółów.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

Można również mapować kolumny z źródłowego zestawu danych na kolumny z zestawu danych ujścia w definicji działania kopiowania. Aby uzyskać szczegółowe informacje, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Zobacz [przewodnik wydajności i dostrajania działania kopiowania,](data-factory-copy-activity-performance.md) aby dowiedzieć się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych (Działanie kopiowania) i na różne sposoby optymalizacji.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

* [Kopiuj samouczek działania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) dla krok po kroku instrukcje dotyczące tworzenia potoku z copy activity.
