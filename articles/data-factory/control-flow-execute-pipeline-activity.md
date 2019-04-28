---
title: Działanie wykonywania potoku w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak umożliwia działanie potoku wykonywania wywołania jeden potok fabryki danych z poziomu innego potoku usługi fabryka danych.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: a0ece499262464bc28f55c37188698a3313e2c04
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60808853"
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Działanie wykonywania potoku w usłudze Azure Data Factory
Działanie Execute Pipeline umożliwia potokowi usługi Data Factory wywoływanie innego potoku.

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
name | Nazwa działania potoku wykonywania. | String | Yes
type | Musi być ustawione na: **ExecutePipeline**. | String | Yes
potok | Odwołanie do zależnego potoku, który wywołuje ten potok. Obiekt odwołania potoku ma dwie właściwości: **referenceName** i **typu**. Właściwość referenceName Określa nazwę potoku odwołania. Właściwość type musi być równa PipelineReference. | PipelineReference | Yes
parameters | Parametry do przekazania do wywoływanej potoku | Obiekt JSON, który mapuje nazwy parametru wartości argumentów | Nie
waitOnCompletion | Określa, czy wykonywania działania ma oczekiwać na wykonywanie potoku zależnych zakończyć. Wartość domyślna to false. | Boolean | Nie

## <a name="sample"></a>Sample
Ten scenariusz obejmuje dwa potoki:

- **Potok wzorca** — ten potok ma jedno działanie Execute Pipeline, który wywołuje potok wywołana. Główny potok przyjmuje dwa parametry: `masterSourceBlobContainer`, `masterSinkBlobContainer`.
- **Wywołany potok** — ten potok zawiera jedno działanie kopiowania kopiuje dane ze źródła obiektów Blob platformy Azure do ujścia obiektu Blob platformy Azure. Wywołany potok przyjmuje dwa parametry: `sourceBlobContainer`, `sinkBlobContainer`.

### <a name="master-pipeline-definition"></a>Definicję wzorca potoku

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
            "sinkBlobContainer": {
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

**Zestaw danych źródłowych**
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

**Zestaw danych będący ujściem**
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

### <a name="running-the-pipeline"></a>Uruchamianie potoku

Aby uruchomić główny potok w tym przykładzie, następujące wartości są przekazywane w celu masterSourceBlobContainer i masterSinkBlobContainer parametrów: 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

Główny potoku przekazuje te wartości do potoku wywołana, jak pokazano w poniższym przykładzie: 

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
        "sinkBlobContainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>Kolejne kroki
Zobacz inne działania przepływu sterowania obsługiwanych przez usługę Data Factory: 

- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie internetowe](control-flow-web-activity.md)
