---
title: Kopiowanie danych do indeksu wyszukiwania przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu wypychania lub skopiować dane do indeksu usługi Azure search za pomocą działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: jingwang
ms.openlocfilehash: faf0cab55ec0cef034638d218f2172f3676ff39b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245107"
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Kopiowanie danych do indeksu usługi Azure Search przy użyciu usługi Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-azure-search-connector.md)
> * [Bieżąca wersja](connector-azure-search.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory, aby skopiować dane do indeksu usługi Azure Search. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do indeksu usługi Azure Search. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika usługi Azure Search.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane przez usługę Azure Search połączone:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **AzureSearch** | Yes |
| url | Adres URL dla usługi Azure Search. | Yes |
| key | Klucz administratora dla usługi Azure Search. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. (Jeśli Twój magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub środowiskiem Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

> [!IMPORTANT]
> Podczas kopiowania danych z magazynem danych w chmurze w usłudze Azure Search indeks w usłudze Azure Search połączoną usługę, należy można znaleźć środowiska Azure Integration Runtime za pomocą jawnego regionu w connactVia. Ustaw region, który znajduje się usługi Azure Search. Dowiedz się więcej z [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime).

**Przykład:**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych usługi Azure Search.

Aby skopiować dane do usługi Azure Search, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **AzureSearchIndex** | Yes |
| indexName | Nazwa indeksu usługi Azure Search. Fabryki danych nie powoduje utworzenia indeksu. Indeks musi istnieć w usłudze Azure Search. | Yes |

**Przykład:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": {
            "referenceName": "<Azure Search linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties" : {
            "indexName": "products"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwane przez usługę Azure Search źródło.

### <a name="azure-search-as-sink"></a>Usługa Azure Search jako ujście

Aby skopiować dane do usługi Azure Search, należy ustawić typ źródłowego w działaniu kopiowania, aby **AzureSearchIndexSink**. Następujące właściwości są obsługiwane w działaniu kopiowania **ujścia** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **AzureSearchIndexSink** | Yes |
| writeBehavior | Określa, czy należy scalić lub Zastąp, jeśli istnieje już dokument w indeksie. Zobacz [właściwość WriteBehavior](#writebehavior-property).<br/><br/>Dozwolone wartości to: **Scal** (ustawienie domyślne) i **przekazywanie**. | Nie |
| writeBatchSize | Przekazywanie danych do indeksu usługi Azure Search, gdy writeBatchSize osiągnie rozmiar buforu. Zobacz [właściwość WriteBatchSize](#writebatchsize-property) Aby uzyskać szczegółowe informacje.<br/><br/>Dozwolone wartości to: liczba całkowita od 1 do 1000; domyślna to 1000. | Nie |

### <a name="writebehavior-property"></a>Właściwość WriteBehavior

AzureSearchSink wykonuje operację UPSERT podczas zapisywania danych. Innymi słowy podczas zapisywania dokumentu, jeśli klucz dokumentu już istnieje w indeksie usługi Azure Search, usługa Azure Search aktualizuje istniejący dokument, a nie zostanie zgłoszony wyjątek konflikt.

AzureSearchSink zawiera następujące dwa zachowania upsert (przy użyciu zestawu SDK AzureSearch):

- **Scal**: łączenie wszystkich kolumn w nowym dokumencie z istniejącymi. Dla kolumn o wartości null w nowy dokument wartość w istniejącym są zachowywane.
- **Przekaż**: Nowy dokument zastąpi istniejącą. Dla kolumn nie jest określona w nowy dokument wartość jest równa null, czy ma wartość inną niż null w istniejący dokument, lub nie.

Domyślnym zachowaniem jest **scalania**.

### <a name="writebatchsize-property"></a>Właściwość WriteBatchSize

Usługa Azure Search obsługuje pisanie dokumentów jako zadania wsadowego. Partii może zawierać od 1 do 1000 akcji. Akcja obsługuje jeden dokument do wykonania tej operacji przekazywania/merge.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

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

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
