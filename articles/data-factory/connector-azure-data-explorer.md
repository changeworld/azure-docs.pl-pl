---
title: Kopiowanie danych do lub z Eksploratora danych Azure przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane do / z Eksploratora danych platformy Azure za pomocą działania kopiowania w potoku usługi Azure Data Factory.
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
ms.date: 02/01/2019
ms.author: orspod
ms.openlocfilehash: f492878ffcb888560d2aed269608950927cebd43
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2019
ms.locfileid: "55570024"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>Kopiuj dane do / z Eksploratora danych Azure przy użyciu usługi Azure Data Factory

W tym artykule przedstawiono sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych do lub z [Eksploratora danych usługi Azure](../data-explorer/data-explorer-overview.md). Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do Eksploratora danych platformy Azure. Można również kopiować dane z Eksploratora danych usługi Azure, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła lub ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md) tabeli.

>[!NOTE]
>Obecnie kopiowanie danych z Eksploratora danych platformy Azure z i do lokalnego magazynu danych przy użyciu środowiskiem Integration Runtime nie jest jeszcze obsługiwany.

Łącznik Eksploratora danych usługi Azure można wykonać następujące czynności:

* Kopiowanie danych przy użyciu uwierzytelniania tokenu aplikacji usługi Azure Active Directory (Azure AD) przy użyciu **nazwy głównej usługi**.
* Jako źródło pobierać dane przy użyciu kwerendy KQL (Kusto).
* Jako obiekt sink dołączyć dane do tabeli docelowej.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi Data Factory określonych do Eksploratora danych usługi Azure łącznika.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku Eksploratora danych platformy Azure, połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość musi być równa **AzureDataExplorer** | Yes |
| endpoint | Adres URL punktu końcowego klastra Eksploratora danych usługi Azure, w tym formacie co `https://<clusterName>.kusto.windows.net`. | Yes |
| baza danych | Nazwa bazy danych. | Yes |
| dzierżawa | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Pobierz ją, umieszczając kursor myszy za pomocą myszy w prawym górnym rogu witryny Azure portal. | Yes |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Yes |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako **SecureString** można bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |

**Przykład właściwości połączonej usługi:**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.kusto.windows.net",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości, które są obsługiwane przez zestaw danych Eksploratora danych usługi Azure.

Aby skopiować dane do Eksploratora danych platformy Azure, należy ustawić właściwość typu zestawu danych na **AzureDataExplorerTable**.

Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość musi być równa **AzureDataExplorerTable** | Yes |
| tabela | Nazwa tabeli, która jest połączona usługa przywołuje. | Tak w przypadku ujścia; Brak źródła |

**Przykład właściwości zestawu danych**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "table": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez Eksploratora usługi Azure danych źródła i ujścia.

### <a name="azure-data-explorer-as-source"></a>Eksplorator danych usługi Azure jako źródło

Aby skopiować dane z Eksploratora danych usługi Azure, należy ustawić **typu** właściwości w źródle działania kopiowania **AzureDataExplorerSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** musi być równa właściwość źródła działania kopiowania: **AzureDataExplorerSource** | Yes |
| query | Zapytanie niestandardowe KQL umożliwia odczytywanie danych. | Yes |
| queryTimeout | Określ czas oczekiwania przed żądaniem zapytania upłynie limit czasu. Wartość domyślna to 10 minut (00: 10:00); dozwolona maksymalna wartość to 1 godzina (01: 00:00). | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Eksplorator danych usługi Azure jako obiekt sink

Aby skopiować dane do Eksploratora danych platformy Azure, należy ustawić właściwość type w ujścia działania kopiowania **AzureDataExplorerSink**. Następujące właściwości są obsługiwane w działaniu kopiowania **ujścia** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** musi być równa właściwości ujścia działania kopiowania: **AzureDataExplorerSink** | Yes |
| ingestionMappingName | Nazwa [mapowania csv](/azure/kusto/management/mappings#csv-mapping) w tabeli. Aby zamapować kolumny ze źródła do eksplorowania danych platformy Azure, umożliwia także działanie kopiowania [mapowania kolumn](copy-activity-schema-and-type-mapping.md). | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional csv mapping name>"
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).