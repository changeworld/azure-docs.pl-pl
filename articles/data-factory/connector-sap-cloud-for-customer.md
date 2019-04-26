---
title: Kopiowanie danych/z rozwiązaniem SAP Cloud klienta przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kopiować dane z rozwiązaniem SAP Cloud klienta do ujścia obsługiwanych magazynów danych (lub) z obsługiwanego źródła danych magazynów w chmurze SAP dla klientów przy użyciu usługi fabryka danych.
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 04/17/2018
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: e4625b934f9e1cf98254f3dee59f9c26e8e16fb5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60578712"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Kopiowanie danych z rozwiązaniem SAP Cloud klienta (C4C) przy użyciu usługi Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do chmury dla oprogramowania SAP dla klientów (C4C). Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Kopiowanie danych z rozwiązaniem SAP Cloud klienta do dowolnego obsługiwanego magazynu danych ujścia lub kopiowanie danych z dowolnego obsługiwanego źródłowego magazynu danych w chmurze SAP dla klienta. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik umożliwia usłudze Azure Data Factory skopiować dane z i do chmury dla oprogramowania SAP dla klientów, w tym w chmurze SAP dla sprzedaży, chmura SAP, usługi i chmura SAP, rozwiązania społecznościowe zaangażowania.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonego w chmurze SAP dla klientów łącznika.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w chmurze SAP dla klientów, połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **SapCloudForCustomer**. | Yes |
| url | Adres URL usługi SAP C4C OData. | Yes |
| nazwa użytkownika | Określ nazwę użytkownika, aby nawiązać połączenie SAP C4C. | Yes |
| password | Określ hasło dla konta użytkownika, która została określona jako nazwy użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. | Brak źródła tak dla ujścia |

>[!IMPORTANT]
>Aby skopiować dane do chmury dla oprogramowania SAP dla klientów, jawnie [tworzenie środowiska Azure IR](create-azure-integration-runtime.md#create-azure-ir) z lokalizacją w chmurze SAP dla klientów i kojarzenia w połączonej usłudze, w poniższym przykładzie:

**Przykład:**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.cn/sap/c4c/odata/v1/c4codata/" ,
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości, obsługiwany przez chmurę SAP dla zestawu danych klienta.

Aby skopiować dane z rozwiązaniem SAP Cloud klienta, należy ustawić właściwość typu zestawu danych na **SapCloudForCustomerResource**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **SapCloudForCustomerResource** |Yes |
| ścieżka | Określ ścieżkę jednostką SAP C4C OData. |Yes |

**Przykład:**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typeProperties": {
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

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez oprogramowanie SAP Cloud klienta źródła.

### <a name="sap-c4c-as-source"></a>C4C SAP jako źródło

Aby skopiować dane z rozwiązaniem SAP Cloud klienta, należy ustawić typ źródłowego w działaniu kopiowania, aby **SapCloudForCustomerSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **SapCloudForCustomerSource**  | Yes |
| query | Określ niestandardowe zapytanie OData do odczytu danych. | Nie |

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

### <a name="sap-c4c-as-sink"></a>C4C SAP jako ujście

Aby skopiować dane do chmury SAP dla klienta, należy ustawić typ ujścia w działaniu kopiowania, aby **SapCloudForCustomerSink**. Następujące właściwości są obsługiwane w działaniu kopiowania **ujścia** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **SapCloudForCustomerSink**  | Yes |
| writeBehavior | Zachowanie zapisu operacji. Może to być "Insert", "Aktualizuj". | Nie. Domyślna "Insert". |
| writeBatchSize | Rozmiar partii operacji zapisu. Rozmiar partii, aby uzyskać najlepszą wydajność może się różnić dla serwera lub innej tabeli. | Nie. Domyślnie 10. |

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

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Mapowanie typu danych w chmurze SAP dla klientów

Podczas kopiowania danych z rozwiązaniem SAP Cloud klienta, następujące mapowania są używane z rozwiązaniem SAP Cloud dla typów danych klienta do typów danych tymczasowych usługi Azure Data Factory. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

| Typ danych OData C4C SAP | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
