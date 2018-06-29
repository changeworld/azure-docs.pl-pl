---
title: Wykonanie działania potok w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej, wykorzystania działania potoku wykonania do wywołania jeden potoku fabryki danych z innego potoku fabryki danych.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 2aa25004fb9c2e914cd8c669095953e174686197
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051767"
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Wykonanie działania potok w fabryce danych Azure
Działanie wykonanie potoku umożliwia potoku fabryki danych wywołać inny potoku.

## <a name="syntax"></a>Składnia

```json
{
    "name": "MyPipeline",
    "properties": {
        "activities": [
            {
                "name": "ExecutePipelineActivity",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {                        
                        "mySourceDatasetFolderPath": {
                            "value": "@pipeline().parameters.mySourceDatasetFolderPath",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "<InvokedPipelineName>",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                 }
            }
        ],
        "parameters": [
            {
                "mySourceDatasetFolderPath": {
                    "type": "String"
                }
            }
        ]
    }
}
```

## <a name="type-properties"></a>Właściwości typu
Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
name | Nazwa działania potoku execute. | Ciąg | Yes
type | Musi mieć wartość: **ExecutePipeline**. | Ciąg | Yes
potok | Potok odwołanie do zależnego potok, który wywołuje tego potoku. Obiekt potoku odwołanie ma dwie właściwości: **nazwę odwołania** i **typu**. Właściwość nazwę odwołania określa nazwę odwołania potoku. Właściwość type musi być równa PipelineReference. | PipelineReference | Yes
parameters | Parametry do przekazania do wywoływanej potoku | Obiekt JSON, który mapuje nazwy parametru wartości argumentów | Nie
waitOnCompletion | Określa, czy wykonania działania czeka na zakończenie wykonywania potoku zależnych. | Wartość domyślna to false. | Wartość logiczna | Nie

## <a name="sample"></a>Sample
Ten scenariusz ma dwie części:

- **Potoku głównego** -tego potoku ma jedno działanie wykonanie potoku wywołującą wywołanej potoku. Główny potoku przyjmuje dwa parametry: `masterSourceBlobContainer`, `masterSinkBlobContainer`.
- **Wywołana potoku** -tego potoku ma jedno działanie kopiowania, które kopiuje dane ze źródła obiektów Blob platformy Azure do ujścia obiektów Blob platformy Azure. Wywołana potoku przyjmuje dwa parametry: `sourceBlobContainer`, `sinkBlobContainer`.

### <a name="master-pipeline-definition"></a>Definicja potoku głównego

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ExecutePipeline",
        "typeProperties": {
          "pipeline": {
            "referenceName": "invokedPipeline",
            "type": "PipelineReference"
          },
          "parameters": {
            "sourceBlobContainer": {
              "value": "@pipeline().parameters.masterSourceBlobContainer",
              "type": "Expression"
            },
            "sinkBlobCountainer": {
              "value": "@pipeline().parameters.masterSinkBlobContainer",
              "type": "Expression"
            }
          },
          "waitOnCompletion": true
        },
        "name": "MyExecutePipelineActivity"
      }
    ],
    "parameters": {
      "masterSourceBlobContainer": {
        "type": "String"
      },
      "masterSinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="invoked-pipeline-definition"></a>Potok wywołanej definicji

```json
{
  "name": "invokedPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "SourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

**Połączona usługa**

```json
{
    "name": "BlobStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "value": "DefaultEndpointsProtocol=https;AccountName=*****",
        "type": "SecureString"
      }
    }
  }
}
```

**Zestaw źródła danych**
```json
{
    "name": "SourceBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sourceBlobContainer",
        "type": "Expression"
      },
      "fileName": "salesforce.txt"
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

**Obiekt sink zestawu danych**
```json
{
    "name": "sinkBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sinkBlobContainer",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

### <a name="running-the-pipeline"></a>Uruchomiona potoku

Aby uruchomić główny potoku, w tym przykładzie, następujące wartości są przekazywane parametrów masterSourceBlobContainer i masterSinkBlobContainer: 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

Główny potoku przekazuje te wartości do potoku wywołanej, jak pokazano w poniższym przykładzie: 

```json
{
    "type": "ExecutePipeline",
    "typeProperties": {
      "pipeline": {
        "referenceName": "invokedPipeline",
        "type": "PipelineReference"
      },
      "parameters": {
        "sourceBlobContainer": {
          "value": "@pipeline().parameters.masterSourceBlobContainer",
          "type": "Expression"
        },
        "sinkBlobCountainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>Kolejne kroki
Zobacz inne działania przepływu sterowania obsługiwane przez fabrykę danych: 

- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działania w sieci Web](control-flow-web-activity.md)
