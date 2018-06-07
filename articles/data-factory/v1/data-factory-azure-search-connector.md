---
title: Wypychanie danych do indeksu wyszukiwania przy użyciu fabryki danych | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu wypychanie danych do indeksu usługi Azure Search przy użyciu fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e30cffa836beb2086e3bc4e94bf60be94136d3a0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620626"
---
# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Wypychanie danych do indeksu usługi Azure Search przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-azure-search-connector.md)
> * [Wersja 2 — wersja zapoznawcza](../connector-azure-search.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [łącznika usługi Azure Search w wersji 2](../connector-azure-search.md).

W tym artykule opisano sposób użycia działanie kopiowania do przekazania danych z obsługiwanych źródła magazynu danych do indeksu usługi Azure Search. Magazyny danych obsługiwanych źródłowych są wymienione w kolumnie Źródło [obsługiwanych źródeł i wychwytywanie](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. W tym artykule opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólny przegląd przenoszenia danych z kombinacji magazynu obsługiwane dane i działanie kopiowania.

## <a name="enabling-connectivity"></a>Włączenie łączności
Aby umożliwić fabryki danych usługa nawiązać połączenia z lokalnym magazynem danych, należy zainstalować bramę zarządzania danymi w środowisku lokalnym. Możesz zainstalować bramę na tym samym komputerze, przechowuje hostów źródło danych lub na osobnym komputerze, aby uniknąć konkurowanie o zasoby z magazynem danych.

Brama zarządzania danymi nawiązuje połączenie lokalnych źródeł danych do usługi w chmurze w sposób bezpieczny i zarządzanie nimi. Zobacz [przenoszenie danych między lokalnymi i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu, aby uzyskać więcej informacji dotyczących bramy zarządzania danymi.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potoku o działanie kopiowania, który wypycha dane z magazynu danych źródła do indeksu usługi Azure Search przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem, aby utworzyć potok jest użycie **kreatora kopiowania**. Zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybkie przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejs API .NET**, i **interfejsu API REST**. Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania. 

Czy można użyć narzędzia i interfejsy API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych: 

1. Utwórz **połączone usługi** Aby połączyć dane wejściowe i wyjściowe są przechowywane w fabryce danych.
2. Utwórz **zestawów danych** do reprezentowania danych wejściowych i wyjściowych operacji kopiowania. 
3. Utwórz **potoku** aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe. 

Korzystając z kreatora, definicje JSON do tych jednostek fabryki danych (połączone usługi, zestawy danych i potoki) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/API (z wyjątkiem interfejs API .NET), należy zdefiniować tych jednostek fabryki danych w formacie JSON.  Dla przykładu z definicji JSON dla jednostek fabryki danych, które są używane, aby skopiować dane do indeksu usługi Azure Search, zobacz [przykład JSON: kopiowanie danych z lokalnego serwera SQL do indeksu usługi Azure Search](#json-example-copy-data-from-on-premises-sql-server-to-azure-search-index) sekcji tego artykułu. 

Poniższe sekcje zawierają szczegółowe informacje o właściwości JSON, które są używane do definiowania jednostek fabryki danych określonej do indeksu usługi Azure Search:

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Poniższa tabela zawiera opisy elementów JSON, które są specyficzne dla usługi Azure Search połączone.

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| type | Właściwość type musi mieć ustawioną: **AzureSearch**. | Yes |
| url | Adres URL dla usługi Azure Search. | Yes |
| key | Klucz administratora dla usługi Azure Search. | Yes |

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. Sekcje zawierają informacje, takie jak struktury, dostępności i zasad zestawu danych JSON są podobne dla wszystkich typów w zestawie danych. **TypeProperties** sekcja jest różne dla każdego typu zestawu danych. TypeProperties sekcja dla zestawu danych typu **AzureSearchIndex** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| type | Właściwość type musi mieć ustawioną **AzureSearchIndex**.| Yes |
| indexName | Nazwa indeksu usługi Azure Search. Fabryki danych nie powoduje utworzenia indeksu. Indeks musi istnieć w usłudze Azure Search. | Yes |


## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i tabele wyjściowe i różnych zasad są dostępne dla wszystkich typów działań. Właściwości, które są dostępne w sekcji typeProperties różnić się z każdym typem działania. Dla działania kopiowania różnią się w zależności od typów źródeł i sink.

Dla działania kopiowania, gdy obiekt sink jest typu **AzureSearchIndexSink**, w sekcji typeProperties dostępne są następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Określa, czy należy scalić lub Zastąp, jeśli istnieje już dokument w indeksie. Zobacz [WriteBehavior właściwości](#writebehavior-property).| Merge (ustawienie domyślne)<br/>Upload| Nie |
| WriteBatchSize | Przekazywanie danych do indeksu usługi Azure Search, gdy writeBatchSize osiągnie rozmiar buforu. Zobacz [właściwości WriteBatchSize](#writebatchsize-property) szczegółowe informacje. | 1 do 1000. Wartość domyślna to 1000. | Nie |

### <a name="writebehavior-property"></a>Właściwość WriteBehavior
Upserts AzureSearchSink podczas zapisywania danych. Innymi słowy podczas zapisywania dokumentu, jeśli klucz dokumentu już istnieje w indeksie usługi Azure Search, usługi Azure Search aktualizuje istniejący dokument zamiast zgłaszanie wyjątków konflikt.

AzureSearchSink zapewnia następujące dwa zachowania upsert (przy użyciu zestawu SDK AzureSearch):

- **Scal**: łączenie wszystkich kolumn w nowy dokument z istniejącymi. Dla kolumn o wartości null w nowy dokument wartość w istniejącym zostanie zachowana.
- **Przekaż**: nowy dokument zastąpi istniejącą. Dla kolumn nie jest określona w nowy dokument wartość jest równa null, czy istnieje wartość inną niż null w istniejącego dokumentu lub nie.

Domyślnym zachowaniem jest **scalania**.

### <a name="writebatchsize-property"></a>Właściwość WriteBatchSize
Usługa Azure Search obsługuje dokumenty Zapisywanie jako zadanie wsadowe. Plik wsadowy może zawierać akcje 1 do 1000. Akcja obsługuje jeden dokument do wykonania tej operacji przekazywania/merge.

### <a name="data-type-support"></a>Obsługa typu danych
Poniższa tabela określa, czy lub nie obsługuje typu danych usługi Azure Search.

| Typ danych w usłudze Azure wyszukiwania | Obsługiwane w ujściu usługi Azure Search |
| ---------------------- | ------------------------------ |
| Ciąg | Tak |
| Int32 | Tak |
| Int64 | Tak |
| O podwójnej precyzji | Tak |
| Wartość logiczna | Tak |
| DataTimeOffset | Tak |
| Tablica ciągów | Nie |
| GeographyPoint | Nie |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>Przykład JSON: kopiowanie danych z lokalnego serwera SQL do indeksu usługi Azure Search

Poniższy przykład przedstawia:

1.  Połączonej usługi typu [AzureSearch](#linked-service-properties).
2.  Połączonej usługi typu [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3.  Dane wejściowe [dataset](data-factory-create-datasets.md) typu [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4.  Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureSearchIndex](#dataset-properties).
4.  A [potoku](data-factory-create-pipelines.md) z działania kopiowania, która używa [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) i [AzureSearchIndexSink](#copy-activity-properties).

Próbki kopiuje dane szeregów czasowych z lokalną bazą danych programu SQL Server do indeksu usługi Azure Search co godzinę. Właściwości JSON używane w tym przykładzie są opisane w sekcjach poniżej próbek.

Pierwszym krokiem konfiguracji bramy zarządzania danymi na komputerze lokalnym. Instrukcje znajdują się w [przenoszenie danych między lokalizacji lokalnej i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu.

**Usługa Azure Search połączone:**

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

**Usługi SQL Server połączone**

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

**Wejściowy zestaw danych programu SQL Server**

Przykładzie przyjęto założenie, utworzono tabelę "MyTable" w programie SQL Server i zawiera kolumnę o nazwie "timestampcolumn" dla czasu serii danych. Kwerendy można na wiele tabel w ramach tej samej bazy danych za pomocą jednego zestawu danych, ale pojedynczej tabeli muszą być używane do typeProperty tableName zestawu danych.

Ustawienie "external": "prawda" informuje usługi fabryka danych czy zestaw danych jest zewnętrzne do fabryki danych i nie jest generowany przez działanie w fabryce danych.

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

**Usługa Azure Search wyjściowy zestaw danych:**

Przykład kopiuje dane do indeksu usługi Azure Search o nazwie **produkty**. Fabryki danych nie powoduje utworzenia indeksu. Aby przetestować próbki, należy utworzyć indeks o tej nazwie. Tworzenie indeksu usługi Azure Search z taką samą liczbę kolumn w zestawie danych wejściowych. Nowe wpisy zostaną dodane do indeksu usługi Azure Search co godzinę.

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

**Działanie kopiowania w potoku z SQL źródłowy i odbiorczy indeksu usługi Azure Search:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **SqlSource** i **zbiornika** ustawiono typ **AzureSearchIndexSink**. Określony dla zapytania SQL **SqlReaderQuery** właściwości wybiera dane w ostatniej godziny do skopiowania.

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

Jeśli kopiujesz danych z magazynu danych chmury do usługi Azure Search `executionLocation` właściwość jest wymagana. Poniższy fragment kodu JSON zawiera zmiany wymagane w obszarze działania kopiowania `typeProperties` jako przykład. Sprawdź [kopiowanie danych między magazyny danych w chmurze](data-factory-data-movement-activities.md#global) sekcji obsługiwane wartości i więcej szczegółów.

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


## <a name="copy-from-a-cloud-source"></a>Skopiuj ze źródłowej chmurze
Jeśli kopiujesz danych z magazynu danych chmury do usługi Azure Search `executionLocation` właściwość jest wymagana. Poniższy fragment kodu JSON zawiera zmiany wymagane w obszarze działania kopiowania `typeProperties` jako przykład. Sprawdź [kopiowanie danych między magazyny danych w chmurze](data-factory-data-movement-activities.md#global) sekcji obsługiwane wartości i więcej szczegółów.

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

Można również mapować kolumn z zestawu źródła danych do kolumn z zestawu danych zbiornika w definicji działania kopiowania. Aby uzyskać więcej informacji, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie  
Zobacz [wydajności działania kopiowania i dostrajania przewodnik](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego wydajności wpływ przenoszenia danych (działanie kopiowania) i zoptymalizować ją na różne sposoby.

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły:

* [Samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku do tworzenia potoku z działania kopiowania.
