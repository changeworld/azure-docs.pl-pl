---
title: Kopiowanie danych z testowania odzyskiwania po awarii przy użyciu usługi Azure Data Factory (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z testowania odzyskiwania po awarii do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
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
ms.date: 06/15/2018
ms.author: jingwang
ms.openlocfilehash: 166dffc6f79784b9a62cea4cb545aee32001441d
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124681"
---
# <a name="copy-data-from-drill-using-azure-data-factory-preview"></a>Kopiowanie danych z testowania odzyskiwania po awarii przy użyciu usługi Azure Data Factory (wersja zapoznawcza)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z testowania odzyskiwania po awarii. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

> [!IMPORTANT]
> Ten łącznik jest obecnie w wersji zapoznawczej. Możesz wypróbować tę funkcję i przekaż nam swoją opinię. Jeśli w swoim rozwiązaniu chcesz wprowadzić zależność od łączników w wersji zapoznawczej, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z testowania odzyskiwania po awarii, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

Usługa Azure Data Factory udostępnia wbudowanego sterownika, aby umożliwić łączność, dlatego nie trzeba ręcznie zainstalować dowolnego sterownika, za pomocą tego łącznika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do testowania odzyskiwania po awarii, łącznik.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku testowania odzyskiwania po awarii połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **testowania odzyskiwania po awarii** | Yes |
| Parametry połączenia | Ciąg połączenia ODBC, aby nawiązać połączenie testowania odzyskiwania po awarii. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Używając środowiskiem Integration Runtime lub Azure Integration Runtime (Jeśli magazyn danych jest publicznie dostępny). Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

**Przykład:**

```json
{
    "name": "DrillLinkedService",
    "properties": {
        "type": "Drill",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "ConnectionType=Direct;Host=<host>;Port=<port>;AuthenticationType=Plain;UID=<user name>;PWD=<password>"
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych testowania odzyskiwania po awarii.

Aby skopiować dane z testowania odzyskiwania po awarii, należy ustawić właściwość typu zestawu danych na **DrillTable**. Nie ma dodatkowych właściwości specyficzne dla danego typu w tego typu zestawu danych.

**Przykład**

```json
{
    "name": "DrillDataset",
    "properties": {
        "type": "DrillTable",
        "linkedServiceName": {
            "referenceName": "<Drill linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło testowania odzyskiwania po awarii.

### <a name="drillsource-as-source"></a>DrillSource jako źródło

Aby skopiować dane z testowania odzyskiwania po awarii, należy ustawić typ źródła w działaniu kopiowania, aby **DrillSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **DrillSource** | Yes |
| query | Umożliwia odczytywanie danych niestandardowe zapytania SQL. Na przykład: `"SELECT * FROM MyTable"`. | Yes |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromDrill",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Drill input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DrillSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
