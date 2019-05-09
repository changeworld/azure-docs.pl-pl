---
title: Kopiowanie danych z Couchbase przy użyciu usługi Azure Data Factory (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z Couchbase do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: 27f327493fbf3d7856b9488ecd0dd2509976ccfc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60533985"
---
# <a name="copy-data-from-couchbase-using-azure-data-factory-preview"></a>Kopiowanie danych z Couchbase przy użyciu usługi Azure Data Factory (wersja zapoznawcza)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych ze Couchbase. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

> [!IMPORTANT]
> Ten łącznik jest obecnie w wersji zapoznawczej. Możesz wypróbować tę funkcję i przekaż nam swoją opinię. Jeśli w swoim rozwiązaniu chcesz wprowadzić zależność od łączników w wersji zapoznawczej, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z Couchbase, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

Usługa Azure Data Factory udostępnia wbudowanego sterownika, aby umożliwić łączność, dlatego nie trzeba ręcznie zainstalować dowolnego sterownika, za pomocą tego łącznika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi Data Factory określonych Couchbase łącznikiem.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku Couchbase połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **Couchbase** | Yes |
| connectionString | Ciąg połączenia ODBC, aby nawiązać połączenie Couchbase. <br/>Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory. Możesz również umieścić ciąg poświadczeń w usłudze Azure Key Vault i ściągania `credString` konfiguracji poza parametry połączenia. Zobacz poniższe przykłady i [Store poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md) artykułu z bardziej szczegółowymi informacjami. | Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Używając środowiskiem Integration Runtime lub Azure Integration Runtime (Jeśli magazyn danych jest publicznie dostępny). Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

**Przykład:**

```json
{
    "name": "CouchbaseLinkedService",
    "properties": {
        "type": "Couchbase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>; Port=<port>;AuthMech=1;CredString=[{\"user\": \"JSmith\", \"pass\":\"access123\"}, {\"user\": \"Admin\", \"pass\":\"simba123\"}];"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: przechowywanie ciąg poświadczeń w usłudze Azure Key Vault**

```json
{
    "name": "CouchbaseLinkedService",
    "properties": {
        "type": "Couchbase",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>; Port=<port>;AuthMech=1;"
            },
            "credString": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Couchbase.

Aby skopiować dane z Couchbase, należy ustawić właściwość typu zestawu danych na **CouchbaseTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **CouchbaseTable** | Yes |
| tableName | Nazwa tabeli. | Nie (Jeśli określono parametr "zapytanie" w źródle działania) |


**Przykład**

```json
{
    "name": "CouchbaseDataset",
    "properties": {
        "type": "CouchbaseTable",
        "linkedServiceName": {
            "referenceName": "<Couchbase linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Couchbase.

### <a name="couchbasesource-as-source"></a>CouchbaseSource jako źródło

Aby skopiować dane z Couchbase, należy ustawić typ źródła w działaniu kopiowania, aby **CouchbaseSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **CouchbaseSource** | Yes |
| query | Umożliwia odczytywanie danych niestandardowe zapytania SQL. Na przykład: `"SELECT * FROM MyTable"`. | Nie (Jeśli określono parametr "tableName" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromCouchbase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Couchbase input dataset name>",
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
                "type": "CouchbaseSource",
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
