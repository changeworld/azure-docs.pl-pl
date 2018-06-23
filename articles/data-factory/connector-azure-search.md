---
title: Kopiowanie danych do indeksu wyszukiwania przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu push lub skopiować dane do indeksu usługi Azure search przy użyciu działania kopiowania w potoku fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 85c854e697e7178bbdb3429c5384d67731770117
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335516"
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Kopiowanie danych do indeksu usługi Azure Search przy użyciu fabryki danych Azure

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-azure-search-connector.md)
> * [Wersja 2 — wersja zapoznawcza](connector-azure-search.md)

W tym artykule omówiono sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane do indeksu usługi Azure Search. Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [łącznika usługi Azure Search w wersji 1](v1/data-factory-azure-search-connector.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować dane z dowolnego źródła obsługiwanych magazynu danych do indeksu usługi Azure Search. Lista magazynów danych, które są obsługiwane jako źródła/wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do łącznika usługi Azure Search.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Obsługiwane są następujące właściwości dla usługi Azure Search połączone:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **AzureSearch** | Yes |
| url | Adres URL dla usługi Azure Search. | Yes |
| key | Klucz administratora dla usługi Azure Search. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. (Jeśli w magazynie danych znajduje się w sieci prywatnej), można użyć środowiska uruchomieniowego integracji Azure lub Self-hosted integracji w czasie wykonywania. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. |Nie |

> [!IMPORTANT]
> Podczas kopiowania danych z magazynu danych chmury do indeksu usługi Azure Search w usłudze Azure Search połączonej usługi, należy odwoływać się w środowisku uruchomieniowym integracji Azure z jawnym region connactVia. Ustaw obszar, który znajduje się usługi Azure Search. Dowiedz się więcej o [środowiska uruchomieniowego integracji Azure](concepts-integration-runtime.md#azure-integration-runtime).

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

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych usługi Azure Search.

Aby skopiować dane do usługi Azure Search, ustaw właściwość Typ zestawu danych do **RelationalTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type zestawu danych: **AzureSearchIndex** | Yes |
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

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwane przez źródło usługi Azure Search.

### <a name="azure-search-as-sink"></a>Usługa Azure Search jako odbioru

Aby skopiować dane do usługi Azure Search, należy ustawić typ źródła w przypadku działania kopiowania do **AzureSearchIndexSink**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **zbiornika** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania: **AzureSearchIndexSink** | Yes |
| writeBehavior | Określa, czy należy scalić lub Zastąp, jeśli istnieje już dokument w indeksie. Zobacz [WriteBehavior właściwości](#writebehavior-property).<br/><br/>Dozwolone wartości to: **scalania** (ustawienie domyślne) i **przekazać**. | Nie |
| writeBatchSize | Przekazywanie danych do indeksu usługi Azure Search, gdy writeBatchSize osiągnie rozmiar buforu. Zobacz [właściwości WriteBatchSize](#writebatchsize-property) szczegółowe informacje.<br/><br/>Dozwolone wartości to: 1 do 1000; liczba całkowita domyślna to 1000. | Nie |

### <a name="writebehavior-property"></a>Właściwość WriteBehavior

Upserts AzureSearchSink podczas zapisywania danych. Innymi słowy podczas zapisywania dokumentu, jeśli klucz dokumentu już istnieje w indeksie usługi Azure Search, usługi Azure Search aktualizuje istniejący dokument zamiast zgłaszanie wyjątków konflikt.

AzureSearchSink zapewnia następujące dwa zachowania upsert (przy użyciu zestawu SDK AzureSearch):

- **Scal**: łączenie wszystkich kolumn w nowy dokument z istniejącymi. Dla kolumn o wartości null w nowy dokument wartość w istniejącym zostanie zachowana.
- **Przekaż**: nowy dokument zastąpi istniejącą. Dla kolumn nie jest określona w nowy dokument wartość jest równa null, czy istnieje wartość inną niż null w istniejącego dokumentu lub nie.

Domyślnym zachowaniem jest **scalania**.

### <a name="writebatchsize-property"></a>Właściwość WriteBatchSize

Usługa Azure Search obsługuje dokumenty Zapisywanie jako zadanie wsadowe. Plik wsadowy może zawierać akcje 1 do 1000. Akcja obsługuje jeden dokument do wykonania tej operacji przekazywania/merge.

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

Poniższa tabela określa, czy lub nie obsługuje typu danych usługi Azure Search.

| Typ danych w usłudze Azure wyszukiwania | Obsługiwane w ujściu usługi Azure Search |
| ---------------------- | ------------------------------ |
| Ciąg | Tak |
| Int32 | Tak |
| Int64 | Tak |
| podwójne | Tak |
| Wartość logiczna | Tak |
| DataTimeOffset | Tak |
| Tablica ciągów | Nie |
| GeographyPoint | Nie |

## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
