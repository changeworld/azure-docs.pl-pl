---
title: Wypychanie danych do indeksu wyszukiwania przy użyciu usługi fabryka danych | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o tym, jak wypychać dane do indeksu wyszukiwania platformy Azure przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7ad328eec7e16b5368b78a0dfccbf5c09adb5c13
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60567235"
---
# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Wypychanie danych do indeksu usługi Azure Search przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](data-factory-azure-search-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-azure-search.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [łącznika usługi Azure Search w wersji 2](../connector-azure-search.md).

W tym artykule opisano sposób używania działania kopiowania do wypychania danych z obsługiwanego źródłowego magazynu danych do indeksu usługi Azure Search. Magazyny danych obsługiwanego źródła są wymienione w kolumnie Źródło [obsługiwane źródłami i ujściami](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. W tym artykule opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przenoszenie danych za pomocą działania kopiowania i kombinacji magazynu obsługiwanych danych.

## <a name="enabling-connectivity"></a>Włączanie połączenia
Aby umożliwić usługi Data Factory, usługa połączyć się z lokalnym magazynem danych, zainstalowaniu bramy zarządzania danymi w środowisku w środowisku lokalnym. Bramę można zainstalować na tym samym komputerze, na którym przechowywany hosty źródła danych lub na osobnym komputerze, aby uniknąć rywalizując o zasoby z magazynem danych.

Brama zarządzania danymi lokalnymi źródłami danych łączy się z usługami w chmurze w sposób bezpieczny i zarządzane. Zobacz [przenoszenie danych między lokalizacją lokalną i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykuł, szczegółowe informacje na temat bramy zarządzania danymi.

## <a name="getting-started"></a>Wprowadzenie
Utworzysz potok z działaniem kopiowania, który wypycha dane ze źródłowego magazynu danych do indeksu usługi Azure Search przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **kreatora kopiowania**. Zobacz [samouczka: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Aby utworzyć potok umożliwia także następujących narzędzi: **Witryna Azure portal**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejsu API platformy .NET**i  **Interfejs API REST**. Zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy używasz narzędzi lub interfejsów API, należy wykonać poniższe kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** połączyć dane wejściowe i wyjściowe przechowywane z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania dane wejściowe i wyjściowe operacji kopiowania.
3. Tworzenie **potoku** za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON dotyczące tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować te jednostki usługi Data Factory przy użyciu formatu JSON.  Przykładowe definicje JSON dotyczące jednostek usługi Data Factory, które są używane do kopiowania danych do indeksu usługi Azure Search, możesz znaleźć [przykład kodu JSON: Kopiowanie danych z lokalnego programu SQL Server do usługi Azure Search index](#json-example-copy-data-from-on-premises-sql-server-to-azure-search-index) dalszej części tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi fabryka danych określonej do indeksu wyszukiwania platformy Azure:

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Poniższa tabela zawiera opisy elementów JSON, które są specyficzne dla usługi Azure Search połączone.

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| type | Właściwość type musi być równa: **AzureSearch**. | Tak |
| url | Adres URL dla usługi Azure Search. | Yes |
| key | Klucz administratora dla usługi Azure Search. | Tak |

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. Sekcje, takie jak struktury, dostępność i zasady zestawem danych JSON są podobne dla wszystkich typów w zestawie danych. **TypeProperties** sekcji różni się dla każdego typu zestawu danych. Zestaw danych o typie sekcji typeProperties **AzureSearchIndex** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| type | Właściwość type musi być równa **AzureSearchIndex**.| Tak |
| indexName | Nazwa indeksu usługi Azure Search. Fabryki danych nie powoduje utworzenia indeksu. Indeks musi istnieć w usłudze Azure Search. | Tak |


## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania działań, zobacz [tworzenia potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i tabele danych wyjściowych i różnych zasad są dostępne dla wszystkich typów działań. Natomiast właściwości, które są dostępne w sekcji typeProperties zależą od każdego typu działania. Działanie kopiowania ich różnią się w zależności od typów źródła i ujścia.

Działanie kopiowania, gdy obiekt sink jest typu **AzureSearchIndexSink**, w sekcji typeProperties dostępne są następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Określa, czy należy scalić lub Zastąp, jeśli istnieje już dokument w indeksie. Zobacz [właściwość WriteBehavior](#writebehavior-property).| Scal (ustawienie domyślne)<br/>Przekazanie| Nie |
| WriteBatchSize | Przekazywanie danych do indeksu usługi Azure Search, gdy writeBatchSize osiągnie rozmiar buforu. Zobacz [właściwość WriteBatchSize](#writebatchsize-property) Aby uzyskać szczegółowe informacje. | od 1 do 1000. Wartość domyślna to 1000. | Nie |

### <a name="writebehavior-property"></a>Właściwość WriteBehavior
AzureSearchSink wykonuje operację UPSERT podczas zapisywania danych. Innymi słowy podczas zapisywania dokumentu, jeśli klucz dokumentu już istnieje w indeksie usługi Azure Search, usługa Azure Search aktualizuje istniejący dokument, a nie zostanie zgłoszony wyjątek konflikt.

AzureSearchSink zawiera następujące dwa zachowania upsert (przy użyciu zestawu SDK AzureSearch):

- **Scal**: łączenie wszystkich kolumn w nowym dokumencie z istniejącymi. Dla kolumn o wartości null w nowy dokument wartość w istniejącym są zachowywane.
- **Przekaż**: Nowy dokument zastąpi istniejącą. Dla kolumn nie jest określona w nowy dokument wartość jest równa null, czy ma wartość inną niż null w istniejący dokument, lub nie.

Domyślnym zachowaniem jest **scalania**.

### <a name="writebatchsize-property"></a>Właściwość WriteBatchSize
Usługa Azure Search obsługuje pisanie dokumentów jako zadania wsadowego. Partii może zawierać od 1 do 1000 akcji. Akcja obsługuje jeden dokument do wykonania tej operacji przekazywania/merge.

### <a name="data-type-support"></a>Obsługa typu danych
W poniższej tabeli określono, czy typ danych usługi Azure Search jest obsługiwany, czy nie.

| Typ danych w usłudze Azure Search | Obsługiwane w ujściu usługi Azure Search |
| ---------------------- | ------------------------------ |
| String | Tak |
| Int32 | Tak |
| Int64 | Tak |
| Double | Tak |
| Boolean | Tak |
| DataTimeOffset | Tak |
| String Array | Nie |
| GeographyPoint | Nie |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>Przykład kodu JSON: Kopiowanie danych z lokalnego programu SQL Server do indeksu usługi Azure Search

Poniższy przykład pokazuje:

1. Połączonej usługi typu [AzureSearch](#linked-service-properties).
2. Połączonej usługi typu [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureSearchIndex](#dataset-properties).
4. A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) i [AzureSearchIndexSink](#copy-activity-properties).

Przykładowy skrypt kopiuje dane szeregów czasowych z lokalnej bazy danych programu SQL Server do indeksu usługi Azure Search co godzinę. Właściwości JSON użytych w tym przykładzie są opisane w sekcjach poniżej przykładów.

Pierwszym krokiem konfiguracji bramy zarządzania danymi na maszynie lokalnej. Instrukcje znajdują się w [przenoszenia danych między lokalizacjami lokalnymi i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykułu.

**Usługa Azure Search, o których połączone:**

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

**Połączonej usługi SQL Server**

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

Przykład przyjęto założenie, utworzono tabelę "MyTable" w programie SQL Server i zawiera kolumnę o nazwie "timestampcolumn" w danych szeregów czasowych. Można tworzyć zapytania za pośrednictwem wielu tabel w ramach tej samej bazy danych przy użyciu jednego zestawu danych, ale pojedynczej tabeli musi być używany dla typeProperty tableName typizowanego zestawu danych.

Ustawienie "external": "true" informuje usługa Data Factory, zestaw danych jest zewnętrzne w usłudze data factory i nie jest generowany przez działanie w usłudze data factory.

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

Przykład kopiuje dane do indeksu usługi Azure Search o nazwie **produktów**. Fabryki danych nie powoduje utworzenia indeksu. Aby przetestować próbki, należy utworzyć indeks o tej nazwie. Tworzenie indeksu usługi Azure Search z taką samą liczbę kolumn, tak jak wejściowego zestawu danych. Nowe wpisy są dodawane do indeksu usługi Azure Search na godzinę.

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

**Działanie kopiowania w potoku za pomocą programu SQL źródła i ujścia indeksu wyszukiwania platformy Azure:**

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **SqlSource** i **ujścia** ustawiono typ **AzureSearchIndexSink**. Zapytanie SQL, określony dla **SqlReaderQuery** właściwość wybiera dane w ciągu ostatniej godziny do skopiowania.

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

Jeśli kopiujesz dane z magazynu danych w chmurze do usługi Azure Search `executionLocation` właściwość jest wymagana. Poniższy fragment kodu JSON zawiera zmiany wymagane w ramach działania kopiowania `typeProperties` jako przykład. Sprawdź [kopiować dane między magazynami danych w chmurze](data-factory-data-movement-activities.md#global) sekcji obsługiwane wartości i uzyskać więcej informacji.

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


## <a name="copy-from-a-cloud-source"></a>Kopiowanie ze źródła chmury
Jeśli kopiujesz dane z magazynu danych w chmurze do usługi Azure Search `executionLocation` właściwość jest wymagana. Poniższy fragment kodu JSON zawiera zmiany wymagane w ramach działania kopiowania `typeProperties` jako przykład. Sprawdź [kopiować dane między magazynami danych w chmurze](data-factory-data-movement-activities.md#global) sekcji obsługiwane wartości i uzyskać więcej informacji.

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

Można również mapować kolumny z zestawu danych źródłowych do kolumn z zestaw danych ujścia w definicji działania kopiowania. Aby uzyskać więcej informacji, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Zobacz [dostrajania przewodnik dotyczący wydajności działania kopiowania i](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego obniżenie wydajności przenoszenia danych (działanie kopiowania) i różne sposoby, aby zoptymalizować ją.

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły:

* [Samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania.
