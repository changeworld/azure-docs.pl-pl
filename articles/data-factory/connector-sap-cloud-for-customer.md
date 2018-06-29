---
title: Kopiowanie danych z/do chmury SAP dla klienta przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z chmury SAP dla klienta do zbiornika obsługiwanych magazynów danych (lub) z obsługiwanych źródłowych baz danych do chmury SAP dla klienta przy użyciu fabryki danych.
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
ms.date: 04/17/2018
ms.author: jingwang
ms.openlocfilehash: df45613105c8fb005fc8ba0c796ef768e293c57e
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052436"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Kopiowanie danych z chmury SAP dla klienta (C4C) przy użyciu fabryki danych Azure

W tym artykule omówiono sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z/do chmury SAP dla klienta (C4C). Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Kopiowanie danych z chmury SAP dla klienta do żadnych obsługiwanych ujścia magazynu danych lub kopiowania danych z dowolnego źródła obsługiwanych magazynu danych do chmury SAP dla klienta. Lista magazynów danych, które są obsługiwane jako źródła/wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik umożliwia fabryki danych Azure skopiować dane z/do chmury SAP dla klienta, w tym chmury SAP do sprzedaży, chmurę SAP dla usługi i Cloud SAP rozwiązań społecznościowych zaangażowania.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonego w chmurze SAP dla łącznika klienta.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Chmury SAP obsługi klientów połączonych obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **SapCloudForCustomer**. | Yes |
| url | Adres URL usługi SAP C4C OData. | Yes |
| nazwa użytkownika | Określ nazwę użytkownika, aby nawiązać połączenie SAP C4C. | Yes |
| hasło | Podaj hasło dla konta użytkownika, określone nazwy użytkownika. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. | Brak źródła tak dla obiekt sink |

>[!IMPORTANT]
>Aby skopiować dane do chmury SAP dla odbiorcy, jawnie [utworzyć IR Azure](create-azure-integration-runtime.md#create-azure-ir) z lokalizacją w pobliżu chmury SAP dla klienta i skojarz w połączonej usłudze jak w poniższym przykładzie:

**Przykład:**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez SAP chmury dla zestawu danych klienta.

Aby skopiować dane z programu SAP chmury dla klienta, ustaw właściwość Typ zestawu danych do **SapCloudForCustomerResource**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type zestawu danych: **SapCloudForCustomerResource** |Yes |
| ścieżka | Określ ścieżkę SAP C4C OData jednostki. |Yes |

**Przykład:**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typePoperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę obsługiwanych w chmurze programu SAP klienta źródła właściwości.

### <a name="sap-c4c-as-source"></a>C4C SAP jako źródło

Aby skopiować dane z programu SAP chmury dla klienta, należy ustawić typ źródła w przypadku działania kopiowania do **SapCloudForCustomerSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **SapCloudForCustomerSource**  | Yes |
| query | Określ niestandardowe zapytania OData do odczytu danych. | Nie |

Przykładowe zapytanie można pobrać danych dla określonego dnia: `"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapCloudForCustomerSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>C4C SAP jako odbioru

Kopiowania danych do chmury SAP dla klienta, należy ustawić typ ujścia w działaniu kopiowania do **SapCloudForCustomerSink**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **zbiornika** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **SapCloudForCustomerSink**  | Yes |
| writeBehavior | Zachowanie zapisu operacji. Może to być "Insert", "Update". | Nie. Domyślna "Insert". |
| writeBatchSize | Rozmiar partii operacji zapisu. Rozmiar partii, aby uzyskać najlepszą wydajność może się różnić dla innej tabeli lub tego serwera. | Nie. Domyślnie 10. |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "dataIntegrationUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Mapowanie typu danych dla chmury programu SAP dla klienta

Podczas kopiowania danych z chmury SAP dla odbiorcy, następujące mapowania są używane z chmury SAP dla typów danych klienta do typów danych tymczasowych fabryki danych Azure. Zobacz [schemat i dane typu mapowania](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typu źródłowego: schemat i dane sink.

| Typ danych OData C4C SAP | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | wartość logiczna |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | podwójne |
| Edm.Single | Pojedyncze |
| Edm.Guid | Identyfikator GUID |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Ciąg |
| Edm.Time | Zakres czasu |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
